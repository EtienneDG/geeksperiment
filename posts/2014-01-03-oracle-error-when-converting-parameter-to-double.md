---
id: 374
title: '[Oracle] Error when converting parameter to double'
date: 2014-01-03T11:50:11+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=374
permalink: /oracle-error-when-converting-parameter-to-double/
categories:
  - Tips and tricks
tags:
  - 'C#'
  - collection
  - Oracle
  - parameter
  - planner
  - tips
---
A quick highlight on an error encountered when trying to call an Oracle stored procedure with parameters that are unwillign to get along. It&rsquo;s a basic type error, that only shows at runtime aka : &laquo;&nbsp;Input string was not in a correct format.&nbsp;&raquo;<!--more-->

# The configuration and the protagonists

My web app, a scheduling and people management (C# &#8211; MVVM/Ssilverlight/[Telerik&rsquo;s ScheduleView](http://demos.telerik.com/silverlight/#ScheduleView/FirstLook)) has a calendar tool, it will be called _&laquo;&nbsp;Planner&nbsp;&raquo;_ through out this post. Appointements can be created in _Planner_ (using telerik&rsquo;s component) or via <span style="line-height: 1.5em;">calling a </span><span style="line-height: 1.5em;">web service . In addition, a interface to another application is set up, this other application writes messages into queue (JMS) and <em>Planner</em> consumes them. Between consumption and integration of the data in the DB, are a few steps. Here is the way it should go:</span>

[<img class="aligncenter wp-image-392 size-medium" src="http://geeksperiment.com/wp-content/uploads/2014/01/simple_diagram_planner3-258x300.png" alt="worflow diagram planner" width="330" height="350" srcset="http://geeksperiment.com/wp-content/uploads/2014/01/simple_diagram_planner3-258x300.png 258w, http://geeksperiment.com/wp-content/uploads/2014/01/simple_diagram_planner3.png 432w" sizes="(max-width: 258px) 100vw, 300px" />](http://geeksperiment.com/wp-content/uploads/2014/01/simple_diagram_planner3.png)

# 

Except it doesn&rsquo;t go that smoothly. The stacktrace is quite explicite though:

```csharp
Input string was not in a correct format.

 at System.Number.ParseDouble(String value, NumberStyles options, NumberFormatInfo numfmt)

 at System.String.System.IConvertible.ToDouble(IFormatProvider provider)

 at System.Convert.ToDouble(Object value)

 at Oracle.DataAccess.Client.OracleParameter.PreBind_Double(OracleConnection conn, OracleDbType OraDbType)

 at Oracle.DataAccess.Client.OracleParameter.PreBind(OracleConnection conn, IntPtr errCtx, Int32 arraySize)

 at Oracle.DataAccess.Client.OracleCommand.ExecuteNonQuery()

 at Dalkia.Dispatch.Data.EntityAccessBase`1.UpdateEntity(T newObj, T oldObj)

 at Dalkia.Dispatch.Service.Services.RDVService.Planner.Services.IRDVService.SaveAllRDV(List`1 listRDV)
```

So clearly I&rsquo;m trying to convert to double something that shouldn&rsquo;t be. Quite simple to find which attribute is raising the error, if you put aside the fact that the only queue up for testing purposes is not reachable on our corporate network, so the interface is not fully tested. Nontheless, the culprit was found.

Below are the parameters passed to Oracle:

```csharp
protected override ICollection<IDataParameter> GetUpdateParameters(IDbCommand command, RDV newRDV, RDV oldRDV)
{

    ICollection<IDataParameter> collection = new List<IDataParameter>();

    // Input
    collection.Add(CreateParameter(command, "RDV_ID_", ParameterDirection.Input, DbType.Decimal, newRDV.IdRdv));

    collection.Add(CreateParameter(command, "SUBJECT_", ParameterDirection.Input, DbType.String, newRDV.Sujet));

    collection.Add(CreateParameter(command, "BODY_", ParameterDirection.Input, DbType.String, newRDV.Corps));

    collection.Add(CreateParameter(command, "START_RDV_", ParameterDirection.Input, DbType.Date, newRDV.DateDeb));

    collection.Add(CreateParameter(command, "END_RDV_", ParameterDirection.Input, DbType.Date, newRDV.DateFin));

    collection.Add(CreateParameter(command, "ISALLDAYEVENT_", ParameterDirection.Input, DbType.String, (newRDV.IsAllDay ? "Y" : "N")));

    return collection;

}
```

<span style="line-height: 1.5em;">Looking in the class EntityBaseAccess which is the standard class giving access to CRUD procedure for any object in Planner.</span>

```csharp
/// <summary>
/// Create IDbDataParameter
/// </summary>
/// <param name="command">Command to create the parameter on</param>
/// <param name="parameterName">Name of the parameter</param>
/// <param name="direction">Direction of the parameter : IN or OUT</param>
/// <param name="dbType">DataType</param>
/// <param name="value">Value of the parameter</param>
/// <returns>Instance of the new parameter</returns>
protected IDataParameter CreateParameter(IDbCommand command, string parameterName,
                     ParameterDirection direction, DbType dbType, object value = null)
{

    OracleParameter para = new OracleParameter();
    para.ParameterName = parameterName;
    para.Direction = direction;

    switch (dbType)
    {
        case DbType.DateTime:
            para.DbType = DbType.DateTime;
            break;
        case DbType.Boolean:
            para.OracleDbType = OracleDbType.Int32;
            break;
        case DbType.String:
            para.OracleDbType = OracleDbType.Varchar2;
            break;
        case DbType.Object:
            para.OracleDbType = OracleDbType.RefCursor;
            break;
        case DbType.Double:
        case DbType.Decimal:
            para.OracleDbType = OracleDbType.Double;
            break;
        default:
            break;
    }

    if (value == null)
        para.Value = DBNull.Value;
    else
        para.Value = value;
    return para;
}
```
Every parameter passed to an Oracle stored procedure is converted to respect the DB format, the only one been converted to double being &laquo;&nbsp;decimal&nbsp;&raquo;. The problem was indeed on the &laquo;&nbsp;IdRdv&nbsp;&raquo; attribute.

# Conclusion

To be frank it&rsquo;s a conception problem rather than a real bug : t<span style="line-height: 1.5em;">he two applications are synchronized but the coherence controls are made on other attributes so </span><span style="line-height: 1.5em;">why do we keep the ID (containing letters) coming from this other application, since we are not sharing a same DB ? No reason at all and since some of this is already in production we can&rsquo;t change our model. So to be perfectly logic the ID of every appointment is derivated from an oracle sequence, insuring the unicity and the preventing troubles like the one described here.</span>
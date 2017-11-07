---
id: 534
title: '[Excel] Converting csv to JSON'
date: 2014-05-14T10:33:53+00:00
author: Etienne
excerpt: A snippet to convert Excel data to JSON.
layout: post
guid: http://geeksperiment.com/?p=534
permalink: /excel-converting-csv-to-json/
categories:
  - Tips and tricks
tags:
  - excel
  - json
  - tips
---
For those Excel lovers out there, a simple snippet to convert Excel data to JSON.

  1. Go to the Developper tab and click on Visual Basic editor
  2. Double click on &lsquo;Module 1&rsquo;
  3. Past the code below:
  ```vb
Function ConvertToJSON(ByRef headers As Range, ByRef content As Range) As String
  'init var
  Dim res As String
  initStr = "{"
  res = "["
  maxJ = content.Rows.Count
  maxI = content.Columns.Count
  'Main loop
  For j = 1 To maxJ
  res = res & initStr
      For i = 1 To maxI
            key_ = Chr(34) & headers(i) & Chr(34) & ": "
            value_ = Chr(34) & content(i)(j) & Chr(34) & ", "
            res = res & key_ & value_
      Next i
      'close json object
      res = Left(res, Len(res) - 2) + "},"
  Next j
  'close array
  res = Left(res, Len(res) - 1) & "]"
  ConvertToJSON = res
End Function
```

4. You should now be able to call your new custom function like so:[<img class="aligncenter size-full wp-image-538" src="http://geeksperiment.com/wp-content/uploads/2014/05/excel_json_converter.png" alt="excel_json_converter" width="901" height="427" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/excel_json_converter.png 901w" sizes="(max-width: 901px) 100vw, 901px" />](http://geeksperiment.com/wp-content/uploads/2014/05/excel_json_converter.png)
5. Result is validated by JSONLint:[<img class="aligncenter size-full wp-image-548" src="http://geeksperiment.com/wp-content/uploads/2014/05/jsonlint_valide.png" alt="jsonlint_valide" width="974" height="500" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/jsonlint_valide.png 974w" sizes="(max-width: 974px) 100vw, 974px" />](http://geeksperiment.com/wp-content/uploads/2014/05/jsonlint_valide.png)
---
id: 640
title: 'String similarity : simplistic implementation of the Levenshtein distance'
date: 2015-05-09T18:25:51+00:00
author: Etienne
excerpt: 'Basic comparison of two strings (i.e. using an equality operator) allows you to determine if they are equal but not if they are <em>similar</em>.'
layout: post
guid: http://geeksperiment.com/?p=640
permalink: /string-similarity-simplistic-implementation-of-the-levenshtein-distance/
image: /wp-content/uploads/2015/05/autocorrect_fail_g1.jpg
categories:
  - Uncategorized
tags:
  - 'C#'
---
Basic comparison of two strings (i.e. using an equality operator) allows you to determine if they are equal but not if they are _similar_.

Why would someone want to determine the similarity of two strings? A few applications are : spell-checking (the famous autocorrect), anti spam techniques&#8230;
  
To determine the similarity a &laquo;&nbsp;few&nbsp;&raquo; algorithms exist. Some of them use this principle :

> The similarity of two strings can be computed from the number of operations necessary to make those two strings equal.

The principal operations are : substition, insertion, deletion. Simple examples:

  * COLD and FOLD => Only <u>one</u> operation (substitution) to make the strings equal.
  * COLD and HOT => <u>Three</u> operations (two substitutions and an insertion)

### Levenshtein distance

A simple C# implementation of the [Levenshtein algorithm](http://en.wikipedia.org/wiki/Levenshtein_distance) :

```csharp
static void Main(string[] args)
{
    var strings = new string[] 
    {
        "carrotte",
        "carreau",
        "crarotte",
        "crotter",
        "",
        "carote"
    };
    //should be tolower
    var originalString = "carote";

    //main loop to confront each string with the originalOne
    foreach (var str in strings)
    {
        int levenshteinDistance  = 0;
        //to avoid loop turns
        if (str == originalString)
        {
            //Strings are identical => distance = 0
            continue;
        }
        if (str.Length == 0)
        {
            //str is empty => distance = originalString.Length
            continue;
        }
        if (originalString.Length == 0)
        {
            //originalString is empty => distance = str.Length
            continue;
        } 
        
        var minDistance = Math.Abs(str.Length - originalString.Length);
        var maxDistance = Math.Max(str.Length, originalString.Length);

        var distanceMatrix = new int[str.Length+1, originalString.Length+1];

        for (int i = 0; i <= str.Length; distanceMatrix[i, 0] = i++) ;
        for (int j = 0; j <= originalString.Length; distanceMatrix[0, j] = j++) ;

        for (int j = 1; j <= originalString.Length; j++)
        {
            for (int i = 1; i <= str.Length; i++)
            {
                //equality for the characters
                if (str[i-1] == originalString[j-1])
                {
                    distanceMatrix[i, j] = distanceMatrix[i - 1, j - 1];
                }
                else
                {
                    distanceMatrix[i, j] = Math.Min(
                                                    Math.Min(
                                                        distanceMatrix[i-1,j]+1, //deletion
                                                        distanceMatrix[i,j-1]+1), //insertion
                                                    distanceMatrix[i-1,j-1]+1 //substition
                                                    );
                }
                
            }    
        }
        //higher i,j point is the Levenshtein distance
        levenshteinDistance = distanceMatrix[str.Length, originalString.Length];
        var result = 100 - (double)(levenshteinDistance ) / (maxDistance) * 100;
    }
    
}
```
Ahd here are a few resutls :

[<img class="aligncenter size-full wp-image-648" src="http://geeksperiment.com/wp-content/uploads/2015/05/levenshteinDistanceExamples.png" alt="levenshteinDistanceExamples" width="373" height="545" srcset="http://geeksperiment.com/wp-content/uploads/2015/05/levenshteinDistanceExamples.png 373w" sizes="(max-width: 373px) 100vw, 373px" />](http://geeksperiment.com/wp-content/uploads/2015/05/levenshteinDistanceExamples.png)

### Going further

As said this implementation is really simplistic. To be more accurate we could add an operation &laquo;&nbsp;transposition&nbsp;&raquo; (i.e. swap) to check if two characters are inverted and count that as one operation instead of two substitions.

Also each operation has a cost of 1 regardless of its nature and the &lsquo;difference&rsquo; between the two characters being compared. We could add a keyboard-distance-based cost between two letters.
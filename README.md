---
services: data-lake-analytics
platforms: dotnet
author: saveenr
---

# USQL Named Lambda functions

### Simple one-line lambda

```
DECLARE @myToUpper Func<string, string> = (s) => s.ToUpper();

@searchlog = 
    EXTRACT UserId          int, 
            Start           DateTime, 
            Region          string, 
            Query           string, 
            Duration        int, 
            Urls            string, 
            ClickedUrls     string
    FROM @"/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

@searchlog = 
    SELECT Query, 
    @myToUpper(Query) AS QueryUpper
    FROM @searchlog;
    
OUTPUT @searchlog 
    TO @"/Samples/Output/SearchLog_output.tsv"
    USING Outputters.Tsv();
```

### Multi-line lambda

```
DECLARE @myToUpper2 Func<string, string> = (s) => 
    {
        if (s.Length == 0 ) { throw new System.ArgumentException(); }
        s = s.Trim();
        s = s.ToUpper();
        return s;
    };

//Define schema of file, must map all columns
@searchlog = 
    EXTRACT UserId          int, 
            Start           DateTime, 
            Region          string, 
            Query           string, 
            Duration        int, 
            Urls            string, 
            ClickedUrls     string
    FROM @"/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

@searchlog = 
    SELECT Query, 
    @myToUpper2(Query) AS QueryUpper
    FROM @searchlog;
    
OUTPUT @searchlog 
    TO @"/Samples/Output/SearchLog_output.tsv"
    USING Outputters.Tsv();
```

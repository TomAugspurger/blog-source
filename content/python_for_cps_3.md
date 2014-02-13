Title: Using Python to tackle the CPS (Part 3)
Date: 2014-02-5 12:00
Slug: tackling the cps (part 3)
Category: Python

Now that we have the data ([part 1](http://tomaugspurger.github.io/blog/2014/01/27/tackling%20the%20cps/)) and the parsed data dictionary files ([part 2]()), we can start to make sense of the raw monthly CPS tables.

As a reminder, we currently have a parsed data dictionary:

```
         id  length  start  end
0    HRHHID      15      1   15
1   HRMONTH       2     16   17
2   HRYEAR4       4     18   21
3  HURESPLI       2     22   23
4   HUFINAL       3     24   26
         ...     ...    ...  ...

```

that describes the raw CPS files:

```bash
/V/H/U/t/D/C/monthly head -n 2 cpsb9401
881605952390 2  286-1 2201-1 1 1 1-1 1 5-1-1-1  22436991 1 2 1 6 194 2A61 -1 2 2-1-1-1-1 363 1-15240115 3-1 4 0 1-1 2 1-1660 1 2 2 2 6 236 2 8-1 0 1-1 1 1 1 2 1 2 57 57 57 1 0-1 2 5 3-1-1 2-1-1-1-1-1 2-1-1-1-1-1-1-1-1-1-1-1 -1-1-1-1-1-1-1-1-1-1-1 -1-1  169-1-1-1-1-1-1-1-1-1-1-1-1-1-1 -1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 -1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 2-1 0 4-1-1-1-1-1-1 -1-1-1 0 1 2-1-1-1-1-1-1-1-1-1 -1 -1-1-1 -1 -1-1-1 0-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 0-1-1-1-1-1  -1  -1  -1  0-1-1      0-1-1-1      -1      0-1-1-1-1-1-1-1-1 2-1-1-1-1  22436991        -1         0  22436991  22422317-1         0 0 0 1 0-1 050 0 0 0 011 0 0 0-1-1-1-1 0 0 0-1-1-1-1-1-1 1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 1 1 1 1 1 1 1 1 1 1 1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 1 1 1-1-1-1
881605952390 2  286-1 2201-1 1 1 1-1 1 5-1-1-1  22436991 1 2 1 6 194 2A61 -1 2 2-1-1-1-1 363 1-15240115 3-1 4 0 1-1 2 3-1580 1 1 1 1 2 239 2 8-1 0 2-1 1 2 1 2 1 2 57 57 57 1 0-1 1 1 1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 2-140-1-1 40-1-1-1-1 2-1 2-140-1 40-1   -1 2 5 5-1 2 3 5 2-1-1-1-1-1-1 -1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 -1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 1-118 1 1 1 4-1-1-1 -1 1-1 1 2-1-1-1-1-1-1-1 4 1242705-1-1-1 -1  3-1-1 1 2 4-1 1 6-1 6-136-1 1 4-110-1 3 1 1 1 0-1-1-1-1  -1-1  -1  -1  0-1-1      0-1-1-1            -10-1-1-1-1-1-1-1-1-1-1-1-1-1  22436991        -1         0  31870604  25650291-1         0 0 0 1 0-1 0 1 0 0 0 0 0 0 0 0-1-1-1-1 0 0-1 1 1 0 1 0 1 1 0 1 1 1 0 1 0 1 1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1 0 0 0-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1-1
```

The goal in this step is to take the raw files and merge them together.
I mentioned in the [first post](http://tomaugspurger.github.io/blog/2014/01/27/tackling%20the%20cps/) that a household is measured 8 times over 16 months.

Choosing the appropriate data structure ahead of time is both important and difficult. It's important because that will determine how easily you can manipulate and analyze your data down the road. It's difficult because you may not know ahead of time what you want to do with your data, or what form actually is easiest. I ended up storing the data in both Panels (`month` x `person` x `feature`) and DataFrames with the `month` and `person` identifiers in a MultiIndex.

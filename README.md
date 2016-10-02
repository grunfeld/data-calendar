# Data

Data is populated in the data-checkins attribute. It is a list with format
```
data1 date1 data2 date2 data3 date3 ...
```

Additional data fields can be added as the 

# Sample screenshot

Squares show contributions of that day on hovering. + - = are line additions, deletions and changes respectively.

![alt tag](https://raw.githubusercontent.com/grunfeld/data-calendar/master/example.png)

# bash + awk script to get the data from a perforce repository

```
p4 changes -s submitted -u USERNAME @2016/01/01,@now | awk '{print $2}' | while read x ; do (p4 describe -ds $x) ; done | grep "^Change\|^add\|^deleted\|^changed" | awk 'BEGIN {a=0; d=0; c=0; dt=""; ta=0; td=0; tc=0;} { if ($1 == "Change") { if (dt == "") { dt = $6; } else if (dt != "" && dt != $6) { s = a+d+c; printf("%d %s ", s, dt); a=0; d=0; c=0; dt = $6;}} if ($1 == "add") { a+=$4; ta+=$4;} if ($1 == "changed") { c+=$4; tc+=$4;} if ($1 == "deleted") { d+=$4; td+=$4;} } END {s = a+d+c; printf("%d %s\n+%d-%d=%d\n", s, dt, ta, td, tc);}' | awk '{ split($0, a, " "); l = length(a); if (l > 1) { for (i = l; i >= 1; i-=2) { printf("%s %s ", a[i-1], a[i]); } print "" } else print $0 }'
```

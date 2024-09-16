---
title : "Step 2 - Load sample data into the table"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2.2. </b> "
---
Now that you have created the table, you can load some sample data into the table by running the following Python script.

```bash
cd /home/ubuntu/workshop
python load_logfile.py logfile ./data/logfile_small1.csv
```

The parameters in the preceding command: 1) Table name = `logfile` 2) File name = `logfile_small1.csv`

The output will look like the following.

```txt
row: 100 in 0.780548095703125
row: 200 in 7.2669219970703125
row: 300 in 1.547729730606079
row: 400 in 3.9651060104370117
row: 500 in 3.98996901512146
RowCount: 500, Total seconds: 17.614499807357788
```

**Curious behavior:** You might wonder why one of the runs took more than five seconds. See the next step for the explanation.
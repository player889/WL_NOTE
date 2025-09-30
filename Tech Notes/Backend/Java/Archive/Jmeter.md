[TOC]

# Jmeter

## JMeter – How to generate the Dashboard Report for your test

`Step 1`

```powershell
jmeter -n -t C:\jmeter\gen-report.jmx -l C:\jmeter\gen-report.jtl
```

==jmeter -n -t [path to test JMX file] -l [path to result file]==

`Step 2`

```powershell
jmeter -g C:\jmeter\gen-report.jtl -o C:\jmeter\report
```

==jmeter -g [path to result file] -o [path to report output folder]==


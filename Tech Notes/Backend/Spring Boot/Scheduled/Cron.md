"0 0 12 * * ?" Fire at 12pm (noon) every day
"0 15 10 ? * *" Fire at 10:15am every day
"0 15 10 * * ?" Fire at 10:15am every day
"0 15 10 * * ? *" Fire at 10:15am every day
"0 15 10 * * ? 2005" Fire at 10:15am every day during the year 2005
"0 * 14 * * ?" Fire every minute starting at 2pm and ending at 2:59pm, every day
"0 0/5 14 * * ?" Fire every 5 minutes starting at 2pm and ending at 2:55pm, every day
"0 0/5 14,18 * * ?" Fire every 5 minutes starting at 2pm and ending at 2:55pm, AND fire every 5 minutes starting at 6pm and ending at 6:55pm, every day
"0 0-5 14 * * ?" Fire every minute starting at 2pm and ending at 2:05pm, every day
"0 10,44 14 ? 3 WED" Fire at 2:10pm and at 2:44pm every Wednesday in the month of March.
"0 15 10 ? * MON-FRI" Fire at 10:15am every Monday, Tuesday, Wednesday, Thursday and Friday
"0 15 10 15 * ?" Fire at 10:15am on the 15th day of every month
"0 15 10 L * ?" Fire at 10:15am on the last day of every month
"0 15 10 ? * 6L" Fire at 10:15am on the last Friday of every month
"0 15 10 ? * 6L" Fire at 10:15am on the last Friday of every month
"0 15 10 ? * 6L 2002-2005" Fire at 10:15am on every last friday of every month during the years 2002, 2003, 2004 and 2005
"0 15 10 ? * 6#3" Fire at 10:15am on the third Friday of every month


[Spring boot Cron tools](http://www.cronmaker.com/)

|          30 * * * * ? | 每半分鐘觸發任務                                |
| --------------------: | ----------------------------------------------- |
|         30 10 * * * ? | 每小時的10分30秒觸發任務                        |
|         30 10 1 * * ? | 每天1點10分30秒觸發任務                         |
|        30 10 1 20 * ? | 每月20號1點10分30秒觸發任務                     |
|     30 10 1 20 10 ? * | 每年10月20號1點10分30秒觸發任務                 |
|  30 10 1 20 10 ? 2011 | 2011年10月20號1點10分30秒觸發任務               |
|   30 10 1 ? 10 * 2011 | 2011年10月每天1點10分30秒觸發任務               |
| 30 10 1 ? 10 SUN 2011 | 2011年10月每周日1點10分30秒觸發任務             |
|    15,30,45 * * * * ? | 每15秒，30秒，45秒時觸發任務                    |
|       15-45 * * * * ? | 15到45秒內，每秒都觸發任務                      |
|        15/5 * * * * ? | 每分鐘的每15秒開始觸發，每隔5秒觸發一次         |
|     15-30/5 * * * * ? | 每分鐘的15秒到30秒之間開始觸發，每隔5秒觸發一次 |
|         0 0/3 * * * ? | 每小時的第0分0秒開始，每三分鐘觸發一次          |
|   0 15 10 ? * MON-FRI | 星期一到星期五的10點15分0秒觸發                 |
|         0 15 10 L * ? | 每個月最後一天的10點15分0秒觸發任務             |
|        0 15 10 LW * ? | 每個月最後一個工作日的10點15分0秒觸發任務       |
|        0 15 10 ? * 5L | 每個月最後一個星期四的10點15分0秒觸發任務       |
|       0 15 10 ? * 5#3 | 每個月第三周的星期四的10點15分0秒觸發任務       |
|   */5 * * * * MON-FRI | 星期一到五每天隔五秒觸發任務                    |
|                       |                                                 |
|                       |                                                 |

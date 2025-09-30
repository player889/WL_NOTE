Cardlite GUI  
4988000000000065  
4966000000000160  
4966000000000343  
4966000000000210

SIT card number  
4966000000000160
4966000000001093 - new Taiwan Dollar

UAT Card number  
5100123400000012 -- CTBC_TWD

LOCAL ENV.
5100123400000020 -- DD

## Blacklist

[https://xd.adobe.com/view/6a10be51-8df5-4bd8-6e91-17c95231557a-1c21/screen/807fc762-fbf2-4a29-a962-93c54c4d2dc0/](https://xd.adobe.com/view/6a10be51-8df5-4bd8-6e91-17c95231557a-1c21/screen/807fc762-fbf2-4a29-a962-93c54c4d2dc0/ "https://xd.adobe.com/view/6a10be51-8df5-4bd8-6e91-17c95231557a-1c21/screen/807fc762-fbf2-4a29-a962-93c54c4d2dc0/")
PW: WLux1000

IAS -
Corporate -> 4 level (When ACP = 0 , becomes 4 level)
Consumer -> 3 level

# Auth Check Item GUI Error

It's related to the data, it seems that the initial data of authorization_check_item all the records the itn_idn_sky is `WL `
I replaced them to WLCN using SQL, then re-run the data sync manually

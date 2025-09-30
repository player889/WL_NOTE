10.20.20.214:22

![[MicrosoftTeams-image (7).png]]  
31505
10.20.20.65:31505  
![[MicrosoftTeams-image (8).png]]  
guest/C6Y\*cfBgj32!  
![[MicrosoftTeams-image (9).png]]  
sometime some local ports cannot be used and you might choose another local port other than 31505
![[MicrosoftTeams-image (10).png]]

![[MicrosoftTeams-image (11).png]]  
"Use module classpath" need to select `IAS-app`        
![[MicrosoftTeams-image (12).png]]  
Then, just add breakpoint and trigger transaction, your debugger should reflect the JVM running the `ias-app` on QA k8s env

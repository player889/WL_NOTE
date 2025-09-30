# Idea Hotkey
1. Navigate to Implementation `ctrl` + `alt` + `B`
2. Find usage `alt` + `F7` 
3. Jump between methods `alt` + `ris:ArrowUp` or `alt` + `ris:ArrowDown`
4. Udo `ctrl` + `Z`
5. Redo `ctrl` + `shift` + `Z`
6. 查看指定方法的所有調用方和被調方 `ctrl` + `alt` + `H`
7. 類關系圖  `ctrl` + `alt` + `U`
8. 父類和子類繼承關系 `ctrl` + `H`
9. 定義的變量在哪裏被聲明/調用 `ctrl` + `B`
10. 查看類的結構  `ctrl` + `7`
11. next/previous method `alt` + `fas:ArrowUp` / `fas:ArrowDown`
12. class structure `ctrl` + `F12`
13. Select All occurrences `shift` + `ctrl` + `alt` + `j`
14. Add occurences `alt` + `j` 
15. Remove occurence  `shift`+`alt` + `j`
16. End of Line `shift` + `alt` + `G`
17. Highlight row and `alt` + `shift` + `ins`
	1. Jump to end of each line `End`
	2. select lines `shift` + `ris:ArrowUp` / `ris:ArrowDown`
18. Fix unused import `ctrl`+`alt`+`o`
19. Find url path `ctrl`+`shift`+`\`

# Change Schema Color

![[Pasted image 20230323153157.png]]

# Format Code after comment java code
![[Pasted image 20230410095122.png]]

# How to fix intellij doest not how the project folder when open the project folder?

- Filer -> Setting -> Repair IDE


# IntelliJ: Never use wildcard imports
![[Pasted image 20230515221351.png]]

# How to exclude files which do not want to show in idea project panel

![[Pasted image 20230522112619.png]]

# Unable to create a new module (module already exists) Intellij
1. Go to .idea folder
2. open compiler.xml
3. delete the module name (already exist)

## Add Sub-Module in Intellij

![[2023-05-22_142318.png|400]]![[2023-05-22_142338.png|350]]

## How to fix the pom.xml file with strikethrough mark
![[2023-05-19_170510 1.png]]

## How to use Cmder as default terminal in Intellij
"cmd.exe" /k "C:\jay\cmder\vendor\init.bat""
![[Pasted image 20230525115158.png]]

## Modules are missing after open the idea.
> Delete the .idea folder inside the workspace folder.
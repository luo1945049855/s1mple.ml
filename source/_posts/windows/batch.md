---
title:  "Batch Script"
date:   2020-09-24
categories: Windows
---

`echo off` command to ensure that the commands are not shown when the code is executed.
`@echo off` makes the command apply to itself as well.

## Command Line Arguments

command line arguments can be accessed by `%1`, `%2`, `%3` till `%9`. But we can use
`SHIFT` command to overcome this limitation.

```batch
:Loop
IF "%1%=="" GOTO :Continue
    echo %1%
SHIFT
GOTO :Loop
:Continue
```

## Set Command

syntax:

```bat
set /A variable=value

set message=Hello World
echo %message%

set /A a=1
set /A b=2
set /A c=%a%+%b%

set global=global variable
setlocal
set local=local variable
endlocal

```

`set` command can be used to initialize a variable.

`/A` is used if the valuee needs to be numeric in nature.

`%` sign has to be used to reference a variable.

`setlocal` command to make variables local to the scope of your script.
any variable assignments revert upon calling `endlocal`, `exit`, or execution
reaches the end of file(EOF) in your script.

`setlocal` command is first used to ensure the command processor takes a backup of all environment variables.
`endlocal` is automatically called when the end of the batch file is reached, i.e. by calling GOTO:EOF

## Environment Variables

```batch
echo %OS%
```

`%` sign has to be used to reference a environment variables.

## Comments

```batch
rem this is rem comments.
::  this is :: comments.
```

`rem` is a command so it will slow down the code.

## String

```batch

set message=Hello World

:: empty string.

set a=

:: check existence of empty string.
:: To Check for existence of an empty string, you need to encompass the variable name in square brackets
:: and also compare it against a value in square brackets as shown in the example.

if [%a%]==[] echo "string a is empty"

:: interpolation
:: concatenation

set a=hello
set b=world
set /A c=50
set d=%a% and %b% plus %c%
echo %d%

:: string length. there is no length function defined for finding the length of a string.

@echo off
set src=hello world
call :strlen src strlen
echo string is %strlen% characters long.
exit /b

:strlen
setlocal enabledelayedexpansion

:strLen_Loop
    if not "!%1:~%len%!"=="" set /A len+=1 & goto :strLen_Loop
(endlocal & set %2=%len%)
goto :eof

:: string to int. var is 12350 after calculation.

set var=12345
set /A var=%var% + 5
echo %var%

:: sub string. the index value can be negative.

%variable:~num_chars_to_skip%
%variable:~num_chars_to_skip,num_chars_to_keep%
%variable:~-num_chars_to_skip,-num_chars_to_keep%

set x=1000
set y=1
set y=%y%
echo %x%

set y=%y:~-4%
echo %y%

rem output will be
1000
1

:: string remove sub string. syntax: %src:stringtoberemoved=%
:: string replace string. syntas: %src:stringtobereplaced=replacestring%

set src=The parent of substring
echo %src%
set src=%src:substring=%
echo %src%

set src=Spaces need to be removed
set src=%src:=%
echo src

:: left sub index.
:: right sub index.

set src=This message needs changed.
echo %src%
set src_left=%src:~4%
echo %src_left%
set src_right=%src:~-8%
echo %src_right%

```

## Array

```batch

:: An array is created by using the following two ways.

set a[0]=1
set a[1]=1
echo %a[0]%

set list = 1 2 3 4
(for %%a in (%list%) do (
    echo %%a
))

:: Length of an Array

set Arr[0]=1
set Arr[1]=2
set Arr[2]=3
set Arr[3]=4
set "x=0"
:SymLoop

if defined Arr[%x%] (
    call echo %%Arr[%x%]%%
    set /a "x+=1"
    GOTO :SymLoop
)
echo "The length of the array is" %x%

:: Create Structures in Arrays(TODO: ?)

set len = 3
set obj[0].Name = Joe
set obj[0].ID = 1
set obj[1].Name = Mark
set obj[1].ID = 2
set obj[2].Name = Mohan
set obj[2].ID = 3
set i = 0
:loop

if %i% equ %len% goto :eof
set cur.Name=
set cur.ID=

for /f "usebackq delims==.tokens=1-3" %%j in (`set obj[%i%]`) do (
   set cur.%%k=%%l
)
echo Name = %cur.Name%
echo Value = %cur.ID%
set /a i = %i%+1
goto loop

```

## If/else

```batch

:: `if else` code is placed in the brackets().

If (condition) (do_something)
ELSE (do_something_else)

:: if defined, which is used to test for the existence of a variable.
:: if exist, which is used to test for the existence of a file.

set str1=string1
if defined str1 echo "variable str1 is defined"

if exist C:\set2.txt echo "File exists"

```

## Operators

```batch
:: Arithmetic Operators
:: +, -, *, /, %

:: Relational Operators
:: EQU, NEQ, LSS, LEQ, GTR, GEQ

:: Logical Operators
:: AND, OR, NOT

:: Assignment Operators
:: +=, -=, *=, /=, %=

:: Bitwise Operators
:: & bitwise "and"
:: | bitwise "or"
:: ^ bitwise "xor" or exclusive or operator
```

## DATE & TIME

```batch

:: format date time(TODO: ?)
@echo off
echo/Today is: %year%-%month%-%day%
goto :EOF
setlocal ENABLEEXTENSIONS
set t = 2&if "%date%z" LSS "A" set t = 1

for /f "skip=1 tokens = 2-4 delims = (-)" %%a in ('echo/^|date') do (
   for /f "tokens = %t%-4 delims=.-/ " %%d in ('date/t') do (
      set %%a=%%d&set %%b=%%e&set %%c=%%f))
endlocal&set %1=%yy%&set %2=%mm%&set %3=%dd%&goto :EOF
```

## Input/Output

```batch
:: stdin is file 0; stdout is file 1; stderr is file 2;
:: combine the stdout and stderr using the file number and the `&` prefix.

Dir C:\ > list.txt
Dir C:\ 2> list.txt
Dir C:\ > list.txt 2>&1

:: stdin. To work with the stdin, you have to use a workaround to archieve this.
:: This can be done by redirecting the command prompt's own stdin, called `CON`.
TYPE CON > list.txt
```

## Return Code

```batch
:: ERRORLEVEL contains the return code of the last executed program or script.

IF %ERRORLEVEL% NEQ 0 (
    do_something
)
EXIT /B %ERRORLEVEL% at the end of the batch file to return the error codes from the batch file.
EXIT /B 7


```

## Loops

```batch
:: while. There is no direct while statement available in Batch Script.
:: But we can do an implementation of this loop very easily by using the if statement and labels.

:label
if (expression) (
    do_something
    increment counter
    GOTO :label
)

set /A index=0
set /A count=5
:while
if %index% leq %count% (
    echo The value of index is %index%
    set /A index+=1
    GOTO :while
)

:: FOR statement. list implementations
:: Syntax: FOR %%variable IN list DO ( do_something )

FOR %%f IN (1 2 3 4 5) DO (
    echo %%f
)

:: FOR statement. Looping through ranges.
:: /L switch is used to denote that the loop is used for iterating through ranges.

FOR /L %%variable IN (lowerlimit, increment, upperlimit) DO do_something

FOR /L %%x IN (0, 1, 5) DO echo %%x

:: Classic Style For Loop

set /A i = 0;
:loop

IF %i% equ 5 GOTO END
echo The value of i is %i%
set /A i=%i%+1
GOTO :loop

:END

```

## Functions

```batch

:: Function is defined by label. Called by `CALL` keyword.
:: Parameter is accessed by %~index

SETLOCAL
CALL :Display 5 10
EXIT /B %ERRORLEVEL%

:Display
echo The first parameter is %~1
echo The second parameter is %~2
EXIT /B 0

:: Return value is implemented by simply passing variables names which will hold the return values.
:: Remarks: There is no sign `%` before and after.

SETLOCAL
CALL :Display return_value
echo %return_value%

:Display
set "%~3=15"

:: Local Variable can be used to avoid name conflicts and
:: Keep variable changes local to the function.

:: Following output will be "Outer; Outer".
:: The variable "str" is being localized in the function SetValue,
:: Thus even though the str value is being returned back to the main function,
:: The value of str in the main function will not be replaced.

@echo off
set str=Outer
@echo %str%
CALL :SetValue str
@echo %str%

:SetValue
SETLOCAL
set str=Inner
set "%~1=%str%"
ENDLOCAL
EXIT /B 0

:: Recursive Functions

set "fst=0"
set "fib=1"
set "limit=10000000"
call :myFibo fib,%fst%,%limit%
echo.The next Fibonacci number greater or equal %limit% is %fib%.
echo.&pause&goto:eof

:myFibo
SETLOCAL
set /A "Number1=%~1"
set /A "Number2=%~2"
set /A "Limit=%~3"
set /A "Number3=Number1+Number2"

IF /i %NumberN% LSS %Limit% call :myFibo NumberN,%Number1%,%Limit%

(ENDLOCAL
    IF "%~1" NEQ "" Set "%~1=%NumberN%"
)GOTO:eof

:: File I/O operations
:: Creating files; Deleting files;

echo "Hello" >C:\new.txt

DEL [/P] [/F] [/S] [/Q] [/A[[:]attributes]] names

:: Writing files;
:: Writing use `>`
:: Appending use `>>`

DIR C:\ >C:\write.txt

echo "This is the directory listing of C: Drive" >C:new.txt
DIR C:\ >>C:\new.txt

:: Reading files;

FOR /F "token=* delims=" %%x in (new.txt) DO echo %%x

:: Renaming files;

RENAME [drive:][path][directoryname1 | filename1] [directoryname2 | filename2]

rename *.txt *.bak
rename "TESTA.txt" "TESTB.txt"

:: Moving files;

MOVE [/Y | /-Y] [drive:][path]filename1[,...] destination

:: Batch Scripts-Files Pipes
:: The pipe operator (|) takes the output of one command and directs it into the input of another command.

:: The following command sorts the contents of the directory C:\
:: In this example, both commands start simultaneously, but then the sort command pauses untile it receives
:: the dir command's output.

DIR C:\ | sort

:: The content of the file C:\new.txt are sent to the sort command through the pipe filter.
TYPE C:\new.txt | sort

:: The following command will first take all the files defined in C:\,
:: then using the pipe command, will find all the files with the .txt entension.
:: it will then take this output and print it to the file AllText.txt.
DIR C:\ |  find "txt" > AllText.txt

:: Find the file names that include the string txt,
:: and then displays them in one command prompt window at a time.
DIR C:\ /s /b | find "txt" | more

:: Create Folders

MKDIR [drive:]path
MD [drive:]path

```

## Process

`TASKLIST` command can be used to get the list of currently running process.

```batch
TASKLIST [/S system [/U username [/P [password]]]] [/M [module] | /SVC | /V] [/FI filter] [/FO format] [/NH]

:: The following command will only fetch those processes whose memory is greater than 40MB
TASKLIST /FI "memusage gt 40960"
```

/S                 specifies the remote system to connect to.
/U  [domain\]user  specifies the user context under which the command should execute.
/P                 specifies the password for the given user context. Prompts for input if omitted.
/M                 list all tasks currently using the given exe/dll.
/SVC               display services hosted in each process.
/V                 display verbose task information.
/FI filter         tasks that match a given criteria specified by the filter.
/FO format         specifies the output format. Valid value: "TABLE", "LIST", "CSV".
/NH                no header. Valid only for "TABLE" and "CSV" formats.

`TASKKILL` command can kill a task from a windows command line by process id or image name.

```batch
TASKKILL [/S system [/U username [/P [password]]]] { [/FI filter] [/PID processid | /IM imagename] } [/T] [/F]
```

/T                  Terminates the specified process and any child processes which were started by it.
/F                  Forcefully

`START` command can start a new process.

```batch
START "title" [/D path] [options] "command" [parameters]
```

opotions:

/MIN            start window minimized
/MAX            start window maximized
/LOW            IDLE priority class
/NORMAL         NORMAL priority class
/ABOVENORMAL    ABOVENORMAL priority class
/BELOWNORMAL    BELOWNORMAL priority class
/HIGH           HIGH priority class
/REALTIME       REALTIME priority class

## Aliases

```batch
DOSKEY [options] [macroname=[text]]
```

## Devices

Batch Script can work with devices attached to system. This is known as the `DevCon`(device console).exe.

`DevCon` can verify that a driver is installed and configured correctly,
including proper INF files, driver stack, driver files, and driver package.
You can also use the DevCon commands(enable, disable, install, start, stop, and continue)
in scripts to test the driver.

## Registry

```batch
:: Reading from the Registry
:: REG QUERY
REG QUERY [ROOT\]RegKey /v ValueName [/s]
REG QUERY [ROOT\]RegKey /ve -- This returns the (default) value
REG QUERY HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Windows\

:: Adding to the Registry
REG ADD [ROOT\]RegKey /v ValueName [/t DataType] [/S Separator] [/d Data] [/f]
REG ADD [ROOT\]RegKey /ve [/d Data] [/f]

:: Deleting from the Registry
REG DEL [ROOT\]RegKey /v ValueName [/f]
REG DEL [ROOT\]RegKey /ve [/f]
REG DEL [ROOT\]RegKey /va [/f]

:: Copying from the registry
REG COPY [\\SourceMachine\][ROOT\]RegKey [\\DestMachine][ROOT\]RegKey
REG COPY HKEY_CURRENT_USER\Console HKEY_CURRENT_USER\Console\Test
REG QUERY HKEY_CURRENT_USER\Console\Test

:: Comparing Registry Keys
REG COMP [ROOT\]RegKey [ROOT\]RegKey [/v ValueName] [Output] [/s]
REG COMP [ROOT\]RegKey [ROOT\]RegKey [/ve] [Output] [/s]
REG COMPARE HKEY_CURRENT_USER\Console HKEY_CURRENT_USER\Console\Test
```

## Network

`Net` command is used to update, fix, or view the network or network settings.

## Printing

```batch
PRINT [/D:device] [[drive:][path]filename[...]]
```

## Debugging

```batch
:: REM out the @ECHO OFF
:: Run the batch file with the required command line parameters,
:: redirecting all output to a log file for later comparision.

test.bat > batch.log 2>&1
```

`TYPE` command can be used to output file contents.
`ECHO` command can be used to insert marks.
`Counter` can be used to help find out multiple calles to subroutine.

```batch
:: Windows Versions
IF NOT "%OS%"=="Windows_NT" GOTO :DontRun

:: Check for Windows NT 4
VER | FIND "Windows NT" > NUL && GOTO :DontRun

:: Check for Windows 2000
VER | FIND "Window 2000" > NUL && GOTO :DontRun

:: Actual Code here...

EXIT

:DontRun
ECHO This batch file was written for Windows XP and later version only.
```

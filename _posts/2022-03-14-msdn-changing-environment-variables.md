---
layout: post
title: "[MSDN] Changing Environment Variables"
date: 2022-03-14 23:28:00 +0900
categories: [Win32]
tags: [MSDN, Win32]
---

[Changing Environment Variables - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/procthread/changing-environment-variables#example-3)

각 프로세스는 그것과 연관된 환경 블럭을 가지고 있습니다. 환경 블럭은 블럭 끝에 두 개의 null 바이트를 가지는 블럭으로 구성되며, 여기에서 각 문자열은 다음 형식으로 되어 있습니다.

```
name=value
```

환경 블럭 내의 모든 문자열들은 반드시 이름을 기준으로 알파벳 순으로 정렬되어야 합니다. 정렬은 대소문자를 구분하지 않고, 유니코드 순서이며, 로케일에 관계 없습니다. 등호 기호(equal sign)이 구분자(separator)이기 때문에, 이것(=등호)을 환경 변수의 이름으로 사용하면 안 됩니다.

## **Example 1**

기본적으로, 자식 프로세스는 부모 프로세스의 환경 블럭의 복사본을 상속받습니다. 아래 예제는 *CreateProcess*를 사용하여 자식 프로세스에 전달할 새로운 환경 블럭을 만드는 방법을 보여줍니다.

이 예제에서는 예제 3의 코드를 자식 프로세스인 ex3.exe로 사용합니다.

```cpp
#include <windows.h>
#include <tchar.h>
#include <stdio.h>
#include <strsafe.h>

#define BUFSIZE 4096

int _tmain()
{
    TCHAR chNewEnv[BUFSIZE];
    LPTSTR lpszCurrentVariable; 
    DWORD dwFlags=0;
    TCHAR szAppName[]=TEXT("ex3.exe");
    STARTUPINFO si;
    PROCESS_INFORMATION pi;
    BOOL fSuccess; 
 
    // Copy environment strings into an environment block. 
 
    lpszCurrentVariable = (LPTSTR) chNewEnv;
    if (FAILED(StringCchCopy(lpszCurrentVariable, BUFSIZE, TEXT("MySetting=A"))))
    {
        printf("String copy failed\\n"); 
        return FALSE;
    }

    lpszCurrentVariable += lstrlen(lpszCurrentVariable) + 1; 
    if (FAILED(StringCchCopy(lpszCurrentVariable, BUFSIZE, TEXT("MyVersion=2")))) 
    {
        printf("String copy failed\\n"); 
        return FALSE;
    }
 
    // Terminate the block with a NULL byte. 
 
    lpszCurrentVariable += lstrlen(lpszCurrentVariable) + 1; 
    *lpszCurrentVariable = (TCHAR)0; 

    // Create the child process, specifying a new environment block. 
 
    SecureZeroMemory(&si, sizeof(STARTUPINFO));
    si.cb = sizeof(STARTUPINFO);

#ifdef UNICODE
    dwFlags = CREATE_UNICODE_ENVIRONMENT;
#endif

    fSuccess = CreateProcess(szAppName, NULL, NULL, NULL, TRUE, dwFlags,
        (LPVOID) chNewEnv,   // new environment block
        NULL, &si, &pi); 
 
    if (! fSuccess) 
    {
        printf("CreateProcess failed (%d)\\n", GetLastError());
        return FALSE;
    }
    WaitForSingleObject(pi.hProcess, INFINITE);
    return TRUE;
}
```

## Example 2

프로세스 생성 중 자식 프로세스의 환경 변수를 바꾸는 것은 한 프로세스가 다른 프로세스의 환경 변수를 직접적으로 변경할 수 있는 유일한 방법입니다. 프로세스는 그의 자식 프로세스가 아닌 다른 프로세스의 환경 변수를 절대로 직접 변경할 수 없습니다.

만약 자식 프로세스가 약간의 변경점 외 대부분의 환경을 상속받도록 하려면, 다음 예제와 같이 *GetEnvironmentVariable*을 사용해 현재 값들을 가져오고, 이 값들을 저장하고, 자식 프로세스가 상속할 업데이트된 블럭을 블럭을 생성하고, 자식 프로세스를 프로세스를 만든 뒤, *SetEnvironmentVariable*을 사용해 저장했던 변수들을 복원합니다.

이 예제에서는 예제 3의 코드를 자식 프로세스인 ex3.exe로 사용합니다.

```cpp
#include <windows.h>
#include <tchar.h>
#include <stdio.h>

#define BUFSIZE 4096
#define VARNAME TEXT("MyVariable")

int _tmain()
{
    DWORD dwRet, dwErr;
    LPTSTR pszOldVal; 
    TCHAR szAppName[]=TEXT("ex3.exe");
    DWORD dwFlags=0;
    STARTUPINFO si;
    PROCESS_INFORMATION pi;
    BOOL fExist, fSuccess; 
 
    // Retrieves the current value of the variable if it exists.
    // Sets the variable to a new value, creates a child process,
    // then uses SetEnvironmentVariable to restore the original
    // value or delete it if it did not exist previously. 
 
    pszOldVal = (LPTSTR) malloc(BUFSIZE*sizeof(TCHAR));
    if(NULL == pszOldVal)
    {
        printf("Out of memory\\n");
        return FALSE;
    }

    dwRet = GetEnvironmentVariable(VARNAME, pszOldVal, BUFSIZE);

    if(0 == dwRet)
    {
        dwErr = GetLastError();
        if( ERROR_ENVVAR_NOT_FOUND == dwErr )
        {
            printf("Environment variable does not exist.\\n");
            fExist=FALSE;
        }
    }
    else if(BUFSIZE < dwRet)
    {
        pszOldVal = (LPTSTR) realloc(pszOldVal, dwRet*sizeof(TCHAR));   
        if(NULL == pszOldVal)
        {
            printf("Out of memory\\n");
            return FALSE;
        }
        dwRet = GetEnvironmentVariable(VARNAME, pszOldVal, dwRet);
        if(!dwRet)
        {
            printf("GetEnvironmentVariable failed (%d)\\n", GetLastError());
            return FALSE;
        }
        else fExist=TRUE;
    }
    else fExist=TRUE;

    // Set a value for the child process to inherit. 
 
    if (! SetEnvironmentVariable(VARNAME, TEXT("Test"))) 
    {
        printf("SetEnvironmentVariable failed (%d)\\n", GetLastError()); 
        return FALSE;
    }
 
    // Create a child process. 

    SecureZeroMemory(&si, sizeof(STARTUPINFO));
    si.cb = sizeof(STARTUPINFO);
 
#ifdef UNICODE
    dwFlags = CREATE_UNICODE_ENVIRONMENT;
#endif

    fSuccess = CreateProcess(szAppName, NULL, NULL, NULL, TRUE, dwFlags, 
        NULL,     // inherit parent's environment 
        NULL, &si, &pi); 
    if (! fSuccess) 
    {
        printf("CreateProcess failed (%d)\\n", GetLastError()); 
    }
    WaitForSingleObject(pi.hProcess, INFINITE);

    // Restore the original environment variable. 
 
    if(fExist)
    {
        if (! SetEnvironmentVariable(VARNAME, pszOldVal)) 
        {
            printf("SetEnvironmentVariable failed (%d)\\n", GetLastError()); 
            return FALSE;
        }
    }
    else SetEnvironmentVariable(VARNAME, NULL);

    free(pszOldVal);
    
    return fSuccess;
}
```

## Example 3

아래 예제에서는 *GetEnvironmentStrings*을 사용하여 프로세스의 환경 블럭을 가져오고 콘솔에 내용을 출력합니다.

```cpp
#include <windows.h>
#include <tchar.h>
#include <stdio.h>

int _tmain()
{
    LPTSTR lpszVariable; 
    LPTCH lpvEnv; 
 
    // Get a pointer to the environment block. 
 
    lpvEnv = GetEnvironmentStrings();

    // If the returned pointer is NULL, exit.
    if (lpvEnv == NULL)
    {
        printf("GetEnvironmentStrings failed (%d)\\n", GetLastError()); 
        return 0;
    }
 
    // Variable strings are separated by NULL byte, and the block is 
    // terminated by a NULL byte. 

    lpszVariable = (LPTSTR) lpvEnv;

    while (*lpszVariable)
    {
        _tprintf(TEXT("%s\\n"), lpszVariable);
        lpszVariable += lstrlen(lpszVariable) + 1;
    }
    FreeEnvironmentStrings(lpvEnv);
    return 1;
}
```

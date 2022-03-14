---
layout: post
title: [MSDN] Creating Processes
date: 2022-03-15 01:19:00 +0900
categories: [Win32]
tags: [MSDN, Win32]
---

[Creating Processes - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/procthread/creating-processes)

CreateProcess 함수는 생성 프로세스(=CreateProcess를 호출한 프로세스)와 독립적으로 실행되는 새로운 프로세스를 생성합니다. 그러나 편의상, 이 관계를 부모-자식 관계라고 합니다.

아래 코드는 어떻게 프로세스를 만드는지 보여줍니다.

```cpp
#include <windows.h>
#include <stdio.h>
#include <tchar.h>

void _tmain( int argc, TCHAR *argv[] )
{
    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory( &si, sizeof(si) );
    si.cb = sizeof(si);
    ZeroMemory( &pi, sizeof(pi) );

    if( argc != 2 )
    {
        printf("Usage: %s [cmdline]\\n", argv[0]);
        return;
    }

    // Start the child process. 
    if( !CreateProcess( NULL,   // No module name (use command line)
        argv[1],        // Command line
        NULL,           // Process handle not inheritable
        NULL,           // Thread handle not inheritable
        FALSE,          // Set handle inheritance to FALSE
        0,              // No creation flags
        NULL,           // Use parent's environment block
        NULL,           // Use parent's starting directory 
        &si,            // Pointer to STARTUPINFO structure
        &pi )           // Pointer to PROCESS_INFORMATION structure
    ) 
    {
        printf( "CreateProcess failed (%d).\\n", GetLastError() );
        return;
    }

    // Wait until child process exits.
    WaitForSingleObject( pi.hProcess, INFINITE );

    // Close process and thread handles. 
    CloseHandle( pi.hProcess );
    CloseHandle( pi.hThread );
}
```

만약 CreateProcess가 성공한다면, 새로운 프로세스와 이것의 기본 스레드의 핸들들과 식별자들이 담긴 PROCESS_INFORMATION 구조체를 반환합니다. 스레드와 프로세스 핸들들은 전체 액세스 권한으로 생성되지만, 보안 설명자를 지정하면 액세스가 제한될 수 있습니다. 이 핸들들이 더 이상 필요하지 않게 된다면, CloseHandle 함수를 사용해 이들을 닫아줄 수 있습니다.

또한 CreateProcessAsUser 또는 CreateProcessWithLogon 함수를 사용하여 프로세스를 생성할 수 있습니다. 이를 통해 프로세스가 실행될 사용자 계정의 보안 컨텍스트를 지정할 수 있습니다.

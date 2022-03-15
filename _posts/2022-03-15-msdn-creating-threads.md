---
layout: post
title: "[MSDN] Creating Threads"
date: 2022-03-15 16:55:00 +0900
categories: [Win32]
tags: [MSDN, Win32]
---

[Creating Threads - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/procthread/creating-threads)

CreateThread 함수는 프로세스에 새로운 스레드를 생성합니다. 생성된 스레드는 반드시 새로운 스레드가 실행할 코드의 시작 주소를 지정해야 합니다. 일반적으로, 시작 주소는 프로그램 코드에 정의된 함수의 이름입니다 (자세한 내용은 ThreadProc 참조). 이 함수는 단일 파라미터를 받으며 DWORD 값을 반환합니다. 프로세스는 동일한 함수를 실행하는 여러 개의 스레드를 동시에 가질 수 있습니다.

아래는 지역적으로 정의된 함수 *MyThreadFunction*을 실행하는 새로운 스레드를 생성하는 방법에 대한 간단한 예제를 보여줍니다.

호출 스레드는 WaitForMultipleObjects 함수를 호출하여 모든 작업자 스레드가 종료될 때까지 지연할 수 있습니다. 대기하는 동안 호출 스레드가 블로킹됩니다. 처리를 계속하려면, 호출 스레드는 WaitForSingleObject를 사용하고 각 작업자 스레드가 wait object에 신호를 보낼 때까지 기다립니다. 종료되기 전에 작업자 스레드에 대한 핸들을 닫으려고 하는 경우, 작업자 스레드를 종료하지 않는다는 것에 유의하세요. 그러나 후속 함수 호출에서는 핸들을 사용할 수 없습니다.

```cpp
#include <windows.h>
#include <tchar.h>
#include <strsafe.h>

#define MAX_THREADS 3
#define BUF_SIZE 255

DWORD WINAPI MyThreadFunction( LPVOID lpParam );
void ErrorHandler(LPTSTR lpszFunction);

// Sample custom data structure for threads to use.
// This is passed by void pointer so it can be any data type
// that can be passed using a single void pointer (LPVOID).
typedef struct MyData {
    int val1;
    int val2;
} MYDATA, *PMYDATA;

int _tmain()
{
    PMYDATA pDataArray[MAX_THREADS];
    DWORD   dwThreadIdArray[MAX_THREADS];
    HANDLE  hThreadArray[MAX_THREADS]; 

    // Create MAX_THREADS worker threads.

    for( int i=0; i<MAX_THREADS; i++ )
    {
        // Allocate memory for thread data.

        pDataArray[i] = (PMYDATA) HeapAlloc(GetProcessHeap(), HEAP_ZERO_MEMORY,
                sizeof(MYDATA));

        if( pDataArray[i] == NULL )
        {
           // If the array allocation fails, the system is out of memory
           // so there is no point in trying to print an error message.
           // Just terminate execution.
            ExitProcess(2);
        }

        // Generate unique data for each thread to work with.

        pDataArray[i]->val1 = i;
        pDataArray[i]->val2 = i+100;

        // Create the thread to begin execution on its own.

        hThreadArray[i] = CreateThread( 
            NULL,                   // default security attributes
            0,                      // use default stack size  
            MyThreadFunction,       // thread function name
            pDataArray[i],          // argument to thread function 
            0,                      // use default creation flags 
            &dwThreadIdArray[i]);   // returns the thread identifier 

        // Check the return value for success.
        // If CreateThread fails, terminate execution. 
        // This will automatically clean up threads and memory. 

        if (hThreadArray[i] == NULL) 
        {
           ErrorHandler(TEXT("CreateThread"));
           ExitProcess(3);
        }
    } // End of main thread creation loop.

    // Wait until all threads have terminated.

    WaitForMultipleObjects(MAX_THREADS, hThreadArray, TRUE, INFINITE);

    // Close all thread handles and free memory allocations.

    for(int i=0; i<MAX_THREADS; i++)
    {
        CloseHandle(hThreadArray[i]);
        if(pDataArray[i] != NULL)
        {
            HeapFree(GetProcessHeap(), 0, pDataArray[i]);
            pDataArray[i] = NULL;    // Ensure address is not reused.
        }
    }

    return 0;
}

DWORD WINAPI MyThreadFunction( LPVOID lpParam ) 
{ 
    HANDLE hStdout;
    PMYDATA pDataArray;

    TCHAR msgBuf[BUF_SIZE];
    size_t cchStringSize;
    DWORD dwChars;

    // Make sure there is a console to receive output results. 

    hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
    if( hStdout == INVALID_HANDLE_VALUE )
        return 1;

    // Cast the parameter to the correct data type.
    // The pointer is known to be valid because 
    // it was checked for NULL before the thread was created.
 
    pDataArray = (PMYDATA)lpParam;

    // Print the parameter values using thread-safe functions.

    StringCchPrintf(msgBuf, BUF_SIZE, TEXT("Parameters = %d, %d\\n"), 
        pDataArray->val1, pDataArray->val2); 
    StringCchLength(msgBuf, BUF_SIZE, &cchStringSize);
    WriteConsole(hStdout, msgBuf, (DWORD)cchStringSize, &dwChars, NULL);

    return 0; 
} 

void ErrorHandler(LPTSTR lpszFunction) 
{ 
    // Retrieve the system error message for the last-error code.

    LPVOID lpMsgBuf;
    LPVOID lpDisplayBuf;
    DWORD dw = GetLastError(); 

    FormatMessage(
        FORMAT_MESSAGE_ALLOCATE_BUFFER | 
        FORMAT_MESSAGE_FROM_SYSTEM |
        FORMAT_MESSAGE_IGNORE_INSERTS,
        NULL,
        dw,
        MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
        (LPTSTR) &lpMsgBuf,
        0, NULL );

    // Display the error message.

    lpDisplayBuf = (LPVOID)LocalAlloc(LMEM_ZEROINIT, 
        (lstrlen((LPCTSTR) lpMsgBuf) + lstrlen((LPCTSTR) lpszFunction) + 40) * sizeof(TCHAR)); 
    StringCchPrintf((LPTSTR)lpDisplayBuf, 
        LocalSize(lpDisplayBuf) / sizeof(TCHAR),
        TEXT("%s failed with error %d: %s"), 
        lpszFunction, dw, lpMsgBuf); 
    MessageBox(NULL, (LPCTSTR) lpDisplayBuf, TEXT("Error"), MB_OK); 

    // Free error-handling buffer allocations.

    LocalFree(lpMsgBuf);
    LocalFree(lpDisplayBuf);
}
```

MyThreadFunction 함수는 특히 multithreaded CRT를 사용하지 않는 경우, 많은 함수가 not thread-safe하기 때문에 C 런타임 라이브러리 (CRT, C run-time library)의 사용을 피합니다. 만약 ThreadProc함수 내에서 CRT를 사용하고 싶다면, _beginthreadex 함수를 대신 사용하세요.

포인터가 유효하지 않게 되어버리기 때문에, 생성 스레드(새로운 스레드를 생성한 부모 스레드)가 새로운 스레드보다 먼저 종료된다면 지역 변수의 주소를 전달하는 것은 위험합니다. 대신, 동적으로 할당된 메모리에 대한 포인터를 전달하거나 새로운 스레드가 종료될 때까지 생성 스레드를 기다리게 하세요. 데이터는 또한 전역 변수로도 전달될 수도 있습니다. 전역 변수를 사용하면, 일반적으로 여러 스레드의 액세스를 동기화해야 합니다. 더 많은 정보를 보려면 [Synchronizing Execution of Multiple Threads](https://docs.microsoft.com/en-us/windows/win32/procthread/synchronizing-execution-of-multiple-threads) 문서를 보세요.

생성 스레드는 인수로 다음 항목들을 지정할 수 있습니다.

- 새로운 스레드에 대한 핸들의 보안 속성들(security attributes)입니다. 이러한 보안 속성에는 핸들이 자식 프로세스에 의해 상속될 수 있는지 아닌지에 대한 여부를 결정하는 inheritance flag를 포함됩니다. 또한 액세스가 허용되기 전에 시스템이 스레드 핸들의 모든 후속 사용에 대한 액세스 검사를 수행하는 데 사용하는 보안 설명자(security descriptor)가 포함됩니다.
- 새로운 스레드의 초기 스택 크기입니다. 스레드의 스택은 프로세스의 메모리 영역에 자동으로 할당됩니다. 시스템은 필요할 때마다 스택의 크기를 늘리며 스레드가 종료될 때 이를 해제합니다. 더 많은 정보를 보려면 [Thread Stack Size](https://docs.microsoft.com/en-us/windows/win32/procthread/thread-stack-size) 문서를 보세요.
- 일시 중단(suspended) 상태로 스레드를 생성할 수 있도록 해주는 생성 플래그(creation flag)입니다. 일시 중단되었을 때 스레드는 ResumeThread 함수가 호출되기 전까지 실행되지 않습니다.

또한 CreateRemoteThread 함수를 사용하여 스레드를 생성할 수도 있습니다. 이 함수는 디버그 중인 프로세스의 주소 공간 영역에서 실행되는 스레드를 생성하기 위해서 디버거에 의해 사용됩니다.


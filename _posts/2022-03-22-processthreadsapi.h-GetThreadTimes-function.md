---
layout: post
title: "GetThreadTimes function"
date: 2022-03-22 15:45:00 +0900
categories: [processthreadsapi.h]
tags: [MSDN, Win32, processthreadsapi.h]
---
[GetThreadTimes function (processthreadsapi.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-getthreadtimes)

지정된 스레드의 시간 정보를 가져옵니다.

## Syntax

```cpp
BOOL GetThreadTimes(
  [in]  HANDLE     hThread,
  [out] LPFILETIME lpCreationTime,
  [out] LPFILETIME lpExitTime,
  [out] LPFILETIME lpKernelTime,
  [out] LPFILETIME lpUserTime
);
```

## Parameters

`[in] hThread`

타이밍 정보를 찾는 스레드에 대한 핸들입니다. 이 핸들은 반드시 THREAD_QUERY_INFORMATION 또는 THREAD_QUERY_LIMITED_INFORMATION 접근 권한을 가져야 합니다. 더 많은 정보를 보려면, [Thread Security and Access Rights](https://docs.microsoft.com/en-us/windows/desktop/ProcThread/thread-security-and-access-rights) 문서를 보세요.

**Windows Server 2003 and Windows XP:** 이 핸들은 반드시 THREAD_QUERY_INFORMATION 접근 권한을 가져야 합니다.

`[out] lpCreationTime`

스레드의 생성 시간(creation time)을 수신하는 FILETIME 구조체에 대한 포인터입니다.

`[out] lpExitTime`

스레드의 종료 시간(exit time)을 수신하는 FILETIME 구조체에 대한 포인터입니다. 스레드가 종료되지 않았다면, 이 구조체에 쓰여질 값은 미정의입니다.

`[out] lpKernelTime`

스레드가 커널 모드에서 실행된 시간을 수신하는 FILETIME 구조체에 대한 포인터입니다.

`[out] lpUserTime`

스레드가 유저 모드에서 실행된 시간을 수신하는 FILETIME 구조체에 대한 포인터입니다.

## Return value

함수가 성공하면, 반환 값은 0이 아닙니다.

함수가 실패하면, 반환 값은 0입니다. 더 많은 에러 정보를 가져오려면, GetLastError를 호출하세요.

## Remarks

모든 시간 정보는 FILETIME 자료 구조체를 사용해 표현됩니다. 이러한 구조체는 100나노초 시간 단위의 64비트 카운트를 형성하기 위해 결합되는 2개의 32비트 값이 포함됩니다.

스레드 생성 시간과 종료 시간은 1601년 1월 1일 영국 그리니치에서 자정 이후 경과된 시간 time point로 표현됩니다. 애플리케이션이 이런 값들을 보다 일반적으로 유용한 형태로 변환할 수 있는 몇 개의 함수들이 있습니다. [Time Functions](https://docs.microsoft.com/en-us/windows/desktop/SysInfo/time-functions) 문서를 보세요.

스레드 커널 모드 시간과 유저 모드 시간은 시간의 양입니다. 예를 들어, 스레드가 커널 모드에서 1초를 보냈다면, 이 함수는 lpKernelTime으로 지정된 FILETIME 구조체에 천만(10,000,000)의 64비트 값을 채울 것입니다. 그것이 1초에 100나노초 단위의 수입니다.

> 1 seconds 
= 1,000 millis 
= 1,000,000 micros 
= 1,000,000,000 nanos
>
1,000,000,000 nanos / 100 nanos = 10,000,000 nanos

스레드가 사용한 CPU 클럭 사이클 수를 가져오려면, [QueryThreadCycleTime](https://docs.microsoft.com/en-us/windows/desktop/api/realtimeapiset/nf-realtimeapiset-querythreadcycletime) 함수를 사용하세요.
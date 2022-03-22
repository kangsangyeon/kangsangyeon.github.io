---
layout: post
title: "GetTickCount64 function"
date: 2022-03-22 16:56:00 +0900
categories: [sysinfoapi.h]
tags: [MSDN, Win32, sysinfoapi.h]
---
[GetTickCount64 function (sysinfoapi.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-gettickcount64)

시스템이 시작된 이후 경과된 시간(밀리초)을 가져옵니다.

## Syntax

```cpp
ULONGLONG GetTickCount64();
```

## Return value

밀리초 값입니다.

## Remarks

GetTickCount 함수의 해상도(resolution)는 일반적으로 10 밀리세컨드부터 16 밀리세컨드 범위에 있는 시스템 타이머의 해상도로 한정됩니다. GetTickCount64 함수의 해상도는 GetSystemTimeAdjustment 함수로 조정된 사항에 영향을 받지 않습니다.

더 높은 해상도의 타이머를 만드려면, [multimedia timer](https://docs.microsoft.com/en-us/windows/desktop/Multimedia/multimedia-timers) 또는 [high-resolution timer](https://docs.microsoft.com/en-us/windows/desktop/winmsg/about-timers)을 사용하세요.

시스템이 시작된 이후 working state에서 보낸 시간을 얻으려면, [QueryUnbiasedInterruptTime](https://docs.microsoft.com/en-us/windows/desktop/api/realtimeapiset/nf-realtimeapiset-queryunbiasedinterrupttime) 함수를 사용하세요.

> note:
QueryUnbiasedInterruptTime 함수는 Windows의 디버그(’선택’) 빌드에서 다른 결과를 낳습니다. 인터럽트 타임 카운트와 틱 카운트가 약 49일 앞당기기 때문입니다. 이는 시스템이 오랫동안 실행될 때까지 발생하지 않을 수 있는 버그를 식별하는 데 도움이 됩니다.
> 

이 함수를 사용하는 애플리케이션을 컴파일하려면, _WIN32_WINNT를 0x0600 이상으로 정의하세요. 더 많은 정보를 보려면, [Using the Windows Headers](https://docs.microsoft.com/en-us/windows/desktop/WinProg/using-the-windows-headers) 문서를 보세요.
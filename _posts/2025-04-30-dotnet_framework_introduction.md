---
layout: post
title: ".NET 플랫폼 정리: 플랫폼, Standard, 구현체, 그리고 변화"
date: 2025-04-30 03:32:00 +0900
categories: [dotnet]
tags: [dotnet, framework, core, standard]
---

.NET은 Microsoft가 개발한 다양한 유형의 애플리케이션을 위한 개발 플랫폼입니다. 이 글에서는 다음 주제를 다룹니다.

- .NET 플랫폼이란?
- .NET의 다양한 구현체들
- .NET Standard 사양이란?
- .NET 5 이전과 이후의 변화

## .NET 플랫폼이란?

.NET은 **애플리케이션 개발을 위한 런타임 환경**, **언어**, **라이브러리**를 제공하는 **종합적인 개발 플랫폼**입니다.
이를 통해 Windows, Linux, macOS, iOS, Android 등 다양한 환경에서 동작하는 프로그램을 만들 수 있습니다.

.NET 플랫폼은 크게 두 가지 주요 구성 요소로 이루어져 있습니다.

- **CLR(Common Language Runtime)**: 메모리 관리, 스레드 관리, 보안 등 런타임 기능을 제공합니다.
- **BCL(Base Class Library)**: 파일 입출력, 네트워크, 컬렉션 등 기본 기능을 제공하는 표준 클래스 라이브러리입니다.

## .NET의 다양한 구현체들

.NET에는 여러 가지 구현체가 존재합니다. 각 구현체는 다양한 플랫폼을 지원합니다.

| 구현체               | 지원 플랫폼                             | 지원 시작일    | 지원 종료일                 | .NET Standard 지원 | 지원 언어               |
| ------------------ | ------------------------------------ | ---------- | ---------------------- | ---------------- | ------------------- |
| .NET Framework 1.x | Windows 2000 이상                      | 2002년 1월  | OS 지원 종료 시까지         | -                | C#, VB.NET 등       |
| .NET Framework 4.8.1 | Windows 10 이상                      | 2022년 10월 | OS 지원 종료 시까지         | 2.0              | C# 7.1              |
| .NET Core 1.0      | Windows, Linux, macOS                 | 2016년 6월  | 2019년 6월 종료           | 1.6              | C#, F#              |
| .NET Core 3.1      | Windows, Linux, macOS                 | 2019년 11월 | 2024년 12월까지 장기 지원 | 2.1              | C#, F#              |
| Mono               | Windows, Linux, macOS, iOS, Android 등 | 2004년 6월  | 계속 개발 중               | 일부             | C#, F#              |
| .NET 5.0           | Windows, Linux, macOS, iOS, Android 등 | 2020년 11월 | 2022년 5월 종료            | -                | C#, F#, VB.NET      |

## .NET Standard 사양이란?

.NET Standard는 다양한 .NET 구현체에서 **공통적으로 사용할 수 있는 API 규격**입니다.
이를 통해 하나의 라이브러리로 여러 플랫폼을 지원할 수 있도록 합니다.

- .NET Framework, .NET Core, Xamarin, Mono 모두가 이 표준을 준수하여 호환성을 유지합니다.
- .NET Standard를 타겟팅하여 빌드하면, 다양한 플랫폼에서도 별도의 수정 없이 라이브러리를 재사용할 수 있습니다.

그러나 .NET 5 출시 이후, 모든 플랫폼을 단일 코드베이스와 런타임(CoreCLR)으로 지원하게 되면서,
**플랫폼 간 API 차이를 조율할 필요 자체가 줄어들었고**,
이에 따라 ".NET Standard처럼 사양-구현체를 분리하여 별도 규격을 유지할 필요성"도 사실상 사라지게 되었습니다.

## .NET 5 이전과 이후

.NET 5부터는 기존의 `.NET Framework`, `.NET Core`, `Xamarin`, `Mono` 등 여러 개로 나뉘어 있던 .NET 구현체들을 하나의 **통합된 플랫폼**으로 재구성하는 전략이 적용되었습니다.
이전에는 운영체제나 용도에 따라 별도의 코드베이스와 런타임을 유지해야 했으며, 이는 다음과 같은 문제점을 야기했습니다.

- **코드 중복**: 유사한 기능을 각 구현체마다 별도로 개발하고 유지해야 했습니다.
- **API 버전 관리의 복잡성**: 플랫폼마다 API 제공 여부나 동작 방식이 달라, 동일한 기능이라도 플랫폼에 따라 차이가 생겼습니다.
- **호환성 문제**: 개발자가 여러 플랫폼을 동시에 지원하려 할 때, 각 플랫폼에 맞는 별도 버전의 라이브러리나 코드를 작성해야 했습니다.
- **개발 및 유지보수 비용 증가**: 중복 코드와 플랫폼별 이슈로 인해 개발과 테스트, 유지보수에 드는 비용이 크게 증가했습니다.

이러한 문제를 완화하기 위해 Microsoft는 **.NET Standard**를 도입했습니다.
.NET Standard는 여러 .NET 구현체에서 **공통으로 사용할 수 있는 최소 API 집합**을 규정하여,
라이브러리 개발자가 하나의 API 세트만 지원하면 다양한 플랫폼에서 재사용할 수 있도록 하는 **사양(specification)** 역할을 했습니다.

그러나 .NET 5부터는 모든 플랫폼을 단일 코드베이스와 런타임(CoreCLR)으로 지원하게 되면서,
**플랫폼 간 API 차이를 조율할 필요 자체가 줄어들었고**,
이에 따라 ".NET Standard처럼 사양-구현체를 분리하여 별도 규격을 유지할 필요성"도 사실상 사라지게 되었습니다.

| 특성                          | .NET Framework 4.x / .NET Core 3.x | .NET 5+ (Unified Platform)       |
| --------------------------- | --------------------------------- | ----------------------------- |
| 코드베이스                    | 플랫폼별 분리                      | 단일 소스 트리 통합                 |
| BCL(Base Class Library)     | 별도 관리                          | System.* 네임스페이스로 통합          |
| 런타임                       | CLR(Windows), CoreCLR(Linux) 구분  | CoreCLR 기반 단일 런타임             |
| 컴파일                       | JIT 중심                           | 기본 JIT, .NET 6+부터 Native AOT 지원 |
| SDK 도구                     | 개별 SDK (netfx, dotnetcore-sdk)  | 통합 SDK (dotnet) 사용             |

## Reference

- [.NET Standard - Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/standard/net-standard?tabs=net-standard-2-0)
- [.NET Framework support policy - Microsoft Learn](https://learn.microsoft.com/en-us/lifecycle/faq/dotnet-framework)
- [.NET Architecture Overview - Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-components)
- [Native AOT - Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/)
- [The future of .NET Standard - .NET Blog](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)
- [.NET 5 Announcement - .NET Blog](https://devblogs.microsoft.com/dotnet/introducing-net-5/)

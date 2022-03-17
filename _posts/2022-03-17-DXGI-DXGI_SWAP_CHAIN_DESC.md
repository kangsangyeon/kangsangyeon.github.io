---
layout: post
title: "DXGI_SWAP_CHAIN_DESC structure"
date: 2022-03-17 18:24:00 +0900
categories: [DXGI]
tags: [MSDN, DirectX, DXGI]
---

[DXGI_SWAP_CHAIN_DESC (dxgi.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/dxgi/ns-dxgi-dxgi_swap_chain_desc)

swap chain을 기술하는 구조체입니다.

## Syntax

```cpp
typedef struct DXGI_SWAP_CHAIN_DESC {
  DXGI_MODE_DESC   BufferDesc;
  DXGI_SAMPLE_DESC SampleDesc;
  DXGI_USAGE       BufferUsage;
  UINT             BufferCount;
  HWND             OutputWindow;
  BOOL             Windowed;
  DXGI_SWAP_EFFECT SwapEffect;
  UINT             Flags;
} DXGI_SWAP_CHAIN_DESC;
```

## Members

`BufferDesc`

Type: **DXGI_MODE_DESC**

back buffer 디스플레이 모드를 기술하는 구조체입니다.

`SampleDesc`

Type: **DXGI_SAMPLE_DESC**

멀티샘플링 매개변수들을 기술하는 구조체입니다.]\

`BufferUsage`

Type: **DXGI_USAGE**

백 버퍼에 대한 surface 사용량과 CPU 액세스 옵션을 기술한 DXGI_USAGE 열거형 타입의 멤버입니다. 백 버퍼는 셰이더 입력(shader input) 또는 렌더 타겟 출력(render-target output)에 사용될 수 있습니다.

`BufferCount`

Type: **UINT**

스왑 체인 내의 버퍼 개수를 기술하는 값입니다. *IDXGIFactory::CreateSwapChain*을 호출하여 풀스크린 스왑 체인을 만들 때, 일반적으로 이 값에 프론트 버퍼를 포함합니다. 스왑 체인 버퍼에 대한 더 많은 정보를 보려면, Remarks를 보세요.

`OutputWindow`

Type: **HWND**

출력 윈도우에 대한 HWND 핸들입니다. 이 멤버는 반드시 nullptr이 아니여야 합니다.

`Windowed`

Type: **BOOL**

창 모드로 출력될 것인지를 지정하는 bool 값입니다. TRUE이면 창모드로 출력합니다.

창 모드 스왑 체인을 만들고 최종 사용자가 *IDXGISwapChain::SetFullscreenState*를 통해 스왑 체인을 전체 화면으로 변경할 수 있도록 하는 것을 권장합니다. 즉, 스왑 체인이 강제로 전체 화면이 되도록 이 값을 FALSE로 설정하지 마세요. 스왑 체인을 전체 화면 모드로 생성하는 경우, 지원되지 않는 디스플레이 모드로 스왑 체인을 생성할 때, 디스플레이가 검게 변하고 최종 사용자는 아무 것도 볼 수 없게 됩니다.

창 모드를 선택할 것인지 전체 화면 모드를 선택할 것인지에 대한 더 많은 정보를 보려면, [IDXGIFactory::CreateSwapChain](https://docs.microsoft.com/en-us/windows/desktop/api/dxgi/nf-dxgi-idxgifactory-createswapchain) 문서를 보세요.

`SwapEffect`

Type: **DXGI_SWAP_EFFECT**

surface를 표시한 뒤 present buffer의 내용을 처리하기 위한 옵션을 기술하는 *DXGI_SWAP_EFFECT* 열거형 타입의 멤버입니다. 

`Flags`

Type: **UINT**

스왑 체인 동작에 대한 옵션을 기술하는 *DXGI_SWAP_CHAIN_FLAG* 열거형 타입의 멤버입니다.

## Remarks

이 구조체는 GetDesc와 CreateSwapChain 메소드들에 의해 사용됩니다.

전체 화면 모드에서는 dedicated front buffer가 있습니다. 창 모드에서 desktop은 frontbuffer입니다.

만약 하나의 버퍼만을 가진 스왑 체인을 생성하는 경우, DXGI_SWAP_EFFECT_SEQUENTIAL를 지정해도 단일 버퍼의 내용이 front buffer와 교체되지 않습니다.

전체 화면 모드에서 스왑 체인 버퍼를 flip하는 것에 대한 성능 정보를 더 보려면, [Full-Screen Application Performance Hints](https://docs.microsoft.com/en-us/windows/desktop/direct3ddxgi/d3d10-graphics-programming-guide-dxgi) 문서를 보세요.
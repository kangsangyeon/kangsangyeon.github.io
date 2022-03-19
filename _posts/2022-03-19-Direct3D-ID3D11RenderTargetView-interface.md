---
layout: post
title: "ID3D11RenderTargetView interface"
date: 2022-03-19 23:57:00 +0900
categories: [Direct3D]
tags: [MSDN, DirectX, Direct3D]
---
[ID3D11RenderTargetView (d3d11.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/d3d11/nn-d3d11-id3d11rendertargetview)

렌더 타겟 뷰 인터페이스는 렌더링 중 액세스 가능한 subresources의 렌더 타겟입니다.

## Inheritance

*ID3d11RenderTargetView* 인터페이스는 [*ID3D11View*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/nn-d3d11-id3d11view)를 상속합니다.

*ID3d11RenderTargetView*는 또한 다음 유형의 멤버들도 있습니다.

## Methods

| ID3D11RenderTargetView::GetDesc | 렌더 타겟 뷰의 속성들을 가져옵니다. |
| --- | --- |

## Remarks

렌더 타겟 뷰를 생성하려면, [*ID3D11Device::CreateRenderTargetView*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createrendertargetview)을 호출하세요. 파이프라인에 렌더 타겟 뷰를 바인드하려면, [*ID3D11DeviceContext::OMSetRenderTargets*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets)을 호출하세요.

렌더 타겟은 render pass가 끝날 때 output-merger 단계에서 쓰여질 수 있는(can be written) 리소스입니다. 각 렌더 타겟은 대응되는 depth-stencil 뷰를 가지고 있어야 합니다.
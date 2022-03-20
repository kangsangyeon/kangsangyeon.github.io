---
layout: post
title: "D3D11_BUFFER_DESC structure"
date: 2022-03-20 21:54:00 +0900
categories: [Direct3D]
tags: [MSDN, DirectX, Direct3D]
---
[D3D11_BUFFER_DESC (d3d11.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/d3d11/ns-d3d11-d3d11_buffer_desc)

버퍼 리소스를 기술합니다.

## Syntax

```cpp
typedef struct D3D11_BUFFER_DESC {
  UINT        ByteWidth;
  D3D11_USAGE Usage;
  UINT        BindFlags;
  UINT        CPUAccessFlags;
  UINT        MiscFlags;
  UINT        StructureByteStride;
} D3D11_BUFFER_DESC;
```

## Members

`ByteWidth`

Type: **UINT**

버퍼의 크기(바이트)입니다.

`Usage`

Type: **D3D11_USAGE**

버퍼에서 어떻게 읽고 쓸 것으로 기대되는지에 대해 식별합니다. 업데이트 빈도는 핵심 요소입니다. 가장 일반적인 값은 *D3D11_USAGE_DEFAULT* 입니다. [*D3D11_USAGE*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/ne-d3d11-d3d11_usage) 문서를 읽어 가능한 모든 값들을 보세요.

`BindFlags`

Type: **UINT**

버퍼가 어떻게 파이프라인에 바인드되었는지에 대해 식별합니다. 플래그는 비트 단위 OR로 결합될 수 있습니다. ([*D3D11_BIND_FLAG*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/ne-d3d11-d3d11_bind_flag))

`CPUAccessFlags`

Type: **UINT**

CPU 액세스 플래그 ([*D3D11_CPU_ACCESS_FLAG*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/ne-d3d11-d3d11_cpu_access_flag))이거나, 또는 CPU 액세스가 필요하지 않은 경우 0입니다. 플래그는 비트 단위 OR로 결합될 수 있습니다.

`MiscFlags`

Type: **UINT**

여러 가지 잡다한 플래그이거나, 또는 사용되지 않는 경우 0입니다. 플래그는 비트 단위 OR로 결합될 수 있습니다.

`StructureByteStride`

Type: **UINT**

버퍼가 구조화된 버퍼를 나타낼 때 버퍼 구조 내에 있는 각 요소들의 크기(바이트)입니다. 구조화된 버퍼에 대한 더 많은 정보를 보려면, [*Structured Buffer*](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/direct3d-11-advanced-stages-cs-resources) 문서를 보세요.

*StructureByteStride*의 크기 값은 사용하는 버퍼의 뷰에 사용하는 형식의 크기와 일치해야 합니다. 예를 들어, pixel shader에서 버퍼를 읽기 위해 shader resource view (SRV)를 사용한다면, SRV format 크기는 반드시 *StructureByteStride* 내의 크기 값과 일치해야 합니다.

## Remarks

이 구조체는 [*ID3D11Device::CreateBuffer*](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer)에서 버퍼 리소스를 만들 때 사용합니다.

이 구조체 외에, D3D11.h에 정의되어 있으며 상속된 클래스처럼 동작하는 *CD3D11_BUFFER_DESC*의 하위 타입을 사용하여 buffer description을 생성할 수 있습니다.

bind flag가 D3D11_BIND_CONSTANT_BUFFER일 때, 반드시 *ByteWidth*를 16의 배수로 설정해야 하며, *D3D11_REQ_CONSTANT_BUFFER_ELEMENT_COUNT* 보다 작거나 같아야 합니다.
---
layout: post
title: "ID3D11Device::CreateBuffer method"
date: 2022-03-20 23:07:00 +0900
categories: [Direct3D]
tags: [MSDN, DirectX, Direct3D]
---
[ID3D11Device::CreateBuffer (d3d11.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/d3d11/nf-d3d11-id3d11device-createbuffer)

버퍼를 생성합니다 (vertex buffer, index buffer, 또는 shader-constant buffer).

## Syntax

```cpp
HRESULT CreateBuffer(
  [in]            const D3D11_BUFFER_DESC      *pDesc,
  [in, optional]  const D3D11_SUBRESOURCE_DATA *pInitialData,
  [out, optional] ID3D11Buffer                 **ppBuffer
);
```

## Parameters

`[in] pDesc`

Type: const **D3D11_BUFFER_DESC***

버퍼를 기술한 *D3D11_BUFFER_DESC* 구조체에 대한 포인터입니다.

`[in, optional] pInitialData`

Type: const **D3D11_SUBRESOURCE_DATA***

초기화 데이터를 기술한 *D3D11_SUBRESOURCE_DATA* 구조체에 대한 포인터입니다. 공간만 할당하려면 nullptr를 사용하세요 (예외적으로 usage flag가 *D3D11_USAGE_IMMUTABLE*인 경우 nullptr로 설정할 수 없습니다).

*pInitialData*에 어떠한 것도 전달하지 않는다면, 버퍼에 대한 메모리의 초기 내용은 정의되지 않습니다. 이 경우, 리소스를 읽기 전에 다른 방법으로 버퍼의 내용을 채워넣어야 합니다.

`[out, optional] ppBuffer`

Type: **ID3D11Buffer****

생성된 버퍼 객체에 대한 *ID3D11Buffer* 인터페이스에 대한 포인터입니다. 다른 입력 매개변수가 유효한지 검사하려면 이 매개변수를 nullptr로 설정하세요 (S_FALSE는 통과를 나타냅니다).

## Return value

Type: **HRESULT**

이 메소드는 버퍼를 만들기 위해 필요한 메모리가 부족한 경우, *E_OUTOFMEMORY*를 반환합니다. 다른 가능한 반환값을 보려면, [Direct3D 11 Return Codes](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/d3d11-graphics-reference-returnvalues) 문서를 보세요.

## Remarks

예제 코드를 보려면, [How to: Create a Vertex Buffer](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-vertex-how-to), [How to: Create an Index Buffer](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-index-how-to) 또는 [How to: Create a Constant Buffer](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-constant-how-to) 문서를 보세요.

constant buffer에 대해서는, *D3D11_BUFFER_DESC*의 *ByteWidth*를 반드시 16의 배수로 설정해야 하며, *D3D11_REQ_CONSTANT_BUFFER_ELEMENT_COUNT* 보다 작거나 같게 설정해야 합니다.

Windows 8과 이후 버전에서 사용 가능한 Direct3D 11.1 런타임은 CreateBuffer에 대해 다음과 같은 새로운 기능을 제공합니다.

셰이더가 접근 가능한 최대 constant buffer 크기보다 더 큰 constant buffer를 생성할 수 있습니다 (4096 32-bit * 4 component constants - 64KB). 파이프라인에 constant buffer를 바인드할 때, 4096 상수 제한 내에 맞는, 셰이더가 접근 가능한 버퍼의 범위를 정의할 수 있습니다.

Windows 8과 이후 버전에서 사용 가능한 Direct3D 11.1 런타임은 feature level 9.1, 9.2, 그리고 9.3에 대해 이 기능을 에뮬레이트합니다. 그러므로, 이 기능은 feature level 9.1, 9.2, 그리고 9.3에서 지원됩니다.

이 기능은 feature level 10 이상의 새 드라이버에서 항상 사용할 수 있습니다.

Direct3D 11.1보다 예전 버전의 런타임에서는, CreateBuffer를 호출해 4096보다 큰 constant buffer 생성을 요청하는 것은 실패합니다.
---
layout: post
title: "CheckMultisampleQualityLevels function"
date: 2022-03-17 20:20:00 +0900
categories: [Direct3D]
tags: [MSDN, DirectX, Direct3D]
---
[ID3D11Device::CheckMultisampleQualityLevels (d3d11.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/d3d11/nf-d3d11-id3d11device-checkmultisamplequalitylevels)

다중 샘플링(multisampling) 중 사용 가능한 quality levels의 개수를 반환합니다.

## Syntax

```cpp
HRESULT CheckMultisampleQualityLevels(
  [in]  DXGI_FORMAT Format,
  [in]  UINT        SampleCount,
  [out] UINT        *pNumQualityLevels
);
```

## Parameters

```
[in] Format
```

Type: **DXGI_FORMAT**

텍스처 포맷입니다. [DXGI_FORMAT](https://docs.microsoft.com/en-us/windows/win32/api/dxgiformat/ne-dxgiformat-dxgi_format) 문서를 보세요.

```
[in] SampleCount
```

Type: **UINT**

다중 샘플링 중인 샘플들의 개수입니다.

```
[out] pNumQualityLevels
```

Type: **UINT***

어댑터가 지원하는 quality levels의 개수입니다. Remarks를 보세요.

## Return value

Type: **HRESULT**

메소드는 [Direct3D 11 Return Codes](https://docs.microsoft.com/en-us/windows/win32/direct3d11/d3d11-graphics-reference-returnvalues) 중 하나를 반환합니다.

## Remarks

텍스처를 다중 샘플링할 때, 어댑터가 사용 가능한 quality levels의 개수는 사용되는 텍스처의 포맷과 요청된 샘플 수에 따라 다릅니다. quality levels의 최대 개수는 D3D11.h 내에 D3D11_MAX_MULTISAMPLE_SAMPLE_COUNT로 정의되어 있습니다.만약 메소드가 S_OK (0)을 반환하며 출력 매개변수인 pNumQualityLevels로 양수를 받았다면, 디바이스는 이 포맷과 샘플 개수 조합을 지원합니다. 만약 사용 불가능한 조합인 경우, 이 메소드는 실패 HRESULT (즉, 음의 정수)를 반환하거나, pNumQualityLevels 출력 매개변수를 0으로 설정하거나, 또는 둘 다 합니다.

뿐만 아니라, quality level의 정의는 각 하드웨어 공급업체(vendor)가 정의하도록 남겨둡니다. 그러나 이 정보를 검색하는 데 도움이 되는 기능은 Direct3D에서 제공하지 않습니다.

FEATURE_LEVEL_10_1 device들은 R32G32B32A32 및 R32G32B32를 제외한 모든 render target들에 대해 4x MSAA를 지원해야 합니다. FEATURE_LEVEL_11_0 device들은 모든 render target 형식에 대해 4x MSAA를, R32G32B32A32 포맷을 제외한 모든 render target 형식에 8x MSAA를 지원해야 합니다.
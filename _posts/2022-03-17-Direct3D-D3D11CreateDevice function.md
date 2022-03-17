---
layout: post
title: "D3D11CreateDevice function"
date: 2022-03-17 19:49:00 +0900
categories: [Direct3D]
tags: [MSDN, DirectX, Direct3D]
---
[D3D11CreateDevice function (d3d11.h) - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/api/d3d11/nf-d3d11-d3d11createdevice)

디스플레이 어댑터를 대표하는 device를 생성합니다.

## Syntax

```cpp
HRESULT D3D11CreateDevice(
  [in, optional]  IDXGIAdapter            *pAdapter,
                  D3D_DRIVER_TYPE         DriverType,
                  HMODULE                 Software,
                  UINT                    Flags,
  [in, optional]  const D3D_FEATURE_LEVEL *pFeatureLevels,
                  UINT                    FeatureLevels,
                  UINT                    SDKVersion,
  [out, optional] ID3D11Device            **ppDevice,
  [out, optional] D3D_FEATURE_LEVEL       *pFeatureLevel,
  [out, optional] ID3D11DeviceContext     **ppImmediateContext
);
```

## Parameters

```
[in, optional] pAdapter
```

Type: **IDXGIAdapter***

device를 생성할 때 사용하는 비디오 어댑터를 가리키는 포인터입니다. IDXGIFactory1::EnumAdapters에 의해 열거되는 첫 번째 어댑터인 기본 어댑터를 사용하려면 nullptr을 전달하세요.

> note: 애플리케이션 내에서 DXGI 1.0 (IDXGIFactory)와 DXGI 1.1 (IDXGIFactory1)을 혼용하지 마세요. IDXGIFactory 또는 IDXGIFactory을 사용하되, 둘 다 동시에 사용하지 마세요.

```
DriverType
```

Type: **D3D_DRIVER_TYPE**

생성할 드라이버의 유형을 표현하는 D3D_DRIVER_TYPE 입니다.

```
Software
```

Type: **HMODULE**

소프트웨어 래스터라이저를 구현한 DLL에 대한 핸들입니다. 만약 *DriverType*이 *D3D_DRIVER_TYPE_SOFTWARE*라면, *Software*는 반드시 nullptr이 아니여야 합니다. *LoadLibrary*, *LoadLibraryEx*, 또는 *GetModuleHandle*을 사용해 핸들을 가져옵니다.

```
Flags
```

Type: **UINT**

활성화할 런타임 레이어(runtime layers)들입니다 ([D3D11_CREATE_DEVICE_FLAG](https://docs.microsoft.com/en-us/windows/desktop/api/d3d11/ne-d3d11-d3d11_create_device_flag)). 값들은 비트단위로 OR될 수 있습니다.

```
[in, optional] pFeatureLevels
```

Type: const **D3D_FEATURE_LEVEL***

생성할 때 시도하는 feature levels의 순서를 결정하는 *D3D_FEATURE_LEVEL*들의 배열을 가리키는 포인터입니다. 만약 *pFeatureLevels*를 nullptr로 설정한다면, 이 함수는 다음 feature levels의 배열을 사용합니다.

```cpp
{
    D3D_FEATURE_LEVEL_11_0,
    D3D_FEATURE_LEVEL_10_1,
    D3D_FEATURE_LEVEL_10_0,
    D3D_FEATURE_LEVEL_9_3,
    D3D_FEATURE_LEVEL_9_2,
    D3D_FEATURE_LEVEL_9_1,
};
```

> note: 컴퓨터에 Direct3D 11.1 런타임이 있고 pFeatureLevels를 nullptr로 설정되었을 때, 이 함수는 D3D_FEATURE_LEVEL_11_1 device를 생성하지 않습니다. D3D_FEATURE_LEVEL_11_1 device를 생성하려면, 반드시 D3D_FEATURE_LEVEL_11_1을 포함한 D3D_FEATURE_LEVEL 배열을 명시적으로 제공해야 합니다. 만약 Direct3D 11.1 런타임이 설치되지 않은 컴퓨터에서 D3D_FEATURE_LEVEL_11_1을 포함한 배열을 제공한다면, 이 함수는 E_INVALIDARG를 반환하며 즉시 실패합니다.

```
FeatureLevels
```

Type: **UINT**

*pFeatureLevels* 내 요소의 개수입니다.

```
SDKVersion
```

Type: **UINT**

SDK의 버전입니다. *D3D11_SDK_VERSION*을 사용하세요.

```
[out, optional] ppDevice
```

Type: **D3D_FEATURE_LEVEL***

성공했을 때, pFeatureLevels 배열에서 성공한 첫 번째 *D3D_FEATURE_LEVEL*을 반환합니다. 지원되는 feature level을 결정할 필요가 없는 경우 nullptr을 입력하세요.

```
[out, optional] ppImmediateContext
```

Type: ID3D11DeviceContext**

device context를 대표하는 *ID3D11DeviceContext* 객체를 가리키는 포인터를 반환합니다. 이 매개변수가 nullptr일 때 *ID3D11DeviceContext*는 반환되지 않습니다.

## Return value

Type: **HRESULT**

메소드는 Direct3D 11 Return Codes 중 하나를 반환할 수 있습니다.

만약 *pAdapter* 매개변수를 nullptr로 설정하지 않았으며 *DriverType* 매개변수를 *D3D_DRIVER_TYPE_HARDWARE* 값으로 설정한 경우, 이 함수는 *E_INVALIDARG*를 반환합니다.

만약 *Flags*로 *D3D11_CREATE_DEVICE_DEBUG*를 지정했으며 컴퓨터에 잘못된 버전의 debug layer가 설치되어 있는 경우, 이 메소드는 *DXGI_ERROR_SDK_COMPONENT_MISSING*을 반환합니다.

## Remarks

이 진입점(entry-point)은 Windows 7, Windows Server 2008 R2, 그리고 Windows Vista 업데이트(KB971644)에서 사용할 수 있는 Direct3D 11 런타임에서 지원됩니다.

Windows 8, Windows Server 2012, 그리고 Windows 7 과 Platform Update for Windows 7이 설치된 Windows Server 2008 R2에서 사용 가능한 Direct3D 11.1 device를 생성하려면, 먼저 이 함수로 *I3D11Device*를 생성하고, 그 뒤 *ID3D11Device* 객체에서 *QueryInterface* 메소드를 호출하여 *ID3D11Device1* 인터페이스를 얻으세요.

Windows 8.1 그리고 Windows Server 2012 R2에서 사용 가능한 Direct3D 11.2 device를 생성하려면, 먼저 이 함수로 *ID3D11Device*를 생성하고, 그 뒤 *ID3D11Device* 객체에서 *QueryInterface* 메소드를 호출하여 *ID3D11Device2* 인터페이스를 얻으세요.

장치를 생성하지 않고 pFeatureLevel을 보고 지원되는 feature level을 결정하려면, *ppDevice*와 *ppImmediateContext*를 nullptr로 설정하세요.

예를 들어, device와 스왑 체인을 동시에 생성하려면, *D3D11CreateDeviceAndSwapChain*을 사용하세요. ([How To: Create a Device and Immediate Context](https://docs.microsoft.com/en-us/windows/desktop/direct3d11/overviews-direct3d-11-devices-initialize))

*pAdapter* 매개변수를 nullptr로 설정하지 않았을 때, 반드시 *DriverType* 매개변수를 *D3D_DRIVER_TYPE_UNKNOWN* 값으로 설정해야 합니다. 만약 *pAdapter* 매개변수가 nullptr이 아니며 DriverType 매개변수를 *D3D_DRIVER_TYPE_HARDWARE* 값으로 설정한 경우, *D3D11CreateDevice*는 *E_INVALIDARG*를 반환합니다.
---
title: Powiązanie grafiki
description: Konfiguracja powiązań graficznych i przypadków użycia
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 4854d5ff9d697a2bf082a788c0e761a2152b0294
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758711"
---
# <a name="graphics-binding"></a>Powiązanie grafiki

Aby można było korzystać z zdalnego renderowania platformy Azure w aplikacji niestandardowej, należy zintegrować ją z potokiem renderowania aplikacji. Integracja jest odpowiedzialna za powiązanie grafiki.

Po skonfigurowaniu, powiązanie grafiki zapewnia dostęp do różnych funkcji, które mają wpływ na renderowany obraz. Te funkcje można podzielić na dwie kategorie: ogólne funkcje, które są zawsze dostępne i określone funkcje, które są odpowiednie dla wybranych `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Powiązanie grafiki w aparacie Unity

W środowisku Unity całe powiązanie jest obsługiwane przez `RemoteUnityClientInit` strukturę przekazaną do `RemoteManagerUnity.InitializeManager` . Aby ustawić tryb grafiki, `GraphicsApiType` pole musi być ustawione na wybrane powiązanie. Pole zostanie wypełnione automatycznie w zależności od tego, czy XRDevice jest obecny. Zachowanie można przesłonić ręcznie przy użyciu następujących zachowań:

* **HoloLens 2**: zawsze używane jest powiązanie grafiki w [rzeczywistości mieszanej systemu Windows](#windows-mixed-reality) .
* **Aplikacja klasyczna Flat platformy UWP**: [symulacja](#simulation) jest zawsze używana. Aby użyć tego trybu, należy postępować zgodnie z krokami opisanymi w [samouczku: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md).
* **Edytor aparatu Unity**: [symulacja](#simulation) jest zawsze używana, chyba że jest podłączony zestaw WMR VR, w którym wypadek to, że ARR zostanie wyłączony, aby umożliwić debugowanie niezwiązanych z nią części aplikacji. Zobacz też [holographice komunikacji zdalnej](../how-tos/unity/holographic-remoting.md).

Jedyną inną istotną częścią aparatu Unity jest uzyskiwanie dostępu do [podstawowego powiązania](#access). wszystkie pozostałe sekcje poniżej można pominąć.

## <a name="graphics-binding-setup-in-custom-applications"></a>Konfiguracja powiązania grafiki w aplikacjach niestandardowych

Aby wybrać powiązanie grafiki, wykonaj następujące dwa kroki: pierwsze, powiązanie grafiki musi być inicjowane statycznie po zainicjowaniu programu:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

Powyższe wywołanie jest niezbędne do zainicjowania zdalnego renderowania platformy Azure do interfejsów API Holographic. Ta funkcja musi być wywoływana przed wywołaniem interfejsu API Holographic i przed uzyskaniem dostępu do innych interfejsów API renderowania zdalnego. Analogicznie, odpowiadająca funkcji DEINIT `RemoteManagerStatic.ShutdownRemoteRendering();` powinna być wywoływana po tym, że żadne interfejsy API Holographic nie są już wywoływane.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Uzyskiwanie dostępu do powiązania grafiki

Po skonfigurowaniu klienta podstawowe powiązanie grafiki można uzyskać za pomocą `AzureSession.GraphicsBinding` metody pobierającej. Przykładowo Statystyka ostatniej ramki może zostać pobrana w następujący sposób:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Graficzne interfejsy API

Istnieją obecnie dwa interfejsy API grafiki, które można wybrać, `WmrD3D11` i `SimD3D11` . Trzecia istnieje, `Headless` ale nie jest jeszcze obsługiwana po stronie klienta.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`jest domyślnym powiązaniem do uruchamiania na serwerze HoloLens 2. Zostanie utworzone `GraphicsBindingWmrD3d11` powiązanie. W tym trybie zdalne renderowanie platformy Azure jest podłączane bezpośrednio do interfejsów API Holographic.

Aby uzyskać dostęp do pochodnych powiązań graficznych, baza `GraphicsBinding` musi być rzutowana.
Istnieją dwie rzeczy, które należy wykonać, aby użyć powiązania WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informowanie zdalnego renderowania używanego układu współrzędnych

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


Gdzie powyżej `ptr` musi być wskaźnikiem do obiektu natywnego `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` , który definiuje układ współrzędnych świata obszaru, w którym współrzędne w interfejsie API są wyrażane w.

#### <a name="render-remote-image"></a>Renderowanie obrazu zdalnego

Na początku każdej ramki zdalna ramka musi być renderowana w buforze zaplecza. W tym celu należy wywołać metodę `BlitRemoteFrame` , która spowoduje wypełnienie informacji o kolorach i głębokości do aktualnie powiązanego obiektu docelowego renderowania. Dlatego ważne jest, aby to zrobić po powiązaniu buforu zapasowego jako elementu docelowego renderowania.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Symulacja

`GraphicsApiType.SimD3D11`jest powiązaniem symulacji i jeśli wybrane, tworzy `GraphicsBindingSimD3d11` powiązanie grafiki. Ten interfejs służy do symulowania przenoszenia głowy, na przykład w aplikacji klasycznej i renderowania obrazu monoscopic.
Konfiguracja jest nieco większa i działa w następujący sposób:

#### <a name="create-proxy-render-target"></a>Utwórz obiekt docelowy renderowania serwera proxy

Zawartość zdalna i lokalna muszą być renderowane do elementu docelowego renderowania koloru/głębokości, zwanego "serwerem proxy" przy użyciu danych z aparatu proxy dostarczonych przez `GraphicsBindingSimD3d11.Update` funkcję. Serwer proxy musi być zgodny z rozdzielczością buforu zapasowego. Gdy sesja będzie gotowa, należy `GraphicsBindingSimD3d11.InitSimulation` wywołać ją przed nawiązaniem połączenia:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Funkcja init musi być dostarczana ze wskaźnikami do natywnej metody D3D-urządzenie, a także do tekstury koloru i głębi elementu docelowego renderowania serwera proxy. Po zainicjowaniu `AzureSession.ConnectToRuntime` i `DisconnectFromRuntime` może być wywoływana wiele razy, ale podczas przełączania do innej sesji należy `GraphicsBindingSimD3d11.DeinitSimulation` najpierw wywołać starą sesję, zanim będzie `GraphicsBindingSimD3d11.InitSimulation` można wywołać inną sesję.

#### <a name="render-loop-update"></a>Aktualizacja pętli renderowania

Aktualizacja pętli renderowania obejmuje wiele kroków:

1. Każda ramka przed każdym renderowaniem `GraphicsBindingSimD3d11.Update` jest wywoływana z bieżącą transformację aparatu, która jest wysyłana do serwera, który ma być renderowany. W tym samym czasie zwrócona transformacja proxy powinna zostać zastosowana do aparatu proxy w celu renderowania do obiektu docelowego renderowania serwera proxy.
Jeśli zwrócona Aktualizacja serwera proxy `SimulationUpdate.frameId` ma wartość null, nie ma jeszcze żadnych danych zdalnych. W takim przypadku zamiast renderowania do obiektu docelowego renderowania proxy, każda Zawartość lokalna powinna być renderowana do buforu zaplecza bezpośrednio przy użyciu bieżących danych aparatu, a następne dwa kroki są pomijane.
1. Aplikacja powinna teraz powiązać obiekt docelowy renderowania serwera proxy i wywołanie `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Spowoduje to wypełnienie zdalnego koloru i szczegółowych informacji w celu renderowania serwera proxy. Każda zawartość lokalna może teraz być renderowana na serwerze proxy przy użyciu transformacji aparatu proxy.
1. Następnie bufor zapasowy musi być powiązany jako obiekt docelowy renderowania i `GraphicsBindingSimD3d11.ReprojectProxy` wywoływany, w którym momencie można przedstawić bufor zapasowy.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md)

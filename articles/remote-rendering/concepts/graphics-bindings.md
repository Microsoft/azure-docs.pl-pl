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
ms.custom: devx-track-csharp
ms.openlocfilehash: cefd00609062c30b036f87a0a01a75dc2afb868b
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246149"
---
# <a name="graphics-binding"></a>Powiązanie grafiki

Aby można było korzystać z zdalnego renderowania platformy Azure w aplikacji niestandardowej, należy zintegrować ją z potokiem renderowania aplikacji. Integracja jest odpowiedzialna za powiązanie grafiki.

Po skonfigurowaniu, powiązanie grafiki zapewnia dostęp do różnych funkcji, które mają wpływ na renderowany obraz. Te funkcje można podzielić na dwie kategorie: ogólne funkcje, które są zawsze dostępne i określone funkcje, które są odpowiednie dla wybranych `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Powiązanie grafiki w aparacie Unity

W środowisku Unity całe powiązanie jest obsługiwane przez `RemoteUnityClientInit` strukturę przekazaną do `RemoteManagerUnity.InitializeManager` . Aby ustawić tryb grafiki, `GraphicsApiType` pole musi być ustawione na wybrane powiązanie. Pole zostanie wypełnione automatycznie w zależności od tego, czy XRDevice jest obecny. Zachowanie można przesłonić ręcznie przy użyciu następujących zachowań:

* **HoloLens 2**: zawsze używane jest powiązanie grafiki w [rzeczywistości mieszanej systemu Windows](#windows-mixed-reality) .
* **Aplikacja klasyczna Flat platformy UWP**: [symulacja](#simulation) jest zawsze używana.
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

`GraphicsApiType.WmrD3D11` jest domyślnym powiązaniem do uruchamiania na serwerze HoloLens 2. Zostanie utworzone `GraphicsBindingWmrD3d11` powiązanie. W tym trybie zdalne renderowanie platformy Azure jest podłączane bezpośrednio do interfejsów API Holographic.

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

Na początku każdej ramki, zdalna ramka musi być renderowana w buforze zaplecza. W tym celu należy wywołać metodę `BlitRemoteFrame` , która spowoduje wypełnienie zarówno informacji o kolorach, jak i głębokości dla obu oczu w aktualnie powiązanym miejscu docelowym renderowania. W związku z tym ważne jest, aby zrobić to po powiązaniu pełnego buforu jako elementu docelowego renderowania.

> [!WARNING]
> Po blit — zdalnego obrazu do buforu zapasowego zawartość lokalna powinna być renderowana przy użyciu jednoprzebiegowej techniki renderowania stereo, np. przy użyciu **SV_RenderTargetArrayIndex**. Korzystanie z innych technik renderowania stereo, takich jak renderowanie poszczególnych oczu w oddzielnym przebiegu, może skutkować poważnym obniżeniem wydajności lub artefaktami graficznymi.

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

`GraphicsApiType.SimD3D11` jest powiązaniem symulacji i jeśli wybrane, tworzy `GraphicsBindingSimD3d11` powiązanie grafiki. Ten interfejs służy do symulowania przenoszenia głowy, na przykład w aplikacji klasycznej i renderowania obrazu monoscopic.

Aby zaimplementować powiązanie symulacji, ważne jest, aby zrozumieć różnicę między aparatem lokalnym i ramką zdalną, zgodnie z opisem na stronie [aparat](../overview/features/camera.md) .

Potrzeba dwóch kamer:

* **Aparat lokalny**: ten aparat reprezentuje bieżące położenie kamery, która jest obsługiwana przez logikę aplikacji.
* **Aparat proxy**: ten aparat jest zgodny z bieżącą *ramką zdalną* , która została wysłana przez serwer. Ponieważ istnieje opóźnienie między klientem żądającym ramki i jej przybyciem, *zdalna ramka* jest zawsze bitem pod względem ruchu aparatu lokalnego.

Podstawowa metoda polega na tym, że obraz zdalny i zawartość lokalna są renderowane w miejscu docelowym poza ekranem przy użyciu aparatu proxy. Obraz serwera proxy jest następnie przemieszczony w lokalnym obszarze aparatu fotograficznego, co jest dokładniej wyjaśnione w [późnym rozmieszczeniu](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` Program obsługuje również renderowanie Stereoscopic, które należy włączyć podczas `InitSimulation` wywołania konfiguracji poniżej. Konfiguracja jest nieco większa i działa w następujący sposób:

#### <a name="create-proxy-render-target"></a>Utwórz obiekt docelowy renderowania serwera proxy

Zawartość zdalna i lokalna muszą być renderowane do elementu docelowego renderowania koloru/głębokości, zwanego "serwerem proxy" przy użyciu danych z aparatu proxy dostarczonych przez `GraphicsBindingSimD3d11.Update` funkcję.

Serwer proxy musi być zgodny z rozdzielczością buforu zapasowego i powinien być w formacie int *DXGI_FORMAT_R8G8B8A8_UNORM* lub *DXGI_FORMAT_B8G8R8A8_UNORM* . W przypadku renderowania Stereoscopic, zarówno tekstury, jak i w przypadku zastosowania głębokości, tekstura serwera proxy musi mieć dwie warstwy tablicy zamiast jednej. Gdy sesja będzie gotowa, należy `GraphicsBindingSimD3d11.InitSimulation` wywołać ją przed nawiązaniem połączenia:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
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
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.frameId != 0)
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

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.frameId != 0)
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

#### <a name="simulation-update-structures"></a>Struktury aktualizacji symulacji

Każda ramka, **Aktualizacja pętli renderowania** z poprzedniej sekcji wymaga wprowadzenia zakresu parametrów aparatu odpowiadającego aparatowi lokalnemu i zwraca zestaw parametrów aparatu, które odpowiadają aparatowi następnej dostępnej ramki. Te dwa zestawy są przechwytywane w `SimulationUpdateParameters` i `SimulationUpdateResult` strukturach odpowiednio:

```cs
public struct SimulationUpdateParameters
{
    public UInt32 frameId;
    public StereoMatrix4x4 viewTransform;
    public StereoCameraFOV fieldOfView;
};

public struct SimulationUpdateResult
{
    public UInt32 frameId;
    public float nearPlaneDistance;
    public float farPlaneDistance;
    public StereoMatrix4x4 viewTransform;
    public StereoCameraFOV fieldOfView;
};
```

Elementy członkowskie struktury mają następujące znaczenie:

| Członek | Opis |
|--------|-------------|
| frameId | Ciągły identyfikator ramki. Niezbędne do SimulationUpdateParameters danych wejściowych i musi być ciągle zwiększana dla każdej nowej ramki. Wartość 0 w SimulationUpdateResult, jeśli żadne dane ramki nie są jeszcze dostępne. |
| viewTransform | Para z lewej strony-stereo macierzy transformacji widoku aparatu ramki. Aby można było zrenderować monoscopic, tylko `left` element członkowski jest prawidłowy. |
| fieldOfView | Para z lewej strony-stereo dla pól w aparacie Frame-View w [polu OpenXR Konwencji widoku](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). Aby można było zrenderować monoscopic, tylko `left` element członkowski jest prawidłowy. |
| nearPlaneDistance | Odległość bliska płaszczyzny używana dla macierzy projekcji bieżącej ramki zdalnej. |
| farPlaneDistance | Odległość między płaszczyznami używana dla macierzy projekcji bieżącej ramki zdalnej. |

Pary stereo `viewTransform` i `fieldOfView` zezwalają na ustawienie wartości kamery oczu w przypadku renderowania Stereoscopic jest włączone. W przeciwnym razie `right` elementy członkowskie zostaną zignorowane. Jak widać, tylko transformacja aparatu jest przenoszona jako macierze transformujące zwykłej 4x4, podczas gdy nie są określone macierze projekcji. Rzeczywiste Macierze są obliczane przez zdalne renderowanie na platformie Azure wewnętrznie przy użyciu określonych pól-widoku oraz bieżącego, najbliższej płaszczyzny i warstwy międzypłaszczyznowej w [interfejsie API CameraSettings](../overview/features/camera.md).

Ponieważ można zmienić [CameraSettings](../overview/features/camera.md) i międzypłaszczyznę w czasie wykonywania zgodnie z potrzebami, a usługa stosuje te ustawienia asynchronicznie, każdy SimulationUpdateResult także przenosi określoną bliską płaszczyznę i międzypłaszczyznę używaną podczas renderowania odpowiedniej ramki. Możesz użyć tych wartości płaszczyzny, aby dostosować macierze projekcji do renderowania obiektów lokalnych w celu dopasowania do zdalnego renderowania ramek.

Na koniec, chociaż wywołanie **aktualizacji symulacji** wymaga pola widzenia w Konwencji OpenXR, ze względów bezpieczeństwa standaryzacji i algorytmów, można użyć funkcji konwersji przedstawionych w następujących przykładach populacji struktury:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(UInt32 frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.frameId = frameId;
    parameters.viewTransform.left = viewTransform;
    if(parameters.fieldOfView.left.fromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        return null;
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out UInt32 frameId)
{
    if(result.frameId == 0)
    {
        // Invalid frame data
        return;
    }
    
    // Use the screenspace depth convention you expect for your projection matrix locally
    if(result.fov.left.toProjectionMatrix(result.nearPlaneDistance, result.farPlaneDistance, DepthConvention.ZeroToOne, projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.viewTransform.left;
    frameId = result.frameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.frameId = frameId;
    parameters.viewTransform.left = viewTransform;
    if(FovFromProjectionMatrix(projectionMatrix, parameters.fieldOfView.left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if(result.frameId == 0)
    {
        // Invalid frame data
        return;
    }
    
    // Use the screenspace depth convention you expect for your projection matrix locally
    if(FovToProjectionMatrix(result.fieldOfView.left, result.nearPlaneDistance, result.farPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.viewTransform.left;
    frameId = result.frameId;
}
```

Te funkcje konwersji umożliwiają szybkie przełączanie się między specyfikacją pola a widokiem i matrycą projekcji z zwykłym 4x4, w zależności od potrzeb renderowania lokalnego. Te funkcje konwersji zawierają logikę weryfikacji i zwracają błędy, bez ustawiania prawidłowego wyniku, w przypadku gdy macierze projekcji wejściowej lub pola wejściowe są nieprawidłowe.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [Klasa grafiki c#binding](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [Klasa GraphicsBindingWmrD3d11 języka C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [Klasa GraphicsBindingSimD3d11 języka C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Struktura C++ RemoteRenderingInitialization](/cpp/api/remote-rendering/remoterenderinginitialization)
* [Klasa C++ Graphicsbinding](/cpp/api/remote-rendering/graphicsbinding)
* [Klasa C++ GraphicsBindingWmrD3d11](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [Klasa C++ GraphicsBindingSimD3d11](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Następne kroki

* [Aparat fotograficzny](../overview/features/camera.md)
* [Reprojekcja na późnym etapie](../overview/features/late-stage-reprojection.md)
* [Samouczek: Wyświetlanie modeli renderowanych zdalnie](../tutorials/unity/view-remote-models/view-remote-models.md)
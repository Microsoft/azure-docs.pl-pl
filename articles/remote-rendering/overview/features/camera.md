---
title: Aparat fotograficzny
description: Opisuje ustawienia i przypadki użycia aparatu
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594133"
---
# <a name="camera"></a>Aparat fotograficzny

Aby upewnić się, że zawartość lokalna i zdalnie renderowane mogą być złożone w sposób ciągły, różne właściwości aparatu muszą pozostawać w synchronizacji między serwerem a klientem. W szczególności transformacja i projekcja aparatu. Na przykład wyrenderowana lokalnie zawartość musi używać tego samego przekształcenia aparatu i rzutowania, z którym jest renderowana Najnowsza ramka zdalna.

![Kamera lokalna i zdalna](./media/camera.png)

Na powyższym obrazie kamera lokalna została przeniesiona w porównaniu do zdalnej ramki wysyłanej przez serwer. W związku z tym zawartość lokalna musi być renderowana z tej samej perspektywy co ramka zdalna, a wreszcie wynikowy obraz zostanie przemieszczony w lokalnym obszarze aparatu fotograficznego, co zostało szczegółowo wyjaśnione w [fazie ponownego przygotowywania](late-stage-reprojection.md).

Opóźnienie między renderowaniem zdalnym i lokalnym oznacza, że wszelkie zmiany tych ustawień są stosowane z opóźnieniem. W związku z tym nie można używać żadnych nowych wartości bezpośrednio w tej samej ramce.

W ustawieniach aparatu można ustawić odległość blisko i na odległość. Na urządzeniu HoloLens 2 pozostałe dane transformacji i projekcji są definiowane przez sprzęt. W przypadku korzystania z [symulacji pulpitu](../../concepts/graphics-bindings.md)są one ustawiane za pośrednictwem wejścia `Update` funkcji.

Zmienione wartości mogą być używane lokalnie zaraz po nadejściu pierwszej ramki zdalnej, która została renderowana z nimi. W przypadku urządzenia HoloLens 2 dane, które mają być używane do renderowania, są dostarczane przez *HolographicFrame* tak samo jak w przypadku dowolnej innej aplikacji Holographic. W przypadku [symulacji pulpitu](../../concepts/graphics-bindings.md)są one pobierane za pośrednictwem danych wyjściowych `Update` funkcji.

## <a name="camera-settings"></a>Ustawienia aparatu

Następujące właściwości można zmienić w ustawieniach aparatu:

**Bliskie i odległe płaszczyzny:**

Aby upewnić się, że nie można ustawić żadnych nieprawidłowych zakresów, właściwości **NearPlane** i **FarPlane** są tylko do odczytu i aby zmienić zakres, istnieje oddzielna funkcja **SetNearAndFarPlane** . Te dane zostaną wysłane na serwer na końcu ramki. Po ustawieniu tych wartości **NearPlane** musi być mniejszy niż **FarPlane**. W przeciwnym razie wystąpi błąd.

> [!IMPORTANT]
> W środowisku Unity jest to obsługiwane automatycznie podczas zmiany głównego aparatu w bliskich i odległych płaszczyznach.

**EnableDepth**:

Czasami warto wyłączyć zapis buforu głębokości obrazu zdalnego na potrzeby debugowania. Wyłączenie głębi spowoduje również zatrzymanie wysyłania danych głębi przez serwer, co zmniejsza przepustowość.

> [!TIP]
> W aparacie Unity jest dostarczany składnik Debug o nazwie **EnableDepthComponent** , który może służyć do przełączania tej funkcji w interfejsie użytkownika edytora.

**InverseDepth**:

> [!NOTE]
> To ustawienie ma znaczenie tylko wtedy `EnableDepth` , gdy jest ustawione na `true` . W przeciwnym razie to ustawienie nie ma żadnego wpływu.

Bufory głębokości zwykle zapisują wartości z w zakresie liczb zmiennoprzecinkowych [0; 1], z wartością 0 oznaczającą głębokość najbliższej płaszczyzny i 1 oznaczającą głębokość międzypłaszczyzny. Istnieje również możliwość odwrócenia tego zakresu i zapisania wartości głębokości z zakresu [1; 0], co oznacza, że głębokość zbliżonej warstwy zmieni się na 1, a głębokość między płaszczyzną będzie równa 0. Ogólnie rzecz biorąc, to drugie zwiększa rozkład liczby zmiennoprzecinkowej w nieliniowej liczbie z.

> [!WARNING]
> Typowym podejściem jest odwracanie wartości zbliżonych i międzypłaszczyznowych w obiektach aparatu. To nie powiedzie się w przypadku renderowania zdalnego na platformie Azure z powodu błędu podczas próby wykonania tej na `CameraSettings` .

Interfejs API renderowania zdalnego platformy Azure musi wiedzieć o konwencji buforu głębokości lokalnego modułu renderowania, aby poprawnie złożyć zdalną głębokość do lokalnego buforu głębokości. Jeśli zakres buforu głębokości wynosi [0; 1], pozostaw tę flagę jako `false` . Jeśli używasz odwróconego buforu głębokości z zakresem [1; 0], ustaw `InverseDepth` flagę na wartość `true` .

> [!NOTE]
> W przypadku aparatu Unity odpowiednie ustawienie jest już stosowane przez program, `RenderingConnection` dlatego nie ma potrzeby interwencji ręcznej.

Zmiana ustawień aparatu można wykonać w następujący sposób:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [CameraSettings C#](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update — funkcja](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update — funkcja](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Następne kroki

* [Powiązanie grafiki](../../concepts/graphics-bindings.md)
* [Reprojekcja na późnym etapie](late-stage-reprojection.md)
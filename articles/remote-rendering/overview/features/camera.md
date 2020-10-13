---
title: Aparat fotograficzny
description: Opisuje ustawienia i przypadki użycia aparatu
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564913"
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

Aby upewnić się, że nie można ustawić żadnych nieprawidłowych zakresów, właściwości **NearPlane** i **FarPlane** są tylko do odczytu i aby zmienić zakres, istnieje oddzielna funkcja **SetNearAndFarPlane** . Te dane zostaną wysłane na serwer na końcu ramki.

> [!IMPORTANT]
> W środowisku Unity jest to obsługiwane automatycznie podczas zmiany głównego aparatu w bliskich i odległych płaszczyznach.

**EnableDepth**:

Czasami warto wyłączyć zapis buforu głębokości obrazu zdalnego na potrzeby debugowania. Wyłączenie głębi spowoduje również zatrzymanie wysyłania danych głębi przez serwer, co zmniejsza przepustowość.

> [!TIP]
> W aparacie Unity jest dostarczany składnik Debug o nazwie **EnableDepthComponent** , który może służyć do przełączania tej funkcji w interfejsie użytkownika edytora.

Zmiana ustawień aparatu można wykonać w następujący sposób:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [CameraSettings C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update — funkcja](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update — funkcja](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Następne kroki

* [Powiązanie grafiki](../../concepts/graphics-bindings.md)
* [Reprojekcja na późnym etapie](late-stage-reprojection.md)

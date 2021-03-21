---
title: Obszar etapu
description: Opisuje ustawienia przestrzeni etapów i przypadków użycia
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594014"
---
# <a name="stage-space"></a>Obszar etapu

W przypadku uruchamiania funkcji ARR na urządzeniu, które udostępnia dane śledzenia głowy, takie jak HoloLens 2, ułożenia głowy jest wysyłana do aplikacji użytkownika i serwera. Miejsce, w którym jest zdefiniowana transformacja elementu podrzędnego, jest nazywane *obszarem etapu*.

Aby wyrównać zawartość lokalną i zdalną, zakłada się, że przestrzeń na etapie i przestrzeń świata są identyczne na klientach i serwerach. Jeśli użytkownik zdecyduje się na dodanie dodatkowego przekształcenia na aparacie, należy go wysłać do serwera, a także prawidłowo dostosować zawartość lokalną i zdalną.

Typowymi przyczynami przemieszczania miejsca na etapy są [Narzędzia do blokowania na świecie](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) lub inne rzeczywiste techniki kotwicowe, a także przeniesienie znaku wirtualnego w VR.

## <a name="stage-space-settings"></a>Ustawienia obszaru przemieszczania

> [!CAUTION]
> EKSPERYMENTALNe: Ta funkcja jest eksperymentalna i zmienia się wraz z upływem czasu. W ten sposób aktualizowanie do nowszych wersji zestawu SDK klienta może wymagać wykonania dodatkowych czynności w celu uaktualnienia kodu. Bieżąca implementacja spowoduje przerwanie wyrównania zawartości lokalnej/zdalnej przez krótki czas podczas zmiany pochodzenia obszaru etapu.
W tym celu jest obecnie przeznaczony tylko do celów blokowania na świecie, takich jak kotwice, które wykazują tylko bardzo małe zmiany w czasie.

Aby poinformować serwer, że do obszaru przemieszczania jest zastosowana dodatkowa transformacja, jego źródło zdefiniowane przez pozycję i obrót w przestrzeni świata muszą być wysyłane. Dostęp do tego ustawienia można uzyskać za pośrednictwem *ustawienia przestrzeni etapowej*.

> [!IMPORTANT]
> W [symulacji pulpitu](../../concepts/graphics-bindings.md) miejsce na świecie jest udostępniane przez aplikację użytkownika. W takim przypadku ustawienie pochodzenie miejsca na etapie musi być pominięte, ponieważ jest już mnożone do przekształcenia aparatu.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Skrypt obszaru przemieszczania aparatu Unity

Integracja aparatu Unity zapewnia skrypt o nazwie **StageSpace** , który można dodać do obiektu nadrzędnegoobject aparatu. Po utworzeniu tego skryptu zajmiemy się ustawieniem początku obszaru etapu.

## <a name="next-steps"></a>Następne kroki

* [Powiązanie grafiki](../../concepts/graphics-bindings.md)
* [Reprojekcja na późnym etapie](late-stage-reprojection.md)

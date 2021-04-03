---
title: Używanie funkcji Holographic Remoting i usługi Remote Rendering w środowisku Unity
description: Jak można używać podglądu usług zdalnych Holographic w połączeniu z funkcją zdalnego renderowania platformy Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92201821"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Używanie funkcji Holographic Remoting i usługi Remote Rendering w środowisku Unity

[Komunikacja](/windows/mixed-reality/holographic-remoting-player) zdalna Holographic i zdalne renderowanie na platformie Azure wykluczają się wzajemnie w ramach jednej aplikacji. W związku z tym [tryb odtwarzania aparatu Unity](/windows/mixed-reality/unity-play-mode) jest również niedostępny.

Dla każdego przebiegu edytora Unity można użyć tylko jednego z nich. Aby użyć drugiej z nich, należy najpierw ponownie uruchomić środowisko Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Korzystanie z trybu odtwarzania aparatu Unity w celu uzyskania podglądu na serwerze HoloLens 2

 Można nadal używać trybu odtwarzania aparatu Unity, na przykład w celu przetestowania interfejsu użytkownika aplikacji. Niemniej jednak ważne jest, aby ARR nigdy nie zostało zainicjowane. W przeciwnym razie wystąpi awaria.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Używanie zestawu słuchawkowego WMR VR do podglądu na pulpicie

Jeśli jest obecny zestaw słuchawkowy w systemie Windows Mixed Reality, można go użyć do podglądu wewnątrz aparatu Unity. W takim przypadku należy zastanowić się nad zainicjowaniem ARR, ale nie będzie możliwe nawiązanie połączenia z sesją podczas korzystania z zestawu słuchawkowego WMR.
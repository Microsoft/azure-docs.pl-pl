---
title: Zainstaluj kotwice przestrzenne platformy Azure dla aparatu Unity
description: Konfigurowanie projektu środowiska Unity do korzystania z kotwic przestrzennych platformy Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812315"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity

## <a name="requirements"></a>Wymagania

Kotwice przestrzenne platformy Azure obecnie obsługują technologię Unity 2019,4 (LTS) z następującymi konfiguracjami.

* Środowisko Unity 2019,4 z AR Foundation 3,1 jest obsługiwane przez kotwice przestrzenne platformy Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Konfigurowanie projektu

Kotwice przestrzenne platformy Azure dla aparatu Unity są obecnie dystrybuowane przy użyciu pakietu zasobów aparatu Unity ( `.unitypackage` ), który można znaleźć w [wersjach usługi GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Importuj pakiet zasobów

1. Pobierz `AzureSpatialAnchors.unitypackage` plik dla wersji, która ma być docelowa z [wydań usługi GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.unity3d.com/Manual/AssetPackagesImport.html) , aby zaimportować pakiet zasobów aparatu Unity do projektu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Tworzenie i lokalizowanie kotwic w środowisku Unity](./create-locate-anchors-unity.md)

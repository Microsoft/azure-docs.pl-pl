---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998042"
---
Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

W okienku **projekt** przejdź do `Assets\AzureSpatialAnchors.SDK\Resources` . Wybierz pozycję `SpatialAnchorConfig`. Następnie w okienku **Inspektor** wprowadź `Account Key` wartość dla `Spatial Anchors Account Key` i `Account ID` jako wartość parametru `Spatial Anchors Account Id` .

Następnie otwórz program `SpatialAnchorManager.cs` . Znajdź `CreateSessionAsync()` i Dodaj następujący wiersz, zastępując w domenie konta wcześniejszie: `session.Configuration.AccountDomain = "MyAccountDomain";` . Możesz dodać ten wiersz bezpośrednio przed tym komentarzem `// Configure authentication` .

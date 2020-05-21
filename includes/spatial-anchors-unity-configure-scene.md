---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: e3ef7e7b8f527cde1fcfbb13141fd3a520921267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673428"
---
Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

W okienku **projekt** przejdź do `Assets\AzureSpatialAnchors.SDK\Resources` . Wybierz pozycję `SpatialAnchorConfig`. Następnie w okienku **Inspektor** wprowadź `Account Key` wartość dla `Spatial Anchors Account Key` i `Account ID` jako wartość parametru `Spatial Anchors Account Id` .

Następnie otwórz program `SpatialAnchorManager.cs` . Znajdź `CreateSessionAsync()` i Dodaj następujący wiersz, zastępując w domenie konta wcześniejszie: `session.Configuration.AccountId = "MyAccountDomain";` . Możesz dodać ten wiersz bezpośrednio przed tym komentarzem `// Configure authentication` .

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005638"
---
## <a name="message-headers"></a>Nagłówki komunikatów
Są to właściwości, które otrzymujesz w nagłówkach wiadomości:

| Nazwa właściwości | Opis |
| ------------- | ----------- | 
| AEG-Subscription-Name | Nazwa subskrypcji zdarzeń. |
| AEG-Delivery-Count | Liczba prób wykonanych dla zdarzenia. |
| AEG — typ zdarzenia | <p>Typ zdarzenia.</p><p>Może to być jedna z następujących wartości:</p><ul><li>SubscriptionValidation</li><li>Powiadomienie</li><li>SubscriptionDeletion</li></ul> | 
| AEG — wersja metadanych | <p>Wersja metadanych zdarzenia.<p> W przypadku **schematu zdarzeń Event Grid** ta właściwość reprezentuje wersję metadanych i **schemat zdarzeń w chmurze**, który reprezentuje **wersję specyfikacji**. </p>|
| AEG — wersja danych | <p>Wersja danych zdarzenia.</p><p>W przypadku **schematu zdarzeń Event Grid** ta właściwość reprezentuje wersję danych i **schemat zdarzeń w chmurze**, nie ma zastosowania.</p> |
| AEG-Output-Event-ID | Identyfikator zdarzenia Event Grid. |



---
title: Kolejka magazynu jako procedura obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można używać kolejek usługi Azure Storage jako programów obsługi zdarzeń dla zdarzeń Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598542"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Kolejka magazynu jako procedura obsługi zdarzeń dla zdarzeń Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **platforma azure queue storage** jest jednym z nich. 

Użyj **queue storage** do odbierania zdarzeń wymagających ściągnięcia. Możesz użyć magazynu kolejek, gdy masz długotrwały proces, który trwa zbyt długo. Wysyłając zdarzenia do magazynu kolejek, aplikacja może ściągać i przetwarzać zdarzenia zgodnie z własnym harmonogramem.

## <a name="tutorials"></a>Samouczki
Zapoznaj się z poniższym samouczkiem, aby zapoznać się z przykładem użycia usługi queue storage jako procedury obsługi zdarzeń. 

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure queue storage przy użyciu interfejsu wiersza polecenia platformy Azure i Event Grid](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 

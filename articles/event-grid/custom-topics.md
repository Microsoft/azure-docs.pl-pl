---
title: Tematy niestandardowe w siatce zdarzeń platformy Azure
description: W tym artykule opisano tematy niestandardowe w usłudze Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394360"
---
# <a name="custom-topics-in-azure-event-grid"></a>Tematy niestandardowe w siatce zdarzeń platformy Azure
Temat siatki zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatki zdarzeń i decyduje, czy źródło zdarzeń potrzebuje jednego tematu, czy więcej niż jednego tematu. Temat jest używany do kolekcji powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy mają być subskrybowanych.

**Tematy niestandardowe** to tematy aplikacji i innych firm. Gdy temat niestandardowy zostanie utworzony lub zostanie przypisany do niego dostęp, będzie on widoczny w ramach subskrypcji. 

Podczas projektowania aplikacji, masz elastyczność przy podejmowaniu decyzji, ile tematów do utworzenia. W przypadku dużych rozwiązań utwórz **temat niestandardowy** dla **każdej kategorii powiązanych zdarzeń**. Może to na przykład być aplikacja, która wysyła zdarzenia powiązane z modyfikowaniem kont użytkowników i przetwarzaniem zamówień. Istnieje małe prawdopodobieństwo, że procedura obsługi zdarzeń oczekuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe, a procedury obsługi zdarzeń subskrybują temat, którymi są zainteresowane. W przypadku małych rozwiązań możesz chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą filtrować typy zdarzeń, które chcą.

## <a name="event-schema"></a>Schemat zdarzeń
Aby uzyskać szczegółowe omówienie schematu zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md). W przypadku tematów niestandardowych wydawca zdarzeń określa obiekt **danych.** Dane najwyższego poziomu powinny mieć te same pola co standardowe zdarzenia zdefiniowane za zasoby.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

W poniższych sekcjach znajdują się łącza do samouczków dotyczących tworzenia tematów niestandardowych przy użyciu szablonów witryny Azure portal, CLI, PowerShell i Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Samouczki portalu Azure
|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą witryny Azure Portal](custom-event-quickstart-portal.md) | Pokazuje, jak używać portalu do wysyłania zdarzeń niestandardowych. |
| [Szybki start: kierowanie zdarzeń niestandardowych do magazynu kolejki platformy Azure](custom-event-to-queue-storage.md) | W tym artykule opisano sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |
| [Jak: publikować w temacie niestandardowym](post-to-custom-topic.md) | Pokazuje, jak opublikować zdarzenie w temacie niestandardowym. |


## <a name="azure-cli-tutorials"></a>Samouczki interfejsu wiersza polecenia platformy Azure
|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą interfejsu wiersza polecenia platformy Azure](custom-event-quickstart.md) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń niestandardowych. |
| [Narzędzie CLI platformy Azure: tworzenie tematu niestandardowego siatki zdarzeń](./scripts/event-grid-cli-create-custom-topic.md)|Przykładowy skrypt, który tworzy temat niestandardowy. Skrypt pobiera punkt końcowy i klucz.|
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń w temacie niestandardowym](./scripts/event-grid-cli-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|

## <a name="azure-powershell-tutorials"></a>Samouczki programu Azure PowerShell
|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą programu Azure PowerShell](custom-event-quickstart-powershell.md) | Pokazuje, jak używać programu Azure PowerShell do wysyłania zdarzeń niestandardowych. |
| [PowerShell: tworzenie tematu niestandardowego usługi Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Przykładowy skrypt, który tworzy temat niestandardowy. Skrypt pobiera punkt końcowy i klucz.|
| [Program PowerShell: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|

## <a name="arm-template-tutorials"></a>Samouczki szablonów ARM
|Tytuł  |Opis  |
|---------|---------|
| [Szablon Menedżera zasobów: temat niestandardowy i punkt końcowy elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Szablon Menedżera zasobów, który tworzy temat niestandardowy i subskrypcję dla tego tematu niestandardowego. Wysyła zdarzenia do elementu WebHook. |
| [Szablon Menedżera zasobów: temat niestandardowy i punkt końcowy centrów zdarzeń](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżera zasobów, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do usługi Azure Event Hubs. |

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Tematy systemowe](system-topics.md)
- [Domeny](event-domains.md)
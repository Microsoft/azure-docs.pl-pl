---
title: Tematy niestandardowe w Azure Event Grid
description: Zawiera opis tematów niestandardowych w Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113789"
---
# <a name="custom-topics-in-azure-event-grid"></a>Tematy niestandardowe w Azure Event Grid
Temat siatki zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatka zdarzeń i decyduje o tym, czy źródło zdarzenia wymaga jednego tematu, czy też więcej niż jednego tematu. Temat służy do zbierania powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy zasubskrybować.

**Tematy niestandardowe** są tematami aplikacji i innych firm. Gdy temat niestandardowy zostanie utworzony lub zostanie przypisany do niego dostęp, będzie on widoczny w ramach subskrypcji. 

Podczas projektowania aplikacji masz elastyczność podczas decydowania o liczbie tematów do utworzenia. W przypadku dużych rozwiązań Utwórz **niestandardowy temat** dla **każdej kategorii powiązanych zdarzeń**. Może to na przykład być aplikacja, która wysyła zdarzenia powiązane z modyfikowaniem kont użytkowników i przetwarzaniem zamówień. Istnieje małe prawdopodobieństwo, że procedura obsługi zdarzeń oczekuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe, a procedury obsługi zdarzeń subskrybują temat, którymi są zainteresowane. W przypadku małych rozwiązań można chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą odfiltrować żądane typy zdarzeń.

## <a name="event-schema"></a>Schemat zdarzeń
Aby zapoznać się ze szczegółowymi informacjami o schemacie zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). W przypadku niestandardowych tematów Wydawca zdarzeń określa obiekt **danych** . Dane najwyższego poziomu powinny mieć te same pola co standardowe zdarzenia zdefiniowane przez zasób.

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

Poniższe sekcje zawierają linki do samouczków umożliwiających tworzenie niestandardowych tematów przy użyciu szablonów Azure Portal, CLI, PowerShell i Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Samouczki Azure Portal
|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą Azure Portal](custom-event-quickstart-portal.md) | Pokazuje, w jaki sposób używać portalu do wysyłania zdarzeń niestandardowych. |
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure queue storage](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |
| [Instrukcje: publikowanie w temacie niestandardowym](post-to-custom-topic.md) | Pokazuje, jak ogłosić zdarzenie w temacie niestandardowym. |


## <a name="azure-cli-tutorials"></a>Samouczki interfejsu wiersza polecenia platformy Azure
|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą interfejsu wiersza polecenia platformy Azure](custom-event-quickstart.md) | Pokazuje, jak wysyłać zdarzenia niestandardowe przy użyciu interfejsu wiersza polecenia platformy Azure. |
| [Interfejs wiersza polecenia platformy Azure: Tworzenie Event Grid tematu niestandardowego](./scripts/event-grid-cli-create-custom-topic.md)|Przykładowy skrypt służący do tworzenia tematu niestandardowego. Skrypt Pobiera punkt końcowy i klucz.|
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-cli-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu webhook.|

## <a name="azure-powershell-tutorials"></a>Samouczki Azure PowerShell
|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą Azure PowerShell](custom-event-quickstart-powershell.md) | Pokazuje, w jaki sposób używać Azure PowerShell do wysyłania zdarzeń niestandardowych. |
| [PowerShell: Tworzenie niestandardowego tematu Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Przykładowy skrypt służący do tworzenia tematu niestandardowego. Skrypt Pobiera punkt końcowy i klucz.|
| [PowerShell: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu webhook.|

## <a name="arm-template-tutorials"></a>Samouczki dotyczące szablonów ARM
|Tytuł  |Opis  |
|---------|---------|
| [Szablon Menedżer zasobów: temat niestandardowy i punkt końcowy elementu webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Szablon Menedżer zasobów, który tworzy niestandardowy temat i subskrypcję dla tego tematu niestandardowego. Wysyła zdarzenia do elementu webhook. |
| [Szablon Menedżer zasobów: temat niestandardowy i Event Hubs punkt końcowy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżer zasobów, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do Event Hubs platformy Azure. |

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Tematy dotyczące systemu](system-topics.md)
- [Domeny](event-domains.md)
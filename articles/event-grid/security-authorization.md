---
title: Azure Event Grid zabezpieczenia i uwierzytelnianie
description: Opis usługi Azure Event Grid i pojęć z nią związanych.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 326fa00645302eb4b9c9bc59f17c1ca153bdb0b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371724"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autoryzowanie dostępu do zasobów Event Grid
Azure Event Grid umożliwia kontrolowanie poziomu dostępu nadawanego różnym użytkownikom w celu wykonywania różnych **operacji zarządzania** , takich jak subskrypcje zdarzeń listy, tworzenie nowych i generowanie kluczy. Event Grid używa kontroli dostępu opartej na rolach (Azure RBAC).

> [!NOTE]
> EventGrid nie obsługuje funkcji RBAC na platformie Azure na potrzeby publikowania zdarzeń do Event Grid tematów lub domen. Użyj klucza sygnatury dostępu współdzielonego (SAS) lub tokenu do uwierzytelniania klientów, którzy publikują zdarzenia. Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie klientów publikowanych](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Typy operacji
Aby zapoznać się z listą operacji obsługiwanych przez Azure Event Grid, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Następujące operacje zwracają potencjalnie tajne informacje, które są filtrowane z normalnych operacji odczytu. Zaleca się ograniczenie dostępu do tych operacji. 

* Microsoft. EventGrid/eventSubscriptions/getFullUrl/akcja
* Microsoft. EventGrid/tematy/listKeys/akcja
* Microsoft. EventGrid/tematy/regenerateKey/akcja


## <a name="built-in-roles"></a>Wbudowane role

Event Grid udostępnia dwie wbudowane role do zarządzania subskrypcjami zdarzeń. Są one ważne podczas implementowania [domen zdarzeń](event-domains.md) , ponieważ zapewniają użytkownikom uprawnienia wymagane do subskrybowania tematów w domenie zdarzeń. Role te są skoncentrowane na subskrypcjach zdarzeń i nie udzielają dostępu do akcji, takich jak tworzenie tematów.

[Te role można przypisać do użytkownika lub grupy](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Współautor EventGrid EventSubscription**: Zarządzanie operacjami subskrypcji Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/systemtopics/eventsubscriptions/*",
          "Microsoft.EventGrid/partnertopics/eventsubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: Odczytuj Event Grid subskrypcje

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Role niestandardowe

Jeśli musisz określić uprawnienia, które są inne niż wbudowane role, możesz utworzyć role niestandardowe.

Poniżej przedstawiono Event Grid przykładowe definicje ról, które umożliwiają użytkownikom podejmowanie różnych akcji. Te role niestandardowe różnią się od wbudowanych ról, ponieważ udzielają szerszego dostępu niż tylko subskrypcje zdarzeń.

**EventGridReadOnlyRole.js**: Zezwalaj tylko na operacje tylko do odczytu.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.js**: Zezwalaj na ograniczone akcje post, ale nie Zezwalaj na akcje usuwania.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.js**: zezwala na wszystkie akcje związane z siatką zdarzeń.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Role niestandardowe można tworzyć przy użyciu [programu PowerShell](../role-based-access-control/custom-roles-powershell.md), [interfejsu wiersza polecenia platformy Azure i usługi](../role-based-access-control/custom-roles-cli.md) [rest](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Szyfrowanie danych magazynowanych

Wszystkie zdarzenia lub dane zapisywane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, dzięki czemu są szyfrowane w stanie spoczynku. Ponadto maksymalny okres czasu, przez jaki zdarzenia lub dane są przechowywane, wynosi 24 godziny zgodnie z [zasadami ponowienia Event Grid](delivery-and-retry.md). Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas wygaśnięcia zdarzenia na żywo, w zależności od tego, która wartość jest mniejsza.

## <a name="permissions-for-event-subscriptions"></a>Uprawnienia dla subskrypcji zdarzeń
Jeśli używasz procedury obsługi zdarzeń, która nie jest elementem webhook (takim jak centrum zdarzeń lub magazyn kolejki), musisz mieć dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia wysyłanie zdarzeń do zasobu przez nieautoryzowanego użytkownika.

Użytkownik musi mieć uprawnienie **Microsoft. EventGrid/EventSubscriptions/Write** do zasobu, który jest źródłem zdarzenia. To uprawnienie jest wymagane, ponieważ piszesz nową subskrypcję w zakresie zasobu. Wymagany zasób różni się w zależności od tego, czy subskrybujesz temat systemowy czy temat niestandardowy. Oba typy zostały opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy systemowe (wydawcy usług platformy Azure)
W przypadku tematów systemowych, jeśli nie jesteś właścicielem lub współautorem zasobu źródłowego, musisz mieć uprawnienia do napisania nowej subskrypcji zdarzeń w zakresie zasobu, który publikuje zdarzenie. Format zasobu: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład w celu subskrybowania zdarzenia na koncie magazynu o nazwie Moja **ACCT** wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dotyczące: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe
W przypadku niestandardowych tematów należy mieć uprawnienia do zapisywania nowej subskrypcji zdarzeń w zakresie tematu usługi Event Grid. Format zasobu: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład w celu subskrybowania niestandardowego tematu o nazwie Moja **tematu** wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dotyczące: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [Informacje o Event Grid](overview.md)

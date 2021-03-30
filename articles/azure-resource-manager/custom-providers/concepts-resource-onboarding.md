---
title: Dołączanie zasobów
description: Dowiedz się więcej na temat przeprowadzania dołączania zasobów przy użyciu niestandardowych dostawców platformy Azure w celu zastosowania zarządzania lub konfiguracji do innych typów zasobów platformy Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650411"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Omówienie dołączania zasobów niestandardowych dostawców platformy Azure

Dołączanie zasobów niestandardowych dostawców platformy Azure jest modelem rozszerzalności dla typów zasobów platformy Azure. Pozwala to na stosowanie operacji lub zarządzania w istniejących zasobach platformy Azure na dużą skalę. Aby uzyskać więcej informacji, zobacz [jak Dostawcy niestandardowi platformy Azure mogą poszerzać platformę Azure](overview.md). W tym artykule opisano:

- Jak można dołączać do zasobów.
- Podstawowe informacje na temat dołączania zasobów i sposoby ich używania.
- Gdzie można znaleźć przewodniki i przykłady kodu, aby rozpocząć pracę.

> [!IMPORTANT]
> Dostawcy niestandardowi są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Co można dołączyć do zasobów?

Podobnie jak niestandardowe [zasoby dostawcy platformy Azure](./custom-providers-resources-endpoint-how-to.md), dołączanie do zasobów definiuje kontrakt, który spowoduje, że serwer proxy wysyła żądania do punktu końcowego. W przeciwieństwie do zasobów niestandardowych, dołączanie do zasobów nie tworzy nowego typu zasobu. Zamiast tego umożliwia rozszerzenie istniejących typów zasobów. I dołączanie do zasobów współdziała z Azure Policy, więc zarządzanie i Konfigurowanie zasobów można przeprowadzić w odpowiedniej skali. Przykłady przepływów pracy związanych z dołączaniem zasobów:

- Instalowanie rozszerzeń maszyn wirtualnych i zarządzanie nimi.
- Przekazuj i Konfiguruj wartości domyślne na kontach usługi Azure Storage.
- Włącz ustawienia diagnostyki linii bazowej w odpowiedniej skali.

## <a name="resource-onboarding-basics"></a>Podstawy dołączania zasobów

Konfigurowanie dołączania zasobów za pośrednictwem dostawców niestandardowych platformy Azure odbywa się przy użyciu typów zasobów Microsoft. CustomProviders/resourceProviders i Microsoft. CustomProviders/Association. Aby włączyć dołączanie zasobów dla niestandardowego dostawcy, podczas procesu konfiguracji Utwórz element typu **ResourceType** o nazwie "Associations" z atrybutem **routingtype** zawierającym wartość "Extension". Microsoft. CustomProviders/Associations i Microsoft. CustomProviders/resourceProviders nie muszą należeć do tej samej grupy zasobów.

Oto przykład niestandardowego dostawcy platformy Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Właściwość | Wymagane? | Opis
---|---|---
name | Tak | Nazwa definicji punktu końcowego. W przypadku dołączania do zasobów nazwa musi mieć wartość "Associations".
routingtype | Tak | Określa typ kontraktu z punktem końcowym. W przypadku dołączania do zasobów prawidłowy **routingTypes** to "proxy, pamięć podręczna, rozszerzenie" i "webhook, cache, Extension".
endpoint | Tak | Punkt końcowy, do którego będą kierowane żądania. Będzie to obsługiwać odpowiedź i wszystkie efekty uboczne żądania.

Po utworzeniu niestandardowego dostawcy z typem zasobu skojarzenia można użyć elementu Microsoft. CustomProviders/Associations. Microsoft. CustomProviders/Associations jest zasobem rozszerzenia, który może rozszerzać każdy inny zasób platformy Azure. Po utworzeniu wystąpienia elementu Microsoft. CustomProviders/Association przyjmujemy Właściwość **element targetresourceid**, która powinna być PRAWIDŁOWYm identyfikatorem zasobu Microsoft. CustomProviders/ResourceProviders lub Microsoft. Solutions/Applications. W takich przypadkach żądanie zostanie przekazane do typu zasobu skojarzenia w utworzonym wystąpieniu Microsoft. CustomProviders/resourceProviders.

> [!NOTE]
> Jeśli identyfikator zasobu Microsoft. Solutions/Applications jest określony jako **element targetresourceid**, w zarządzanej grupie zasobów musi być wdrożony pakiet Microsoft. CustomProviders/resourceProviders, który ma nazwę "Public".

Przykładowe skojarzenie dostawców niestandardowych platformy Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Właściwość | Wymagane? | Opis
---|---|---
Element targetresourceid | Tak | Identyfikator zasobu Microsoft. CustomProviders/resourceProviders lub Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Jak korzystać z dołączania zasobów

Proces dołączania do zasobów działa przez rozszerzanie innych zasobów przy użyciu zasobu rozszerzenia Microsoft. CustomProviders/Associations. W poniższym przykładzie żądanie jest wykonywane dla maszyny wirtualnej, ale można rozszerzyć dowolny zasób.

Najpierw należy utworzyć zasób niestandardowego dostawcy z typem zasobu skojarzenia. Spowoduje to zadeklarowanie adresu URL wywołania zwrotnego, który będzie używany podczas tworzenia odpowiedniego zasobu Microsoft. CustomProviders/Association, który jest przeznaczony dla dostawcy niestandardowego.

Przykładowe żądanie Microsoft. CustomProviders/resourceProviders Create:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Po utworzeniu niestandardowego dostawcy można wskazać inne zasoby i zastosować efekty uboczne niestandardowego dostawcy.

Przykładowe żądanie utworzenia pakietu Microsoft. CustomProviders/Association:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

To żądanie zostanie następnie przekazane do punktu końcowego określonego w utworzonym dostawcy niestandardowym, do którego odwołuje się **element targetresourceid** w tej formie:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Punkt końcowy powinien odpowiedzieć na wartość Application/JSON `Content-Type` i prawidłową treść odpowiedzi JSON. Pola, które są zwracane w ramach obiektu **Właściwości** w formacie JSON, zostaną dodane do odpowiedzi zwrotnej skojarzenia.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące programowania niestandardowych dostawców zasobów platformy Azure, spróbuj zadawać je na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobnej pytanie mogły już zostać odebrane odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o dostawcach niestandardowych. Zobacz następujące artykuły, aby dowiedzieć się więcej:

- [Samouczek: dołączanie zasobów z dostawcami niestandardowymi](./tutorial-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)

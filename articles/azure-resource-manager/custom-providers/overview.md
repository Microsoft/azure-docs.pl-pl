---
title: Przegląd dostawców niestandardowych
description: Dowiedz się więcej o dostawcach zasobów niestandardowych platformy Azure i jak zwiększyć płaszczyznę interfejsu API platformy Azure, aby dopasować ją do przepływów pracy.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80398477"
---
# <a name="azure-custom-resource-providers-overview"></a>Niestandardowe dostawcy zasobów platformy Azure — omówienie

Dostawcy zasobów niestandardowych platformy Azure to platforma rozszerzalności na platformie Azure. Umożliwia zdefiniowanie niestandardowych interfejsów API, które mogą być używane do wzbogacania domyślnego środowiska platformy Azure. W tej dokumentacji opisano:

- Jak skompilować i wdrożyć niestandardowego dostawcę zasobów platformy Azure.
- Jak korzystać z niestandardowych dostawców zasobów platformy Azure w celu rozbudowania istniejących przepływów pracy.
- Gdzie można znaleźć przewodniki i przykłady kodu, aby rozpocząć pracę.

![Przegląd dostawcy niestandardowego](./media/overview/overview.png)

> [!IMPORTANT]
> Dostawcy niestandardowi są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Jakie mogą być niestandardowi dostawcy zasobów

Poniżej przedstawiono kilka przykładów, które można osiągnąć za pomocą niestandardowych dostawców zasobów platformy Azure:

- Rozszerzona Azure Resource Manager interfejs API REST w celu uwzględnienia usług wewnętrznych i zewnętrznych.
- Włącz scenariusze niestandardowe na podstawie istniejących przepływów pracy platformy Azure.
- Dostosuj formant i efekt Azure Resource Manager szablonów.

## <a name="what-is-a-custom-resource-provider"></a>Co to jest dostawca zasobów niestandardowych

Dostawcy zasobów niestandardowych platformy Azure są tworzone przez utworzenie kontraktu między platformą Azure a punktem końcowym. Ten kontrakt definiuje listę nowych zasobów i akcji za pomocą nowego zasobu, **Microsoft. CustomProviders/resourceProviders**. Dostawca zasobów niestandardowych będzie uwidaczniał te nowe interfejsy API na platformie Azure. Dostawcy zasobów niestandardowych platformy Azure składają się z trzech części: niestandardowego dostawcy zasobów, **punktów końcowych** i zasobów niestandardowych.

## <a name="how-to-build-custom-resource-providers"></a>Jak utworzyć niestandardowych dostawców zasobów

Dostawcy zasobów niestandardowych to lista kontraktów między platformą Azure i punktami końcowymi. W tym artykule opisano sposób współpracy platformy Azure z punktem końcowym. Dostawca zasobów działa jak serwer proxy i przekazuje żądania i odpowiedzi do i z określonego **punktu końcowego**. Dostawca zasobów może określić dwa typy kontraktów: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) i [**Actions**](./custom-providers-action-endpoint-how-to.md). Są one włączane za poorednictwem definicji punktów końcowych. Definicja punktu końcowego składa się z trzech pól: **name**, **routingtype** i **Endpoint**.

Przykładowy punkt końcowy:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Właściwość | Wymagane | Opis
---|---|---
name | *opcję* | Nazwa definicji punktu końcowego. Platforma Azure udostępni tę nazwę za pomocą interfejsu API w obszarze "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/"<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingtype | *znaleziono* | Określa typ kontraktu z **punktem końcowym**. Jeśli nie zostanie określony, domyślna wartość to "proxy".
endpoint | *opcję* | Punkt końcowy, do którego będą kierowane żądania. Będzie to obsługiwać odpowiedź oraz wszystkie efekty uboczne żądania.

### <a name="building-custom-resources"></a>Tworzenie zasobów niestandardowych

**ResourceTypes** opisz nowe zasoby niestandardowe, które są dodawane do platformy Azure. Uwidaczniają one podstawowe metody RESTful CRUD. Zobacz [więcej na temat tworzenia zasobów niestandardowych](./custom-providers-resources-endpoint-how-to.md)

Przykładowy dostawca zasobów niestandardowych z **resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Interfejsy API dodane do platformy Azure dla powyższego przykładu:

HttpMethod | Przykładowy identyfikator URI | Opis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 r-09 -01 — wersja zapoznawcza | Wywołanie interfejsu API REST platformy Azure w celu utworzenia nowego zasobu.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 r-09 -01 — wersja zapoznawcza | Wywołanie interfejsu API REST platformy Azure w celu usunięcia istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 r-09 -01 — wersja zapoznawcza | Wywołanie interfejsu API REST platformy Azure w celu pobrania istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018 r-09 -01 — wersja zapoznawcza | Wywołanie interfejsu API REST platformy Azure w celu pobrania listy istniejących zasobów.

### <a name="building-custom-actions"></a>Kompilowanie akcji niestandardowych

W obszarze **Akcje** opisano nowe akcje, które są dodawane do platformy Azure. Mogą one być widoczne u góry dostawcy zasobów lub zagnieżdżone w ramach typu **zasobu**. Zobacz [więcej na temat tworzenia akcji niestandardowych](./custom-providers-action-endpoint-how-to.md)

Przykładowy dostawca zasobów niestandardowych z **akcjami**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Interfejsy API dodane do platformy Azure dla powyższego przykładu:

HttpMethod | Przykładowy identyfikator URI | Opis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-Version = 2018 r-09 -01 — wersja zapoznawcza | Wywołanie interfejsu API REST platformy Azure w celu aktywowania akcji.

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące opracowywania niestandardowego dostawcy zasobów platformy Azure, spróbuj zadać [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o dostawcach niestandardowych. Przejdź do następnego artykułu, aby utworzyć niestandardowego dostawcę.

- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)

---
title: Tworzenie zasobu Cognitive Services przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługą Azure Cognitive Services, tworząc i subskrybując zasób przy użyciu interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: usługi poznawcze, analiza poznawcze, rozwiązania poznawcze, usługi AI
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 08ff2f416a00002cde5767111ba5a6824a721324
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868175"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu interfejsu Azure Command-Line Interface (CLI)

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pracę z usługą Azure Cognitive Services przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Usługa Azure Cognitive Services to usługi w chmurze z interfejsami API REST, a także dostępne zestawy SDK dla deweloperów, które ułatwiają deweloperom tworzenie analiz poznawczych w aplikacjach, bez konieczności bezpośredniego sztucznej analizy danych lub wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.

Cognitive Services są reprezentowane przez [zasoby](../azure-resource-manager/management/manage-resources-portal.md) platformy Azure utworzone w ramach subskrypcji platformy Azure. Po utworzeniu zasobu Użyj kluczy i punktu końcowego wygenerowanego do uwierzytelniania aplikacji.

W tym przewodniku szybki start dowiesz się, jak zarejestrować się w usłudze Azure Cognitive Services i utworzyć konto z subskrypcją pojedynczego lub wielousługowego za pomocą [interfejsu wiersza polecenia (CLI) platformy Azure](/cli/azure/install-azure-cli). Te usługi są reprezentowane przez [zasoby](../azure-resource-manager/management/manage-resources-portal.md)platformy Azure, które umożliwiają nawiązywanie połączenia z co najmniej jedną interfejsy API usług Cognitive Services platformy Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją](https://azure.microsoft.com/free/cognitive-services) bezpłatnie.
* [Interfejs wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalowanie interfejsu wiersza polecenia platformy Azure i logowanie

Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

Możesz również użyć zielonego przycisku **Wypróbuj** , aby uruchomić te polecenia w przeglądarce.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Utwórz nową grupę zasobów usługi Azure Cognitive Services

Przed utworzeniem zasobu Cognitive Services należy mieć grupę zasobów platformy Azure, która będzie zawierać zasób. W przypadku tworzenia nowego zasobu można utworzyć nową grupę zasobów lub użyć istniejącej grupy. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

### <a name="choose-your-resource-group-location"></a>Wybierz lokalizację grupy zasobów

Aby utworzyć zasób, potrzebna jest jedna z lokalizacji platformy Azure dostępnych dla Twojej subskrypcji. Listę dostępnych lokalizacji można pobrać za pomocą polecenia [AZ Account List-Locations](/cli/azure/account#az-account-list-locations) . Większość Cognitive Services można uzyskać z kilku lokalizacji. Wybierz najbliżej siebie lub Zobacz, które lokalizacje są dostępne dla usługi.

> [!IMPORTANT]
> * Zapamiętaj swoją lokalizację platformy Azure, ponieważ będzie ona potrzebna podczas wywoływania Cognitive Services platformy Azure.
> * Dostępność niektórych Cognitive Services może różnić się w zależności od regionu. Aby uzyskać więcej informacji, zobacz [produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po utworzeniu lokalizacji platformy Azure Utwórz nową grupę zasobów w interfejsie wiersza polecenia platformy Azure przy użyciu poleceń [AZ Group Create](/cli/azure/group#az-group-create) .

W poniższym przykładzie Zastąp lokalizację platformy Azure `westus2` jedną z lokalizacji platformy Azure dostępnych dla Twojej subskrypcji.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Wybieranie usługi poznawczej i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu.

### <a name="multi-service"></a>Wiele usług

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Wiele usług. Aby uzyskać więcej informacji, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


> [!NOTE]
> Wiele Cognitive Services poniżej ma bezpłatną warstwę, której można użyć do wypróbowania usługi. Aby skorzystać z warstwy Bezpłatna, użyj `F0` jako jednostki SKU dla zasobu.

### <a name="vision"></a>Obraz

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Przetwarzanie obrazów            | `ComputerVision`          |
| Custom Vision — przewidywanie | `CustomVision.Prediction` |
| Custom Vision — szkolenie   | `CustomVision.Training`   |
| Rozpoznawanie twarzy                       | `Face`                    |
| Rozpoznawanie formularzy            | `FormRecognizer`          |
| Rozpoznawanie pisma odręcznego             | `InkRecognizer`           |

### <a name="speech"></a>Mowa

| Usługa            | Rodzaj                 |
|--------------------|----------------------|
| Usługi mowy    | `SpeechServices`     |
| Rozpoznawanie mowy | `SpeakerRecognition` |

### <a name="language"></a>Język

| Usługa            | Rodzaj                |
|--------------------|---------------------|
| Zrozumienie formularza | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analiza tekstu     | `TextAnalytics`     |
| Tłumaczenie tekstu   | `TextTranslation`   |

### <a name="decision"></a>Decyzja

| Usługa           | Rodzaj               |
|-------------------|--------------------|
| Narzędzie do wykrywania anomalii  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizacja      | `Personalizer`     |

Listę dostępnych rodzajów usługi poznawczej można znaleźć za pomocą polecenia [AZ cognitiveservices Account List-Kinds](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Dodawanie nowego zasobu do grupy zasobów

Aby utworzyć i subskrybować nowy zasób Cognitive Services, użyj polecenia [AZ cognitiveservices Account Create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . To polecenie dodaje nowy zasób rozliczany do utworzonej wcześniej grupy zasobów. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure:

Można utworzyć zasób F0 (wolny) dla detektora anomalii o nazwie `anomaly-detector-resource` przy użyciu poniższego polecenia.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze dla zasobu

Aby zalogować się do lokalnej instalacji interfejsu Command-Line (CLI), użyj polecenia [AZ login](/cli/azure/reference-index#az-login) .

```azurecli-interactive
az login
```

Użyj polecenia [AZ cognitiveservices Account Keys list](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) , aby uzyskać klucze dla zasobu usługi poznawczej.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Warstwy cenowe i rozliczenia

Warstwy cenowe (oraz opłata naliczana) są zależne od liczby wysyłanych transakcji przy użyciu informacji o uwierzytelnianiu. Każda warstwa cenowa określa:
* Maksymalna liczba dozwolonych transakcji na sekundę (TPS).
* funkcje usługi są włączone w ramach warstwy cenowej.
* Koszt wstępnie zdefiniowanej liczby transakcji. Przekroczenie tej kwoty spowoduje dodatkową opłatą określoną w [szczegółach cennika](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) usługi.

## <a name="get-current-quota-usage-for-your-resource"></a>Pobierz bieżące użycie przydziału dla zasobu

Użyj polecenia [AZ cognitiveservices Account List-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) , aby uzyskać użycie zasobu usługi poznawczej.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć zasób Cognitive Services, można go usunąć lub grupy zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów znajdujących się w grupie.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, użyj polecenia AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zobacz też

* Zobacz temat **[uwierzytelnianie żądań na platformie Azure Cognitive Services](authentication.md)** , jak bezpiecznie współpracować z Cognitive Services.
* Zobacz, **[co to jest usługa Azure Cognitive Services?](./what-are-cognitive-services.md)** , aby uzyskać listę różnych kategorii w programie Cognitive Services.
* Zobacz **[obsługę języka naturalnego](language-support.md)** , aby wyświetlić listę języków naturalnych obsługiwanych przez Cognitive Services.
* Zobacz **[używanie Cognitive Services jako kontenerów,](cognitive-services-container-support.md)** aby zrozumieć, jak używać Cognitive Services w Premium.
* Zobacz **[Planowanie i zarządzanie kosztami Cognitive Services](plan-manage-costs.md)** , aby oszacować koszt korzystania z Cognitive Services.

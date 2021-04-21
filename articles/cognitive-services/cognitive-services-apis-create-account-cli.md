---
title: Tworzenie zasobu Cognitive Services przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Cognitive Services
description: Rozpoczynanie pracy z Azure Cognitive Services przez utworzenie zasobu i subskrybowanie go przy użyciu interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789145"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Szybki start: tworzenie zasobu Cognitive Services pomocą interfejsu wiersza polecenia usługi Azure Command-Line

Skorzystaj z tego przewodnika Szybki start, aby rozpocząć pracę z Azure Cognitive Services interfejsem wiersza polecenia [platformy Azure.](/cli/azure/install-azure-cli)

Azure Cognitive Services to usługi bazowe w chmurze z interfejsami API REST i zestawy SDK biblioteki klienta, które ułatwiają deweloperom tworzenie analiz poznawczych w aplikacjach bez konieczności posiadania bezpośrednich umiejętności ani wiedzy z zakresu sztucznej inteligencji, nauki o danych. Azure Cognitive Services umożliwia deweloperom łatwe dodawanie funkcji poznawczych do swoich aplikacji za pomocą rozwiązań poznawczych, które widzą, słyszą, mówią, rozumieją, a nawet zaczynają rozumować.

Cognitive Services są reprezentowane przez zasoby [platformy](../azure-resource-manager/management/manage-resources-portal.md) Azure, które tworzysz w ramach subskrypcji platformy Azure. Po utworzeniu zasobu użyj kluczy i punktu końcowego wygenerowanych do uwierzytelniania aplikacji.

W tym przewodniku Szybki start dowiesz się, jak zarejestrować się w usłudze Azure Cognitive Services i utworzyć konto z subskrypcją z jedną usługą lub subskrypcją wielu usług przy użyciu interfejsu wiersza polecenia [(CLI)](/cli/azure/install-azure-cli)platformy Azure. Te usługi są reprezentowane przez zasoby [platformy](../azure-resource-manager/management/manage-resources-portal.md)Azure, które umożliwiają nawiązywanie połączenia z co najmniej jednym z Azure Cognitive Services API.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure [— utwórz subskrypcję](https://azure.microsoft.com/free/cognitive-services) bezpłatnie.
* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalowanie interfejsu wiersza polecenia platformy Azure i logowanie

Zainstaluj interfejs [wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom [polecenie az login:](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Możesz również użyć zielonego **przycisku Wypróbuj,** aby uruchomić te polecenia w przeglądarce.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Tworzenie nowej Azure Cognitive Services zasobów

Przed utworzeniem Cognitive Services zasobów musisz mieć grupę zasobów platformy Azure, która będzie zawierać ten zasób. Podczas tworzenia nowego zasobu możesz utworzyć nową grupę zasobów lub użyć istniejącej. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

### <a name="choose-your-resource-group-location"></a>Wybierz lokalizację grupy zasobów

Aby utworzyć zasób, musisz mieć jedną z lokalizacji platformy Azure dostępnych dla Twojej subskrypcji. Listę dostępnych lokalizacji można pobrać za pomocą [polecenia az account list-locations.](/cli/azure/account#az_account_list_locations) Większość Cognitive Services jest dostępna z kilku lokalizacji. Wybierz lokalizację najbliżej Ciebie lub sprawdź, które lokalizacje są dostępne dla usługi.

> [!IMPORTANT]
> * Zapamiętaj lokalizację platformy Azure, ponieważ będzie ona potrzebna podczas wywoływania Azure Cognitive Services.
> * Dostępność niektórych zasobów Cognitive Services różnić się w zależności od regionu. Aby uzyskać więcej informacji, zobacz [Produkty platformy Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Po utworzeniu lokalizacji platformy Azure utwórz nową grupę zasobów w interfejsie wiersza polecenia platformy Azure przy użyciu [polecenia az group create.](/cli/azure/group#az_group_create)

W poniższym przykładzie zastąp lokalizację platformy Azure jedną z lokalizacji `westus2` platformy Azure dostępnych dla Twojej subskrypcji.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Wybieranie usługi Cognitive Service i warstwy cenowej

Podczas tworzenia nowego zasobu musisz znać "rodzaj" usługi, której chcesz użyć, wraz z potrzebną warstwą cenową [(lub](https://azure.microsoft.com/pricing/details/cognitive-services/) sku). Te i inne informacje zostaną podane jako parametry podczas tworzenia zasobu.

### <a name="multi-service"></a>Wiele usług

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Wiele usług. Aby uzyskać [więcej informacji,](https://azure.microsoft.com/pricing/details/cognitive-services/) zobacz stronę cennika.            | `CognitiveServices`     |


> [!NOTE]
> Wiele z poniższych Cognitive Services w warstwie Bezpłatna, za pomocą których można wypróbować usługę. Aby korzystać z warstwy Bezpłatna, `F0` użyj jako sku zasobu.

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
| Omówienie formularzy | `FormUnderstanding` |
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

Listę dostępnych "rodzajów" usług Cognitive Service można znaleźć za pomocą polecenia [az cognitiveservices account list-kinds:](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Dodawanie nowego zasobu do grupy zasobów

Aby utworzyć i zasubskrybować nowy zasób usługi Cognitive Services, użyj [polecenia az cognitiveservices account create.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) To polecenie dodaje nowy zasób rozliczany do utworzonej wcześniej grupy zasobów. Podczas tworzenia nowego zasobu musisz znać "rodzaj" usługi, której chcesz użyć, wraz z warstwą cenową (lub sku) i lokalizacją platformy Azure:

Możesz utworzyć zasób F0 (bezpłatny) dla Narzędzie do wykrywania anomalii o nazwie `anomaly-detector-resource` za pomocą poniższego polecenia.

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

## <a name="get-the-keys-for-your-resource"></a>Uzyskiwanie kluczy dla zasobu

Aby zalogować się do lokalnej instalacji interfejsu Command-Line (CLI), użyj [polecenia az login.](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Użyj polecenia [az cognitiveservices account keys list,](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) aby uzyskać klucze dla zasobu usługi Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Warstwy cenowe i rozliczenia

Warstwy cenowe (i kwota naliczanych opłat) są oparte na liczbie transakcji, które wysyłasz przy użyciu informacji o uwierzytelnianiu. Każda warstwa cenowa określa:
* maksymalna liczba dozwolonych transakcji na sekundę (TPS).
* funkcje usługi włączone w warstwie cenowej.
* Koszt wstępnie zdefiniowanej liczby transakcji. Przesuniesz tę kwotę na dodatkową opłatę określoną w [szczegółach cennika](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) usługi.

## <a name="get-current-quota-usage-for-your-resource"></a>Uzyskiwanie bieżącego użycia limitu przydziału dla zasobu

Użyj polecenia [az cognitiveservices account list-usage,](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) aby pobrać użycie zasobu usługi Cognitive Service.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć zasób Cognitive Services, możesz go usunąć lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych zasobów zawartych w grupie.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, użyj polecenia az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zobacz też

* Zobacz **[Uwierzytelnianie żądań Azure Cognitive Services,](authentication.md)** aby dowiedzieć się, jak bezpiecznie pracować z Cognitive Services.
* Zobacz **[co to Azure Cognitive Services?,](./what-are-cognitive-services.md)** aby uzyskać listę różnych kategorii w Cognitive Services.
* Zobacz **[Obsługa języka naturalnego,](language-support.md)** aby wyświetlić listę języków naturalnych, które Cognitive Services obsługuje.
* Zobacz **[Używanie Cognitive Services jako kontenerów,](cognitive-services-container-support.md)** aby dowiedzieć się, Cognitive Services używać Cognitive Services jako kontenerów.
* Aby **[oszacować koszty korzystania](plan-manage-costs.md)** z Cognitive Services Cognitive Services, zobacz Planowanie kosztów dla Cognitive Services zarządzania nimi.

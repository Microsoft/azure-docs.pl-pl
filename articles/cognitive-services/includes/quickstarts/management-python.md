---
title: 'Szybki Start: Biblioteka kliencka zarządzania platformy Azure dla języka Python'
description: W tym przewodniku Szybki Start zacznij korzystać z biblioteki klienta zarządzania platformy Azure dla języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607786"
---
[Dokumentacja](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

Aby aplikacja mogła korzystać z Twojego konta platformy Azure, do zarządzania uprawnieniami potrzebna jest jednostka usługi platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Podczas tworzenia nazwy głównej usługi zobaczysz, że będzie ona mieć wartość klucza tajnego, identyfikator i identyfikator aplikacji. Zapisz identyfikator aplikacji i klucz tajny w tymczasowej lokalizacji dla dalszych kroków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobu Cognitive Services konto musi mieć grupę zasobów platformy Azure, która będzie zawierać zasób. Jeśli nie masz jeszcze grupy zasobów, utwórz ją w [Azure Portal](https://ms.portal.azure.com/).

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python przy użyciu preferowanego edytora lub środowiska IDE i przejdź do projektu w oknie konsoli.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Bibliotekę kliencką można zainstalować za pomocą programu:

```console
pip install azure-mgmt-cognitiveservices
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

### <a name="import-libraries"></a>Importowanie bibliotek

Otwórz skrypt w języku Python i zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj następujące pola do katalogu głównego skryptu i wypełnij ich wartości przy użyciu utworzonej jednostki usługi i informacji o koncie platformy Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Następnie Dodaj następujący kod, aby utworzyć obiekt **CognitiveServicesManagementClient** . Ten obiekt jest wymagany dla wszystkich operacji zarządzania platformy Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Poniższa funkcja zawiera listę dostępnych rodzajów usługi poznawczej.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Zobacz listę jednostek SKU i informacje o cenach poniżej. 

#### <a name="multi-service"></a>Wiele usług

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Wiele usług. Aby uzyskać więcej informacji, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Obraz

| Usługa                    | Rodzaj                      |
|----------------------------|---------------------------|
| Przetwarzanie obrazów            | `ComputerVision`          |
| Custom Vision — przewidywanie | `CustomVision.Prediction` |
| Custom Vision — szkolenie   | `CustomVision.Training`   |
| Rozpoznawanie twarzy                       | `Face`                    |
| Rozpoznawanie formularzy            | `FormRecognizer`          |
| Rozpoznawanie pisma odręcznego             | `InkRecognizer`           |

#### <a name="search"></a>Wyszukaj

| Usługa            | Rodzaj                  |
|--------------------|-----------------------|
| Automatyczne sugerowanie Bing   | `Bing.Autosuggest.v7` |
| Wyszukiwanie niestandardowe Bing | `Bing.CustomSearch`   |
| Wyszukiwanie jednostek Bing | `Bing.EntitySearch`   |
| Wyszukiwanie Bing        | `Bing.Search.v7`      |
| Sprawdzanie pisowni Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Mowa

| Usługa            | Rodzaj                 |
|--------------------|----------------------|
| Usługi mowy    | `SpeechServices`     |
| Rozpoznawanie mowy | `SpeakerRecognition` |

#### <a name="language"></a>Język

| Usługa            | Rodzaj                |
|--------------------|---------------------|
| Zrozumienie formularza | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analiza tekstu     | `TextAnalytics`     |
| Tłumaczenie tekstu   | `TextTranslation`   |

#### <a name="decision"></a>Decyzja

| Usługa           | Rodzaj               |
|-------------------|--------------------|
| Narzędzie do wykrywania anomalii  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizacja      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Warstwy cenowe i rozliczenia

Warstwy cenowe (oraz opłata naliczana) są zależne od liczby wysyłanych transakcji przy użyciu informacji o uwierzytelnianiu. Każda warstwa cenowa określa:
* Maksymalna liczba dozwolonych transakcji na sekundę (TPS).
* funkcje usługi są włączone w ramach warstwy cenowej.
* Koszt dla wstępnie zdefiniowanej liczby transakcji. Przekroczenie tej liczby spowoduje dodatkową opłatą określoną w [szczegółach cennika](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) usługi.

> [!NOTE]
> Wiele Cognitive Services ma bezpłatną warstwę, której można użyć do wypróbowania usługi. Aby skorzystać z warstwy Bezpłatna, użyj `F0` jako jednostki SKU dla zasobu.

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

Aby utworzyć nowy zasób Cognitive Services i subskrybować go, użyj funkcji **Create** . Ta funkcja dodaje nowy zasób rozliczany do grupy zasobów, która została przekazana. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure. Następująca funkcja przyjmuje wszystkie te argumenty i tworzy zasób.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>Wyświetlanie zasobów

Aby wyświetlić wszystkie zasoby na koncie platformy Azure (we wszystkich grupach zasobów), użyj następującej funkcji:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Usuwanie zasobu

Poniższa funkcja usuwa określony zasób z danej grupy zasobów.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Funkcje zarządzania wywołaniami

Dodaj następujący kod na dole skryptu, aby wywołać powyższe funkcje. Ten kod zawiera listę dostępnych zasobów, powoduje utworzenie przykładowego zasobu, wyświetlenie zasobów należących do użytkownika, a następnie usunięcie przykładowego zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z wiersza polecenia za pomocą `python` polecenia.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja zestawu SDK zarządzania platformy Azure](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Co to jest platforma Azure Cognitive Services?](../../Welcome.md)
* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](../../authentication.md)
* [Tworzenie nowego zasobu przy użyciu witryny Azure Portal](../../cognitive-services-apis-create-account.md)
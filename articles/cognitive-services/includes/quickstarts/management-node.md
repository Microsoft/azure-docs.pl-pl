---
title: 'Szybki Start: Biblioteka kliencka usługi Azure Management dla Node.js'
description: W tym przewodniku szybki start Rozpocznij pracę z biblioteką klienta zarządzania systemu Azure dla Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607787"
---
[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [Node.js](https://nodejs.org/)

## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

Aby aplikacja mogła korzystać z Twojego konta platformy Azure, do zarządzania uprawnieniami potrzebna jest jednostka usługi platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Podczas tworzenia nazwy głównej usługi zobaczysz, że będzie ona mieć wartość klucza tajnego, identyfikator i identyfikator aplikacji. Zapisz identyfikator aplikacji i klucz tajny w tymczasowej lokalizacji dla dalszych kroków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobu Cognitive Services konto musi mieć grupę zasobów platformy Azure, która będzie zawierać zasób. Jeśli nie masz jeszcze grupy zasobów, utwórz ją w [Azure Portal](https://ms.portal.azure.com/).

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```

Utwórz plik o nazwie _index.js_ przed kontynuowaniem.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj następujące pakiety NPM:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

### <a name="import-libraries"></a>Importowanie bibliotek

Otwórz skrypt _index.js_ i zaimportuj następujące biblioteki.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj następujące pola do katalogu głównego skryptu i wypełnij ich wartości przy użyciu utworzonej jednostki usługi i informacji o koncie platformy Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Następnie Dodaj następującą funkcję, `quickstart` Aby obsłużyć główną służbową wersję programu. Pierwszy blok kodu konstruuje obiekt **CognitiveServicesManagementClient** przy użyciu zmiennych poświadczeń wprowadzonych powyżej. Ten obiekt jest wymagany dla wszystkich operacji zarządzania platformy Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Funkcje zarządzania wywołaniami

Dodaj następujący kod na końcu `quickstart` funkcji, aby wyświetlić listę dostępnych zasobów, utworzyć przykładowy zasób, wyświetlić zasoby należące do użytkownika, a następnie usunąć przykładowy zasób. Te funkcje zostaną zdefiniowane w następnych krokach.

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Poniższa funkcja zawiera listę dostępnych rodzajów usługi poznawczej.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

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

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Wyświetlanie zasobów

Aby wyświetlić wszystkie zasoby na koncie platformy Azure (we wszystkich grupach zasobów), użyj następującej funkcji:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Usuwanie zasobu

Poniższa funkcja usuwa określony zasób z danej grupy zasobów.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Dodaj następujący kod na dole skryptu, aby wywołać główną `quickstart` funkcję z obsługą błędów.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Następnie w oknie konsoli Uruchom aplikację za pomocą `node` polecenia.

```console
node index.js
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja zestawu SDK zarządzania platformy Azure](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Co to jest platforma Azure Cognitive Services?](../../Welcome.md)
* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](../../authentication.md)
* [Tworzenie nowego zasobu przy użyciu witryny Azure Portal](../../cognitive-services-apis-create-account.md)
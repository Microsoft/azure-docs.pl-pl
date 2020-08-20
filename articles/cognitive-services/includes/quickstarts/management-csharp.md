---
title: 'Szybki Start: Biblioteka kliencka zarządzania platformą Azure dla platformy .NET'
description: W tym przewodniku Szybki Start zacznij korzystać z biblioteki klienta zarządzania Azure dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607782"
---
[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

Aby aplikacja mogła korzystać z Twojego konta platformy Azure, do zarządzania uprawnieniami potrzebna jest jednostka usługi platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Podczas tworzenia nazwy głównej usługi zobaczysz, że będzie ona mieć wartość klucza tajnego, identyfikator i identyfikator aplikacji. Zapisz identyfikator aplikacji i klucz tajny w tymczasowej lokalizacji dla dalszych kroków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobu Cognitive Services konto musi mieć grupę zasobów platformy Azure, która będzie zawierać zasób. Jeśli nie masz jeszcze grupy zasobów, utwórz ją w [Azure Portal](https://ms.portal.azure.com/) przed kontynuowaniem.

## <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

Utwórz nową aplikację platformy .NET Core. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `azure-management-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę kliencką klienta zarządzania Azure dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

### <a name="import-libraries"></a>Importowanie bibliotek

Otwórz *program.cs* i Dodaj następujące `using` instrukcje na początku pliku:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj następujące pola do katalogu głównego *program.cs* i wypełnij ich wartości przy użyciu utworzonej jednostki usługi i informacji o koncie platformy Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Następnie w metodzie **Main** Użyj tych wartości, aby utworzyć obiekt **CognitiveServicesManagementClient** . Ten obiekt jest wymagany dla wszystkich operacji zarządzania platformy Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Metody zarządzania wywołaniami

Dodaj następujący kod do metody **Main** , aby wyświetlić listę dostępnych zasobów, utworzyć przykładowy zasób, wyświetlić zasoby należące do użytkownika, a następnie usunąć przykładowy zasób. Te metody zostaną zdefiniowane w następnych krokach.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Listę dostępnych rodzajów usługi poznawczej można znaleźć, wywołując następującą metodę w skrypcie:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

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

Aby utworzyć nowy zasób Cognitive Services i zasubskrybować go, użyj metody **Create** . Ta metoda dodaje nowy zasób rozliczany do grupy zasobów, która została przekazana. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure. Poniższa metoda przyjmuje wszystkie te wartości jako argumenty i tworzy zasób.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Wyświetlanie zasobów

Aby wyświetlić wszystkie zasoby na koncie platformy Azure (we wszystkich grupach zasobów), użyj następującej metody:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Usuwanie zasobu

Poniższa metoda usuwa określony zasób z danej grupy zasobów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja zestawu SDK zarządzania platformy Azure](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Co to jest platforma Azure Cognitive Services?](../../Welcome.md)
* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](../../authentication.md)
* [Tworzenie nowego zasobu przy użyciu witryny Azure Portal](../../cognitive-services-apis-create-account.md)
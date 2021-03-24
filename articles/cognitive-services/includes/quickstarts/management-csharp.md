---
title: 'Szybki Start: Biblioteka kliencka zarządzania platformą Azure dla platformy .NET'
description: W tym przewodniku Szybki Start zacznij korzystać z biblioteki klienta zarządzania Azure dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 1ecfd58e961e70182bfc2e0cc8eaf77f5053eb77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879712"
---
[Dokumentacja](/dotnet/api/overview/azure/cognitiveservices/management)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>Wymagania wstępne języka C#

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

Utwórz nową aplikację platformy .NET Core. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `azure-management-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program. cs*. 

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

Otwórz plik *program. cs* i Dodaj następujące `using` instrukcje na początku pliku:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj następujące pola do katalogu głównego *programu program. cs* i wypełnij ich wartości przy użyciu utworzonej jednostki usługi i informacji o koncie platformy Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Następnie w metodzie **Main** Użyj tych wartości, aby utworzyć obiekt **CognitiveServicesManagementClient** . Ten obiekt jest wymagany dla wszystkich operacji zarządzania platformy Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Metody zarządzania wywołaniami

Dodaj następujący kod do metody **Main** , aby wyświetlić listę dostępnych zasobów, utworzyć przykładowy zasób, wyświetlić zasoby należące do użytkownika, a następnie usunąć przykładowy zasób. Te metody zostaną zdefiniowane w następnych krokach.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Tworzenie zasobu Cognitive Services (C#)

Aby utworzyć nowy zasób Cognitive Services i zasubskrybować go, użyj metody **Create** . Ta metoda dodaje nowy zasób rozliczany do grupy zasobów, która została przekazana. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure. Poniższa metoda przyjmuje wszystkie te wartości jako argumenty i tworzy zasób.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Listę dostępnych rodzajów usługi poznawczej można znaleźć, wywołując następującą metodę w skrypcie:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

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

* Zobacz temat **[uwierzytelnianie żądań na platformie Azure Cognitive Services](../../authentication.md)** , jak bezpiecznie współpracować z Cognitive Services.
* Zobacz, **[co to jest usługa Azure Cognitive Services?](../../what-are-cognitive-services.md)** , aby uzyskać listę różnych kategorii w programie Cognitive Services.
* Zobacz **[obsługę języka naturalnego](../../language-support.md)** , aby wyświetlić listę języków naturalnych obsługiwanych przez Cognitive Services.
* Zobacz **[używanie Cognitive Services jako kontenerów,](../../cognitive-services-container-support.md)** aby zrozumieć, jak używać Cognitive Services w Premium.
* Zobacz **[Planowanie i zarządzanie kosztami Cognitive Services](../../plan-manage-costs.md)** , aby oszacować koszt korzystania z Cognitive Services.
* Więcej informacji na temat zestawu SDK zarządzania znajdziesz w dokumentacji dotyczącej **[zestawu SDK zarządzania Cognitive Services](/dotnet/api/overview/azure/cognitiveservices/management)** .

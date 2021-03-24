---
title: 'Szybki Start: Biblioteka kliencka usługi Azure Management dla Node.js'
description: W tym przewodniku szybki start Rozpocznij pracę z biblioteką klienta zarządzania systemu Azure dla Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 41f6c8e260968eacd04249b3f887d4865907df0d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879672"
---
[Dokumentacja](/javascript/api/@azure/arm-cognitiveservices/)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>Wymagania wstępne języka JavaScript

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

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

## <a name="create-a-cognitive-services-resource-nodejs"></a>Tworzenie zasobu Cognitive Services (Node.js)

Aby utworzyć nowy zasób Cognitive Services i subskrybować go, użyj funkcji **Create** . Ta funkcja dodaje nowy zasób rozliczany do grupy zasobów, która została przekazana. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure. Następująca funkcja przyjmuje wszystkie te argumenty i tworzy zasób.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Poniższa funkcja zawiera listę dostępnych rodzajów usługi poznawczej.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

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

* Zobacz temat **[uwierzytelnianie żądań na platformie Azure Cognitive Services](../../authentication.md)** , jak bezpiecznie współpracować z Cognitive Services.
* Zobacz, **[co to jest usługa Azure Cognitive Services?](../../what-are-cognitive-services.md)** , aby uzyskać listę różnych kategorii w programie Cognitive Services.
* Zobacz **[obsługę języka naturalnego](../../language-support.md)** , aby wyświetlić listę języków naturalnych obsługiwanych przez Cognitive Services.
* Zobacz **[używanie Cognitive Services jako kontenerów,](../../cognitive-services-container-support.md)** aby zrozumieć, jak używać Cognitive Services w Premium.
* Zobacz **[Planowanie i zarządzanie kosztami Cognitive Services](../../plan-manage-costs.md)** , aby oszacować koszt korzystania z Cognitive Services.
* Więcej informacji na temat zestawu SDK zarządzania można znaleźć w dokumentacji dotyczącej **[zestawu SDK zarządzania Azure](/javascript/api/@azure/arm-cognitiveservices/)** .

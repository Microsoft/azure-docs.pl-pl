---
title: 'Szybki Start: Biblioteka kliencka zarządzania platformą Azure dla języka Java'
description: W tym przewodniku Szybki Start zacznij korzystać z biblioteki klienta zarządzania platformy Azure dla języka Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 4c0d4dd1a834e42a75da5199b7aaed0e123f8e63
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879685"
---
[Dokumentacja](/java/api/com.microsoft.azure.management.cognitiveservices)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18)  |  źródłowy biblioteki [Pakiet (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Wymagania wstępne dotyczące języka Java

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS* , który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Bibliotekę i informacje o kliencie można znaleźć dla innych menedżerów zależności w [repozytorium centralnym Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

W pliku *Build. Gradle. KTS* projektu Dołącz bibliotekę klienta jako `implementation` instrukcję wraz z wymaganymi wtyczkami i ustawieniami.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Importowanie bibliotek

Przejdź do nowego folderu **src/Main/Java** i Utwórz plik o nazwie *Management. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj klasę w *Management. Java*, a następnie Dodaj następujące pola i ich wartości. Wypełnij wartości przy użyciu utworzonej jednostki usługi i innych informacji o koncie platformy Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Następnie w metodzie **Main** Użyj tych wartości, aby utworzyć obiekt **CognitiveServicesManager** . Ten obiekt jest wymagany dla wszystkich operacji zarządzania platformy Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Metody zarządzania wywołaniami

Dodaj następujący kod do metody **Main** , aby wyświetlić listę dostępnych zasobów, utworzyć przykładowy zasób, wyświetlić zasoby należące do użytkownika, a następnie usunąć przykładowy zasób. Te metody zostaną zdefiniowane w następnych krokach.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Tworzenie zasobu Cognitive Services (Java)

Aby utworzyć nowy zasób Cognitive Services i zasubskrybować go, użyj metody **Create** . Ta metoda dodaje nowy zasób rozliczany do grupy zasobów, która została przekazana. Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z jej warstwą cenową (lub jednostką SKU) i lokalizacją platformy Azure. Poniższa metoda przyjmuje wszystkie te wartości jako argumenty i tworzy zasób.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Wybieranie usługi i warstwy cenowej

Podczas tworzenia nowego zasobu należy znać "rodzaj" usługi, która ma być używana, wraz z żądaną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/) (lub jednostką SKU). Te i inne informacje są używane jako parametry podczas tworzenia zasobu. Listę dostępnych rodzajów usługi poznawczej można znaleźć, wywołując następującą metodę:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Wyświetlanie zasobów

Aby wyświetlić wszystkie zasoby na koncie platformy Azure (we wszystkich grupach zasobów), użyj następującej metody:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Usuwanie zasobu

Poniższa metoda usuwa określony zasób z danej grupy zasobów.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Zobacz też

* Zobacz temat **[uwierzytelnianie żądań na platformie Azure Cognitive Services](../../authentication.md)** , jak bezpiecznie współpracować z Cognitive Services.
* Zobacz, **[co to jest usługa Azure Cognitive Services?](../../what-are-cognitive-services.md)** , aby uzyskać listę różnych kategorii w programie Cognitive Services.
* Zobacz **[obsługę języka naturalnego](../../language-support.md)** , aby wyświetlić listę języków naturalnych obsługiwanych przez Cognitive Services.
* Zobacz **[używanie Cognitive Services jako kontenerów,](../../cognitive-services-container-support.md)** aby zrozumieć, jak używać Cognitive Services w Premium.
* Zobacz **[Planowanie i zarządzanie kosztami Cognitive Services](../../plan-manage-costs.md)** , aby oszacować koszt korzystania z Cognitive Services.
* Więcej informacji na temat zestawu SDK zarządzania można znaleźć w dokumentacji dotyczącej **[zestawu SDK zarządzania Azure](/java/api/com.microsoft.azure.management.cognitiveservices)** .

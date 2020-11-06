---
title: Łączenie funkcji języka Java z usługą Azure Storage
description: Dowiedz się, jak połączyć funkcję Java wyzwalaną przez protokół HTTP z usługą Azure Storage przy użyciu powiązania wyjściowego magazynu kolejki.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422658"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Łączenie funkcji języka Java z usługą Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](./create-first-function-cli-java.md?tabs=bash,browser) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, użyj konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Szybki Start dotyczącej języka Java](./create-first-function-cli-java.md?tabs=bash,browser).

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz możesz dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Teraz możesz przystąpić do wypróbowania lokalnego nowego powiązania danych wyjściowych.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak wcześniej, użyj następującego polecenia, aby skompilować projekt i uruchomić środowisko uruchomieniowe funkcji lokalnie:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Ponieważ włączono zbiory rozszerzeń w host.jsna, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#add-to-your-functions-app) zostało pobrane i zainstalowane podczas uruchamiania, wraz z innymi rozszerzeniami powiązań firmy Microsoft.

Jak wcześniej, wyzwól funkcję z wiersza polecenia przy użyciu Zwinięciea w nowym oknie terminalu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tym razem powiązanie danych wyjściowych tworzy również kolejkę o nazwie `outqueue` na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Możesz również wyświetlić kolejkę za pomocą [Eksplorator usługi Microsoft Azure Storage][Azure Storage Explorer] lub [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Wdróż ponownie projekt 

Aby zaktualizować opublikowaną aplikację, ponownie uruchom następujące polecenie:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Ponownie można użyć Zwinięciea do przetestowania wdrożonej funkcji. Tak jak wcześniej, przekaż wartość `AzureFunctions` w treści żądania post na adres URL, jak w poniższym przykładzie:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Możesz [sprawdzić ponownie komunikat kolejki magazynu](#query-the-storage-queue) , aby sprawdzić, czy powiązanie danych wyjściowych generuje nowy komunikat w kolejce zgodnie z oczekiwaniami.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia Azure Functions przy użyciu języka Java, zobacz [przewodnik dewelopera Azure Functions Java](functions-reference-java.md) oraz [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md). Aby zapoznać się z przykładami kompletnych projektów funkcji w języku Java, zobacz [przykłady funkcji języka Java](/samples/browse/?products=azure-functions&languages=Java). 

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/

---
title: Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio
description: Dowiedz się, jak dodać powiązanie danych wyjściowych, aby połączyć funkcje biblioteki klas języka C# z kolejką usługi Azure Storage przy użyciu programu Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 0711516143839dbcdbafec56ab1d9643b4b8cdc7
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167125"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak za pomocą programu Visual Studio połączyć funkcję utworzoną w [poprzednim artykule szybki start] z usługą Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure queue storage. 

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić sobie korzystanie z konta magazynu utworzonego za pomocą aplikacji funkcji programu. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu należy: 

 - Ukończ [część 1 przewodnika Szybki Start dotyczącego programu Visual Studio](./functions-create-your-first-function-visual-studio.md). 

- Zaloguj się do subskrypcji platformy Azure z poziomu programu Visual Studio.

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](functions-create-first-function-vs-code.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do *local.settings.js* pliku. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. 

1. W obszarze **Akcje**wybierz pozycję **Edytuj ustawienia Azure App Service**. 

    ![Edytuj ustawienia aplikacji](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. W obszarze **AzureWebJobsStorage**skopiuj wartość ciągu **zdalnego** do **lokalnego**, a następnie wybierz przycisk **OK**. 

Powiązanie magazynu, które używa `AzureWebJobsStorage` Ustawienia dla połączenia, może teraz połączyć się z magazynem kolejki podczas lokalnego uruchamiania.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ze względu na to, że jest używane powiązanie danych wyjściowych magazynu kolejek, przed uruchomieniem projektu należy zainstalować rozszerzenie powiązania magazynu. Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. 

1. W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet**  >  **konsola Menedżera pakietów**. 

1. W konsoli programu uruchom następujące polecenie [instalacji pakietu](/nuget/tools/ps-ref-install-package) , aby zainstalować rozszerzenia magazynu:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nowa kolejka o nazwie `outqueue` zostanie utworzona na koncie magazynu przez środowisko uruchomieniowe funkcji, gdy zostanie użyte powiązanie danych wyjściowych. Użyjesz Eksploratora chmury do sprawdzenia, czy kolejka została utworzona wraz z nową wiadomością.

## <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W programie Visual Studio z menu **Widok** wybierz opcję **Eksplorator chmury**.

1. W **Eksploratorze chmury**rozwiń swoje konta subskrypcji i **magazynu**platformy Azure, a następnie rozwiń konto magazynu używane przez funkcję. Jeśli nie pamiętasz nazwy konta magazynu, sprawdź `AzureWebJobsStorage` ustawienie parametrów połączenia w *local.settings.jsna* pliku.  

1. Rozwiń węzeł **kolejki** , a następnie kliknij dwukrotnie kolejkę o nazwie **dequeueing** , aby wyświetlić zawartość kolejki w programie Visual Studio. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat w kolejce wyświetlany w Eksplorator usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom ponownie funkcję, Wyślij kolejne żądanie, a w kolejce zobaczysz nowy komunikat.  

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Wdróż ponownie i sprawdź zaktualizowaną aplikację

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Publikuj**, a następnie wybierz polecenie **Publikuj** , aby ponownie opublikować projekt na platformie Azure.

1. Po zakończeniu wdrażania możesz użyć przeglądarki, aby przetestować ponownie wdrożoną funkcję. Tak jak wcześniej, dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

1. Ponownie [Wyświetl komunikat w kolejce magazynu](#examine-the-output-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania funkcji, zobacz [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[Poprzedni artykuł szybkiego startu]: functions-create-your-first-function-visual-studio.md

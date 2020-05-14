---
title: 'Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i opublikować wyzwalacz HTTP platformy Azure przy użyciu programu Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: a4549bd2947332d7140f4f440a5344f417430554
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122752"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio

Azure Functions umożliwia uruchamianie kodu w środowisku bezserwerowym bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji sieci Web.

W tym przewodniku szybki start dowiesz się, jak używać programu Visual Studio 2019 do lokalnego tworzenia i testowania aplikacji funkcji wyzwalającej języka C# "Hello World", która następnie jest publikowana na platformie Azure. 

![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Ten przewodnik Szybki Start został zaprojektowany z założenia dla programu Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, najpierw zainstaluj [program Visual Studio 2019](https://azure.microsoft.com/downloads/). Upewnij się, że podczas instalacji wybrano obciążenie **Programowanie na platformie Azure** . Jeśli chcesz utworzyć projekt Azure Functions przy użyciu programu Visual Studio 2017 zamiast tego, musisz najpierw zainstalować [najnowsze Azure Functions narzędzia](functions-develop-vs.md#check-your-tools-version).

![Instalowanie programu Visual Studio przy użyciu obciążeń programistycznych platformy Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/dotnet/).

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Program Visual Studio tworzy projekt i klasę, która zawiera kod standardowy dla typu funkcji wyzwalacza HTTP. `FunctionName`Atrybut metody ustawia nazwę funkcji, która domyślnie jest `Function1` . Ten `HttpTrigger` atrybut określa, że funkcja jest wyzwalana przez żądanie HTTP. Standardowy kod wysyła odpowiedź HTTP zawierającą wartość z treści żądania lub ciągu zapytania.

Rozwiń możliwości funkcji za pomocą powiązań wejściowych i wyjściowych przez zastosowanie odpowiednich atrybutów do metody. Aby uzyskać więcej informacji, zobacz sekcję [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Wyzwalacze i powiązania) [dokumentacji usługi Azure Functions dla deweloperów w C#](functions-dotnet-class-library.md).

Teraz, po utworzeniu projektu funkcji i funkcji wyzwalacza HTTP, można je przetestować na komputerze lokalnym.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Program Visual Studio integruje się z Azure Functions Core Tools, dzięki czemu można testować funkcje lokalnie przy użyciu pełnego środowiska uruchomieniowego Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Przed opublikowaniem projektu musisz mieć aplikację funkcji w ramach subskrypcji platformy Azure. Publikacja programu Visual Studio tworzy aplikację funkcji przy pierwszym publikowaniu projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj podstawowy adres URL aplikacji funkcji ze strony profil **publikowania** . Zastąp `localhost:port` część adresu URL użytego do lokalnego przetestowania funkcji przy użyciu nowego podstawowego adresu URL. Dołącz ciąg zapytania `?name=<YOUR_NAME>` do tego adresu URL i uruchom żądanie.

    Adres URL, który wywołuje funkcję wyzwalacza HTTP, ma następujący format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję:

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto programu Visual Studio do tworzenia i publikowania aplikacji funkcji C# na platformie Azure z prostą funkcją wyzwalacza HTTP. 

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać powiązanie kolejki usługi Azure Storage do funkcji:
> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs.md)


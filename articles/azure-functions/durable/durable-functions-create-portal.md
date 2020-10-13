---
title: Tworzenie Durable Functions przy użyciu Azure Portal
description: Dowiedz się, jak zainstalować rozszerzenie Durable Functions dla Azure Functions do tworzenia aplikacji w portalu.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081920"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Tworzenie Durable Functions przy użyciu Azure Portal

Rozszerzenie [Durable Functions](durable-functions-overview.md) dla Azure Functions jest dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). To rozszerzenie musi być zainstalowane w aplikacji funkcji. W tym artykule przedstawiono sposób instalowania tego pakietu, dzięki któremu można opracowywać trwałe funkcje w Azure Portal.

> [!NOTE]
> 
> * W przypadku tworzenia trwałych funkcji w języku C# należy wziąć pod uwagę [Programowanie programu Visual Studio 2019](durable-functions-create-first-csharp.md).
> * W przypadku tworzenia trwałych funkcji w języku JavaScript należy zamiast tego rozważyć [tworzenie Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aby hostować wykonywanie dowolnej funkcji, musisz mieć aplikację funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów. Możesz utworzyć aplikację platformy .NET lub JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Domyślnie utworzona aplikacja funkcji używa wersji 2. x środowiska uruchomieniowego Azure Functions. Rozszerzenie Durable Functions działa zarówno w wersjach 1. x i 2. x środowiska uruchomieniowego Azure Functions w języku C#, jak i w wersji 2. x w języku JavaScript. Jednak szablony są dostępne tylko w przypadku, gdy celem jest wersja 2. x środowiska uruchomieniowego, niezależnie od wybranego języka.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalowanie pakietu npm o trwałych funkcjach (tylko kod JavaScript)

W przypadku tworzenia Durable Functions JavaScript należy zainstalować [ `durable-functions` pakiet npm](https://www.npmjs.com/package/durable-functions):

1. Na stronie aplikacji funkcji wybierz pozycję **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** w okienku po lewej stronie.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funkcje platformy funkcji wybierz kudu":::

2. Na stronie **Narzędzia zaawansowane** wybierz pozycję **Przejdź**.

3. W konsoli kudu wybierz pozycję **konsola debugowania**, a następnie polecenie **cmd**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funkcje platformy funkcji wybierz kudu"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funkcje platformy funkcji wybierz kudu":::

4. Po `package.json` przekazaniu należy uruchomić `npm install` polecenie z konsoli wykonywania zdalnego kudu.

   ![Kudu Uruchom instalację npm](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Tworzenie funkcji programu Orchestrator

1. W aplikacji funkcji wybierz pozycję **funkcje** w okienku po lewej stronie, a następnie wybierz pozycję **Dodaj** z górnego menu. 

1. W polu wyszukiwania na stronie **Nowa funkcja** wprowadź `durable` , a następnie wybierz szablon **Durable Functions http Starter** .

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Funkcje platformy funkcji wybierz kudu":::

1. Dla **nowej nazwy funkcji** wprowadź wartość `HttpStart` , a następnie wybierz pozycję **Utwórz funkcję**.

   Utworzona funkcja jest używana do uruchomienia aranżacji.

1. Utwórz kolejną funkcję w aplikacji funkcji, tym razem używając szablonu **Durable Functions Orchestrator** . Nazwij nową funkcję aranżacji `HelloSequence` .

1. Utwórz trzecią funkcję o nazwie przy `Hello` użyciu szablonu **działania Durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Testowanie aranżacji funkcji trwałych

1. Wróć do funkcji **HttpStart** , wybierz pozycję **Pobierz adres URL funkcji**i wybierz ikonę **Kopiuj do schowka** , aby skopiować adres URL. Ten adres URL jest używany do uruchamiania funkcji **HelloSequence** .

1. Użyj narzędzia HTTP, takiego jak Poster lub zwinięcie, aby wysłać żądanie POST do skopiowanego adresu URL. Poniższy przykład jest poleceniem zwinięcie, które wysyła żądanie POST do funkcji trwałej:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    W tym przykładzie `{your-function-app-name}` jest to domena, która jest nazwą aplikacji funkcji. Komunikat odpowiedzi zawiera zestaw punktów końcowych identyfikatora URI, które umożliwiają monitorowanie wykonywania i zarządzanie nim, co wygląda podobnie jak w poniższym przykładzie:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Wywołaj `statusQueryGetUri` Identyfikator URI punktu końcowego i zobaczysz bieżący stan funkcji trwałej, która może wyglądać podobnie do tego przykładu:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Kontynuuj wywoływanie `statusQueryGetUri` punktu końcowego do momentu zmiany stanu na **zakończone**i zobaczysz odpowiedź podobną do poniższego przykładu:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Twoja pierwsza trwała funkcja jest teraz uruchomiona na platformie Azure.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)

---
title: 'Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i opublikować wyzwalacz HTTP platformy Azure przy użyciu programu Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701419"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio

W tym artykule opisano użycie programu Visual Studio do utworzenia funkcji opartej na bibliotece klasy C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.  

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, najpierw zainstaluj [program Visual Studio 2019](https://azure.microsoft.com/downloads/). Upewnij się, że podczas instalacji wybrano obciążenie **Programowanie na platformie Azure** . Jeśli chcesz utworzyć projekt Azure Functions przy użyciu programu Visual Studio 2017 zamiast tego, musisz najpierw zainstalować [najnowsze Azure Functions narzędzia](functions-develop-vs.md#check-your-tools-version).

![Instalowanie programu Visual Studio przy użyciu obciążeń programistycznych platformy Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/dotnet/) .

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Program Visual Studio tworzy projekt i klasę, która zawiera kod standardowy dla typu funkcji wyzwalacza HTTP. Standardowy kod wysyła odpowiedź HTTP zawierającą wartość z treści żądania lub ciągu zapytania. Ten `HttpTrigger` atrybut określa, że funkcja jest wyzwalana przez żądanie HTTP. 

## <a name="rename-the-function"></a>Zmień nazwę funkcji

`FunctionName`Atrybut metody ustawia nazwę funkcji, która domyślnie jest generowana jako `Function1` . Ponieważ narzędzia nie umożliwiają przesłaniania domyślnej nazwy funkcji podczas tworzenia projektu, poświęć minutę, aby utworzyć lepszą nazwę klasy, pliku i metadanych funkcji.

1. W **Eksploratorze plików** kliknij prawym przyciskiem myszy plik Function1.cs i zmień jego nazwę na `HttpExample.cs` .

1. W kodzie zmień nazwę klasy Function1 na "HttpExample".

1. W `HttpTrigger` metodzie o nazwie `Run` Zmień nazwę `FunctionName` atrybutu metody na `HttpExample` .

Po zmianie nazwy funkcji można ją przetestować na komputerze lokalnym.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Program Visual Studio integruje się z Azure Functions Core Tools, dzięki czemu można testować funkcje lokalnie przy użyciu pełnego środowiska uruchomieniowego Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Przed opublikowaniem projektu musisz mieć aplikację funkcji w ramach subskrypcji platformy Azure. Publikacja programu Visual Studio tworzy aplikację funkcji przy pierwszym publikowaniu projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. W programie Cloud Explorer należy wybrać nową aplikację funkcji. Jeśli nie, rozwiń **App Services**> subskrypcji i wybierz nową aplikację funkcji.

1. Kliknij prawym przyciskiem myszy aplikację funkcji i wybierz polecenie **Otwórz w przeglądarce**. Spowoduje to otwarcie katalogu głównego aplikacji funkcji w domyślnej przeglądarce sieci Web i wyświetlenie strony wskazującej, że aplikacja funkcji jest uruchomiona. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Uruchomiona aplikacja funkcji":::

1. Na pasku adresu w przeglądarce Dołącz ciąg `/api/HttpExample?name=Functions` do podstawowego adresu URL i uruchom żądanie.

    Adres URL, który wywołuje funkcję wyzwalacza HTTP, ma następujący format:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Przejdź do tego adresu URL i zobaczysz odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję, która wygląda podobnie do poniższego przykładu:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Odpowiedź funkcji wyświetlona w przeglądarce":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz współpracować z kolejnymi przewodnikami Szybki Start, samouczkami lub z dowolnymi usługami utworzonymi w ramach tego przewodnika Szybki Start, nie czyść zasobów.

*Zasoby* na platformie Azure dotyczą aplikacji funkcji, funkcji, kont magazynu i tak dalej. Są one pogrupowane w *grupy zasobów*, a wszystkie elementy w grupie można usunąć, usuwając grupę. 

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

1. W Eksploratorze chmury rozwiń **App Services**> subskrypcji, kliknij prawym przyciskiem myszy aplikację funkcji, a następnie wybierz polecenie **Otwórz w portalu**. 

1. Na stronie aplikacja funkcji wybierz kartę **Przegląd** , a następnie wybierz link w obszarze **Grupa zasobów**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Wybierz grupę zasobów do usunięcia ze strony aplikacji funkcji":::

2. Na stronie **Grupa zasobów** zapoznaj się z listą uwzględnionych zasobów i sprawdź, czy są one tymi, które chcesz usunąć.
 
3. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

   Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto programu Visual Studio do tworzenia i publikowania aplikacji funkcji C# na platformie Azure z prostą funkcją wyzwalacza HTTP. 

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać powiązanie kolejki usługi Azure Storage do funkcji:
> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs.md)


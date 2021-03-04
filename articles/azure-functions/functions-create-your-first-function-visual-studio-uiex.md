---
title: 'Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i opublikować wyzwalacz HTTP platformy Azure przy użyciu programu Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050124"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Szybki Start: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio

W tym artykule opisano użycie programu Visual Studio do utworzenia funkcji opartej na bibliotece klasy C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go na <abbr title="Środowisko obliczeniowe środowiska uruchomieniowego, w którym wszystkie szczegóły serwera są niewidoczne dla deweloperów aplikacji, co upraszcza proces wdrażania kodu i zarządzania nim.">praca bezserwerowa</abbr> Środowisko programu <abbr title="Usługa platformy Azure, która zapewnia nieekonomiczne środowisko obliczeniowe dla aplikacji.">Azure Functions</abbr>.

Po ukończeniu tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">Konto platformy Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

+ Utwórz platformę Azure <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">account</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Zainstaluj [program Visual Studio 2019](https://azure.microsoft.com/downloads/) i wybierz obciążenie **Programowanie na platformie Azure** podczas instalacji. 

![Instalowanie programu Visual Studio przy użyciu obciążeń programistycznych platformy Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>Zamiast tego użyj projektu Azure Functions</strong></summary>
Jeśli chcesz utworzyć <abbr title="Kontener logiczny dla jednej lub kilku funkcji, które mogą być wdrażane i zarządzane wspólnie.">Projekt Azure Functions</abbr> przy użyciu programu Visual Studio 2017 zamiast tego należy najpierw zainstalować [najnowsze narzędzia Azure Functions](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. Utwórz projekt aplikacji funkcji

1. Z menu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt** wprowadź *funkcje* w polu wyszukiwania, wybierz szablon **Azure Functions** , a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** wprowadź **<abbr title=" nazwę aplikacji funkcji, która musi być prawidłowa jako przestrzeń nazw języka C#, więc nie używaj podkreśleń, łączników ani żadnych innych znaków niealfanumerycznych. "> </abbr>Nazwa projektu** dla projektu, a następnie wybierz pozycję **Utwórz**. 

1. Podaj następujące informacje dotyczące **tworzenia nowych Azure Functions ustawień aplikacji** :

    + Wybierz **<abbr title=" tę wartość tworzy projekt funkcji, który używa środowiska uruchomieniowego w wersji 3. x Azure Functions, który obsługuje program .NET Core 3. x. Azure Functions 1. x obsługuje .NET Framework. "> Azure Functions v3 (.NET Core) </abbr>** z listy rozwijanej środowisko uruchomieniowe funkcji. (Aby uzyskać więcej informacji, zobacz [Azure Functions Omówienie wersji środowiska uruchomieniowego](functions-versions.md)).
    
    + Wybierz **<abbr title=" tę wartość, aby utworzyć funkcję wyzwalaną przez żądanie HTTP. "> Wyzwalacz </abbr> http** jako szablon funkcji.
    
    + Wybierz opcję **<abbr title=" , ponieważ funkcja platformy Azure wymaga konta magazynu, po opublikowaniu projektu na platformie Azure jest on przypisywany lub tworzony. Wyzwalacz HTTP nie korzysta z parametrów połączenia konta usługi Azure Storage; wszystkie inne typy wyzwalaczy wymagają prawidłowych parametrów połączenia z kontem usługi Azure Storage. "> Emulator </abbr> magazynu** z listy rozwijanej konta magazynu.
        
    + Wybierz pozycję **anonimowe** z <abbr title="Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji.">Poziom autoryzacji</abbr> Przyjmij. (Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz temat [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys) i [powiązania protokołu HTTP i elementu webhook](functions-bindings-http-webhook.md)).

    + Wybierz pozycję **Utwórz**
        
## <a name="3-rename-the-function"></a>3. Zmień nazwę funkcji

`FunctionName`Atrybut metody ustawia nazwę funkcji, która domyślnie jest generowana jako `Function1` . Ponieważ narzędzia nie umożliwiają przesłaniania domyślnej nazwy funkcji podczas tworzenia projektu, poświęć minutę, aby utworzyć lepszą nazwę klasy, pliku i metadanych funkcji.

1. W **Eksploratorze plików** kliknij prawym przyciskiem myszy plik Function1.cs i zmień jego nazwę na *HttpExample.cs*.

1. W kodzie zmień nazwę klasy Function1 na "HttpExample".

1. W `HttpTrigger` metodzie o nazwie `Run` Zmień nazwę `FunctionName` atrybutu metody na `HttpExample` .


## <a name="4-run-the-function-locally"></a>4. Uruchom funkcję lokalnie

1. Aby uruchomić funkcję, naciśnij klawisz <kbd>F5</kbd> w programie Visual Studio.

1. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Lokalne środowisko uruchomieniowe platformy Azure](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Dołącz ciąg zapytania **? name =<YOUR_NAME>** do tego adresu URL i uruchom żądanie. 

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Aby zatrzymać debugowanie, naciśnij klawisz <kbd>SHIFT</kbd> + <kbd>F5</kbd> w programie Visual Studio.

<br/>
<details>
<summary><strong>Rozwiązywanie problemów</strong></summary>
 Może być konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP. Poziomy autoryzacji nigdy nie są wymuszane w przypadku lokalnego uruchamiania funkcji.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. Opublikuj projekt na platformie Azure

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W obszarze **cel** wybierz pozycję **Azure**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Wybierz element docelowy platformy Azure":::

1. W obszarze **określony element docelowy** wybierz pozycję **Azure aplikacja funkcji (system Windows)**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Wybierz aplikacja funkcji platformy Azure":::

1. W obszarze **wystąpienie funkcji** wybierz pozycję **Utwórz nową funkcję platformy Azure...** , a następnie użyj wartości określonych w następujących elementach:

    + Dla **nazwy** Podaj <abbr title="Użyj nazwy, która jednoznacznie identyfikuje nową aplikację funkcji. Zaakceptuj tę nazwę lub wprowadź nową nazwę. Prawidłowe znaki to: `a-z` , `0-9` , i `-` ..">Nazwa unikatowa w skali globalnej</abbr>
    
    + **Wybierz** subskrypcję z listy rozwijanej.
    
    + **Wybierz** istniejący <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr> z listy rozwijanej lub wybierz pozycję **Nowy** , aby utworzyć nową grupę zasobów.
    
    + **Wybierz** <abbr title="Po opublikowaniu projektu w aplikacji funkcji, która jest uruchamiana w planie zużycia, płacisz tylko za wykonywanie aplikacji funkcji. Inne plany hostingu wiążą się z wyższymi kosztami.">Zużycie</abbr> na liście rozwijanej Typ odtwarzania. (Aby uzyskać więcej informacji, zobacz [Plan zużycia](consumption-plan.md)).
    
    + **Wybierz**  <abbr title="Odniesienia geograficzne do określonego centrum danych platformy Azure, w którym są przydzielono zasoby. Listę dostępnych regionów można znaleźć w [regionach](https://azure.microsoft.com/regions/) .">location</abbr> z listy rozwijanej.
    
    + **Wybierz** <abbr = "konto usługi Azure Storage jest wymagane przez środowisko uruchomieniowe funkcji. Wybierz pozycję Nowy, aby skonfigurować konto magazynu ogólnego przeznaczenia. Możesz również wybrać istniejące konto spełniające wymagania dotyczące konta magazynu ". >usługi Azure Storage</abbr> konto z listy rozwijanej

    ![Okno dialogowe Tworzenie usługi App Service](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Wybierz pozycję **Utwórz** 

1. W **wystąpieniu funkcji** upewnij się, że jest zaznaczone pole wyboru **Uruchom z pliku pakietu** . 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Zakończ tworzenie profilu":::

    <br/>
    <details>
    <summary><strong>Co to jest ustawienie?</strong></summary>
    Przy użyciu polecenia **Uruchom z pliku pakietu** aplikacja funkcji jest wdrażana przy użyciu narzędzia [zip Deploy](functions-deployment-technologies.md#zip-deploy) z włączonym trybem [uruchamiania z pakietu](run-functions-from-deployment-package.md) . Jest to zalecana metoda wdrażania dla projektu usługi Functions, ponieważ powoduje to lepszą wydajność.    
    </details>   

1. Wybierz pozycję **Zakończ**.

1. Na stronie Publikowanie wybierz pozycję **Publikuj**.

1. Na stronie publikowanie Przejrzyj główny adres URL aplikacji funkcji.

1. Na karcie publikowanie wybierz pozycję **Zarządzaj w programie <abbr title=" Cloud Explorer, aby użyć programu Visual Studio do wyświetlania zawartości witryny, uruchamiania i zatrzymywania aplikacji funkcji oraz przeglądania bezpośrednio do zasobów aplikacji na platformie Azure i w Azure Portal. "> Eksplorator </abbr> chmury**.
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Komunikat o powodzeniu publikowania":::
    

## <a name="6-test-your-function-in-azure"></a>6. Przetestuj swoją funkcję na platformie Azure

1. W programie Cloud Explorer należy wybrać nową aplikację funkcji. Jeśli nie, rozwiń swoją subskrypcję, rozwiń węzeł **App Services** i wybierz nową aplikację funkcji.

1. Kliknij prawym przyciskiem myszy aplikację funkcji i wybierz polecenie **Otwórz w przeglądarce**. Spowoduje to otwarcie katalogu głównego aplikacji funkcji w domyślnej przeglądarce sieci Web i wyświetlenie strony wskazującej, że aplikacja funkcji jest uruchomiona. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Uruchomiona aplikacja funkcji":::

1. Na pasku adresu w przeglądarce Dołącz ciąg **/API/HttpExample? Name = Functions** do podstawowego adresu URL i uruchom żądanie.

    Adres URL, który wywołuje funkcję wyzwalacza HTTP, ma następujący format:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Przejdź do tego adresu URL i zobaczysz odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję, która wygląda podobnie do poniższego przykładu:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Odpowiedź funkcji wyświetlona w przeglądarce":::

## <a name="7-clean-up-resources"></a>7. Wyczyść zasoby

Usuń aplikację funkcji i jej zasoby, aby uniknąć ponoszenia dalszych kosztów.

1. W programie Cloud Explorer Rozwiń swoją subskrypcję, rozwiń węzeł **App Services**, kliknij prawym przyciskiem myszy aplikację funkcji, a następnie wybierz polecenie **Otwórz w portalu**. 

1. Na stronie aplikacja funkcji wybierz kartę **Przegląd** , a następnie wybierz link w obszarze **Grupa zasobów**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Wybierz grupę zasobów do usunięcia ze strony aplikacji funkcji":::

1. Na stronie **Grupa zasobów** zapoznaj się z listą uwzględnionych zasobów i sprawdź, czy są one tymi, które chcesz usunąć.
 
1. Wybierz pozycję **Usuń grupę zasobów**, a następnie postępuj zgodnie z instrukcjami.

    Usuwanie może potrwać kilka minut. Po jego zakończeniu przez kilka sekund będzie widoczne powiadomienie. Możesz również wybrać ikonę dzwonka w górnej części strony, aby wyświetlić powiadomienie.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać <abbr title="Oznacza to, że funkcja jest skojarzona z kolejką magazynu, dzięki czemu może tworzyć komunikaty w kolejce. Powiązania są połączeniami deklaratywnymi między funkcją i innymi zasobami. Powiązanie danych wejściowych zawiera dane do funkcji; powiązanie danych wyjściowych udostępnia dane z funkcji do innych zasobów.">Powiązanie danych wyjściowych kolejki usługi Azure Storage</abbr> do funkcji:

> [!div class="nextstepaction"]
> [Dodawanie do funkcji powiązania kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-vs.md)

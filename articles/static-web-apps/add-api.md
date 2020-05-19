---
title: Dodawanie interfejsu API do Web Apps statycznej platformy Azure za pomocą Azure Functions
description: Rozpocznij pracę z Web Appsami statycznymi platformy Azure, dodając bezserwerowy interfejs API do statycznej aplikacji sieci Web przy użyciu Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598458"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Dodawanie interfejsu API do usługi Azure static Web Apps Preview przy użyciu Azure Functions

Bezserwerowe interfejsy API można dodać do Web Apps statycznej platformy Azure za pośrednictwem integracji z Azure Functions. W tym artykule pokazano, jak dodać i wdrożyć interfejs API w witrynie Web Apps statycznej platformy Azure.

Informacje o sposobach zabezpieczania tras interfejsu API można znaleźć w [przewodniku routingu](routes.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla Visual Studio Code
- Rozszerzenie [Visual Studio Code serwera na żywo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) .

## <a name="create-a-git-repository"></a>Tworzenie repozytorium git 

Poniższe kroki przedstawiają sposób tworzenia nowego repozytorium i klonowania plików na komputerze.

1. Przejdź do, aby https://github.com/staticwebdev/vanilla-basic/generate utworzyć nowe repozytorium.
1. W polu _Nazwa repozytorium_ wprowadź **My-Wanili-API**.
1. Kliknij pozycję **Utwórz repozytorium na podstawie szablonu**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Tworzenie nowego repozytorium z usługi Wanili — podstawowa":::

Po utworzeniu projektu możesz użyć Visual Studio Code, aby sklonować repozytorium git.

1. Naciśnij klawisz **F1** , aby otworzyć polecenie w palecie poleceń.
1. Wklej adres URL do monitu _git: Clone_ , a następnie naciśnij klawisz **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klonowanie projektu GitHub przy użyciu Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Klonowanie projektu GitHub przy użyciu Visual Studio Code":::


## <a name="create-your-local-project"></a>Utwórz projekt lokalny

W tej sekcji użyto Visual Studio Code do utworzenia projektu Azure Functions lokalnego. Później należy opublikować aplikację Functions na platformie Azure.

1. W projekcie _My-Wanili-API_ utwórz podfolder o nazwie **API**.

   > [!NOTE]
   > Możesz nadać temu folderowi dowolną nazwę. Ten przykład używa `api` . 

2. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń
3. Wpisz **Azure Functions: Utwórz nowy projekt...**
4. Naciśnij klawisz **Enter**
5. Wybierz **Przeglądaj**
6. Wybierz folder **interfejsu API** jako katalog obszaru roboczego projektu
7. Wybierz **pozycję Wybierz**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Utwórz nowy Azure Functions przy użyciu Visual Studio Code":::

8. Podaj następujące informacje na ekranie:

    - _Wybierz język dla projektu funkcji_: Wybierz **JavaScript**
    - _Wybierz szablon dla pierwszej funkcji projektu_: Wybierz **wyzwalacz http**
    - _Podaj nazwę funkcji_: wpisz **GetMessage**
    - _Poziom autoryzacji_: wybierz opcję **anonimowe**, co umożliwia wszystkim użytkownikom wywoływanie punktu końcowego funkcji.
        - Aby dowiedzieć się więcej na temat poziomów autoryzacji, zobacz [klucze autoryzacji](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z wyzwalaczem HTTP.
    - Pliki projektu lokalnego można wyświetlić w oknie Eksploratora Visual Studio Code.
    - Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Aplikacja powinna teraz mieć strukturę projektu podobną do tego przykładu.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Następnie zaktualizuj `GetMessage` funkcję w obszarze _API/GetMessage/index. js_ przy użyciu następującego kodu.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Zaktualizuj `GetMessage` konfigurację w obszarze `api/GetMessage/function.json` z użyciem następujących ustawień.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
W przypadku powyższych ustawień punkt końcowy interfejsu API to:

- Wyzwolono żądanie HTTP do funkcji
- Dostępne dla wszystkich żądań bez względu na stan uwierzytelniania
- Uwidocznione za pośrednictwem trasy _/API/Message_

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Uruchom funkcję, naciskając klawisz **F5** , aby uruchomić aplikację Functions, a dane wyjściowe podstawowych narzędzi są wyświetlane w panelu _terminalu_ .

2. Jeśli Azure Functions Core Tools nie jest już zainstalowana, wybierz opcję **Zainstaluj** w wierszu polecenia.

    Po zainstalowaniu podstawowych narzędzi aplikacja zostanie uruchomiona na panelu _terminalu_ . W ramach danych wyjściowych można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Utwórz nowy Azure Functions przy użyciu Visual Studio Code":::

3. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby wykonać `GET` żądanie.

   <http://localhost:7071/api/message>

   Zostanie zwrócona odpowiedź, która wygląda podobnie do następującej w przeglądarce:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Utwórz nowy Azure Functions przy użyciu Visual Studio Code":::

Po sprawdzeniu, czy funkcja działa prawidłowo, można wywołać interfejs API z poziomu aplikacji JavaScript.

### <a name="call-the-api-from-the-application"></a>Wywoływanie interfejsu API z aplikacji

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Aktualizowanie plików w celu uzyskania dostępu do interfejsu API

1. Następnie zaktualizuj zawartość pliku _index. html_ przy użyciu następującego kodu, aby pobrać tekst z funkcji API i wyświetlić go na ekranie:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   W przypadku podstawowych narzędzi z systemem Użyj rozszerzenia serwer Visual Studio Code na [żywo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) , aby obsłużyć plik _index. html_ i otworzyć go w przeglądarce. 

2. Naciśnij klawisz **F1** , a następnie wybierz pozycję **serwer aktywny: Otwórz za pomocą serwera na żywo**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Utwórz nowy Azure Functions przy użyciu Visual Studio Code":::

   > [!NOTE]
   > Do obsłużenia pliku można użyć innych serwerów HTTP lub proxy `index.html` . Uzyskanie dostępu do programu `index.html` z `file:///` nie będzie działało.

### <a name="commit-and-push-your-changes-to-github"></a>Zatwierdź i wypchnij zmiany do usługi GitHub

Za pomocą Visual Studio Code Zatwierdź i wypchnij zmiany do zdalnego repozytorium git.

1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń
1. Typ **git: zatwierdź wszystko**
1. Dodawanie komunikatu zatwierdzenia
1. Wpisz w **git: push**

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji sieci Web

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Tworzenie aplikacji statycznej na Azure Portal ekranie 1":::

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukaj **Web Apps statyczny**
1. Kliknij pozycję **statyczne Web Apps (wersja zapoznawcza)**
1. Kliknij przycisk **Utwórz**
1. Wybierz swoją _subskrypcję platformy Azure_
1. Wybierz lub Utwórz nową _grupę zasobów_
1. Nadaj aplikacji nazwę **My-wanilid-API**.
1. Wybierz _region_ znajdujący się najbliżej siebie
1. Wybierz **bezpłatną** _jednostkę SKU_
1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** i Uwierzytelnij się za pomocą usługi GitHub
1. Wybierz preferowaną _organizację_
1. Wybierz pozycję **My-wanilid-API** z listy rozwijanej _repozytorium_
1. Wybierz pozycję **główna** z listy rozwijanej _rozgałęzienie_
1. Kliknij przycisk **Dalej: kompiluj >** , aby edytować konfigurację kompilacji

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Tworzenie aplikacji statycznej na Azure Portal ekranie 2":::

Następnie Dodaj następujące szczegóły kompilacji.

1. Wprowadź wartość **./** dla _lokalizacji aplikacji_.

1. Wprowadź **interfejs API** w polu _Lokalizacja interfejsu API_ .

   Jest to nazwa folderu interfejsu API utworzonego w poprzednim kroku.
   
1. Wyczyść wartość domyślną z _lokalizacji artefaktu aplikacji_, pozostawiając pole puste.

1. Kliknij pozycję **Przegląd + utwórz**.

| Ustawienie | Opis             | Wymagane |
| -------- | ----------------------- |
|  Lokalizacja aplikacji | Lokalizacja kodu źródłowego aplikacji statycznej | Tak |
|  Lokalizacja interfejsu API | Lokalizacja zaplecza interfejsu API. Wskazuje folder główny projektu aplikacji Azure Functions | Nie |
|  Lokalizacja artefaktu aplikacji | Lokalizacja folderu danych wyjściowych kompilacji. Niektóre platformy języka JavaScript frontonu mają krok kompilacji, który umieszcza pliki produkcyjne w folderze. To ustawienie wskazuje folder danych wyjściowych kompilacji. | Nie |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Tworzenie aplikacji statycznej na Azure Portal ekranie 3":::

1. Kliknij przycisk **Utwórz**
1. Poczekaj na zakończenie wdrożenia (może to potrwać minutę)
1. Przejdź do`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Upewnij się, że kompilacja zakończyła się pomyślnie

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Przepływ pracy w usłudze GitHub":::

Wdrożony interfejs API będzie dostępny pod adresem `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Przepływ pracy w usłudze GitHub":::

Możesz również znaleźć adres URL aplikacji z Azure Portal:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Dostęp do statycznego adresu URL aplikacji z Azure Portal":::

Alternatywnie możesz bezpośrednio uzyskać dostęp do statycznej aplikacji sieci Web platformy Azure pod adresem `https://<STATIC_APP_NAME>.azurestaticapps.net` i sprawdzić wynik w przeglądarce.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz zachować tej aplikacji do dalszej pracy, możesz wykonać następujące czynności, aby usunąć statyczną aplikację sieci Web platformy Azure i powiązane z nią zasoby.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wyszukiwania wpisz **grupy zasobów**
1. Kliknij pozycję **grupy zasobów** 
1. Wybierz pozycję Moja **resourceName**
1. Na stronie Moja _zasobów_ upewnij się, że wymienione zasoby są tymi, które chcesz usunąć.
1. Wybierz pozycję **Usuń**
1. Wpisz w polu tekstowym element **Webresourcename**
1. Wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień aplikacji](./application-settings.md)

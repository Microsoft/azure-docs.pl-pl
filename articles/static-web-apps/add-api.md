---
title: Dodawanie interfejsu API do Web Apps statycznej platformy Azure za pomocą Azure Functions
description: Rozpocznij pracę z Web Appsami statycznymi platformy Azure, dodając bezserwerowy interfejs API do statycznej aplikacji sieci Web przy użyciu Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 33adaf33ba15b49b0a7e4d3e8f1b2a8c435a7966
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449039"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Dodawanie interfejsu API do usługi Azure static Web Apps Preview przy użyciu Azure Functions

Bezserwerowe interfejsy API można dodać do Web Apps statycznej platformy Azure za pośrednictwem integracji z Azure Functions. W tym artykule pokazano, jak dodać i wdrożyć interfejs API w witrynie Web Apps statycznej platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją.
  - Jeśli nie masz konta, możesz [utworzyć je bezpłatnie](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla Visual Studio Code
- Rozszerzenie [Visual Studio Code serwera na żywo](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) .
- [Node.js](https://nodejs.org/download/) uruchomić aplikację interfejsu API lokalnie

## <a name="create-a-git-repository"></a>Utworzenie repozytorium Git

Poniższe kroki przedstawiają sposób tworzenia nowego repozytorium i klonowania plików na komputerze.

1. Upewnij się, że zalogowano się do usługi GitHub, a następnie przejdź do witryny, [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) Aby utworzyć nowe repozytorium.
1. W polu _Nazwa repozytorium_ wprowadź **My-Wanili-API**.
1. Kliknij pozycję **Utwórz repozytorium na podstawie szablonu**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Tworzenie nowego repozytorium z usługi Wanili — podstawowa":::

Po utworzeniu projektu Skopiuj adres URL w przeglądarce w celu utworzenia nowego repozytorium. Ten adres URL jest używany w Visual Studio Code, aby sklonować repozytorium git.

1. Naciśnij klawisz **F1** , aby otworzyć polecenie w palecie poleceń.
1. Wklej adres URL do monitu _git: Clone_ , a następnie naciśnij klawisz **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klonowanie projektu GitHub przy użyciu Visual Studio Code":::

    Postępuj zgodnie z monitami, aby wybrać lokalizację repozytorium w celu sklonowania projektu.

## <a name="create-the-api"></a>Tworzenie interfejsu API

Następnie utworzysz projekt Azure Functions jako interfejs API aplikacji. 

1. W projekcie _My-Wanili-API_ utwórz podfolder o nazwie **API**.
1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń
1. Wpisz **Azure Functions: Utwórz nowy projekt...**
1. Naciśnij klawisz **Enter**
1. Wybierz **Przeglądaj**
1. Wybierz folder **interfejsu API** jako katalog obszaru roboczego projektu
1. Wybierz **pozycję Wybierz**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Zrzut ekranu przedstawia folder P i i przycisk Wybierz.":::

1. Podaj następujące informacje po wyświetleniu monitów:

    - _Wybierz język_: Wybierz **JavaScript**
    - _Wybierz szablon dla pierwszej funkcji projektu_: Wybierz **wyzwalacz http**
    - _Podaj nazwę funkcji_: wprowadź **GetMessage**
    - _Poziom autoryzacji_: wybierz opcję **anonimowe**, co umożliwia wszystkim użytkownikom wywoływanie punktu końcowego funkcji.
        - Aby dowiedzieć się więcej na temat poziomów autoryzacji, zobacz [klucze autoryzacji](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code generuje projekt Azure Functions z funkcją wyzwalaną przez protokół HTTP.

Aplikacja ma teraz strukturę projektu podobną do poniższego przykładu.

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

Następnie zmienisz `GetMessage` funkcję, aby zwracała komunikat do frontonu.

1. Zaktualizuj `GetMessage` funkcję w obszarze _API/GetMessage/index.js_ przy użyciu następującego kodu.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Zaktualizuj `GetMessage` konfigurację w obszarze `api/GetMessage/function.json` z użyciem następujących ustawień.

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

- Wyzwalane, gdy do funkcji zostanie wysłane żądanie HTTP
- Dostępne dla wszystkich żądań bez względu na stan uwierzytelniania
- Uwidocznione za pośrednictwem trasy _/API/Message_

## <a name="run-the-api-locally"></a>Uruchamianie interfejsu API lokalnie

Visual Studio Code integruje się z [Azure Functions Core Tools](../azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

> [!TIP]
> Przed kontynuowaniem upewnij się, że masz wszystkie zasoby wymienione w sekcji [wymagania wstępne](#prerequisites) .

1. Uruchom funkcję, naciskając klawisz **F5** , aby uruchomić aplikację Functions.

1. Jeśli Azure Functions Core Tools nie jest już zainstalowana, wybierz opcję **Zainstaluj** w wierszu polecenia.

    Podstawowe narzędzia przedstawiają dane wyjściowe z działającej aplikacji na panelu _terminalu_ . W ramach danych wyjściowych można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Zrzut ekranu przedstawia kartę TERMINALa, w której można zobaczyć U R L.":::

1. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby sprawdzić, czy interfejs API działa prawidłowo: `http://localhost:7071/api/message` .

   Odpowiedź w przeglądarce powinna wyglądać podobnie do poniższego przykładu:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt textzrzut ekranu wyświetla tekstową odpowiedź w przeglądarce.":::

1. Naciśnij klawisze **Shift + F5** , aby zatrzymać sesję debugowania.

### <a name="call-the-api-from-the-application"></a>Wywoływanie interfejsu API z aplikacji

W przypadku wdrożenia na platformie Azure żądania kierowane do interfejsu API są automatycznie przekierowywane do aplikacji funkcji dla żądań wysyłanych do `api` trasy. Pracując lokalnie, musisz skonfigurować ustawienia aplikacji do żądań proxy do lokalnego interfejsu API.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Aktualizowanie plików HTML w celu uzyskania dostępu do interfejsu API

1. Następnie zaktualizuj zawartość pliku _index.html_ przy użyciu następującego kodu, aby pobrać tekst z funkcji API i wyświetlić go na ekranie:

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
       <p>Loading content from the API: <b id="name">...</b></p>
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

1. Naciśnij klawisz **F5** , aby uruchomić projekt interfejsu API.

1. Naciśnij klawisz **F1** , a następnie wybierz pozycję **serwer aktywny: Otwórz za pomocą serwera na żywo**.

    Komunikat API powinien być teraz widoczny na stronie sieci Web.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Zrzut ekranu przedstawia komunikat P I w przeglądarce.":::

   > [!NOTE]
   > Do obsłużenia pliku można użyć innych serwerów HTTP lub proxy `index.html` . Uzyskanie dostępu do programu `index.html` z `file:///` nie będzie działało.

1. Naciśnij klawisze **Shift + F5** , aby zatrzymać projekt interfejsu API.

### <a name="commit-and-push-your-changes-to-github"></a>Zatwierdź i wypchnij zmiany do usługi GitHub

Za pomocą Visual Studio Code Zatwierdź i wypchnij zmiany do zdalnego repozytorium git.

1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń
1. Typ **git: zatwierdź wszystko**
1. Dodaj komunikat dotyczący zatwierdzenia i naciśnij klawisz **Enter**
1. Naciśnij klawisz **F1**
1. Wpisz w **git: push** i naciśnij klawisz **Enter**

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Utwórz zasób**
1. Wyszukiwanie **statycznej aplikacji sieci Web**
1. Kliknij pozycję **statyczna aplikacja internetowa (wersja zapoznawcza)**
1. Kliknij pozycję **Utwórz**

Następnie Dodaj ustawienia specyficzne dla aplikacji.

1. Wybierz swoją _subskrypcję platformy Azure_
1. Wybierz lub Utwórz nową _grupę zasobów_
1. Nadaj aplikacji nazwę **My-wanilid-API**.
1. Wybierz _region_ znajdujący się najbliżej siebie
1. Wybierz **bezpłatną** _jednostkę SKU_
1. Kliknij przycisk **Zaloguj się przy użyciu usługi GitHub** i uwierzytelnij się przy użyciu usługi GitHub
1. Wybierz preferowaną _organizację_
1. Wybierz pozycję **My-wanilid-API** z listy rozwijanej _repozytorium_
1. Wybierz pozycję **główna** z listy rozwijanej _rozgałęzienie_
1. Wybierz wybraną platformę dla konfiguracji kompilacji na liście rozwijanej _Ustawienia wstępne kompilacji_

 > Te pola będą odzwierciedlać domyślną strukturę projektu typu aplikacji. Zmień wartości, aby odpowiadały aplikacji.

Następnie Dodaj następujące szczegóły kompilacji.

1. Wprowadź **/** _lokalizację aplikacji_.
1. Wprowadź **interfejs API** w polu _Lokalizacja interfejsu API_ .
1. Wyczyść wartość domyślną z _lokalizacji artefaktu aplikacji_, pozostawiając pole puste.
1. Kliknij pozycję **Przejrzyj i utwórz**.
1. Kliknij przycisk **Utwórz**

    Po kliknięciu przycisku _Utwórz_ na platformie Azure są wykonywane dwie rzeczy. Najpierw zostaną utworzone podstawowe usługi w chmurze obsługujące aplikację. Następnie proces w tle rozpocznie się Kompilowanie i wdrażanie aplikacji.

1. Kliknij przycisk **Przejdź do zasobu** , aby przejść do strony _Przegląd_ aplikacji sieci Web.

    Gdy aplikacja jest kompilowana w tle, możesz kliknąć transparent, który zawiera link umożliwiający wyświetlenie stanu kompilacji.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="Przepływ pracy w usłudze GitHub":::

1. Po zakończeniu wdrażania możesz przejść do aplikacji sieci Web, klikając link _adresu URL_ widoczny na stronie _Przegląd_ .

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Dostęp do statycznego adresu URL aplikacji z Azure Portal":::

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

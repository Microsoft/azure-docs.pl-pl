---
title: Wprowadzenie do kontrolki mapy sieci Web Microsoft Azure Maps
description: Dowiedz się, jak dodać mapy do aplikacji sieci Web i mobilnych przy użyciu kontrolka mapy biblioteki JavaScript po stronie klienta w programie Azure Maps. Zobacz jak lokalizować mapy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 21fd5f5c94b56f2f63d0e90d9982edd60a2d95d6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895447"
---
# <a name="use-the-azure-maps-map-control"></a>Jak używać kontrolki mapy usługi Azure Maps

Biblioteka JavaScript po stronie klienta kontrolka mapy umożliwia renderowanie map i osadzonych funkcji Azure Maps w aplikacji sieci Web lub mobilnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kontrolka mapy na stronie sieci Web, musisz mieć jeden z następujących warunków wstępnych:

* [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) i [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

* Uzyskaj poświadczenia usługi Azure Active Directory (AAD) z [opcjami uwierzytelniania](/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Utwórz nową mapę na stronie sieci Web

Mapę można osadzić na stronie sieci Web przy użyciu kontrolka mapy biblioteki JavaScript po stronie klienta.

1. Utwórz nowy plik HTML.

2. Załaduj do Azure Maps Web SDK. Można wybrać jedną z dwóch opcji:

    * Użyj globalnej hostowanej wersji usługi CDN Azure Maps Web SDK przez dodanie odwołań do kodu JavaScript i arkusza stylów w `<head>` elemencie pliku HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Załaduj kod źródłowy Azure Maps Web SDK lokalnie przy użyciu pakietu [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm i hostowania go wraz z Twoją aplikacją. Ten pakiet zawiera również definicje języka TypeScript.

      > **npm Install Azure-Maps-Control**

    Następnie Dodaj odwołania do arkusza stylów Azure Maps do `<head>` elementu pliku:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Definicje języka TypeScript można zaimportować do aplikacji, dodając następujący kod:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Aby renderować mapę tak, aby wypełniał całą treść strony, Dodaj następujący `<style>` element do `<head>` elementu.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. W treści strony Dodaj `<div>` element i nadaj mu `id` **myMap** .

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Teraz zostanie zainicjowany formant mapy. Aby można było uwierzytelnić kontrolkę, należy podać klucz subskrypcji Azure Maps lub użyć poświadczeń Azure Active Directory (AAD) z [opcjami uwierzytelniania](/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Jeśli używasz klucza subskrypcji do uwierzytelniania, skopiuj i wklej następujący element skryptu wewnątrz `<head>` elementu i poniżej pierwszego `<script>` elementu. Zamień `<Your Azure Maps Key>` na klucz subskrypcji Azure Maps podstawowej.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    Jeśli używasz usługi Azure Active Directory (AAD) do uwierzytelniania, skopiuj i wklej następujący element skryptu wewnątrz `<head>` elementu i poniżej pierwszego `<script>` elementu.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            }
        });
    </script>
   ```

    Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu Azure Maps, zobacz temat [uwierzytelnianie przy użyciu Azure Maps](azure-maps-authentication.md) dokumentu. Ponadto listę przykładów pokazujących sposób integrowania usługi Azure Active Directory (AAD) z usługą Azure Maps można znaleźć [tutaj](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >W tym przykładzie przeszedłmy do `id` mapy `<div>` . Innym sposobem jest przekazanie `HTMLElement` obiektu przez przekazanie `document.getElementById('myMap')` jako pierwszy parametr.

6. Opcjonalnie może być przydatne dodanie następujących `meta` elementów do `head` elementu strony:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. W każdym miejscu plik HTML powinien wyglądać podobnie do następującej:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Otwórz plik w przeglądarce internetowej i Wyświetl renderowane mapowanie. Powinien wyglądać podobnie do poniższego obrazu:

   ![Obraz mapy pokazujący renderowany wynik](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps oferuje dwa różne sposoby ustawiania języka i widoku regionalnego dla renderowanej mapy. Pierwsza opcja polega na dodaniu tych informacji do globalnej `atlas` przestrzeni nazw, co spowoduje, że wszystkie wystąpienia formantów mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawia język francuski ("fr-FR") i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy w następujący sposób:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Istnieje możliwość załadowania wielu wystąpień mapy na tej samej stronie z różnymi ustawieniami języka i regionu. Ponadto te ustawienia można aktualizować po załadowaniu mapy przy użyciu `setStyle` funkcji mapy.

Poniżej znajduje się przykład Azure Maps z językiem ustawionym na "fr-FR" i widokiem regionalnym ustawionym na "Auto".

![Obraz mapy przedstawiający etykiety w języku francuskim](./media/how-to-use-map-control/websdk-localization.png)

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Obsługa Azure Government w chmurze

Azure Maps Web SDK obsługuje chmurę Azure Government. Wszystkie adresy URL JavaScript i CSS używane do uzyskiwania dostępu do zestawu SDK sieci Web Azure Maps pozostają takie same. Aby nawiązać połączenie z wersją Azure Government w chmurze platformy Azure Maps, należy wykonać następujące zadania.

W przypadku używania kontrolki mapy interaktywnej Dodaj następujący wiersz kodu przed utworzeniem wystąpienia `Map` klasy.

```javascript
atlas.setDomain('atlas.azure.us');
```

Podczas uwierzytelniania mapy i usług upewnij się, że Azure Maps szczegóły uwierzytelniania na platformie Azure Government Cloud.

W przypadku korzystania z modułu usługi domena dla usług musi być ustawiona podczas tworzenia wystąpienia punktu końcowego adresu URL interfejsu API. Na przykład poniższy kod tworzy wystąpienie `SearchURL` klasy i wskazuje domenę na Azure Government chmurę.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Jeśli bezpośredni dostęp do usług REST Azure Maps, Zmień domenę adresu URL na `atlas.azure.us` . Na przykład jeśli korzystasz z usługi interfejsu API wyszukiwania, Zmień domenę adresu URL z `https://atlas.microsoft.com/search/` na `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>Struktury języka JavaScript

W przypadku opracowywania przy użyciu struktury JavaScript jeden z następujących projektów typu "open source" może być przydatny:

- [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) — otoka o pokątowym 10 wokół usługi Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) — składnik Blazor Azure Maps.
- [Azure Maps reaguje składnik](https://github.com/WiredSolutions/react-azure-maps) — otoka reaguje na Azure Maps kontrolkę.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) — składnik Azure Maps dla aplikacji Vue.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć mapę i korzystać z niej:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

Dowiedz się, jak stylować mapę:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](choose-map-style.md)

Aby dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](/samples/browse/?products=azure-maps)

Aby uzyskać listę przykładów pokazujących, jak zintegrować Azure Active Directory (AAD) z Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
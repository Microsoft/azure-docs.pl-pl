---
title: Użyj modułu Azure Maps Services | Mapy Microsoft Azure
description: Dowiedz się więcej o module Azure Maps Services. Zobacz jak załadować i używać tej biblioteki pomocnika, aby uzyskiwać dostęp do Azure Maps usług REST w aplikacjach sieci Web lub Node.js.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008603"
---
# <a name="use-the-azure-maps-services-module"></a>Korzystanie z modułu Azure Maps Services

Zestaw SDK sieci Web Azure Maps zawiera *moduł usług*. Ten moduł jest biblioteką pomocników ułatwiającą korzystanie z Azure Maps usług REST w aplikacjach sieci Web lub Node.js za pomocą języka JavaScript lub TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Korzystanie z modułu usług na stronie sieci Web

1. Utwórz nowy plik HTML.
1. Załaduj moduł Azure Maps Services. Możesz załadować go na jeden z dwóch sposobów:
    - Użyj ogólnie hostowanej wersji Content Delivery Network platformy Azure modułu Azure Maps Services. Dodaj odwołanie do skryptu do `<head>` elementu pliku:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Alternatywnie możesz załadować moduł usługi dla kodu źródłowego Azure Maps Web SDK lokalnie przy użyciu pakietu [Azure-Maps-REST](https://www.npmjs.com/package/azure-maps-rest) npm, a następnie hostować go w aplikacji. Ten pakiet zawiera również definicje języka TypeScript. Użyj następującego polecenia:
    
        > **npm install azure-maps-rest**
    
        Następnie Dodaj odwołanie do skryptu do `<head>` elementu pliku:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Utwórz potok uwierzytelniania. Należy utworzyć potok, aby można było zainicjować punkt końcowy klienta adresu URL usługi. Aby uwierzytelnić klienta usługi wyszukiwania Azure Maps, użyj własnego klucza konta Azure Maps lub poświadczeń Azure Active Directory (Azure AD). W tym przykładzie zostanie utworzony klient adresu URL usługi wyszukiwania. 

    Jeśli używasz klucza subskrypcji do uwierzytelniania:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Jeśli używasz usługi Azure AD do uwierzytelniania:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Aby uzyskać więcej informacji, zobacz [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md).

1. Poniższy kod używa nowo utworzonego klienta adresu URL usługi wyszukiwania Azure Maps, aby geokodować adres: "1 Microsoft Way, Redmond, WA". Kod używa `searchAddress` funkcji i wyświetla wyniki jako tabelę w treści strony.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Oto pełny, uruchomiony przykład kodu:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Korzystanie z modułu usług" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>przy użyciu modułu usług</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Obsługa Azure Government w chmurze

Azure Maps Web SDK obsługuje chmurę Azure Government. Wszystkie adresy URL JavaScript i CSS używane do uzyskiwania dostępu do zestawu SDK sieci Web Azure Maps pozostają takie same. należy jednak wykonać następujące zadania w celu nawiązania połączenia z wersją Azure Government w chmurze platformy Azure Maps.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Aby uzyskać więcej przykładów kodu, które korzystają z modułu usługi, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Pokaż wyniki wyszukiwania na mapie](./map-search-location.md)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](./map-route.md)
---
title: Usługa zaufana JavaScript
description: Jest to wersja języka JavaScript tworząca zaufaną usługę dla usług komunikacyjnych.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d4ef8baa123f805d380b14fa24abff65903cb41d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947382"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), aktywne wersje LTS LTS i Maintenance (zalecane 10.14.1). Użyj `node --version` polecenia, aby sprawdzić wersję. 
- [Azure Functions rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code. 
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Omówienie

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagram dla architektury zaufanych usług":::

W tym samouczku utworzymy funkcję platformy Azure, która będzie służyć jako zaufana usługa udostępniania tokenów. Korzystając z tego samouczka, można zainicjować samodzielną obsługę tokenów.

Ta usługa jest odpowiedzialna za uwierzytelnianie użytkowników w usłudze Azure Communications Services. Użytkownicy aplikacji usług komunikacyjnych będą musieli uczestniczyć w `Access Token` wątkach rozmowy i wywołań VoIP. Funkcja platformy Azure będzie działać jako zaufany środek pośredniczący między użytkownikiem a usługami komunikacyjnymi. Pozwala to na udostępnianie tokenów dostępu bez uwidaczniania parametrów połączenia zasobu użytkownikom.

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [architektury klienta](../../concepts/client-and-server-architecture.md) i [uwierzytelniania oraz](../../concepts/authentication.md) informacje o pojęciach dotyczących autoryzacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="azure-functions-set-up"></a>Konfiguracja Azure Functions

Najpierw Skonfigurujmy podstawową strukturę naszej funkcji platformy Azure. Instrukcje krok po kroku dotyczące konfiguracji można znaleźć tutaj: [Tworzenie funkcji przy użyciu Visual Studio Code](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)

Nasza funkcja platformy Azure wymaga następującej konfiguracji:

- Język: JavaScript
- Szablon: wyzwalacz HTTP
- Poziom autoryzacji: anonimowe (można go później zmienić, jeśli wolisz inny model autoryzacji)
- Nazwa funkcji: zdefiniowane przez użytkownika

Po wykonaniu [instrukcji Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript) w powyższej konfiguracji należy mieć projekt w Visual Studio Code dla funkcji platformy Azure z `index.js` plikiem zawierającym samą funkcję. Kod wewnątrz tego pliku powinien być następujący:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Teraz będziemy instalować biblioteki usług Azure Communication Services.

### <a name="install-communication-services-libraries"></a>Instalowanie bibliotek usług komunikacyjnych

Użyjemy `Administration` biblioteki do wygenerowania `User Access Tokens` .

Użyj `npm install` polecenia, aby zainstalować bibliotekę kliencką klienta usługi Azure Communications Services dla języka JavaScript.

```console

npm install @azure/communication-administration --save

```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

W górnej części `index.js` pliku zaimportuj interfejs dla `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>Generowanie tokenu dostępu

Aby umożliwić generowanie funkcji platformy Azure `User Access Tokens` , najpierw musimy używać parametrów połączenia dla zasobu usług komunikacyjnych.

Przejdź do [przewodnika Szybki Start dotyczącego udostępniania zasobów](../../quickstarts/create-communication-resource.md) , aby uzyskać więcej informacji na temat pobierania parametrów połączenia.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Następnie zmodyfikujemy oryginalną funkcję do wygenerowania `User Access Tokens` . 

`User Access Tokens` są generowane przez utworzenie użytkownika z `createUser` metody. Po utworzeniu użytkownika możemy użyć `issueToken` metody w celu wygenerowania tokenu dla tego użytkownika, który zwraca funkcja platformy Azure.

Na potrzeby tego przykładu zostanie skonfigurowany zakres tokenów `voip` . Inne zakresy mogą być niezbędne dla Twojej aplikacji. Dowiedz się więcej o [zakresach](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

W przypadku istniejących usług komunikacyjnych `CommunicationUser` można pominąć krok tworzenia i po prostu wygenerować token dostępu. Więcej szczegółów znajduje się w temacie [Tworzenie tokenów dostępu użytkowników — szybki start](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Testowanie funkcji platformy Azure

Uruchom lokalnie funkcję platformy Azure przy użyciu polecenia `F5` . Spowoduje to zainicjowanie funkcji platformy Azure lokalnie i udostępnienie jej w programie: `http://localhost:7071/api/FUNCTION_NAME` . Zapoznaj się z dodatkową dokumentacją na temat [uruchamiania lokalnego](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-locally)

Otwórz adres URL w przeglądarce i dla tokenu powinna zostać wyświetlona treść odpowiedzi z identyfikatorem użytkownika, tokenem i wygaśnięciem komunikacji.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Diagram dla architektury zaufanych usług":::

## <a name="deploy-the-function-to-azure"></a>Wdróż funkcję na platformie Azure

Aby wdrożyć funkcję platformy Azure, możesz wykonać [instrukcje krok po kroku](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#sign-in-to-azure)

Ogólnie rzecz biorąc, konieczne będzie:
1. Logowanie do platformy Azure z poziomu programu Visual Studio
2. Opublikuj projekt na koncie platformy Azure. W tym miejscu musisz wybrać istniejącą subskrypcję.
3. Utwórz nowy zasób funkcji platformy Azure za pomocą Kreatora programu Visual Studio lub Użyj istniejącego zasobu. W przypadku nowego zasobu należy skonfigurować go w żądanym regionie, czasie wykonywania i unikatowym identyfikatorze.
4. Poczekaj na zakończenie wdrożenia
5. Uruchom funkcję 🎉

## <a name="run-azure-function"></a>Uruchom funkcję platformy Azure

Uruchamianie funkcji platformy Azure przy użyciu adresu URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Aby znaleźć adres URL, kliknij prawym przyciskiem myszy funkcję na Visual Studio Code i skopiuj adres URL funkcji.

Aby uzyskać więcej informacji na temat [uruchamiania funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-in-azure)

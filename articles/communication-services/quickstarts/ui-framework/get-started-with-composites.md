---
title: Wprowadzenie do składników złożonych zestawu SDK platformy Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę ze składnikami złożonymi programu UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488061"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Szybki Start: Rozpoczynanie pracy ze składnikami złożonymi struktury interfejsu użytkownika

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu struktury interfejsu użytkownika, aby szybko zintegrować środowiska komunikacji z aplikacjami. W tym przewodniku szybki start dowiesz się, jak zintegrować składniki złożone środowiska interfejsu użytkownika z aplikacją w celu tworzenia rozwiązań komunikacyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Active LTS i Maintenance — wersje LTS (zalecany węzeł 12).
- Zasób aktywnych usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](./../create-communication-resource.md).
- Token dostępu użytkownika do tworzenia wystąpienia złożonego wywołania. Dowiedz się [, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](./../access-tokens.md).

## <a name="setting-up"></a>Konfigurowanie

Struktura interfejsu użytkownika wymaga instalacji środowiska reagowania. Następnie wykonamy tę czynność. Jeśli masz już aplikację do reagowania, możesz pominąć tę sekcję.

### <a name="set-up-react-app"></a>Konfigurowanie aplikacji do reagowania

W tym przewodniku szybki start będziemy używać szablonu tworzenia i reagowania na aplikację. Aby uzyskać więcej informacji, zobacz: [Rozpoczynanie pracy z reagowaniem](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

Po zakończeniu tego procesu powinna istnieć pełna aplikacja w folderze `my-app` . W tym przewodniku szybki start zmodyfikujemy pliki w `src` folderze.

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta wywołującego usługi Azure Communications Services dla języka JavaScript. Przenieś podaną plik tar (prywatna wersja zapoznawcza) do katalogu my-app.

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

### <a name="run-create-react-app"></a>Uruchom aplikację Tworzenie aplikacji do reagowania

Przetestujmy instalację aplikacji do tworzenia reakcji, uruchamiając:

```console

npm run start 

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta interfejsu użytkownika usługi Azure Communication Services:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | Składnik złożony, który renderuje środowisko wywoływania grupy z galerią i kontrolkami uczestników. |
| GroupChat | Składnik złożony, który renderuje środowisko rozmowy grupowej przy użyciu wątku rozmowy i danych wejściowych |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Inicjuj składniki złożone wywołania grupy i rozmowy grupowej

Przejdź do `src` folderu w programie `my-app` i Wyszukaj plik `app.js` . Tutaj porzucimy Poniższy kod, aby zainicjować nasze składniki złożone dla rozmowy grupowej i nawiązać połączenia. Możesz wybrać, który z nich ma być używany w zależności od używanego typu środowiska komunikacji. W razie konieczności można użyć obu jednocześnie. Aby zainicjować składniki, należy uzyskać token dostępu pobrany z usług Azure Communications Services. Aby uzyskać szczegółowe informacje na temat uzyskiwania tokenów dostępu, zobacz: [Tworzenie tokenów dostępu użytkowników i zarządzanie nimi](./../access-tokens.md).

> [!NOTE]
> Składniki nie generują tokenów dostępu, identyfikatorów grup ani identyfikatorów wątków. Te elementy pochodzą z usług, które przechodzą przez odpowiednie kroki w celu wygenerowania tych identyfikatorów i przekazania ich do aplikacji klienckiej. Aby uzyskać więcej informacji, zobacz: [Architektura serwera klienta](./../../concepts/client-and-server-architecture.md).
> 
> Na przykład: kompozyt rozmowy grupowej oczekuje, że `userId` skojarzone z `token` używanym do jego zainicjowania, został już przyłączony do `threadId` podanego elementu. Jeśli token nie został przyłączony do identyfikatora wątku, nastąpi awaria złożona przez rozmowę z grupą. Aby uzyskać więcej informacji na temat rozmowy, zobacz: [wprowadzenie z czatem](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Uruchamianie przewodnika Szybki Start

Aby uruchomić kod powyżej, użyj polecenia:

```console

npm run start 

```

Aby w pełni przetestować możliwości, potrzebny jest drugi klient z funkcjami wywoływania i rozmowy w celu dołączenia do wątku wywołania i rozmowy. Zapoznaj się z naszym Przykładem [wywołującym Hero](./../../samples/calling-hero-sample.md) i [zaczatem Hero](./../../samples/chat-hero-sample.md) jako potencjalne opcje.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wypróbuj podstawowe składniki interfejsu użytkownika](./get-started-with-components.md)

Więcej informacji można znaleźć w następujących zasobach:
- [Przegląd struktury interfejsu użytkownika](../../concepts/ui-framework/ui-sdk-overview.md)
- [Możliwości struktury interfejsu użytkownika](./../../concepts/ui-framework/ui-sdk-features.md)

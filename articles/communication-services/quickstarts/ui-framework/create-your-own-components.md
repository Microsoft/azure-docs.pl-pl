---
title: Utwórz własny składnik struktury interfejsu użytkownika
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak utworzyć niestandardowy składnik zgodny z strukturą interfejsu użytkownika
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 88de5ca56738e13446be30fb14df6bcdac360827
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539870"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Szybki Start: Tworzenie własnego składnika struktury interfejsu użytkownika

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu struktury interfejsu użytkownika, aby szybko zintegrować środowiska komunikacji z aplikacjami.

W tym przewodniku szybki start dowiesz się, jak tworzyć własne składniki przy użyciu wstępnie zdefiniowanego interfejsu stanu oferowanego przez strukturę interfejsu użytkownika. Takie podejście jest idealne dla deweloperów, którzy potrzebują dodatkowych dostosowań i chcą korzystać z własnych zasobów projektowych dla środowiska. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Active LTS i Maintenance — wersje LTS (zalecany węzeł 12).
- Zasób aktywnych usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](./../create-communication-resource.md).
- Token dostępu użytkownika do tworzenia wystąpienia klienta wywołania. Dowiedz się [, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](./../access-tokens.md).

Struktura interfejsu użytkownika wymaga skonfigurowania środowiska reagowania. Następnie wykonamy tę czynność. Jeśli masz już aplikację do reagowania, możesz pominąć tę sekcję.

### <a name="set-up-react-app"></a>Konfigurowanie aplikacji do reagowania

W tym przewodniku szybki start będziemy używać szablonu tworzenia i reagowania na aplikację. Aby uzyskać więcej informacji, zobacz: [Rozpoczynanie pracy z reagowaniem](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

Na końcu tego procesu powinna istnieć pełna aplikacja w folderze `my-app` . W tym przewodniku szybki start zmodyfikujemy pliki w `src` folderze.

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta wywołującego usługi Azure Communications Services dla języka JavaScript. Przenieś podaną plik tar (prywatna wersja zapoznawcza) do katalogu my-app.

```console

//For Private Preview install tarball

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
| Dostawca| Dostawca interfejsu użytkownika Fluent, który pozwala deweloperom modyfikować podstawowe składniki interfejsu użytkownika Fluent|
| CallingProvider| Wywoływanie dostawcy w celu utworzenia wystąpienia. Wymagane do dodania składników podstawowych|
| ChatProvider | Dostawca rozmowy, aby utworzyć wystąpienie wątku rozmowy. Wymagane do dodania składników podstawowych|
| connectFuncsToContext | Metoda łączenia składników struktury interfejsu użytkownika z podstawowymi dostawcami przy użyciu mapera |
| MapToChatMessageProps | Mapowanie warstwy danych komunikatów rozmowy, które udostępnia składniki z właściwościami wiadomości czatu |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Inicjowanie dostawców rozmów przy użyciu poświadczeń usługi Azure Communications Services

Na potrzeby tego przewodnika Szybki Start użyjemy przykładowego samouczka, aby uzyskać więcej informacji na temat wywoływania usługi, zobacz temat podstawowe składniki — [Szybki Start](./get-started-with-components.md) i [składniki złożone — szybki start](./get-started-with-composites.md).

Przejdź do `src` folderu w programie `my-app` i Wyszukaj plik `app.js` . Tutaj porzucimy Poniższy kod, aby zainicjować dostawcę rozmowy. Ten dostawca jest odpowiedzialny za obsługę kontekstu wywołań i rozmowy. Aby zainicjować składniki, należy uzyskać token dostępu pobrany z usług Azure Communications Services. Aby uzyskać szczegółowe informacje na temat uzyskiwania tokenu dostępu, zobacz: [Tworzenie tokenów dostępu i zarządzanie nimi](./../access-tokens.md).

Składniki struktury interfejsu użytkownika są zgodne z tą samą ogólną architekturą dla pozostałej części usługi. Składniki nie generują tokenów dostępu, identyfikatorów grup ani identyfikatorów wątków. Te elementy pochodzą z usług, które przechodzą przez odpowiednie kroki w celu wygenerowania tych identyfikatorów i przekazania ich do aplikacji klienckiej. Aby uzyskać więcej informacji, zobacz [Architektura serwera klienta](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Po zainicjowaniu ten dostawca umożliwia utworzenie własnego układu przy użyciu składnika struktury interfejsu użytkownika i dowolnej dodatkowej logiki układu. Dostawca zajmuje się inicjalizacją całej logicznej logiki i prawidłowo łączącej poszczególne składniki. Następnie utworzymy niestandardowy składnik przy użyciu odwzorowań struktury interfejsu użytkownika, aby nawiązać połączenie z naszym dostawcą rozmowy.


## <a name="create-a-custom-component-using-mappers"></a>Tworzenie niestandardowego składnika przy użyciu mapera

Zaczniemy od utworzenia nowego pliku o nazwie, w `SimpleChatThread.js` którym zostanie utworzony składnik. Zaczniemy od zaimportowania składników struktury interfejsu użytkownika. W tym miejscu będziemy używać języka HTML z Box i reagujemy, aby utworzyć w pełni niestandardowy składnik dla prostego wątku rozmowy. Korzystając z `connectFuncsToContext` metody, użyjemy `MapToChatMessageProps` mapowania do mapowania właściwości props na  `SimpleChatThread` składniki niestandardowe. Te właściwości będą dać nam dostęp do wiadomości czatu wysyłanych i odbieranych w celu wypełnienia ich do prostego wątku.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Dodawanie niestandardowego składnika do aplikacji

Teraz, gdy naszym składnikiem niestandardowym jest gotowy, zaimportujemy go i dodamy do naszego układu.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Uruchamianie przewodnika Szybki Start

Aby uruchomić kod powyżej, użyj polecenia:

```console

npm run start   

```

Aby w pełni przetestować możliwości, potrzebny jest drugi klient z funkcją rozmowy do wysyłania wiadomości, które będą odbierane przez nasz prosty wątek rozmowy. Zapoznaj się z naszym Przykładem [wywołującym Hero](./../../samples/calling-hero-sample.md) i [zaczatem Hero](./../../samples/chat-hero-sample.md) jako potencjalne opcje.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wypróbuj składniki złożone środowiska interfejsu użytkownika](./get-started-with-composites.md)

Więcej informacji można znaleźć w następujących zasobach:
- [Przegląd struktury interfejsu użytkownika](../../concepts/ui-framework/ui-sdk-overview.md)
- [Możliwości struktury interfejsu użytkownika](./../../concepts/ui-framework/ui-sdk-features.md)
- [Podstawowe składniki interfejsu użytkownika — Szybki Start](./get-started-with-components.md)

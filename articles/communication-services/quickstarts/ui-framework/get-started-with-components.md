---
title: Rozpocznij pracę z podstawowymi składnikami struktury interfejsu użytkownika platformy Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę ze składnikami podstawowymi struktury interfejsu użytkownika
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488180"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Szybki Start: Rozpoczynanie pracy z podstawowymi składnikami interfejsu użytkownika

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu struktury interfejsu użytkownika, aby szybko zintegrować środowiska komunikacji z aplikacjami. W tym przewodniku szybki start dowiesz się, jak zintegrować podstawowe składniki interfejsu użytkownika z Twoją aplikacją, aby tworzyć środowiska komunikacji. 

Składniki struktury interfejsu użytkownika są dostępne w dwóch wersjach: podstawowej i złożonej.

- **Składniki podstawowe** reprezentują możliwości komunikacji dyskretnej; są one podstawowymi blokami konstrukcyjnymi, których można użyć do tworzenia złożonych środowisk komunikacji. 
- **Składniki złożone** to kluczowe środowiska dla typowych scenariuszy komunikacji, które zostały skompilowane przy użyciu **składników podstawowych** jako bloków konstrukcyjnych i spakowane, aby można je było łatwo zintegrować z aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Active LTS i Maintenance — wersje LTS (zalecany węzeł 12).
- Zasób aktywnych usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](./../create-communication-resource.md).
- Token dostępu użytkownika do tworzenia wystąpienia klienta wywołania. Dowiedz się [, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](./../access-tokens.md).

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
| CallingProvider| Wywoływanie dostawcy w celu utworzenia wystąpienia. Wymagane do dodania dodatkowych składników|
| ChatProvider | Dostawca rozmowy, aby utworzyć wystąpienie wątku rozmowy. Wymagane do dodania dodatkowych składników|
| MediaGallery | Składnik podstawowy, który pokazuje uczestników połączeń i ich zdalne strumienie wideo |
| MediaControls | Składnik podstawowy do kontrolowania wywołania, w tym wyciszenie, wideo, udostępnianie ekranu |
| ChatThread | Składnik podstawowy, który renderuje wątek rozmowy przy użyciu wskaźników pisania, odczytów, itp. |
| SendBox | Składnik podstawowy, który umożliwia użytkownikowi wprowadzanie wiadomości, które będą wysyłane do dołączonego wątku|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Inicjowanie dostawców wywołań i rozmów przy użyciu poświadczeń usługi Azure Communications Services

Przejdź do `src` folderu w programie `my-app` i Wyszukaj plik `app.js` . Tutaj porzucamy Poniższy kod w celu zainicjowania dostawców wywołań i rozmów. Ci dostawcy są odpowiedzialni za utrzymanie kontekstu rozmowy i rozmowy. Możesz wybrać, który z nich ma być używany w zależności od używanego typu środowiska komunikacji. W razie konieczności można użyć obu jednocześnie. Aby zainicjować składniki, należy uzyskać token dostępu pobrany z usług Azure Communications Services. Aby uzyskać szczegółowe informacje na temat uzyskiwania tokenów dostępu, zobacz: [Tworzenie tokenów dostępu i zarządzanie nimi](./../access-tokens.md).

> [!NOTE]
> Składniki nie generują tokenów dostępu, identyfikatorów grup ani identyfikatorów wątków. Te elementy pochodzą z usług, które przechodzą przez odpowiednie kroki w celu wygenerowania tych identyfikatorów i przekazania ich do aplikacji klienckiej. Aby uzyskać więcej informacji, zobacz: [Architektura serwera klienta](./../../concepts/client-and-server-architecture.md).
> 
> Na przykład: dostawca rozmowy oczekuje, że skojarzony z `userId` `token` użytym do jego zainicjowania został już przyłączony do `threadId` podanego elementu. Jeśli token nie został przyłączony do identyfikatora wątku, dostawca rozmowy zakończy się niepowodzeniem. Aby uzyskać więcej informacji na temat rozmowy, zobacz: [wprowadzenie z czatem](./../chat/get-started.md)

Użyjemy motywu interfejsu użytkownika Fluent, aby wzbogacić wygląd i działanie aplikacji:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Po zainicjowaniu ten dostawca umożliwia utworzenie własnego układu przy użyciu składników podstawowych struktury interfejsu użytkownika i dowolnej dodatkowej logiki układu. Dostawca zajmuje się inicjalizacją całej logicznej logiki i prawidłowo łączącej poszczególne składniki. Następnie użyjemy różnych składników podstawowych dostarczonych przez strukturę interfejsu użytkownika do tworzenia środowisk komunikacyjnych. Można dostosować układ tych składników i dodać wszystkie inne składniki niestandardowe, które mają być z nimi renderowane. 

## <a name="build-ui-framework-calling-component-experiences"></a>Kompiluj platformę interfejsu użytkownika, wywołującą środowisko składników

W przypadku wywołania użyjemy `MediaGallery` `MediaControls` składników i. Aby uzyskać więcej informacji na ten temat, zobacz [możliwości struktury interfejsu użytkownika](./../../concepts/ui-framework/ui-sdk-features.md). Aby rozpocząć, w `src` folderze Utwórz nowy plik o nazwie `CallingComponents.js` . W tym miejscu zostanie zainicjowany składnik funkcji, który będzie przechowywać składniki podstawowe, a następnie zaimportować w programie `app.js` . Można dodać dodatkowy układ i style wokół składników. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

W dolnej części tego pliku wyeksportuje składniki wywołujące przy użyciu  `connectFuncsToContext` metody z struktury interfejsu użytkownika, aby połączyć składniki interfejsu użytkownika wywołującego ze stanem źródłowym przy użyciu funkcji mapowania `MapToCallingSetupProps` . Ta metoda zapewnia, że składnik ma wypełnione jego właściwości, które następnie używamy do sprawdzenia stanu i przyłączenia wywołania. Przy użyciu `isCallInitialized` właściwości, aby sprawdzić, czy `CallAgent` jest gotowa, a następnie użyć `joinCall` metody do przyłączenia. Struktura interfejsu użytkownika obsługuje funkcje mapowania niestandardowego, które mają być używane w scenariuszach, w których deweloperzy chcą kontrolować sposób wypychania danych do składników.

## <a name="build-ui-framework-chat-component-experiences"></a>Środowisko budowania składnika rozmowy struktury interfejsu użytkownika

W przypadku rozmowy będziemy używać `ChatThread` `SendBox` składników i. Aby uzyskać więcej informacji o tych składnikach, zobacz [możliwości struktury interfejsu użytkownika](./../../concepts/ui-framework/ui-sdk-features.md). Aby rozpocząć, w `src` folderze Utwórz nowy plik o nazwie `ChatComponents.js` . W tym miejscu zostanie zainicjowany składnik funkcji, który będzie przechowywać składniki podstawowe, a następnie zaimportować w programie `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Dodawanie elementów wywołujących i czatu do głównej aplikacji

Po powrocie do `app.js` pliku zostaną teraz dodane składniki do `CallingProvider` i, `ChatProvider` jak pokazano poniżej.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

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
> [Wypróbuj składniki złożone środowiska interfejsu użytkownika](./get-started-with-composites.md)

Więcej informacji można znaleźć w następujących zasobach:
- [Przegląd struktury interfejsu użytkownika](../../concepts/ui-framework/ui-sdk-overview.md)
- [Możliwości struktury interfejsu użytkownika](./../../concepts/ui-framework/ui-sdk-features.md)

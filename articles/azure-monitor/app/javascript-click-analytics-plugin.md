---
title: Kliknij opcję wtyczka Autocollection Analytics dla Application Insights JavaScript SDK
description: Jak zainstalować i używać wtyczki automatycznej kolekcji analiz dla Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e69d5cc76f8f4b14ab87e13546c98859bb801418
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234847"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Kliknij opcję wtyczka Autocollection Analytics dla Application Insights JavaScript SDK

Kliknij opcję wtyczka automatycznej kolekcji analizy dla Application Insights JavaScript SDK, umożliwiając automatyczne śledzenie zdarzeń kliknięcia na stronach sieci Web na podstawie `data-*` tagów Meta. Ta wtyczka używa `data-*` atrybutów globalnych do przechwytywania zdarzeń kliknięcia i wypełniania danych telemetrycznych.

## <a name="getting-started"></a>Wprowadzenie

Użytkownicy mogą skonfigurować wtyczkę funkcji autokolekcjonowania usługi analizy za pośrednictwem npm.

### <a name="npm-setup"></a>Konfiguracja npm

Zainstaluj pakiet npm:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Jak efektywnie korzystać z wtyczki

1. Dane telemetryczne wygenerowane z zdarzeń kliknięcia są przechowywane jak `customEvents` w sekcji Application Insights Azure Portal.
2. `name`CustomEvent jest wypełniana na podstawie następujących reguł:
    1.  Wartość `id` podana w elemencie `data-*-id` zostanie użyta jako nazwa customEvent. Na przykład jeśli kliknięty element HTML ma atrybut "Data-Sample-ID" = "Button1", to "Button1" będzie nazwą customEvent.
    2. Jeśli taki atrybut nie istnieje i jeśli `useDefaultContentNameOrId` jest ustawiony na wartość `true` w konfiguracji, atrybut HTML klikniętego elementu `id` lub jego nazwa zostanie użyta jako nazwa customEvent.
    3. Jeśli `useDefaultContentNameOrId` ma wartość false, nazwa customEvent będzie równa "not_specified".

    > [!TIP]
    > Nasze zalecenia są ustawione `useDefaultContentNameOrId` na wartość true w przypadku generowania znaczących danych.  
3. `parentDataTag` wykonuje dwie czynności:
    1. Jeśli ten tag jest obecny, wtyczka pobierze `data-*` atrybuty i wartości ze wszystkich elementów nadrzędnych HTML klikniętego elementu.
    2. Aby zwiększyć wydajność, Wtyczka używa tego znacznika jako flagi, gdy wystąpił, przestanie ona być w większym zakresie niż w dalsze przetwarzanie modelu DOM (Document Object Model) w górę.
    
    > [!CAUTION]
    > Gdy `parentDataTag` jest używany, ma trwały wpływ na całą aplikację, a nie tylko element HTML, który był przez niego używany.
4. `customDataPrefix` dostarczone przez użytkownika powinny zawsze rozpoczynać się od `data-` , na przykład `data-sample-` . W kodzie HTML `data-*` atrybuty globalne tworzą klasę atrybutów o nazwie Custom Data Attributes, które umożliwiają wymianę informacji zastrzeżonych między kodem HTML i jego reprezentacją modelu Dom przez skrypty. Starsze przeglądarki (Internet Explorer, Safari) będą upuszczać niezrozumiałe atrybuty, o ile nie zaczynają się od `data-` .

    `*`Program w programie `data-*` może być zastąpiony dowolną nazwą, [która jest](https://www.w3.org/TR/REC-xml/#NT-Name) następująca:
    - Nazwa nie może rozpoczynać się od ciągu "XML", niezależnie od wielkości liter.
    - Nazwa nie może zawierać żadnych średników (U + 003A).
    - Nazwa nie może zawierać wielkich liter.

## <a name="configuration"></a>Konfiguracja

| Nazwa                  | Typ                               | Domyślne | Opis                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoprzechwytywanie           | boolean                            | true    | Automatyczna konfiguracja przechwytywania.                                                                                                         |
| wywołania zwrotnego              | [IValueCallback](#ivaluecallback)  | null    | Konfiguracja wywołania zwrotnego.                                                                                                                 |
| pageTags              | ciąg                             | null    | Tagi strony.                                                                                                                               |
| Tagi              | [ICustomDataTags](#icustomdatatags)| null    | Niestandardowe Tagi danych, które umożliwiają przesłonięcie tagów domyślnych używanych do przechwytywania danych.                                                           |
| urlCollectHash        | boolean                            | fałsz   | Włącza rejestrowanie wartości po znaku "#" adresu URL.                                                                          |
| urlCollectQuery       | boolean                            | fałsz   | Włącza rejestrowanie ciągu zapytania w adresie URL.                                                                                      |
| behaviorValidator     | Funkcja                           | null  | Funkcja wywołania zwrotnego używana do `data-*-bhvr` sprawdzania poprawności wartości. Aby uzyskać więcej informacji, przejdź do [sekcji behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | ciąg (lub) numer                 | ''      | Domyślna wartość zachowania po wystąpieniu zdarzenia po kliknięciu prawym przyciskiem myszy. Ta wartość zostanie przesłonięta, jeśli element ma `data-*-bhvr` atrybut. |
| dropInvalidEvents     | boolean                            | fałsz   | Oflaguj, aby porzucić zdarzenia, które nie mają przydatnego kliknięcia danych.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Nazwa               | Typ     | Domyślne | Opis                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Funkcja | null    | Funkcja przesłonięcia domyślnego działania przechwytywania PageName.                           |
| pageActionPageTags | Funkcja | null    | Funkcja wywołania zwrotnego, która rozszerza domyślny pageTags zebrany podczas zdarzenia pageAction.  |
| contentName        | Funkcja | null    | Funkcja wywołania zwrotnego służąca do wypełniania dostosowanej zawartości.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Nazwa                      | Typ    | Domyślne   | Opis                                                                                       |
|---------------------------|---------|-----------|---------------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | fałsz     | Jeśli określony element nie jest oznaczony jako default customDataPrefix lub customDataPrefix nie jest dostarczany przez użytkownika, ta flaga służy do zbierania standardowego atrybutu HTML dla elementu contentname. |
| customDataPrefix          | ciąg  | `data-`   | Automatyczne przechwytywanie nazwy zawartości i wartości elementów oznaczonych za pomocą podanego prefiksu.       |
| aiBlobAttributeTag        | ciąg  | `ai-blob` | Wtyczka obsługuje tagowanie metadanych zawartości obiektu BLOB JSON zamiast poszczególnych `data-*` atrybutów. |
| metaDataPrefix            | ciąg  | null      | Automatycznie Przechwytuj nazwę i zawartość elementu meta nagłówka HTML z dostarczonym prefiksem. |
| captureAllMetaDataContent | ciąg  | null      | Automatycznie Przechwytuj nazwy i treści elementu meta nagłówka HTML. Wartość domyślna to false. Jeśli ta funkcja zostanie włączona, przesłonimy podane metaDataPrefix. |
| parentDataTag             | ciąg  | null      | Przerywa przechodzenie modelu DOM w celu przechwycenia nazwy zawartości i wartości elementów w przypadku napotkania tego tagu.|
| dntDataTag                | ciąg  | `ai-dnt`  | Elementy HTML z tym atrybutem będą ignorowane przez wtyczkę do przechwytywania danych telemetrycznych.|

### <a name="behaviorvalidator"></a>behaviorValidator

Możesz użyć funkcji behaviorValidator, gdy chcesz zapewnić spójność danych, chociaż sprawdzają, czy otagowane zachowania w kodzie są zgodne ze wstępnie zdefiniowaną listą znanych i zaakceptowanych taksonomii w przedsiębiorstwie. Nie jest to wymagane ani oczekuje się, że większość Azure Monitor klienci będą korzystać z tej usługi, ale jest ona dostępna dla zaawansowanych scenariuszy. W ramach tego rozszerzenia dostępne są trzy różne funkcje wywołania zwrotnego behaviorValidator. Jednak użytkownicy mogą korzystać z własnych funkcji wywołania zwrotnego, jeśli widoczne funkcje nie rozwiązują wymagań. Celem jest przeprowadzenie własnej struktury danych zachowań, Wtyczka używa tej funkcji do sprawdzania poprawności podczas wyodrębniania zachowań ze znaczników danych.

| Nazwa                   | Opis                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Użyj tej funkcji wywołania zwrotnego, jeśli struktura danych zachowań jest tablicą ciągów.|
| BehaviorMapValidator   | Użyj tej funkcji wywołania zwrotnego, jeśli struktura danych zachowań jest słownikiem.       |
| BehaviorEnumValidator  | Użyj tej funkcji wywołania zwrotnego, jeśli struktura danych zachowań jest wyliczeniem.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Przykładowe użycie z behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Przykładowa aplikacja

[Prosta aplikacja internetowa z włączonym dodatkiem plug Autocollection analizy](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Następne kroki

- [Analiza zdarzeń w środowisku użycia](usage-segmentation.md) służy do analizowania górnych kliknięć i wycinków według dostępnych wymiarów.
- Znajdź pozycję dane w obszarze pole zawartości w atrybucie customDimensions w tabeli CustomEvents w [log Analytics](../log-query/log-analytics-tutorial.md#write-a-query).
- Utwórz [skoroszyt](../platform/workbooks-overview.md) , aby utworzyć niestandardowe wizualizacje danych do kliknięcia.

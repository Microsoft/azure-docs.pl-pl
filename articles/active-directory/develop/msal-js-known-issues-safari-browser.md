---
title: Znane problemy z przeglądarką Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o znanych problemach dotyczących używania biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) za pomocą przeglądarki Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76696116"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Znane problemy w przeglądarce Safari z MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Odnawianie tokenu dyskretnego w przeglądarce Safari 12 i ITP 2,0

Systemy operacyjne Apple iOS 12 i MacOS 10,14 zawierają wersję [przeglądarki Safari 12](https://developer.apple.com/safari/whats-new/). W celu zapewnienia bezpieczeństwa i ochrony prywatności przeglądarka Safari 12 obejmuje [Inteligentne śledzenie zapobiegania 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Zasadniczo powoduje to, że przeglądarka ma ustawione pliki cookie innych firm. ITP 2,0 również traktuje pliki cookie ustawiane przez dostawców tożsamości jako pliki cookie innych firm.

### <a name="impact-on-msaljs"></a>Wpływ na MSAL.js

MSAL.js używa ukrytego elementu IFRAME do przejęcia i odnowienia tokenu dyskretnego w ramach `acquireTokenSilent` wywołań. Żądania tokenów dyskretnych polegają na elemencie iframe mającym dostęp do sesji uwierzytelnionego użytkownika reprezentowanej przez pliki cookie ustawiane przez usługę Azure AD. W przypadku ITP 2,0, który uniemożliwia dostęp do tych plików cookie, MSAL.js nie można w trybie dyskretnym uzyskać i odnowić tokenów i spowodować `acquireTokenSilent` błędy.

W tym momencie nie ma rozwiązania tego problemu i oceniamy opcje w społeczności ze standardami.

### <a name="work-around"></a>Obejście

Domyślnie ustawienie ITP jest włączone w przeglądarce Safari. To ustawienie można wyłączyć, przechodząc do **preferencji**  ->  **prywatność** i usuwając zaznaczenie opcji **Zapobiegaj śledzeniu między witrynami** .

![ustawienie Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Musisz obsłużyć `acquireTokenSilent` Błędy w wywołaniu interaktywnego tokenu pozyskiwania, który będzie monitował użytkownika o zalogowanie się.
Aby uniknąć powtarzających się logowań, podejście, które można zaimplementować, ma obsłużyć `acquireTokenSilent` awarię i udostępnić użytkownikowi opcję wyłączenia ustawienia itp w przeglądarce Safari przed kontynuowaniem wywołania interaktywnego. Po wyłączeniu tego ustawienia kolejne odnowienia tokenu dyskretnego powinny się powieść.

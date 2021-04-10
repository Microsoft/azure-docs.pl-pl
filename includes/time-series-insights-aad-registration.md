---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105103972"
---
* Po wybraniu odpowiedniej platformy w kroku 4 opcji [Konfigurowanie ustawień platformy](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) Skonfiguruj **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu po prawej stronie interfejsu użytkownika.

    * **Identyfikatory URI przekierowania** muszą być zgodne z adresem podanym w żądaniu uwierzytelniania:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobile & Desktop)**. Upewnij się, że ustawiono opcję **Klient publiczny** na **wartość tak**.
        * W przypadku aplikacji Single-Page hostowanych na Azure App Service wybierz pozycję **Sieć Web**.

    * Ustal, czy **adres URL wylogowania** jest odpowiedni.

    * Włącz przepływ niejawnego udzielenia, sprawdzając **tokeny dostępu** lub **tokeny identyfikatorów**.

    [![Tworzenie identyfikatorów URI przekierowania](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kliknij przycisk **Konfiguruj**, a następnie **Zapisz**.

* Skojarz Azure Time Series Insights aplikacji Azure Active Directory. Wybierz pozycję **uprawnienia interfejsu API**  >  **Dodawanie uprawnień** interfejsy API, które są  >  **wykorzystywane przez moją organizację**.

    [![Kojarzenie interfejsu API z aplikacją Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Wpisz `Azure Time Series Insights` na pasku wyszukiwania, a następnie wybierz pozycję `Azure Time Series Insights` .

* Następnie określ rodzajowe uprawnienia interfejsu API wymagane przez aplikację. Domyślnie zostaną wyróżnione **uprawnienia delegowane** . Wybierz typ uprawnienia, a następnie wybierz pozycję **Dodaj uprawnienia**.

    [![Określ rodzaj uprawnień interfejsu API wymaganych przez aplikację](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Dodaj poświadczenia](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) , jeśli aplikacja będzie wywoływała interfejsy API środowiska jako same. Poświadczenia umożliwiają aplikacji uwierzytelnianie jako same, co nie wymaga interakcji z użytkownikiem w czasie wykonywania.
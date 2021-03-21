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
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724181"
---
* Po wybraniu odpowiedniej platformy w kroku 4 opcji [Konfigurowanie ustawień platformy](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) Skonfiguruj **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu po prawej stronie interfejsu użytkownika.

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

* [Dodaj poświadczenia](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials) , jeśli aplikacja będzie wywoływała interfejsy API środowiska jako same. Poświadczenia umożliwiają aplikacji uwierzytelnianie jako same, co nie wymaga interakcji z użytkownikiem w czasie wykonywania.
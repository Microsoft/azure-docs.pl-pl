---
title: plik dołączany
description: plik dołączany
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 7de4dc21391f7dbd817c56ce51606a808cf9e3c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665819"
---
1. W [Azure Portal](https://ms.portal.azure.com/)wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.

   [![Rejestracja nowej aplikacji w Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Aplikacja zostanie wyświetlona w tym miejscu po zarejestrowaniu.

1. Nadaj aplikacji nazwę i wybierz **konta w tym katalogu organizacji tylko w** celu określenia **obsługiwanych typów kont** , które mogą uzyskiwać dostęp do interfejsu API. Jeśli tworzysz [publiczną aplikację kliencką](https://docs.microsoft.com/azure/active-directory/develop/msal-client-application-configuration#redirect-uri), Dodaj prawidłowy identyfikator URI przekierowania, a następnie **zarejestruj**się.

   [![Tworzenie aplikacji w Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Ważne informacje o aplikacji Azure Active Directory są wyświetlane w bloku **Przegląd** aplikacji na liście. Wybierz swoją aplikację w obszarze **posiadane aplikacje**, a następnie pozycję **Przegląd**.

   [![Kopiuj identyfikator aplikacji](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Skopiuj **Identyfikator aplikacji (klienta)** do użycia w aplikacji klienckiej.

1. Blok **uwierzytelnianie** określa ważne ustawienia konfiguracji uwierzytelniania.

    1. Dodaj **identyfikatory URI przekierowania** i skonfiguruj **tokeny dostępu** , wybierając pozycję **+ Dodaj platformę**.

    1. Określ, czy aplikacja jest **klientem publicznym** , **czy nie,** wybierając opcję **tak** lub nie.

    1. Sprawdź, które konta i dzierżawcy są obsługiwane.

    [![Konfigurowanie niejawnego przydzielenia](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Po wybraniu odpowiedniej platformy Skonfiguruj **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu po prawej stronie interfejsu użytkownika.

    1. **Identyfikatory URI przekierowania** muszą być zgodne z adresem podanym w żądaniu uwierzytelniania:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobile & Desktop)**. Upewnij się, że ustawiono opcję **Klient publiczny** na **wartość tak**.
        * W przypadku aplikacji Single-Page hostowanych na Azure App Service wybierz pozycję **Sieć Web**.

    1. Ustal, czy **adres URL wylogowania** jest odpowiedni.

    1. Włącz przepływ niejawnego udzielenia, sprawdzając **tokeny dostępu** lub **tokeny identyfikatorów**.

    [![Tworzenie identyfikatorów URI przekierowania](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kliknij przycisk **Konfiguruj**, a następnie **Zapisz**.

1. Wybierz kolejno pozycje **certyfikaty &** wpisy tajne i **Nowy wpis tajny klienta** , aby utworzyć hasło aplikacji, za pomocą którego aplikacja kliencka może potwierdzić swoją tożsamość.

   [![Utwórz nowy klucz tajny klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zostanie wyświetlone hasło klucza tajnego klienta. Skopiuj klucz do ulubionego edytora tekstu.

   > [!NOTE]
   > Możesz zamiast tego zaimportować certyfikat. W celu zwiększenia bezpieczeństwa zaleca się używanie certyfikatu. Aby użyć certyfikatu, wybierz opcję **Przekaż certyfikat**.

1. Skojarz Azure Time Series Insights aplikacji Azure Active Directory. Wybierz pozycję **uprawnienia interfejsu API**  >  **Dodawanie uprawnień**interfejsy API, które są  >  **wykorzystywane przez moją organizację**.

    [![Kojarzenie interfejsu API z aplikacją Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Wpisz `Azure Time Series Insights` na pasku wyszukiwania, a następnie wybierz pozycję `Azure Time Series Insights` .

1. Następnie określ rodzajowe uprawnienia interfejsu API wymagane przez aplikację. Domyślnie zostaną wyróżnione **uprawnienia delegowane** . Wybierz typ uprawnienia, a następnie wybierz pozycję **Dodaj uprawnienia**.

    [![Określ rodzaj uprawnień interfejsu API wymaganych przez aplikację](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

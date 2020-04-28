---
title: Publikowanie natywnych aplikacji klienckich — Azure AD | Microsoft Docs
description: W tym artykule opisano, jak włączyć natywne aplikacje klienckie do komunikowania się z łącznikiem usługi Azure serwer proxy aplikacji usługi Azure AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77159288"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak włączyć natywne aplikacje klienckie do współdziałania z aplikacjami proxy

Za pomocą serwera proxy aplikacji usługi Azure Active Directory (Azure AD) można publikować aplikacje sieci Web, ale mogą one również służyć do publikowania natywnych aplikacji klienckich, które są skonfigurowane przy użyciu biblioteki Azure AD Authentication Library (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci Web, ponieważ są one instalowane na urządzeniu, podczas gdy aplikacje sieci Web są dostępne za pośrednictwem przeglądarki.

Aby zapewnić obsługę natywnych aplikacji klienckich, serwer proxy aplikacji akceptuje tokeny wystawione przez usługę Azure AD, które są wysyłane w nagłówku. Usługa serwera proxy aplikacji wykonuje uwierzytelnianie dla użytkowników. To rozwiązanie nie korzysta z tokenów aplikacji na potrzeby uwierzytelniania.

![Relacja między użytkownikami końcowymi, usługą Azure AD i opublikowanymi aplikacjami](./media/application-proxy-configure-native-client-application/richclientflow.png)

Aby opublikować aplikacje natywne, należy użyć biblioteki Azure AD Authentication Library, która jest obsługiwana przez uwierzytelnianie i obsługuje wiele środowisk klienta. Serwer proxy aplikacji mieści się w [scenariuszu interfejsu API sieci Web aplikacji natywnej](../azuread-dev/native-app.md).

W tym artykule omówiono cztery kroki umożliwiające opublikowanie aplikacji natywnej przy użyciu serwera proxy aplikacji i biblioteki uwierzytelniania usługi Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1. publikowanie aplikacji serwera proxy

Opublikuj swoją aplikację proxy tak jak każda inna aplikacja i przypisz użytkownikom dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2. rejestrowanie aplikacji natywnej

Teraz musisz zarejestrować aplikację w usłudze Azure AD w następujący sposób:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlony **pulpit nawigacyjny** **Centrum administracyjnego Azure Active Directory** .
1. Na pasku bocznym wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przegląd **Azure Active Directory** .
1. Na pasku bocznym przegląd usługi Azure AD wybierz pozycję **rejestracje aplikacji**. Zostanie wyświetlona lista wszystkich rejestracji aplikacji.
1. Wybierz pozycję **Nowa rejestracja**. Zostanie wyświetlona strona **zarejestruj aplikację** .

   ![Utwórz nową rejestrację aplikacji w Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. W nagłówku **Nazwa** Określ nazwę wyświetlaną użytkownika dla aplikacji.
1. W obszarze **obsługiwane typy kont** wybierz poziom dostępu, korzystając z następujących wskazówek:

   - Aby kierować tylko konta, które są wewnętrzne dla Twojej organizacji, wybierz opcję **konta tylko w tym katalogu organizacji**.
   - Aby kierować tylko do klientów branżowych lub edukacyjnych, wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
   - Aby określić szeroki zbiór tożsamości firmy Microsoft, wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft**.

1. W nagłówku **URI przekierowania** wybierz pozycję **Klient publiczny (Mobile & Desktop)**, a następnie wpisz identyfikator URI przekierowania dla aplikacji.
1. Wybierz i przeczytaj **Zasady platformy Microsoft**, a następnie wybierz pozycję **zarejestruj**. Zostanie utworzona i wyświetlona strona przeglądu nowej rejestracji aplikacji.

Aby uzyskać szczegółowe informacje na temat tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3. udzielanie dostępu do aplikacji serwera proxy

Po zarejestrowaniu natywnej aplikacji można udzielić jej dostępu do innych aplikacji w katalogu w tym przypadku w celu uzyskania dostępu do aplikacji serwera proxy. Aby umożliwić uwidocznienie aplikacji natywnej aplikacji serwera proxy:

1. Na pasku bocznym strony rejestracja nowej aplikacji wybierz pozycję **uprawnienia interfejsu API**. Zostanie wyświetlona strona **uprawnienia interfejsu API** dla nowej rejestracji aplikacji.
1. Wybierz pozycję **Dodaj uprawnienie**. Zostanie wyświetlona strona **uprawnienia do interfejsu API żądania** .
1. W obszarze **Wybierz ustawienie interfejsu API** wybierz opcję **interfejsy API, które są wykorzystywane przez moją organizację**. Zostanie wyświetlona lista zawierająca aplikacje w katalogu, które uwidaczniają interfejsy API.
1. Wpisz w polu wyszukiwania lub przewiń, aby znaleźć aplikację serwera proxy opublikowaną w [kroku 1: publikowanie aplikacji serwera proxy](#step-1-publish-your-proxy-application), a następnie wybierz aplikację serwera proxy.
1. W obszarze **jakiego typu uprawnienia aplikacja jest wymagana?** wybierz pozycję Typ uprawnienia. Jeśli aplikacja natywna musi uzyskać dostęp do interfejsu API aplikacji serwera proxy jako zalogowanego użytkownika, wybierz pozycję **uprawnienia delegowane**.
1. W nagłówku **Wybierz uprawnienia** wybierz odpowiednie uprawnienia, a następnie wybierz pozycję **Dodaj uprawnienia**. Strona **uprawnienia interfejsu API** dla aplikacji natywnej zawiera teraz dodaną aplikację serwera proxy i interfejs API uprawnień.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4. Edytowanie Active Directory Authentication Library

Edytuj natywny kod aplikacji w kontekście uwierzytelniania Active Directory Authentication Library (ADAL) w celu uwzględnienia następującego tekstu:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Informacje wymagane w przykładowym kodzie można znaleźć w portalu usługi Azure AD w następujący sposób:

| Wymagane informacje | Jak znaleźć je w portalu usługi Azure AD |
| --- | --- |
| \<> identyfikatora dzierżawy | **Azure Active Directory** > **Properties** > **Identyfikator katalogu** właściwości Azure Active Directory |
| \<Zewnętrzny adres URL aplikacji serwera proxy> | **Aplikacje** > korporacyjne —**zewnętrzny adres URL** **serwera proxy** > aplikacji*serwera proxy* >  |
| \<Identyfikator aplikacji natywnej> | **Aplikacje** > korporacyjne —**Identyfikator aplikacji** **dla aplikacji** > *natywnych* >  |
| \<Identyfikator URI przekierowania aplikacji natywnej> | **Azure Active Directory** > **App registrations**rejestracje aplikacji >  > **identyfikatorów URI przekierowania** *aplikacji natywnych* |
| \<Adres URL interfejsu API aplikacji serwera proxy> | **Azure Active Directory** > **App registrations**rejestracje aplikacji > uprawnienia > interfejsu > **API***aplikacji natywnych do***interfejsu API/uprawnień** |

Po przeprowadzeniu edycji biblioteki ADAL przy użyciu tych parametrów użytkownicy mogą uwierzytelniać się w natywnych aplikacjach klienckich nawet wtedy, gdy znajdują się poza siecią firmową.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat natywnego przepływu aplikacji, zobacz [aplikacje natywne w Azure Active Directory](../azuread-dev/native-app.md).

Dowiedz się więcej na temat konfigurowania [logowania jednokrotnego do aplikacji w Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

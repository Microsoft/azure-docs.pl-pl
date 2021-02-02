---
title: Logowanie jednokrotne oparte na nagłówkach dla aplikacji lokalnych z serwerem proxy aplikacja usługi Azure AD
description: Dowiedz się, jak zapewnić Logowanie jednokrotne dla aplikacji lokalnych, które są zabezpieczone przy użyciu uwierzytelniania opartego na nagłówkach.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d4cd69a90c4af2f996bd965ffaa145dce761018f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259540"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Logowanie jednokrotne oparte na nagłówkach dla aplikacji lokalnych z serwerem proxy aplikacja usługi Azure AD (wersja zapoznawcza)

Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) natywnie obsługuje dostęp za pomocą logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania. Możesz skonfigurować wartości nagłówka wymagane przez aplikację w usłudze Azure AD. Wartości nagłówka zostaną wysłane do aplikacji za pośrednictwem serwera proxy aplikacji. Niektóre zalety korzystania z natywnej obsługi uwierzytelniania opartego na nagłówkach za pomocą serwera proxy aplikacji obejmują:  

* **Uproszczenie zapewniania dostępu zdalnego do aplikacji lokalnych** — serwer proxy aplikacji umożliwia uproszczenie istniejącej architektury dostępu zdalnego. Można zastąpić dostęp sieci VPN do tych aplikacji. Można również usunąć zależności od lokalnych rozwiązań do uwierzytelniania. Użytkownicy nie będą zarejestrowani w przypadku logowania się do korzystania z aplikacji firmowych. Mogą oni nadal korzystać z dowolnego miejsca na dowolnym urządzeniu.  

* **Brak dodatkowego oprogramowania lub zmian w aplikacjach** — możesz użyć istniejących łączników serwera proxy aplikacji i nie jest wymagane żadne dodatkowe oprogramowanie do zainstalowania.  

* **Szeroka lista dostępnych atrybutów i przekształceń** — wszystkie dostępne wartości nagłówka są oparte na standardowych oświadczeniach wystawionych przez usługę Azure AD. Wszystkie atrybuty i przekształcenia dostępne do [konfigurowania oświadczeń dla aplikacji SAML lub OIDC](../develop/active-directory-saml-claims-customization.md#attributes) są również dostępne jako wartości nagłówka. 

## <a name="pre-requisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z logowaniem jednokrotnym w aplikacjach uwierzytelniania opartych na nagłówkach upewnij się, że środowisko jest gotowe z następującymi ustawieniami i konfiguracjami:
- Należy włączyć serwer proxy aplikacji i zainstalować łącznik zawierający lokację aplikacji. Zapoznaj się z samouczkiem [Dodawanie lokalnej aplikacji do dostępu zdalnego za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) , aby dowiedzieć się, jak przygotować środowisko lokalne, zainstalować i zarejestrować łącznik oraz przetestować łącznik. 

## <a name="supported-capabilities"></a>Obsługiwane możliwości

W poniższej tabeli wymieniono typowe funkcje wymagane w przypadku aplikacji uwierzytelniania opartych na nagłówkach, które są obsługiwane przez serwer proxy aplikacji. 

|Wymaganie   |Opis|
|----------|-----------|
|Federacyjne logowanie jednokrotne |W trybie przed uwierzytelnieniem wszystkie aplikacje są chronione za pomocą uwierzytelniania usługi Azure AD i umożliwiają użytkownikom logowanie jednokrotne. |
|Dostęp zdalny |Serwer proxy aplikacji umożliwia zdalny dostęp do aplikacji. Użytkownicy mogą uzyskiwać dostęp do aplikacji z Internetu w dowolnej przeglądarce przy użyciu zewnętrznego adresu URL. Serwer proxy aplikacji nie jest przeznaczony do użytku przez firmowe. |
|Integracja oparta na nagłówkach |Serwer proxy aplikacji umożliwia integrację z logowaniem jednokrotnym z usługą Azure AD, a następnie przekazuje tożsamość lub inne dane aplikacji jako nagłówki HTTP do aplikacji. |
|Autoryzacja aplikacji |Typowe zasady można określić na podstawie aplikacji, do której uzyskuje się dostęp, przynależności do grupy użytkowników i innych zasad. W usłudze Azure AD zasady są implementowane przy użyciu [dostępu warunkowego](../conditional-access/overview.md). Zasady autoryzacji aplikacji stosują się tylko do żądania uwierzytelniania początkowego. |
|Uwierzytelnianie krok po kroku |Zasady można definiować w celu wymuszenia dodatkowego uwierzytelniania, na przykład w celu uzyskania dostępu do poufnych zasobów. |
|Szczegółowa autoryzacja |Zapewnia kontrolę dostępu na poziomie adresu URL. Dodane zasady można wymusić na podstawie adresu URL, do którego uzyskuje się dostęp. Wewnętrzny adres URL skonfigurowany dla aplikacji definiuje zakres aplikacji, do której zasady są stosowane. Zasady skonfigurowane dla najwyższej ścieżki są wymuszane.  |

> [!NOTE] 
> Ten artykuł zawiera funkcje łączenia aplikacji uwierzytelniania opartego na nagłówkach z usługą Azure AD przy użyciu serwera proxy aplikacji i jest zalecanym wzorcem. Alternatywnie istnieje również wzorzec integracji, który korzysta z PingAccess z usługą Azure AD w celu włączenia uwierzytelniania opartego na nagłówkach. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji i PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Jak to działa

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Jak logowanie jednokrotne oparte na nagłówkach współdziała z serwerem proxy aplikacji." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. Administrator dostosowuje mapowania atrybutów wymagane przez aplikację w portalu usługi Azure AD. 
2. Gdy użytkownik uzyskuje dostęp do aplikacji, serwer proxy aplikacji gwarantuje, że użytkownik jest uwierzytelniany przez usługę Azure AD 
3. Usługa w chmurze serwera proxy aplikacji ma świadomość wymaganych atrybutów. W związku z tym usługa pobiera odpowiednie oświadczenia z tokenu identyfikatora otrzymanego podczas uwierzytelniania. Następnie usługa tłumaczy wartości do wymaganych nagłówków HTTP w ramach żądania do łącznika. 
4. Żądanie jest następnie przesyłane do łącznika, który jest następnie przesyłany do aplikacji zaplecza. 
5. Aplikacja otrzymuje nagłówki i może używać tych nagłówków zgodnie z wymaganiami. 

## <a name="publish-the-application-with-application-proxy"></a>Publikowanie aplikacji przy użyciu serwera proxy aplikacji

1. Opublikuj swoją aplikację zgodnie z instrukcjami opisanymi w artykule [publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - Wewnętrzna wartość adresu URL określa zakres aplikacji. W przypadku skonfigurowania wewnętrznej wartości adresu URL w ścieżce katalogu głównego aplikacji wszystkie ścieżki podrzędne znajdujące się pod katalogiem głównym otrzymają tę samą konfigurację nagłówka i inną konfigurację aplikacji. 
    - Utwórz nową aplikację, aby ustawić inną konfigurację nagłówka lub przypisanie użytkownika dla bardziej szczegółowej ścieżki niż skonfigurowana aplikacja. W nowej aplikacji Skonfiguruj wewnętrzny adres URL z określoną żądaną ścieżką, a następnie skonfiguruj określone nagłówki wymagane dla tego adresu URL. Serwer proxy aplikacji zawsze będzie pasował do ustawień konfiguracji do najbardziej szczegółowej ścieżki ustawionej dla aplikacji. 

2. Wybierz **Azure Active Directory**   jako **metodę wstępnego uwierzytelniania**. 
3. Przypisz użytkownika testowego, przechodząc do **użytkowników i grup** i przypisując odpowiednich użytkowników i grupy. 
4. Otwórz przeglądarkę i przejdź do **zewnętrznego adresu URL**   z ustawień serwera proxy aplikacji. 
5. Sprawdź, czy możesz nawiązać połączenie z aplikacją. Mimo że możesz nawiązać połączenie, nie możesz uzyskać dostępu do aplikacji jeszcze od czasu, gdy nagłówki nie są skonfigurowane. 

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego 
Przed rozpoczęciem pracy z logowaniem jednokrotnym w przypadku aplikacji opartych na nagłówku należy wcześniej zainstalować łącznik serwera proxy aplikacji i łącznik może uzyskać dostęp do aplikacji docelowych. Jeśli nie, wykonaj kroki opisane w [samouczku: Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)   następnie wróć tutaj. 

1. Gdy aplikacja zostanie wyświetlona na liście aplikacji dla przedsiębiorstw, zaznacz ją i wybierz pozycję **Logowanie jednokrotne**. 
2. Ustaw tryb logowania jednokrotnego na **oparty na nagłówku**. 
3. W obszarze Konfiguracja podstawowa **Azure Active Directory**, zostanie wybrana jako domyślna. 
4. Wybierz pozycję Edytuj ołówek, w nagłówkach, aby skonfigurować nagłówki do wysłania do aplikacji. 
5. Wybierz pozycję **Dodaj nowy nagłówek**. Podaj **nazwę** nagłówka i wybierz jeden z **atrybutów** lub **transformacji** i wybierz z listy rozwijanej, z którego nagłówka jest potrzebna aplikacja.  
    - Aby dowiedzieć się więcej na temat listy dostępnych atrybutów, zobacz temat [dostosowania oświadczeń — atrybuty](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Aby dowiedzieć się więcej na temat listy dostępnych transformacji, zobacz [dostosowania oświadczeń — przekształcenia oświadczeń](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Możesz również dodać **nagłówek grupy**, aby wysłać wszystkie grupy, do których należy użytkownik, lub grup przypisanych do aplikacji jako nagłówek. Aby dowiedzieć się więcej o konfigurowaniu grup jako wartości, zobacz: [Konfigurowanie oświadczeń grupy dla aplikacji](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Wybierz pozycję Zapisz. 

## <a name="test-your-app"></a>Testowanie aplikacji 

Po wykonaniu wszystkich powyższych czynności aplikacja powinna być uruchomiona i dostępna. Aby przetestować aplikację: 
1. Otwórz nową przeglądarkę lub okno przeglądarki prywatnej, aby upewnić się, że wcześniej buforowane nagłówki są wyczyszczone. Następnie przejdź do **zewnętrznego adresu URL**   z ustawień serwera proxy aplikacji.
2. Zaloguj się przy użyciu konta testowego, które zostało przypisane do aplikacji. Jeśli możesz załadować aplikację i zalogować się do niej za pomocą logowania jednokrotnego, możesz to zrobić. 

## <a name="considerations"></a>Zagadnienia do rozważenia

- Serwer proxy aplikacji służy do zapewnienia dostępu zdalnego do aplikacji lokalnych lub w chmurze prywatnej. Serwer proxy aplikacji nie jest zalecany do obsługi ruchu pochodzącego z sieci firmowej.
- Dostęp do aplikacji uwierzytelniania opartych na nagłówkach powinien być ograniczony tylko do ruchu z łącznika lub innego dozwolonego rozwiązania uwierzytelniania opartego na nagłówkach. Jest to zazwyczaj realizowane poprzez ograniczenie dostępu do sieci do aplikacji przy użyciu zapory lub ograniczenia adresów IP na serwerze aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Co to jest logowanie jednokrotne?](what-is-single-sign-on.md)
- [Co to jest serwer proxy aplikacji?](what-is-application-proxy.md)
- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)

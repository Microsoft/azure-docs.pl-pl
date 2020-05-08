---
title: Nowe środowisko rejestracji aplikacji w witrynie Azure Portal
titleSuffix: Microsoft identity platform
description: Wprowadzenie do nowego środowiska rejestracji aplikacji w Azure Portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 3a954167dc0698389680a92511621fb6acf4b12b
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889996"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Nowe środowisko rejestracji aplikacji Azure Portal

Istnieje wiele ulepszeń w nowych [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowiska Azure Portal. Jeśli wiesz już, jak korzystać z portalu rejestracji aplikacji (apps.dev.microsoft.com) na potrzeby rejestrowania aplikacji i zarządzania nimi, o ile jest to "stare środowisko", ten przewodnik umożliwi rozpoczęcie pracy przy użyciu nowego środowiska.

## <a name="whats-not-changing"></a>Co nie jest zmieniane?

- Aplikacje i powiązane konfiguracje można znaleźć w temacie w nowym środowisku. Nie trzeba ponownie rejestrować aplikacji, a użytkownicy aplikacji nie będą musieli zalogować się ponownie.

    > [!NOTE]
    > Musisz zalogować się przy użyciu konta użytego do zarejestrowania aplikacji, aby znaleźć je w Azure Portal. Zalecamy sprawdzenie, czy zalogowany użytkownik w Azure Portal jest zgodny z użytkownikiem, który został zalogowany do portalu rejestracji aplikacji, porównując adres e-mail z Twojego profilu.
    >
    > W niektórych przypadkach, zwłaszcza gdy logujesz się za pomocą osobistych kont Microsoft (np. Outlook, Live, Xbox itp.) przy użyciu adresu e-mail usługi Azure AD, stwierdzamy, że po przejściu do Azure Portal ze starego środowiska użytkownik zostanie zalogowany do innego konta z tą samą wiadomością e-mail w dzierżawie usługi Azure AD. Jeśli nadal sądzisz, że brakuje aplikacji, Wyloguj się i zaloguj się przy użyciu odpowiedniego konta.

- Aplikacje na żywo SDK utworzone przy użyciu osobistych kont Microsoft nie są jeszcze obsługiwane w Azure Portal i nadal pozostają w starym środowisku w najbliższej przyszłości.

## <a name="key-changes"></a>Zmiany kluczy

-   W starym środowisku aplikacje były domyślnie zarejestrowane jako aplikacje *zbieżne* — aplikacje obsługujące wszystkie konta organizacji (wielodostępne) oraz osobiste konta Microsoft. Nie można jej zmodyfikować przy użyciu starego środowiska, co utrudnia Tworzenie aplikacji obsługujących tylko konta organizacji (z wieloma dzierżawami lub pojedynczym dzierżawcą).
    Nowe środowisko pozwala zarejestrować aplikacje obsługujące wszystkie te opcje. [Dowiedz się więcej o typach aplikacji](active-directory-v2-registration-portal.md).

-   W nowym środowisku, jeśli Twoje osobiste konto Microsoft znajdują się również w dzierżawie usługi Azure AD, zobaczysz trzy karty — wszystkie aplikacje w dzierżawie, należące do nich aplikacje w dzierżawie oraz aplikacje z konta osobistego. Tak więc, jeśli uważasz, że aplikacje zarejestrowane przy użyciu Twojego osobistego konto Microsoft są niedostępne, sprawdź **aplikacje na karcie konto osobiste** .

-   W nowym środowisku można łatwo przełączać się między dzierżawcami, przechodząc do swojego profilu, a następnie wybierając pozycję Przełącz katalog.

## <a name="list-of-applications"></a>Lista aplikacji

-   Nowa lista aplikacji zawiera aplikacje, które zostały zarejestrowane za pośrednictwem środowiska rejestracji starszej aplikacji w Azure Portal (aplikacje, które logują się tylko na kontach usługi Azure AD), a także aplikacje zarejestrowane przy użyciu [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) (aplikacje, które logują się zarówno do usługi Azure AD, jak i osobiste konta Microsoft).

-   Nowa lista aplikacji ma dwie dodatkowe kolumny: **utworzono w** kolumnie i **certyfikatach &** Secret, która przedstawia stan (bieżące, wygasa wkrótce lub wygasłe) poświadczeń zarejestrowanych w aplikacji.

## <a name="new-app-registration"></a>Rejestrowanie nowej aplikacji

W starym środowisku, aby zarejestrować aplikację, musisz podać nazwę. Utworzone aplikacje zostały zarejestrowane jako aplikacje o *zbieżności* — aplikacje obsługujące wszystkie katalogi organizacyjne (wielodostępne) oraz osobiste konta Microsoft.  Nie można jej zmodyfikować przy użyciu starego środowiska, co utrudnia Tworzenie aplikacji, które obsługują tylko konta organizacji (pojedyncza lub wielodostępna). [Dowiedz się więcej o obsługiwanych typach kont](v2-supported-account-types.md)

W nowym środowisku należy podać nazwę aplikacji i wybrać obsługiwane typy kont. Opcjonalnie możesz podać identyfikator URI przekierowania.
W przypadku podania identyfikatora URI przekierowania należy określić, czy jest on w sieci Web, czy publiczny (natywny/mobilny i pulpit). Aby uzyskać więcej informacji na temat sposobu rejestrowania aplikacji przy użyciu nowego środowiska rejestracji aplikacji, zobacz [ten przewodnik Szybki Start](quickstart-register-app.md).

## <a name="app-management-page"></a>Strona zarządzania aplikacjami

Stare środowisko miało jedną stronę zarządzania aplikacjami dla aplikacji z następującymi sekcjami: właściwości, wpisy tajne aplikacji, platformy, właściciele, uprawnienia Microsoft Graph, profil i opcje zaawansowane.

Nowe środowisko Azure Portal przedstawia te funkcje na oddzielnych stronach. Tutaj można znaleźć funkcje równoważne:

- Właściwości-Name i ID aplikacji znajduje się na stronie Przegląd.
- Wpisy tajne aplikacji są na stronie certyfikatów & Secret
- Konfiguracja platform znajduje się na stronie uwierzytelnianie
- Uprawnienia Microsoft Graph są na stronie uprawnień interfejsu API wraz z innymi uprawnieniami
- Profil jest na stronie znakowania
- Opcja zaawansowana — obsługa zestawu SDK na żywo znajduje się na stronie uwierzytelnianie.

## <a name="application-secretscertificates--secrets"></a>Wpisy tajne/certyfikaty aplikacji & wpisy tajne

W nowym środowisku **klucze tajne aplikacji** zostały zmienione na **Certyfikaty & wpisy tajne**. Ponadto **klucze publiczne** nazywa się **certyfikatami** i **hasłami** są określane jako wpisy **tajne klienta**. Wybrano, aby nie dopełnić tej funkcji w nowym środowisku ze względów bezpieczeństwa, dlatego nie można już wygenerować nowej pary kluczy.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/uwierzytelnianie: adresy URL odpowiedzi/identyfikatory URI przekierowania
W starym doświadczeniu aplikacja zawierała sekcję platforms dla sieci Web, natywnej i internetowego interfejsu API w celu skonfigurowania adresów URL przekierowania, adresu URL wylogowywania i przepływu niejawnego.

W nowym środowisku adresy URL odpowiedzi można znaleźć w sekcji uwierzytelnianie aplikacji\'. Ponadto są one określane jako identyfikatory URI przekierowania, a format identyfikatorów URI przekierowania został zmieniony. Muszą być skojarzone z typem aplikacji (Klient internetowy lub publiczny — Mobile i Desktop). [Dowiedz się więcej](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Interfejsy API sieci Web są konfigurowane na stronie Uwidacznianie interfejsu API.

> [!NOTE]
> Wypróbuj nowe ustawienia uwierzytelniania, w którym można skonfigurować ustawienia dla aplikacji na podstawie platformy lub urządzenia, które mają być docelowe. [Dowiedz się więcej](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Uprawnienia Microsoft Graph/uprawnienia interfejsu API

-   W przypadku wybrania interfejsu API w starym środowisku można wybrać tylko Microsoft Graph interfejsów API. W nowym środowisku można wybierać spośród wielu interfejsów API firmy Microsoft, w tym Microsoft Graph, interfejsów API z organizacji i interfejsów API, co jest prezentowane na trzech kartach: Microsoft API, interfejsy API używane przez moją organizację lub moje interfejsy API. Pasek wyszukiwania w obszarze interfejsy API Moja organizacja używa wyszukiwania na kartach za pośrednictwem jednostek usługi w dzierżawie.

    > [!NOTE]
    > Ta karta nie zostanie wyświetlona, jeśli aplikacja nie jest skojarzona z dzierżawcą. Aby uzyskać więcej informacji na temat żądania uprawnień przy użyciu nowego środowiska, zobacz [ten przewodnik Szybki Start](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   Stare środowisko nie miało przycisku **Udziel uprawnień** . W nowym doświadczeniu znajduje się sekcja zgody na przyznanie uprawnień **administratora** w sekcji uprawnienia do interfejsu API aplikacji. Tylko administrator może udzielić zgody i ten przycisk jest dostępny tylko dla administratorów. Gdy administrator wybierze przycisk **Udziel zgody administratora** , zgoda administratora zostanie udzielona na wszystkie żądane uprawnienia.

## <a name="profile"></a>Profil
W starym środowisku profil zawiera logo, adres URL strony głównej, adres URL warunków użytkowania i konfigurację adresu URL zasad zachowania poufności informacji. Nowe środowisko można znaleźć na stronie znakowania.

## <a name="application-manifest"></a>Manifest aplikacji
Na stronie nowe środowisko Strona manifestu umożliwia edytowanie i aktualizowanie atrybutów aplikacji. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika
Istnieje nowy interfejs użytkownika dla właściwości, które wcześniej mogły być ustawiane tylko przy użyciu edytora manifestu lub interfejsu API lub nie istnieją.

-   Niejawny przepływ grantu (oauth2AllowImplicitFlow) można znaleźć na stronie uwierzytelniania. W przeciwieństwie do starego środowiska, można włączyć tokeny dostępu lub tokeny identyfikatora lub oba te elementy.

-   Zakresy zdefiniowane przez ten interfejs API (oauth2Permissions) i autoryzowane aplikacje klienckie (preAuthorizedApplications) można skonfigurować za pomocą strony Uwidacznianie interfejsu API. Aby uzyskać więcej informacji na temat konfigurowania aplikacji jako interfejsu API sieci Web i uwidaczniania uprawnień/zakresów, zobacz [ten przewodnik Szybki Start](quickstart-configure-app-expose-web-apis.md).

-   Domenę wydawcy (wyświetlaną dla użytkowników w [monicie o zgodę\'aplikacji s](application-consent-experience.md)) można znaleźć na stronie znakowania. Aby uzyskać więcej informacji na temat konfigurowania domeny wydawcy, zobacz [ten sposób](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

-   Nowe środowisko nie obsługuje jeszcze Rejestracje aplikacji dla dzierżawców Azure AD B2C.

-   Nowe środowisko nie obsługuje jeszcze aplikacji Live SDK utworzonych przy użyciu osobistych kont Microsoft.

-   Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Musisz użyć manifestu aplikacji, chyba że będziesz\'ponownie przełączać się między dzierżawą i wieloma dzierżawcami usługi Azure AD.

   > [!NOTE]
   > Jeśli jesteś użytkownikiem konto Microsoft osobistym w dzierżawie usługi Azure AD, a Administrator dzierżawy ograniczył dostęp do Azure Portal, nastąpi odmowa dostępu. Jeśli jednak przejdziesz przez skrót, wpisując Rejestracje aplikacji na pasku wyszukiwania lub przypinając go, będziesz mieć dostęp do nowego środowiska.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z nowym interfejsem rejestracji aplikacji, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

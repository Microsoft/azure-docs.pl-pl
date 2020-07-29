---
title: Jak przeprowadzić integrację z usługą Azure Active Directory
description: Poznaj zalety integracji aplikacji z usługą Azure Active Directory i uzyskaj zasoby dla funkcji, takich jak uproszczone logowanie, zarządzanie tożsamościami, uwierzytelnianie wieloskładnikowe i kontrola dostępu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 4eaca7350ac006a2fcf2eed8d3eb0cb8ef274841
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885705"
---
# <a name="integrating-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ten artykuł zawiera informacje na temat korzyści z integracji aplikacji z usługą Azure Active Directory (Azure AD) i uzyskiwaniem zasobów do integracji. Usługa Azure AD udostępnia organizacjom klasy korporacyjnej Zarządzanie tożsamościami dla aplikacji w chmurze. Integracja z usługą Azure AD zapewnia użytkownikom usprawnione środowisko logowania i pomaga aplikacji z zasadami IT.

## <a name="how-to-integrate"></a>Sposób przeprowadzania integracji

Istnieje kilka sposobów integracji aplikacji z usługą Azure AD. Korzystaj z kilku z tych scenariuszy, które są odpowiednie dla Twojej aplikacji.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Obsługa usługi Azure AD w celu zalogowania się do aplikacji

**Zmniejsz liczbę logowań i zmniejsz koszty pomocy technicznej.** Przy użyciu usługi Azure AD do logowania się do aplikacji użytkownicy nie będą mieć jeszcze jednej nazwy i hasła. Jako deweloper będziesz mieć co najmniej jedno hasło do przechowywania i ochrony. Nie trzeba obsługiwać resetowania zapomnianych haseł. Usługa Azure AD umożliwia zalogowanie się do niektórych najpopularniejszych aplikacji w chmurze, w tym pakietów Office 365 i Microsoft Azure. Setki milionów użytkowników z milionów organizacji prawdopodobnie użytkownik jest już zalogowany w usłudze Azure AD. Dowiedz się więcej [na temat dodawania obsługi logowania do usługi Azure AD](authentication-scenarios.md).

**Uprość rejestrowanie się w aplikacji.**  Podczas tworzenia konta w aplikacji usługa Azure AD może wysyłać podstawowe informacje o użytkowniku, aby można było wstępnie wypełnić formularz tworzenia konta lub całkowicie wyeliminować. Użytkownicy mogą zarejestrować się w celu korzystania z aplikacji przy użyciu konta usługi Azure AD za pomocą dobrze znanego środowiska, podobnego do tych znajdujących się w multimediach społecznościowych i aplikacjach mobilnych. Każdy użytkownik może utworzyć konto i zalogować się do aplikacji zintegrowanej z usługą Azure AD bez konieczności jej zaangażowania. Dowiedz się więcej o [rejestrowaniu aplikacji na potrzeby logowania do konta usługi Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Przeglądanie w poszukiwaniu użytkowników, zarządzanie aprowizacji użytkowników i kontrolowanie dostępu do aplikacji

**Przeglądaj w poszukiwaniu użytkowników w katalogu.**  Użyj interfejsu API Microsoft Graph, aby ułatwić użytkownikom wyszukiwanie i przeglądanie w poszukiwaniu innych osób w organizacji Podczas zapraszania osób lub udzielania dostępu, zamiast wymagać od nich wpisywania adresów e-mail. Użytkownicy mogą przeglądać przy użyciu znanego interfejsu stylu książki adresowej, w tym wyświetlania szczegółów hierarchii organizacyjnej. Dowiedz się więcej o [interfejsie API Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Ponowne używanie grup Active Directory i list dystrybucyjnych, którymi już zarządza Twój klient.**  Usługa Azure AD zawiera grupy, które są już używane przez klienta do dystrybucji poczty e-mail i zarządzania dostępem. Korzystając z interfejsu API Microsoft Graph, należy ponownie używać tych grup zamiast konieczności tworzenia osobnego zestawu grup i zarządzania nim w aplikacji. Informacje o grupach można także wysyłać do aplikacji w tokenach logowania. Dowiedz się więcej o [interfejsie API Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do aplikacji.**  Administratorzy i właściciele aplikacji w usłudze Azure AD mogą przypisywać dostęp do aplikacji do określonych użytkowników i grup. Za pomocą interfejsu API Microsoft Graph można odczytać tę listę i korzystać z niej w celu kontrolowania aprowizacji i cofania aprowizacji zasobów i dostępu w aplikacji.

**Użyj usługi Azure AD na potrzeby ról opartych na Access Control.**  Administratorzy i właściciele aplikacji mogą przypisywać użytkowników i grupy do ról zdefiniowanych podczas rejestrowania aplikacji w usłudze Azure AD. Informacje o rolach są wysyłane do aplikacji w tokenach logowania i można je również odczytać przy użyciu interfejsu API Microsoft Graph. Dowiedz się więcej o [korzystaniu z usługi Azure AD do autoryzacji](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Uzyskiwanie dostępu do profilu, kalendarza, poczty e-mail, kontaktów, plików i innych użytkowników

**Usługa Azure AD to serwer autoryzacji dla pakietu Office 365 i innych usług firmy Microsoft.**  Jeśli usługa Azure AD jest obsługiwana w celu logowania się do aplikacji lub obsługi łączenia bieżących kont użytkowników z kontami użytkowników usługi Azure AD przy użyciu protokołu OAuth 2,0, możesz poprosić o dostęp do odczytu i zapisu w profilu użytkownika, w kalendarzu, w wiadomościach e-mail, kontaktach, plikach i innych informacjach. Możesz bezproblemowo pisać zdarzenia do kalendarza użytkownika i odczytywać i zapisywać pliki w swojej usłudze OneDrive. Dowiedz się więcej o [uzyskiwaniu dostępu do interfejsów API pakietu Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promuj swoją aplikację w portalu Azure i witrynie Office 365 Marketplace

**Promuj swoją aplikację do milionów organizacji, którzy już korzystają z usługi Azure AD.**  Użytkownicy, którzy przeszukują i przeglądają te rynki Marketplace, korzystają już z jednej lub kilku usług w chmurze, dzięki czemu są wykwalifikowanymi klientami usług w chmurze. Dowiedz się więcej na temat promowania aplikacji w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Gdy użytkownicy logują się do aplikacji, zostanie ona wyświetlona w panelu dostępu usługi Azure AD i uruchamiania aplikacji pakietu Office 365.**  Użytkownicy będą mogli szybko i łatwo wrócić do swojej aplikacji, ulepszając zaangażowanie użytkowników. Dowiedz się więcej o [panelu dostępu usługi Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpieczanie komunikacji między urządzeniami i usługami

**Korzystanie z usługi Azure AD do zarządzania tożsamościami usług i urządzeń zmniejsza kod, który trzeba napisać, i umożliwia działowi IT zarządzanie dostępem.**  Usługi i urządzenia mogą uzyskać tokeny z usługi Azure AD przy użyciu protokołu OAuth i używać tych tokenów do uzyskiwania dostępu do interfejsów API sieci Web. Za pomocą usługi Azure AD można uniknąć pisania złożonego kodu uwierzytelniania. Ponieważ tożsamości usług i urządzeń są przechowywane w usłudze Azure AD, może ona zarządzać kluczami i odwołaniami w jednym miejscu, zamiast wykonywać te czynności osobno w aplikacji.

## <a name="benefits-of-integration"></a>Zalety integracji

Integracja z usługą Azure AD wiąże się z korzyściami, które nie wymagają pisania dodatkowego kodu.

### <a name="integration-with-enterprise-identity-management"></a>Integracja z zarządzaniem tożsamościami w przedsiębiorstwie

**Pomóż zachować zgodność aplikacji z zasadami IT.**  Organizacje integrują swoje systemy zarządzania tożsamościami przedsiębiorstwa z usługą Azure AD, więc gdy osoba opuści organizację, automatycznie utraci dostęp do aplikacji bez konieczności podejmowania dodatkowych czynności. Może ona zarządzać osobami, które mogą uzyskać dostęp do aplikacji i określić, jakie zasady dostępu są wymagane — na przykład uwierzytelnianie wieloskładnikowe — zmniejszając potrzebę pisania kodu w celu zachowania zgodności ze złożonymi zasadami firmowymi. Usługa Azure AD zapewnia administratorom szczegółowy dziennik inspekcji, który zalogował się w aplikacji, aby mógł śledzić użycie.

**Usługa Azure AD rozszerza Active Directory do chmury, dzięki czemu aplikacja może zintegrować się z usługą AD.**  Wiele organizacji na całym świecie używa Active Directory jako ich głównego logowania i systemu zarządzania tożsamościami, a także wymaga, aby ich aplikacje pracowały z usługą AD. Integracja z usługą Azure AD integruje aplikację z Active Directory.

### <a name="advanced-security-features"></a>Zaawansowane funkcje zabezpieczeń

**Uwierzytelnianie wieloskładnikowe.**  Usługa Azure AD zapewnia natywne uwierzytelnianie wieloskładnikowe. Administratorzy IT mogą wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do Twojej aplikacji, dzięki czemu nie musisz samodzielnie zakodować tej pomocy technicznej. Dowiedz się więcej o [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Wykrywanie nietypowego logowania.**  Usługa Azure AD przetwarza więcej niż miliard logowań dziennie, przy użyciu algorytmów uczenia maszynowego do wykrywania podejrzanych działań i powiadamiania administratorów IT o możliwych problemach. Dzięki obsłudze logowania usługi Azure AD Twoja aplikacja uzyskuje korzyść tej ochrony. Dowiedz się więcej na temat [wyświetlania raportu dostępu Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Dostęp warunkowy.**  Oprócz uwierzytelniania wieloskładnikowego Administratorzy mogą wymagać spełnienia określonych warunków, zanim użytkownicy będą mogli zalogować się do aplikacji. Warunki, które można ustawić, obejmują zakres adresów IP urządzeń klienckich, członkostwo w określonych grupach oraz stan urządzenia używanego na potrzeby dostępu. Dowiedz się więcej na temat [Azure Active Directory dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Łatwe programowanie

**Standardowe protokoły branżowe.**  Firma Microsoft jest zobowiązana do wspierania standardów branżowych. Platforma tożsamości firmy Microsoft obsługuje standardowe w branży protokoły OAuth 2,0 i OpenID Connect Connect 1,0. Dowiedz się więcej na temat [protokołów uwierzytelniania platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

**Biblioteki Open Source.**  Firma Microsoft oferuje w pełni obsługiwane biblioteki Open Source dla popularnych języków i platform, aby przyspieszyć programowanie. Kod źródłowy jest licencjonowany w ramach oprogramowania Apache 2,0 i jest bezpłatny do rozwidlenia i współtworzenia projektów. Dowiedz się więcej o [bibliotece uwierzytelniania firmy Microsoft (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Ogólnoświatowa obecność i wysoka dostępność

**Usługa Azure AD jest wdrażana w centrach danych na całym świecie i jest zarządzana i monitorowana wokół zegara.**  Usługa Azure AD to system zarządzania tożsamościami dla Microsoft Azure i pakietu Office 365 i jest wdrażany w 28 centrach danych na całym świecie. Dane katalogu mają być replikowane do co najmniej trzech centrów danych. Globalne moduły równoważenia obciążenia zapewniają użytkownikom dostęp do najbliższej kopii usługi Azure AD zawierającej swoje dane i automatycznie ponownie kierują żądania do innych centrów danych w przypadku wykrycia problemu.

## <a name="next-steps"></a>Następne kroki

[Zacznij pisać kod](v2-overview.md#getting-started).

[Logowanie użytkowników przy użyciu platformy tożsamości firmy Microsoft](authentication-scenarios.md)


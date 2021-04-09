---
title: Jak przeprowadzić integrację z platformą tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Poznaj zalety integracji aplikacji z platformą tożsamości firmy Microsoft i uzyskaj zasoby dla funkcji, takich jak uproszczone logowanie, zarządzanie tożsamościami, uwierzytelnianie wieloskładnikowe i kontrola dostępu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755924"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Integracja z platformą tożsamości firmy Microsoft

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ten artykuł zawiera informacje o korzyściach związanych z integracją aplikacji z platformą tożsamości firmy Microsoft i uzyskiwaniem zasobów do integracji. Platforma tożsamości firmy Microsoft i usługa Azure Active Directory (AD) udostępniają organizacjom klasy korporacyjnej Zarządzanie tożsamościami dla aplikacji w chmurze. Integracja z platformą tożsamości firmy Microsoft zapewnia użytkownikom usprawnione środowisko logowania i pomaga aplikacji z zasadami IT.

## <a name="how-to-integrate"></a>Sposób przeprowadzania integracji

Istnieje kilka sposobów integracji aplikacji z platformą tożsamości firmy Microsoft. Korzystaj z kilku z tych scenariuszy, które są odpowiednie dla Twojej aplikacji.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Obsługa platformy tożsamości firmy Microsoft w celu zalogowania się do aplikacji

**Zmniejsz liczbę logowań i zmniejsz koszty pomocy technicznej.** Aby zalogować się do aplikacji przy użyciu platformy tożsamości firmy Microsoft, użytkownicy nie będą musieli pamiętać żadnej nazwy i hasła. Jako deweloper będziesz mieć co najmniej jedno hasło do przechowywania i ochrony. Nie trzeba obsługiwać resetowania zapomnianych haseł. Platforma tożsamości firmy Microsoft umożliwia zalogowanie się do niektórych najpopularniejszych aplikacji w chmurze, w tym Microsoft 365 i Microsoft Azure. Setki milionów użytkowników z milionów organizacji prawdopodobnie użytkownik jest już zalogowany na platformie tożsamości firmy Microsoft. Dowiedz się więcej o [dodawaniu pomocy technicznej dla platformy tożsamości firmy Microsoft](./authentication-vs-authorization.md).

**Uprość rejestrowanie się w aplikacji.**  Podczas tworzenia konta w aplikacji platforma tożsamości firmy Microsoft może wysyłać podstawowe informacje o użytkowniku, aby można było wstępnie wypełnić formularz rejestracji lub całkowicie wyeliminować. Użytkownicy mogą zarejestrować się w celu korzystania z aplikacji przy użyciu konta usługi Azure AD za pomocą dobrze znanego środowiska, podobnego do tych znajdujących się w multimediach społecznościowych i aplikacjach mobilnych. Każdy użytkownik może utworzyć konto i zalogować się do aplikacji zintegrowanej z platformą tożsamości firmy Microsoft bez konieczności jej zaangażowania. Dowiedz się więcej o [rejestrowaniu aplikacji na potrzeby logowania do konta usługi Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Przeglądanie w poszukiwaniu użytkowników, zarządzanie aprowizacji użytkowników i kontrolowanie dostępu do aplikacji

**Przeglądaj w poszukiwaniu użytkowników w katalogu.**  Użyj interfejsu API Microsoft Graph, aby ułatwić użytkownikom wyszukiwanie i przeglądanie w poszukiwaniu innych osób w organizacji Podczas zapraszania osób lub udzielania dostępu, zamiast wymagać od nich wpisywania adresów e-mail. Użytkownicy mogą przeglądać przy użyciu znanego interfejsu stylu książki adresowej, w tym wyświetlania szczegółów hierarchii organizacyjnej. Dowiedz się więcej o [interfejsie API Microsoft Graph](/graph/overview).

**Ponowne używanie grup Active Directory i list dystrybucyjnych, którymi już zarządza Twój klient.**  Usługa Azure AD zawiera grupy, które są już używane przez klienta do dystrybucji poczty e-mail i zarządzania dostępem. Korzystając z interfejsu API Microsoft Graph, należy ponownie używać tych grup zamiast konieczności tworzenia osobnego zestawu grup i zarządzania nim w aplikacji. Informacje o grupach można także wysyłać do aplikacji w tokenach logowania. Dowiedz się więcej o [interfejsie API Microsoft Graph](/graph/overview).

**Użyj platformy tożsamości firmy Microsoft, aby kontrolować, kto ma dostęp do aplikacji.**  Administratorzy i właściciele aplikacji w usłudze Azure AD mogą przypisywać dostęp do aplikacji do określonych użytkowników i grup. Za pomocą interfejsu API Microsoft Graph można odczytać tę listę i korzystać z niej w celu kontrolowania aprowizacji i cofania aprowizacji zasobów i dostępu w aplikacji.

**Użyj platformy tożsamości firmy Microsoft dla ról opartych na Access Control.**  Administratorzy i właściciele aplikacji mogą przypisywać użytkowników i grupy do ról zdefiniowanych podczas rejestrowania aplikacji na platformie tożsamości firmy Microsoft. Informacje o rolach są wysyłane do aplikacji w tokenach logowania i można je również odczytać przy użyciu interfejsu API Microsoft Graph. Dowiedz się więcej o [korzystaniu z platformy tożsamości firmy Microsoft na potrzeby autoryzacji](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Uzyskaj dostęp do profilu, kalendarza, poczty e-mail, kontaktów, plików i innych użytkowników

**Platforma tożsamości firmy Microsoft to serwer autoryzacji dla Microsoft 365 i innych usług firmy Microsoft.**  Jeśli obsługujesz platformę tożsamości firmy Microsoft w celu logowania się do aplikacji lub obsługi łączenia bieżących kont użytkowników z kontami użytkowników usługi Azure AD przy użyciu protokołu OAuth 2,0, możesz zażądać dostępu do odczytu i zapisu w profilu użytkownika, w kalendarzu, w wiadomościach e-mail, kontaktach, plikach i innych informacjach. Możesz bezproblemowo pisać zdarzenia do kalendarza użytkownika i odczytywać i zapisywać pliki w swojej usłudze OneDrive. Dowiedz się więcej [na temat Microsoft 365 interfejsów API](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Promuj swoją aplikację na platformie Azure i Microsoft 365 Marketplace

**Promuj swoją aplikację do milionów organizacji, którzy już korzystają z usługi Azure AD.**  Użytkownicy, którzy przeszukują i przeglądają te rynki Marketplace, korzystają już z jednej lub kilku usług w chmurze, dzięki czemu są wykwalifikowanymi klientami usług w chmurze. Dowiedz się więcej na temat promowania aplikacji w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Gdy użytkownicy logują się do aplikacji, będzie ona wyświetlana w panelu dostępu usługi Azure AD i Microsoft 365 uruchamiania aplikacji.**  Użytkownicy będą mogli szybko i łatwo wrócić do swojej aplikacji, ulepszając zaangażowanie użytkowników. Dowiedz się więcej o [panelu dostępu usługi Azure AD](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpieczanie komunikacji między urządzeniami i usługami

**Korzystanie z platformy tożsamości firmy Microsoft do zarządzania tożsamościami usług i urządzeń zmniejsza kod, który trzeba napisać, i umożliwia działowi IT zarządzanie dostępem.**  Usługi i urządzenia mogą uzyskać tokeny z platformy tożsamości firmy Microsoft przy użyciu protokołu OAuth i używać tych tokenów do uzyskiwania dostępu do interfejsów API sieci Web. Korzystając z platformy tożsamości firmy Microsoft, można uniknąć pisania złożonego kodu uwierzytelniania. Ponieważ tożsamości usług i urządzeń są przechowywane w usłudze Azure AD, może ona zarządzać kluczami i odwołaniami w jednym miejscu, zamiast wykonywać te czynności osobno w aplikacji.

## <a name="benefits-of-integration"></a>Zalety integracji

Integracja z platformą tożsamości firmy Microsoft obejmuje korzyści, które nie wymagają pisania dodatkowego kodu.

### <a name="integration-with-enterprise-identity-management"></a>Integracja z zarządzaniem tożsamościami w przedsiębiorstwie

**Pomóż zachować zgodność aplikacji z zasadami IT.**  Organizacje integrują swoje systemy zarządzania tożsamościami przedsiębiorstwa z platformą tożsamości firmy Microsoft, więc gdy osoba opuści organizację, automatycznie utraci dostęp do aplikacji bez konieczności podejmowania dodatkowych czynności. Może ona zarządzać osobami, które mogą uzyskać dostęp do aplikacji i określić, jakie zasady dostępu są wymagane — na przykład uwierzytelnianie wieloskładnikowe — zmniejszając potrzebę pisania kodu w celu zachowania zgodności ze złożonymi zasadami firmowymi. Usługa Azure AD zapewnia administratorom szczegółowy dziennik inspekcji, który zalogował się w aplikacji, aby mógł śledzić użycie.

**Usługa Azure AD rozszerza Active Directory do chmury, dzięki czemu aplikacja może zintegrować się z usługą AD.**  Wiele organizacji na całym świecie używa Active Directory jako ich głównego logowania i systemu zarządzania tożsamościami, a także wymaga, aby ich aplikacje pracowały z usługą AD. Integracja z usługą Azure AD integruje aplikację z Active Directory.

### <a name="advanced-security-features"></a>Zaawansowane funkcje zabezpieczeń

**Uwierzytelnianie wieloskładnikowe.**  Platforma tożsamości firmy Microsoft zapewnia natywne uwierzytelnianie wieloskładnikowe. Administratorzy IT mogą wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do Twojej aplikacji, dzięki czemu nie musisz samodzielnie zakodować tej pomocy technicznej. Dowiedz się więcej o [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Wykrywanie nietypowego logowania.**  Platforma tożsamości firmy Microsoft przetwarza więcej niż miliard logowań dziennie, przy użyciu algorytmów uczenia maszynowego do wykrywania podejrzanych działań i powiadamiania administratorów IT o możliwych problemach. Dzięki obsłudze logowania platformy tożsamości firmy Microsoft Twoja aplikacja uzyskuje korzyść tej ochrony. Dowiedz się więcej na temat [wyświetlania raportu dostępu Azure Active Directory](../reports-monitoring/overview-reports.md).

**Dostęp warunkowy.**  Oprócz uwierzytelniania wieloskładnikowego Administratorzy mogą wymagać spełnienia określonych warunków, zanim użytkownicy będą mogli zalogować się do aplikacji. Warunki, które można ustawić, obejmują zakres adresów IP urządzeń klienckich, członkostwo w określonych grupach oraz stan urządzenia używanego na potrzeby dostępu. Dowiedz się więcej na temat [Azure Active Directory dostępu warunkowego](../conditional-access/overview.md).

### <a name="easy-development"></a>Łatwe programowanie

**Standardowe protokoły branżowe.**  Firma Microsoft jest zobowiązana do wspierania standardów branżowych. Platforma tożsamości firmy Microsoft obsługuje standardowe w branży protokoły OAuth 2,0 i OpenID Connect Connect 1,0. Dowiedz się więcej na temat [protokołów uwierzytelniania platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

**Biblioteki Open Source.**  Firma Microsoft oferuje w pełni obsługiwane biblioteki Open Source dla popularnych języków i platform, aby przyspieszyć programowanie. Kod źródłowy jest licencjonowany w ramach oprogramowania Apache 2,0 i jest bezpłatny do rozwidlenia i współtworzenia projektów. Dowiedz się więcej o [bibliotece uwierzytelniania firmy Microsoft (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Ogólnoświatowa obecność i wysoka dostępność

**Usługa Azure AD jest wdrażana w centrach danych na całym świecie i jest zarządzana i monitorowana wokół zegara.**  Usługa Azure AD jest systemem zarządzania tożsamościami dla Microsoft Azure i Microsoft 365 i jest wdrażany w 28 centrach danych na całym świecie. Dane katalogu mają być replikowane do co najmniej trzech centrów danych. Globalne moduły równoważenia obciążenia zapewniają użytkownikom dostęp do najbliższej kopii usługi Azure AD zawierającej swoje dane i automatycznie ponownie kierują żądania do innych centrów danych w przypadku wykrycia problemu.

## <a name="next-steps"></a>Następne kroki

[Zacznij pisać kod](v2-overview.md#getting-started).

[Logowanie użytkowników przy użyciu platformy tożsamości firmy Microsoft](./authentication-vs-authorization.md)
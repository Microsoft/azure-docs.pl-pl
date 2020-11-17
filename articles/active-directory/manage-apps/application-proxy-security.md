---
title: Zagadnienia dotyczące zabezpieczeń serwer proxy aplikacji usługi Azure AD platformy Azure | Microsoft Docs
description: Obejmuje zagadnienia dotyczące zabezpieczeń dotyczące korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794c4e1a0859fc8a36b0abf4fcc9d5243c8bd308
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649572"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Zagadnienia dotyczące zabezpieczeń dotyczące zdalnego uzyskiwania dostępu do aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD

W tym artykule wyjaśniono składniki, które działają, aby zapewnić bezpieczeństwo użytkowników i aplikacji podczas korzystania z serwer proxy aplikacji usługi Azure Active Directory.

Na poniższym diagramie przedstawiono sposób, w jaki usługa Azure AD umożliwia bezpieczny dostęp zdalny do aplikacji lokalnych.

 ![Diagram bezpiecznego dostępu zdalnego za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Korzyści z zabezpieczeń

Usługa Azure serwer proxy aplikacji usługi Azure AD oferuje następujące korzyści w zakresie zabezpieczeń:

### <a name="authenticated-access"></a>Dostęp uwierzytelniony 

Jeśli zdecydujesz się użyć wstępnego uwierzytelniania Azure Active Directory, tylko uwierzytelnione połączenia będą mogły uzyskać dostęp do sieci.

Usługa Azure serwer proxy aplikacji usługi Azure AD korzysta z usługi tokenu zabezpieczającego (STS) usługi Azure AD w celu uzyskania wszystkich uwierzytelnień.  Ze względu na to, że bardzo istotnie, usługa blokuje znaczną liczbę ataków anonimowych, ponieważ tylko uwierzytelnione tożsamości mogą uzyskać dostęp do aplikacji zaplecza.

W przypadku wybrania opcji przekazywania jako metody wstępnego uwierzytelniania nie uzyskasz tej korzyści. 

### <a name="conditional-access"></a>Dostęp warunkowy

Zastosuj bogatsze kontrolki zasad przed ustanowieniem połączeń z siecią.

Przy użyciu [dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md)można definiować ograniczenia dotyczące sposobu, w jaki użytkownicy mogą uzyskiwać dostęp do aplikacji. Można utworzyć zasady ograniczające logowanie na podstawie lokalizacji, siły uwierzytelniania i profilu ryzyka użytkownika.

Możesz również użyć dostępu warunkowego, aby skonfigurować zasady Multi-Factor Authentication, dodając kolejną warstwę zabezpieczeń do uwierzytelniania użytkowników. Ponadto aplikacje mogą być również kierowane do Microsoft Cloud App Security za pośrednictwem dostępu warunkowego usługi Azure AD w celu zapewnienia monitorowania i kontroli w czasie rzeczywistym za pomocą zasad [dostępu](/cloud-app-security/access-policy-aad) i [sesji](/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Zakończenie ruchu

Cały ruch jest zakończony w chmurze.

Ponieważ platforma Azure serwer proxy aplikacji usługi Azure AD jest serwerem proxy zwrotnego, cały ruch do aplikacji zaplecza zostanie przerwany w usłudze. Sesja może zostać ponownie ustanowiona tylko z serwerem zaplecza, co oznacza, że serwery zaplecza nie są narażone na kierowanie ruchu HTTP. Ta konfiguracja oznacza, że lepsza ochrona przed atakami skierowanymi do firmy.

### <a name="all-access-is-outbound"></a>Cały dostęp jest wychodzący 

Nie trzeba otwierać połączeń przychodzących z siecią firmową.

Łączniki serwera proxy aplikacji używają tylko połączeń wychodzących do usługi Azure serwer proxy aplikacji usługi Azure AD, co oznacza, że nie ma potrzeby otwierania portów zapory dla połączeń przychodzących. Tradycyjne serwery proxy wymagają sieci obwodowej (znanej także jako *DMZ*, *zdemilitaryzowana Zone* lub *podsieć z osłoną*) i zezwalają na dostęp do nieuwierzytelnionych połączeń na granicy sieci. Ten scenariusz wymagał inwestycji w produkty zapory aplikacji sieci Web w celu przeanalizowania ruchu i ochrony środowiska. Serwer proxy aplikacji nie wymaga sieci obwodowej, ponieważ wszystkie połączenia są wychodzące i odbywają się za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji na temat łączników, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analiza i uczenie maszynowe w skali chmury 

Uzyskaj ochronę przed zmniejszeniem zabezpieczeń.

Ponieważ jest częścią Azure Active Directory, serwer proxy aplikacji może korzystać z [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), z danymi z centrum Microsoft Security Response Center oraz z jednostki zbrodni cyfrowych. Aktywnie wykrywamy naruszone konta i oferują ochronę przed logowaniem wysokiego ryzyka. W celu ustalenia, które próby logowania są wysokie, należy wziąć pod uwagę wiele czynników. Te czynniki obejmują Oflagowanie zainfekowanych urządzeń, sieci anonymizing i nietypowe lub prawdopodobne lokalizacje.

Wiele z tych raportów i zdarzeń jest już dostępnych za pomocą interfejsu API w celu integracji z systemami informacji o zabezpieczeniach i systemach zarządzania zdarzeniami (SIEM).

### <a name="remote-access-as-a-service"></a>Dostęp zdalny jako usługa

Nie musisz martwić się o utrzymanie i stosowanie poprawek do serwerów lokalnych.

Niepoprawione oprogramowanie nadal jest kontem dla dużej liczby ataków. Azure serwer proxy aplikacji usługi Azure AD to usługa skalowalna w Internecie, do której należy firma Microsoft, dzięki czemu zawsze otrzymujesz najnowsze poprawki zabezpieczeń i uaktualnienia.

Aby zwiększyć bezpieczeństwo aplikacji publikowanych przez usługę Azure serwer proxy aplikacji usługi Azure AD, usługa przeszukiwarki sieci Web nie może indeksować i archiwizowania aplikacji. Za każdym razem, gdy Robot przeszukiwarki sieci Web próbuje pobrać ustawienia robotów dla opublikowanej aplikacji, serwer proxy aplikacji tworzy plik robots.txt zawierający `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Usługa Azure DDoS Protection

Aplikacje publikowane za poorednictwem serwera proxy aplikacji są chronione przed atakami typu "odmowa usługi" (DDoS). Ta ochrona jest zarządzana przez firmę Microsoft i jest automatycznie włączana we wszystkich naszych centrach danych. Usługa Azure DDoS Protection oferuje zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. 

## <a name="under-the-hood"></a>Kulisy

Serwer proxy aplikacji usługi Azure AD platformy Azure składa się z dwóch części:

* Usługa oparta na chmurze: Ta usługa działa na platformie Azure i polega na tym, że są nawiązywane zewnętrzne połączenia klienta/użytkownika.
* [Łącznik On-Premises Connector](application-proxy-connectors.md): składnik lokalny, łącznik nasłuchuje żądań z usługi Azure serwer proxy aplikacji usługi Azure AD i obsługuje połączenia z wewnętrznymi aplikacjami. 

Przepływ między łącznikiem a usługą serwera proxy aplikacji jest ustanawiany, gdy:

* Łącznik jest najpierw skonfigurowany.
* Łącznik ściąga informacje o konfiguracji z usługi serwera proxy aplikacji.
* Użytkownik uzyskuje dostęp do opublikowanej aplikacji.

>[!NOTE]
>Cała komunikacja odbywa się za pośrednictwem protokołu TLS i zawsze pochodzi od łącznika do usługi serwera proxy aplikacji. Usługa jest tylko wychodząca.

Łącznik używa certyfikatu klienta do uwierzytelniania w usłudze serwera proxy aplikacji dla niemal wszystkich wywołań. Jedynym wyjątkiem od tego procesu jest wstępny krok konfiguracji, w którym znajduje się certyfikat klienta.

### <a name="installing-the-connector"></a>Instalowanie łącznika

Po pierwszym skonfigurowaniu łącznika wykonywane są następujące zdarzenia przepływu:

1. Rejestracja łącznika do usługi odbywa się w ramach instalacji łącznika. Użytkownicy są monitowani o wprowadzenie poświadczeń administratora usługi Azure AD. Token uzyskany z tego uwierzytelniania jest następnie prezentowany w usłudze Azure serwer proxy aplikacji usługi Azure AD Service.
2. Usługa serwera proxy aplikacji szacuje token. Sprawdza, czy użytkownik jest administratorem firmy w dzierżawie. Jeśli użytkownik nie jest administratorem, proces zostanie zakończony.
3. Łącznik generuje żądanie certyfikatu klienta i przekazuje go wraz z tokenem do usługi serwera proxy aplikacji. Usługa z kolei weryfikuje token i podpisuje żądanie certyfikatu klienta.
4. Łącznik używa certyfikatu klienta do przyszłej komunikacji z usługą serwera proxy aplikacji.
5. Łącznik wykonuje początkową ściąganie danych konfiguracji systemu z usługi przy użyciu certyfikatu klienta i jest teraz gotowa do podjęcia żądań.

### <a name="updating-the-configuration-settings"></a>Aktualizowanie ustawień konfiguracji

Za każdym razem, gdy usługa serwer proxy aplikacji aktualizuje ustawienia konfiguracji, wykonywane są następujące zdarzenia przepływu:

1. Łącznik nawiązuje połączenie z punktem końcowym konfiguracji w ramach usługi serwera proxy aplikacji przy użyciu certyfikatu klienta.
2. Po sprawdzeniu poprawności certyfikatu klienta usługa serwera proxy aplikacji zwraca dane konfiguracji do łącznika (na przykład grupę łączników, do której należy ten łącznik).
3. Jeśli bieżący certyfikat jest większy niż 180 dni, łącznik generuje nowe żądanie certyfikatu, które efektywnie aktualizuje certyfikat klienta co 180 dni.

### <a name="accessing-published-applications"></a>Uzyskiwanie dostępu do opublikowanych aplikacji

Gdy użytkownicy uzyskują dostęp do opublikowanej aplikacji, następujące zdarzenia są wykonywane między usługą serwera proxy aplikacji a łącznikiem serwera proxy aplikacji:

1. Usługa uwierzytelnia użytkownika aplikacji
2. Usługa umieszcza żądanie w kolejce łączników
3. Łącznik przetwarza żądanie z kolejki
4. Łącznik czeka na odpowiedź
5. Usługa przesyła strumieniowo dane do użytkownika

Aby dowiedzieć się więcej na temat tego, co znajduje się w każdym z tych kroków, należy pamiętać o przeczytaniu.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Usługa uwierzytelnia użytkownika aplikacji

Jeśli aplikacja została skonfigurowana tak, aby korzystała z przekazywania jako metody wstępnego uwierzytelniania, kroki opisane w tej sekcji są pomijane.

Jeśli aplikacja została skonfigurowana do preuwierzytelniania przy użyciu usługi Azure AD, użytkownicy zostaną przekierowani do usługi Azure AD STS w celu uwierzytelnienia i zostaną wykonane następujące kroki:

1. Serwer proxy aplikacji sprawdza wymagania dotyczące zasad dostępu warunkowego dla określonej aplikacji. Ten krok zapewnia, że użytkownik został przypisany do aplikacji. Jeśli wymagana jest weryfikacja dwuetapowa, sekwencja uwierzytelniania będzie monitował użytkownika o drugą metodę uwierzytelniania.

2. Po przekazaniu wszystkich testów usługa Azure AD STS wystawia podpisany token dla aplikacji i przekierowuje użytkownika z powrotem do usługi serwera proxy aplikacji.

3. Serwer proxy aplikacji weryfikuje, czy token został wystawiony dla poprawnej aplikacji. Wykonuje inne sprawdzenia również, takie jak upewnienie się, że token został podpisany przez usługę Azure AD, i że nadal znajduje się w prawidłowym oknie.

4. Serwer proxy aplikacji ustawia zaszyfrowany plik cookie uwierzytelniania, aby wskazać, że nastąpiło uwierzytelnienie aplikacji. Plik cookie zawiera sygnaturę czasową wygaśnięcia opartą na tokenie z usługi Azure AD i inne dane, takie jak nazwa użytkownika, na którym jest oparte uwierzytelnianie. Plik cookie jest szyfrowany przy użyciu klucza prywatnego znanego tylko usłudze serwera proxy aplikacji.

5. Serwer proxy aplikacji przekierowuje użytkownika z powrotem do pierwotnie żądanego adresu URL.

Jeśli jakakolwiek część kroków procedury uwierzytelniania wstępnego nie powiedzie się, żądanie użytkownika zostanie odrzucone, a użytkownik zostanie pokazany komunikat informujący o źródle problemu.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Usługa umieszcza żądanie w kolejce łączników.

Łączniki przechowują połączenie wychodzące w usłudze serwera proxy aplikacji. Po odebraniu żądania w usłudze Usługa będzie kolejkować żądanie na jednym z otwartych połączeń, aby można było pobrać łącznik.

Żądanie zawiera elementy z aplikacji, takie jak nagłówki żądań, dane z zaszyfrowanego pliku cookie, użytkownik wykonujący żądanie oraz identyfikator żądania. Mimo że dane z zaszyfrowanego pliku cookie są wysyłane wraz z żądaniem, sam plik cookie uwierzytelniania nie jest.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Łącznik przetwarza żądanie z kolejki. 

Na podstawie żądania serwer proxy aplikacji wykonuje jedną z następujących akcji:

* Jeśli żądanie jest prostą operacją (na przykład nie ma żadnych danych w treści w ramach żądania *Get* RESTful), łącznik nawiązuje połączenie z docelowym zasobem wewnętrznym, a następnie czeka na odpowiedź.

* Jeśli żądanie zawiera dane skojarzone z nim w treści (na przykład operacja RESTful *post* ), łącznik wykonuje połączenie wychodzące przy użyciu certyfikatu klienta z wystąpieniem serwera proxy aplikacji. To połączenie umożliwia zażądanie danych i otwarcie połączenia z zasobem wewnętrznym. Po odebraniu żądania od łącznika usługa serwera proxy aplikacji rozpoczyna akceptowanie zawartości od użytkownika i przekazuje dane do łącznika. Łącznik, z kolei przekazuje dane do zasobu wewnętrznego.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Łącznik czeka na odpowiedź.

Po zakończeniu żądania i przesyłania całej zawartości do zaplecza łącznik czeka na odpowiedź.

Po odebraniu odpowiedzi łącznik wykonuje połączenie wychodzące z usługą serwera proxy aplikacji, aby zwrócić szczegóły nagłówka i rozpocząć przesyłanie strumieniowe danych zwrotnych.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Usługa przesyła strumieniowo dane do użytkownika. 

Niektóre przetwarzanie aplikacji może wystąpić w tym miejscu. Jeśli skonfigurowano serwer proxy aplikacji do translacji nagłówków lub adresów URL w aplikacji, przetwarzanie odbywa się zgodnie z potrzebami w tym kroku.


## <a name="next-steps"></a>Następne kroki

[Zagadnienia dotyczące topologii sieci w przypadku korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-network-topology.md)

[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
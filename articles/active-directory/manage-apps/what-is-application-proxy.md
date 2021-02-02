---
title: Publikowanie aplikacji lokalnych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
description: Informacje o tym, dlaczego należy używać serwera proxy aplikacji do publikowania lokalnych aplikacji sieci Web poza użytkownikami zdalnymi. Poznaj architekturę serwera proxy aplikacji, łączniki, metody uwierzytelniania i korzyści z zabezpieczeń.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1569e577ec535a4680cfaec6f7975c056275ecbe
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258782"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Publikowanie aplikacji lokalnych dla użytkowników zdalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD

Azure Active Directory (Azure AD) oferuje wiele funkcji ochrony użytkowników, aplikacji i danych w chmurze i lokalnie. W szczególności funkcja serwer proxy aplikacji usługi Azure AD platformy Azure może być implementowana przez specjalistów IT, którzy chcą zewnętrznie publikować lokalne aplikacje sieci Web. Użytkownicy zdalni, którzy potrzebują dostępu do aplikacji wewnętrznych, mogą uzyskiwać do nich dostęp w bezpieczny sposób.

Możliwość bezpiecznego dostępu do wewnętrznych aplikacji spoza sieci stało się jeszcze bardziej krytyczne w nowoczesnych miejscach pracy. Dzięki scenariuszom, takim jak BYOD (przenoszenie własnego urządzenia) i urządzeniami przenośnymi, specjaliści IT są wzywani do spełnienia dwóch celów:

* Zwiększ produktywność użytkowników końcowych w dowolnym czasie i w dowolnym miejscu
* Ochrona zasobów firmowych przez cały czas

Wiele organizacji uważa, że są one w kontroli i są chronione, gdy zasoby znajdują się w granicach ich sieci firmowych. Jednak w dzisiejszej cyfrowej przestrzeni dyskowej ta granica została rozszerzona o zarządzane urządzenia przenośne i zasoby i usługi w chmurze. Teraz musisz zarządzać złożonością ochrony tożsamości użytkowników i danych przechowywanych na ich urządzeniach i aplikacjach.

Być może korzystasz już z usługi Azure AD do zarządzania użytkownikami w chmurze, którzy muszą mieć dostęp do Microsoft 365 i innych aplikacji SaaS, a także aplikacji sieci Web hostowanych lokalnie. Jeśli masz już usługę Azure AD, możesz użyć jej jako jednej płaszczyzny kontroli, aby zapewnić bezproblemowy i bezpieczny dostęp do aplikacji lokalnych. Może być też możliwe zamierzają przenoszenie do chmury. Jeśli tak, możesz rozpocząć podróż do chmury, implementując serwer proxy aplikacji i wykonując pierwszy krok w kierunku tworzenia silnej usługi Identity Foundation.

Na poniższej liście przedstawiono niektóre elementy, które można włączyć przez zaimplementowanie serwera proxy aplikacji w scenariuszu hybrydowego współistnienia:

* Publikowanie lokalnych aplikacji sieci Web na zewnątrz w sposób uproszczony bez strefy DMZ
* Obsługa logowania jednokrotnego (SSO) między urządzeniami, zasobami i aplikacjami w chmurze i lokalnie
* Obsługa uwierzytelniania wieloskładnikowego w przypadku aplikacji w chmurze i lokalnych
* Szybko korzystaj z funkcji w chmurze, korzystając z zabezpieczeń Microsoft Cloud
* Scentralizowane zarządzanie kontami użytkowników
* Scentralizowana kontrola tożsamości i zabezpieczeń
* Automatycznie dodawaj lub usuwaj dostęp użytkowników do aplikacji na podstawie przynależności do grupy

W tym artykule opisano sposób, w jaki usługa Azure AD i serwer proxy aplikacji zapewniają użytkownikom zdalnym środowisko logowania jednokrotnego (SSO). Użytkownicy bezpiecznie łączą się z aplikacjami lokalnymi bez serwerów sieci VPN lub z dwoma adresami IP oraz reguł zapory. Ten artykuł pomaga zrozumieć, w jaki sposób serwer proxy aplikacji oferuje możliwości i zalety chmury do lokalnych aplikacji sieci Web. Opisano w nim również architekturę i topologie, które są możliwe.

## <a name="remote-access-in-the-past"></a>Dostęp zdalny w przeszłości

Wcześniej płaszczyzna kontroli służąca do ochrony zasobów wewnętrznych przed osobami atakującymi, przy jednoczesnym umożliwieniu dostępu przez użytkowników zdalnych, była w strefie DMZ lub sieci obwodowej. Jednak rozwiązania VPN i zwrotnego serwera proxy wdrożone w strefie DMZ używanej przez klientów zewnętrznych do uzyskiwania dostępu do zasobów firmy nie są odpowiednie dla chmury chmurowej. Zazwyczaj cierpią od następujących wad:

* Koszty sprzętu
* Zachowanie zabezpieczeń (stosowanie poprawek, monitorowanie portów itp.)
* Uwierzytelnianie użytkowników na brzegu
* Uwierzytelnianie użytkowników na serwerach sieci Web w sieci obwodowej
* Obsługa dostępu do sieci VPN dla użytkowników zdalnych z dystrybucją i konfiguracją oprogramowania klienckiego sieci VPN. Ponadto Obsługa serwerów przyłączonych do domeny w strefie DMZ, które mogą być podatne na ataki zewnętrzne.

W świecie współczesnym w chmurze usługa Azure AD najlepiej nadaje się do kontrolowania, kto i co to jest w Twojej sieci. Platforma Azure serwer proxy aplikacji usługi Azure AD integruje się z nowoczesnymi uwierzytelnianiem i opartymi na chmurze technologiami, takimi jak aplikacje SaaS i dostawcy tożsamości. Ta Integracja umożliwia użytkownikom dostęp do aplikacji z dowolnego miejsca. Serwer proxy aplikacji jest bardziej odpowiedni dla dzisiejszych, cyfrowych miejsc pracy, jest bardziej bezpieczny niż w przypadku rozwiązań VPN i zwrotnego serwera proxy i łatwiejszy do wdrożenia. Użytkownicy zdalni mogą uzyskiwać dostęp do aplikacji lokalnych w taki sam sposób, w jaki uzyskują dostęp do firmy Microsoft i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmieniać ani aktualizować aplikacji, aby móc pracować z serwerem proxy aplikacji. Ponadto serwer proxy aplikacji nie wymaga otwierania połączeń przychodzących przez zaporę. Za pomocą serwera proxy aplikacji po prostu ustaw go i zapomnij.

## <a name="the-future-of-remote-access"></a>Przyszłość dostępu zdalnego

W dzisiejszym, cyfrowym miejscu pracy użytkownicy pracują w dowolnym miejscu w wielu urządzeniach i aplikacjach. Jedyną stałą jest tożsamość użytkownika. Dlatego pierwszy krok do bezpiecznej sieci to użycie funkcji [zarządzania tożsamościami w usłudze Azure AD](../../security/fundamentals/identity-management-overview.md) jako płaszczyzny kontroli zabezpieczeń. Model, który używa tożsamości, jako płaszczyzny kontroli zwykle składa się z następujących składników:

* Dostawca tożsamości, który śledzi użytkowników i informacje związane z użytkownikiem.
* Katalog urządzenia do obsługi listy urządzeń, które mają dostęp do zasobów firmy. Ten katalog zawiera odpowiednie informacje o urządzeniu (na przykład typ urządzenia, integralność itp.).
* Usługa oceny zasad, aby określić, czy użytkownik i urządzenie jest zgodne z zasadami określonymi przez administratorów zabezpieczeń.
* Możliwość udzielenia lub odmowy dostępu do zasobów organizacji.

Dzięki serwerowi proxy aplikacji usługa Azure AD śledzi użytkowników, którzy muszą uzyskać dostęp do aplikacji sieci Web opublikowanych lokalnie i w chmurze. Udostępnia centralne punkty zarządzania dla tych aplikacji. Chociaż nie jest to wymagane, zaleca się również włączenie dostępu warunkowego usługi Azure AD. Definiując warunki uwierzytelniania użytkowników i uzyskiwania do nich dostępu, możesz dodatkowo upewnić się, że odpowiednie osoby mają dostęp do aplikacji.

**Uwaga:** Ważne jest, aby zrozumieć, że usługa Azure serwer proxy aplikacji usługi Azure AD ma służyć jako zamiennik sieci VPN lub zwrotnego serwera proxy dla użytkowników mobilnych (lub zdalnych), którzy potrzebują dostępu do zasobów wewnętrznych. Nie jest on przeznaczony dla użytkowników wewnętrznych w sieci firmowej. Użytkownicy wewnętrzni niekoniecznie korzystający z serwera proxy aplikacji mogą wprowadzać nieoczekiwane i niepożądane problemy z wydajnością.

![Azure Active Directory i wszystkie Twoje aplikacje](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Omówienie sposobu działania serwera proxy aplikacji

Serwer proxy aplikacji jest usługą usługi Azure AD, którą konfigurujesz w Azure Portal. Umożliwia opublikowanie zewnętrznego publicznego adresu URL protokołu HTTP/HTTPS w chmurze platformy Azure, który łączy się z adresem URL wewnętrznego serwera aplikacji w organizacji. Te lokalne aplikacje sieci Web można zintegrować z usługą Azure AD w celu obsługi logowania jednokrotnego. Użytkownicy końcowi mogą następnie uzyskiwać dostęp do lokalnych aplikacji sieci Web w taki sam sposób, w jaki uzyskują dostęp do Microsoft 365 i innych aplikacji SaaS.

Składniki tej funkcji obejmują usługę serwera proxy aplikacji, która działa w chmurze, łącznik serwera proxy aplikacji, który jest lekkim agentem działającym na serwerze lokalnym i usługą Azure AD, który jest dostawcą tożsamości. Wszystkie trzy składniki współpracują ze sobą, aby zapewnić użytkownikowi możliwość logowania jednokrotnego w celu uzyskania dostępu do lokalnych aplikacji sieci Web.

Po zalogowaniu się użytkownicy zewnętrzni mogą uzyskiwać dostęp do lokalnych aplikacji sieci Web przy użyciu znajomego adresu URL lub [aplikacji](../user-help/my-apps-portal-end-user-access.md) z komputerów stacjonarnych lub urządzeń z systemem iOS/Mac. Na przykład serwer proxy aplikacji może zapewnić dostęp zdalny i logowanie jednokrotne do Pulpit zdalny, witryn programu SharePoint, Tableau, Qlik, programu Outlook w sieci Web i aplikacji biznesowych.

![Architektura usługi Azure serwer proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Istnieje kilka sposobów konfigurowania aplikacji do logowania jednokrotnego, a wybrana metoda zależy od uwierzytelniania używanego przez aplikację. Serwer proxy aplikacji obsługuje następujące typy aplikacji:

* Aplikacje internetowe
* Interfejsy API sieci Web, które mają zostać ujawnione w rozbudowanych aplikacjach na różnych urządzeniach
* Aplikacje hostowane za bramą Pulpit zdalny
* Rozbudowane aplikacje klienckie zintegrowane z [biblioteką uwierzytelniania firmy Microsoft (MSAL)](../develop/v2-overview.md)

Serwer proxy aplikacji działa z aplikacjami korzystającymi z następującego natywnego protokołu uwierzytelniania:

* **[Zintegrowane uwierzytelnianie systemu Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** W przypadku IWA łączniki serwera proxy aplikacji używają ograniczonego delegowania protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji Kerberos.

Serwer proxy aplikacji obsługuje również następujące protokoły uwierzytelniania z integracją innej firmy lub w określonych scenariuszach konfiguracji:

* [**Uwierzytelnianie oparte na nagłówkach**](./application-proxy-configure-single-sign-on-with-headers.md). Ta metoda logowania używa usługi uwierzytelniania innej firmy o nazwie PingAccess i jest używana, gdy aplikacja używa nagłówków na potrzeby uwierzytelniania. W tym scenariuszu uwierzytelnianie jest obsługiwane przez PingAccess.
* [**Uwierzytelnianie oparte na formularzach lub hasłach**](application-proxy-configure-single-sign-on-password-vaulting.md). Przy użyciu tej metody uwierzytelniania Użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła podczas pierwszego uzyskiwania do niego dostępu. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji. W tym scenariuszu uwierzytelnianie jest obsługiwane przez usługę Azure AD.
* [**Uwierzytelnianie SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Logowanie jednokrotne oparte na języku SAML jest obsługiwane w przypadku aplikacji korzystających z protokołów SAML 2,0 lub WS-Federation. Za pomocą logowania jednokrotnego SAML usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika.

Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [Wybieranie metody logowania](sso-options.md#choosing-a-single-sign-on-method)jednokrotnego.

### <a name="security-benefits"></a>Korzyści z zabezpieczeń

Rozwiązanie dostępu zdalnego oferowane przez serwer proxy aplikacji i usługę Azure AD obsługują kilka korzyści z zabezpieczeń, w tym:

* **Dostęp uwierzytelniony**. Serwer proxy aplikacji najlepiej nadaje się do publikowania aplikacji z [uwierzytelnianiem wstępnym](application-proxy-security.md#authenticated-access) , aby upewnić się, że tylko połączenia uwierzytelnione trafią do sieci. W przypadku aplikacji opublikowanych przy użyciu wstępnego uwierzytelniania żaden ruch nie może przechodzić przez usługę proxy aplikacji do środowiska lokalnego, bez prawidłowego tokenu. Ze względu na to, że jest to bardzo ważne, program blokuje znaczną liczbę ataków, ponieważ tylko tożsamości uwierzytelnione mogą uzyskać dostęp do aplikacji zaplecza.
* **Dostęp warunkowy**. Aby nawiązać połączenie z siecią, można zastosować bogatsze kontrolki zasad. Przy użyciu dostępu warunkowego można definiować ograniczenia dotyczące ruchu, który pozwala na osiągnięcie aplikacji zaplecza. Tworzysz zasady ograniczające logowanie na podstawie lokalizacji, siły uwierzytelniania i profilu ryzyka użytkownika. W miarę rozwoju dostępu warunkowego dodawane są więcej kontrolek w celu zapewnienia dodatkowych zabezpieczeń, takich jak integracja z Microsoft Cloud App Security (MCAS). Integracja z usługą MCAS umożliwia skonfigurowanie aplikacji lokalnej do [monitorowania w czasie rzeczywistym](application-proxy-integrate-with-microsoft-cloud-application-security.md) , wykorzystując dostęp warunkowy do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego.
* **Zakończenie ruchu**. Cały ruch do aplikacji zaplecza zostanie przerwany w usłudze serwera proxy aplikacji w chmurze, podczas gdy sesja zostanie ponownie ustanowiona na serwerze wewnętrznej bazy danych. Ta strategia połączenia oznacza, że serwery wewnętrznej bazy danych nie są narażone na kierowanie ruchu HTTP. Są lepiej chronione przed ukierunkowanymi atakami DoS (odmowa usługi), ponieważ Zapora nie jest w trakcie ataku.
* **Cały dostęp jest wychodzący**. Łączniki serwera proxy aplikacji używają tylko połączeń wychodzących do usługi serwera proxy aplikacji w chmurze przez porty 80 i 443. W przypadku braku połączeń przychodzących nie ma potrzeby otwierania portów zapory dla połączeń przychodzących lub składników w strefie DMZ. Wszystkie połączenia są wychodzące i za pośrednictwem bezpiecznego kanału.
* Analiza **zabezpieczeń i Machine Learning (ml) analizy**. Ponieważ jest częścią Azure Active Directory, serwer proxy aplikacji może korzystać z [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) (wymaga licencji na wersję [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection łączy analizę zabezpieczeń Machine-Learning ze źródłami danych z [jednostki zbrodni cyfrowych](https://news.microsoft.com/stories/cybercrime/index.html) firmy Microsoft i [centrum Microsoft Security Response](https://www.microsoft.com/msrc) , aby aktywnie identyfikować naruszone konta. Ochrona tożsamości zapewnia ochronę w czasie rzeczywistym przed logowaniem wysokiego ryzyka. Bierze pod uwagę czynniki, takie jak dostęp z zainfekowanych urządzeń, za pośrednictwem sieci anonymizing lub z nietypowych i największej liczby lokalizacji, aby zwiększyć profil ryzyka sesji. Ten profil ryzyka jest używany do ochrony w czasie rzeczywistym. Wiele z tych raportów i zdarzeń jest już dostępnych za pomocą interfejsu API w celu integracji z systemami SIEM.

* **Dostęp zdalny jako usługa**. Nie musisz martwić się o utrzymywanie i poprawianie serwerów lokalnych w celu włączenia dostępu zdalnego. Serwer proxy aplikacji to usługa internetowa, której właścicielem jest firma Microsoft, dzięki czemu zawsze można uzyskać najnowsze poprawki zabezpieczeń i uaktualnienia. Niepoprawione oprogramowanie nadal jest kontem dla dużej liczby ataków. Zgodnie z Departamentem zabezpieczeń departamentu, [nie można zapobiec 85 procent ataków](https://www.us-cert.gov/ncas/alerts/TA15-119A). Przy użyciu tego modelu usług nie trzeba już korzystać z dużych obciążeń związanych z zarządzaniem serwerami brzegowymi i szyfrować je w razie potrzeby.

* **Integracja** z usługą Intune. W przypadku usługi Intune ruch firmowy jest kierowany niezależnie od ruchu osobistego. Serwer proxy aplikacji zapewnia, że ruch firmowy jest uwierzytelniany. [Serwer proxy aplikacji i możliwość Intune Managed Browser](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) mogą być również używane razem, aby umożliwić użytkownikom zdalnym bezpieczne uzyskiwanie dostępu do wewnętrznych witryn sieci Web z urządzeń z systemem iOS i Android.

### <a name="roadmap-to-the-cloud"></a>Przewodnik po chmurze

Kolejną zaletą wdrożenia serwera proxy aplikacji jest rozszerzenie usługi Azure AD do środowiska lokalnego. W rzeczywistości implementowanie serwera proxy aplikacji to kluczowy krok dotyczący przeniesienia organizacji i aplikacji do chmury. Dzięki przeniesieniu do chmury i z dala od uwierzytelniania lokalnego możesz zmniejszyć zasięg lokalnego i korzystać z funkcji zarządzania tożsamościami usługi Azure AD jako płaszczyzny kontroli. Dzięki minimalnym lub brakom aktualizacji istniejących aplikacji masz dostęp do możliwości chmury, takich jak logowanie jednokrotne, uwierzytelnianie wieloskładnikowe i centralne zarządzanie. Instalowanie niezbędnych składników do serwera proxy aplikacji jest prostym procesem tworzenia struktury dostępu zdalnego. Po przeniesieniu do chmury masz dostęp do najnowszych funkcji, aktualizacji i funkcjonalności usługi Azure AD, takich jak wysoka dostępność i odzyskiwanie po awarii.

Aby dowiedzieć się więcej na temat migrowania aplikacji do usługi Azure AD, zobacz artykuł [Migrowanie aplikacji do Azure Active Directory](https://aka.ms/migrateapps/whitepaper) oficjalny dokument.

## <a name="architecture"></a>Architektura

Na poniższym diagramie przedstawiono ogólnie, jak usługi uwierzytelniania usługi Azure AD i serwer proxy aplikacji współpracują ze sobą, aby zapewnić użytkownikom końcowym Logowanie jednokrotne do aplikacji lokalnych.

![Przepływ uwierzytelniania usługi Azure serwer proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Gdy użytkownik uzyska dostęp do aplikacji za pomocą punktu końcowego, użytkownik zostanie przekierowany do strony logowania usługi Azure AD. Jeśli skonfigurowano zasady dostępu warunkowego, w tym momencie sprawdzane są określone warunki, aby upewnić się, że spełniają one wymagania dotyczące zabezpieczeń w organizacji.
2. Po pomyślnym zalogowaniu usługa Azure AD wysyła token do urządzenia klienckiego użytkownika.
3. Klient wysyła token do usługi serwera proxy aplikacji, która pobiera główną nazwę użytkownika (UPN) i główną nazwę zabezpieczeń (SPN) z tokenu.
4. Serwer proxy aplikacji przekazuje żądanie, które jest pobierane przez [Łącznik](application-proxy-connectors.md)serwera proxy aplikacji.
5. Łącznik wykonuje dodatkowe wymagane uwierzytelnienie w imieniu użytkownika (*opcjonalnie w zależności od metody uwierzytelniania*), żąda wewnętrznego punktu końcowego serwera aplikacji i wysyła żądanie do aplikacji lokalnej.
6. Odpowiedź z serwera aplikacji jest wysyłana za pośrednictwem łącznika do usługi serwera proxy aplikacji.
7. Odpowiedź jest wysyłana z usługi serwera proxy aplikacji do użytkownika.

|**Składnik**|**Opis**|
|:-|:-|
|Punkt końcowy|Punkt końcowy jest adresem URL lub [portalem użytkowników końcowych](end-user-experiences.md). Użytkownicy mogą uzyskiwać dostęp do aplikacji poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w sieci mogą uzyskiwać dostęp do aplikacji za pomocą adresu URL lub portalu użytkowników końcowych. Gdy użytkownicy przejdą do jednego z tych punktów końcowych, uwierzytelniają się w usłudze Azure AD, a następnie są kierowani przez łącznik do aplikacji lokalnej.|
|Azure AD|Usługa Azure AD wykonuje uwierzytelnianie przy użyciu katalogu dzierżawy przechowywanego w chmurze.|
|Usługa serwera proxy aplikacji|Ta usługa serwera proxy aplikacji działa w chmurze w ramach usługi Azure AD. Przekazuje on token logowania od użytkownika do łącznika serwera proxy aplikacji. Serwer proxy aplikacji przekazuje wszystkie dostępne nagłówki w żądaniu i ustawia nagłówki zgodnie z protokołem na adres IP klienta. Jeśli żądanie przychodzące do serwera proxy ma już ten nagłówek, adres IP klienta zostanie dodany na końcu listy rozdzielanej przecinkami, która jest wartością nagłówka.|
|Łącznik serwera proxy aplikacji|Łącznik jest lekkim agentem działającym na serwerze z systemem Windows w sieci. Łącznik zarządza komunikacją między usługą serwera proxy aplikacji w chmurze a aplikacją lokalną. Łącznik używa tylko połączeń wychodzących, więc nie trzeba otwierać żadnych portów przychodzących ani umieszczać żadnych elementów w strefie DMZ. Łączniki są bezstanowe i pobierają informacje z chmury w razie potrzeby. Aby uzyskać więcej informacji na temat łączników, takich jak równoważenie obciążenia i uwierzytelnianie, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory działa lokalnie, aby przeprowadzić uwierzytelnianie dla kont domeny. Po skonfigurowaniu logowania jednokrotnego łącznik komunikuje się z usługą AD, aby wykonać wymagane dodatkowe uwierzytelnianie.|
|Aplikacja lokalna|Na koniec użytkownik może uzyskać dostęp do aplikacji lokalnej.|

Serwer proxy aplikacji usługi Azure AD platformy Azure składa się z usługi serwera proxy aplikacji opartej na chmurze i łącznika On-Premises Connector. Łącznik nasłuchuje żądań z usługi serwera proxy aplikacji i obsługuje połączenia z wewnętrznymi aplikacjami. Należy pamiętać, że cała komunikacja odbywa się za pośrednictwem protokołu TLS i zawsze pochodzi od łącznika do usługi serwera proxy aplikacji. Oznacza to, że komunikacja odbywa się tylko w ruchu wychodzącym. Łącznik używa certyfikatu klienta do uwierzytelniania w usłudze serwera proxy aplikacji dla wszystkich wywołań. Jedynym wyjątkiem od zabezpieczeń połączenia jest początkowy krok konfiguracji, w którym certyfikat klienta został nawiązany. Aby uzyskać więcej informacji, zobacz serwer proxy aplikacji [pod okapem](application-proxy-security.md#under-the-hood) .

### <a name="application-proxy-connectors"></a>Łączniki serwera proxy aplikacji

[Łączniki serwera proxy aplikacji](application-proxy-connectors.md) to lekkie agenci wdrożoni lokalnie, które ułatwiają połączenie wychodzące z usługą serwera proxy aplikacji w chmurze. Łączniki muszą być zainstalowane na serwerze z systemem Windows, który ma dostęp do aplikacji zaplecza. Użytkownicy łączą się z usługą proxy aplikacji w chmurze, która kieruje ruch do aplikacji za pośrednictwem łączników, jak pokazano poniżej.

![Połączenia sieciowe w usłudze Azure serwer proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Konfiguracja i rejestracja między łącznikiem a usługą serwera proxy aplikacji jest realizowana w następujący sposób:

1. Administrator IT otwiera porty 80 i 443 dla ruchu wychodzącego i zezwala na dostęp do kilku adresów URL, które są niezbędne przez łącznik, usługę serwera proxy aplikacji i usługi Azure AD.
2. Administrator loguje się do Azure Portal i uruchamia plik wykonywalny, aby zainstalować łącznik na lokalnym serwerze Windows.
3. Łącznik zaczyna się od "Listen" do usługi serwera proxy aplikacji.
4. Administrator dodaje aplikację lokalną do usługi Azure AD i konfiguruje ustawienia, takie jak adresy URL, które użytkownicy muszą połączyć ze swoimi aplikacjami.

Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).

Zalecane jest, aby zawsze wdrażać wiele łączników w celu zapewnienia nadmiarowości i skalowania. Łączniki, w połączeniu z usługą, zapoznają się ze wszystkimi zadaniami wysokiej dostępności i mogą być dynamicznie dodawane lub usuwane. Za każdym razem, gdy nowe żądanie dociera do jednego z dostępnych łączników. Gdy łącznik jest uruchomiony, pozostaje aktywny, gdy łączy się z usługą. Jeśli łącznik jest tymczasowo niedostępny, nie odpowiada na ten ruch. Nieużywane łączniki są oznaczane jako nieaktywne i usuwane po 10 dniach braku aktywności.

Łączniki również sonduje serwer, aby sprawdzić, czy jest dostępna nowsza wersja łącznika. Mimo że można wykonać aktualizację ręczną, łączniki będą automatycznie aktualizowane, o ile usługa Aktualizator łączników serwera proxy aplikacji jest uruchomiona. W przypadku dzierżaw z wieloma łącznikami aktualizacje automatyczne są przeznaczone dla jednego łącznika na raz w każdej grupie, aby zapobiec przestojom w danym środowisku.

**Uwaga**: na [stronie historia wersji](application-proxy-release-version-history.md) serwera proxy aplikacji można monitorować powiadomienia o wydaniu aktualizacji przez subskrybowanie jej kanału informacyjnego RSS.

Każdy łącznik serwera proxy aplikacji jest przypisany do [grupy łączników](application-proxy-connector-groups.md). Łączniki w tej samej grupie łączników działają jako pojedyncza jednostka w celu zapewnienia wysokiej dostępności i równoważenia obciążenia. Można tworzyć nowe grupy, przypisywać do nich łączniki w Azure Portal, a następnie przypisywać określone łączniki do obsłużenia określonych aplikacji. Zalecane jest posiadanie co najmniej dwóch łączników w każdej grupie łączników w celu zapewnienia wysokiej dostępności.

Grupy łączników są przydatne, gdy konieczne jest wsparcie następujących scenariuszy:

* Publikowanie aplikacji geograficznej
* Segmentacja/izolacja aplikacji
* Publikowanie aplikacji sieci Web działających w chmurze lub lokalnie

Aby uzyskać więcej informacji na temat wybierania miejsca instalacji łączników i optymalizowania sieci, zobacz [zagadnienia dotyczące topologii sieci podczas korzystania z serwer proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Inne przypadki użycia

Do tego momentu firma Microsoft koncentruje się na używaniu serwera proxy aplikacji do publikowania aplikacji lokalnych na zewnątrz podczas włączania logowania jednokrotnego do wszystkich aplikacji w chmurze i lokalnych. Istnieją jednak inne przypadki użycia dla serwera proxy aplikacji, który jest cenny. Obejmują one:

* **Bezpieczne publikowanie interfejsów API REST**. Jeśli masz logikę biznesową lub interfejsy API działające lokalnie lub hostowane na maszynach wirtualnych w chmurze, serwer proxy aplikacji udostępnia publiczny punkt końcowy do uzyskiwania dostępu do interfejsu API. Dostęp do punktu końcowego interfejsu API umożliwia sterowanie uwierzytelnianiem i autoryzacją bez konieczności używania portów przychodzących. Zapewnia ona dodatkowe zabezpieczenia za pośrednictwem funkcji Azure AD — wersja Premium, takich jak uwierzytelnianie wieloskładnikowe i dostęp warunkowy oparty na urządzeniach dla komputerów stacjonarnych, urządzeń z systemem iOS, MAC i Android przy użyciu usługi Intune. Aby dowiedzieć się więcej, zobacz [jak włączyć natywne aplikacje klienckie do współdziałania z aplikacjami proxy](application-proxy-configure-native-client-application.md) i [chronić interfejs API przy użyciu protokołu OAuth 2,0 z Azure Active Directory i API Management](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Usługi pulpitu zdalnego** **(RDS)**. Standardowe wdrożenia usług RDS wymagają otwartych połączeń przychodzących. Jednak [wdrożenie usług pulpitu zdalnego z serwerem proxy aplikacji](application-proxy-integrate-with-remote-desktop-services.md) ma trwałe połączenie wychodzące z serwera, na którym działa usługa łącznika. W ten sposób możesz zaoferować więcej aplikacji użytkownikom końcowym, publikując aplikacje lokalne za pomocą Usługi pulpitu zdalnego. Możesz również zmniejszyć powierzchnię ataku wdrożenia z ograniczonym zestawem weryfikacji dwuetapowej i kontroli dostępu warunkowego do usług pulpitu zdalnego.
* **Publikuj aplikacje, które łączą się przy użyciu usługi WebSockets**. Pomoc techniczna z [Qlik sensem](application-proxy-qlik.md) jest w publicznej wersji zapoznawczej i zostanie rozszerzona na inne aplikacje w przyszłości.
* **Włącz natywne aplikacje klienckie do współpracy z aplikacjami proxy**. Za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD można publikować aplikacje sieci Web, ale mogą one również służyć do publikowania [natywnych aplikacji klienckich](application-proxy-configure-native-client-application.md) , które są skonfigurowane przy użyciu biblioteki Azure AD Authentication Library (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci Web, ponieważ są one instalowane na urządzeniu, podczas gdy aplikacje sieci Web są dostępne za pośrednictwem przeglądarki.

## <a name="conclusion"></a>Podsumowanie

Sposób pracy i używane przez nas narzędzia zmieniają się szybko. Dzięki większej liczbie pracowników, którzy pracują z własnymi urządzeniami i korzystają z zalet aplikacji typu "oprogramowanie jako usługa" (SaaS), sposób, w jaki organizacje zarządzają i zabezpieczają swoje dane, również muszą rozwijać. Firmy nie działają już wyłącznie w obrębie własnych ścian, chroniąc je za pomocą Moat otaczającej ich obramowanie. Dane są przesyłane do większej liczby lokalizacji niż kiedykolwiek wcześniej — zarówno w środowiskach lokalnych, jak i w chmurze. Ta ewolucja umożliwiła zwiększenie produktywności i możliwości współpracy użytkowników, ale chroni również poufne dane.

Bez względu na to, czy obecnie korzystasz z usługi Azure AD do zarządzania użytkownikami w scenariuszu hybrydowego współistnienia, czy interesuje się rozpoczęcie podróży do chmury, wdrożenie usługi Azure serwer proxy aplikacji usługi Azure AD może pomóc w zmniejszeniu rozmiaru lokalnego zasięgu, zapewniając dostęp zdalny jako usługę.

Organizacje powinny zacząć korzystać z serwera proxy aplikacji już dziś, aby skorzystać z następujących korzyści:

* Publikowanie aplikacji lokalnych zewnętrznie bez narzutu związanego z utrzymywaniem tradycyjnej sieci VPN lub innych rozwiązań do publikowania w sieci Web i podejścia strefy DMZ
* Logowanie jednokrotne do wszystkich aplikacji, ich Microsoft 365 lub innych aplikacji SaaS, w tym aplikacji lokalnych
* Zabezpieczenia w skali chmury, w których usługa Azure AD wykorzystuje Microsoft 365 telemetrii, aby zapobiec nieautoryzowanemu dostępowi
* Integracja usługi Intune w celu zagwarantowania, że ruch firmowy jest uwierzytelniany
* Scentralizowanie zarządzania kontami użytkowników
* Aktualizacje automatyczne, aby upewnić się, że masz najnowsze poprawki zabezpieczeń
* Nowe funkcje po ich wydaniu; Najnowsza obsługa logowania jednokrotnego w protokole SAML i bardziej szczegółowe zarządzanie plikami cookie aplikacji

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat planowania, działania i zarządzania usługą Azure serwer proxy aplikacji usługi Azure AD, zobacz [Planowanie wdrożenia usługi azure serwer proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).
* Aby zaplanować wersję demonstracyjną na żywo lub uzyskać bezpłatną 90-dniową wersję próbną do oceny, zobacz [wprowadzenie do Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).

---
title: Planowanie wdrożenia Azure Active Directory serwer proxy aplikacji wdrożenia
description: End-to-end guide for planning the deployment of Application proxy within your organization (Przewodnik po planowaniu wdrożenia serwera proxy aplikacji w organizacji)
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: b0a3653d2cc840d745b1bb5788406b8d374c76d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533784"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planowanie wdrożenia serwera proxy aplikacji usługi Azure AD

Azure Active Directory (Azure AD) serwer proxy aplikacji to bezpieczne i ekonomiczne rozwiązanie dostępu zdalnego dla aplikacji lokalnych. Zapewnia natychmiastową ścieżkę przejścia dla organizacji korzystających z chmury w celu zarządzania dostępem do starszych aplikacji lokalnych, które nie są jeszcze w stanie korzystać z nowoczesnych protokołów. Aby uzyskać dodatkowe informacje wprowadzające, zobacz [Co to jest serwer proxy aplikacji](./application-proxy.md).

serwer proxy aplikacji jest zalecane w przypadku dawania użytkownikom zdalnym dostępu do zasobów wewnętrznych. serwer proxy aplikacji zastępuje potrzebę sieci VPN lub zwrotnego serwera proxy dla tych przypadków użycia dostępu zdalnego. Nie jest przeznaczony dla użytkowników, którzy znajdują się w sieci firmowej. Ci użytkownicy, którzy używają serwer proxy aplikacji do uzyskiwania dostępu do intranetu, mogą wystąpić niepożądane problemy z wydajnością.

Ten artykuł zawiera zasoby potrzebne do planowania, obsługi i zarządzania usługą Azure AD serwer proxy aplikacji.

## <a name="plan-your-implementation"></a>Planowanie implementacji

W poniższej sekcji przedstawiono szeroki widok kluczowych elementów planowania, które skonfigurują cię w celu wydajnego wdrażania.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem implementacji należy spełnić następujące wymagania wstępne. Więcej informacji na temat konfigurowania środowiska, w tym wymagań wstępnych, można znaleźć w tym [samouczku.](application-proxy-add-on-premises-application.md)

* **Łączniki:** Łączniki to lekkie agenty, których można wdrożyć w:
   * Sprzęt fizyczny w środowisku lokalnym
   * Maszyna wirtualna hostowana w ramach dowolnego rozwiązania funkcji hypervisor
   * Maszyna wirtualna hostowana na platformie Azure w celu włączenia połączenia wychodzącego do serwer proxy aplikacji wirtualnej.

* Zobacz [Omówienie aplikacja usługi Azure AD łączników serwera proxy,](application-proxy-connectors.md) aby uzyskać bardziej szczegółowe omówienie.

     * Przed zainstalowaniem [łączników należy włączyć obsługę usługi TLS 1.2](application-proxy-add-on-premises-application.md) na maszynach łączników.

     * Jeśli to możliwe, należy wdrożyć łączniki w tej [samej sieci](application-proxy-network-topology.md) i segmencie co serwery aplikacji internetowych na zadomowieniach. Najlepiej wdrożyć łączniki po ukończeniu odnajdywania aplikacji.
     * Zalecamy, aby każda grupa łączników ma co najmniej dwa łączniki w celu zapewnienia wysokiej dostępności i skali. Posiadanie trzech łączników jest optymalne w przypadku, gdy w dowolnym momencie może być konieczne serwisienie maszyny. Przejrzyj [tabelę pojemności łącznika,](./application-proxy-connectors.md#capacity-planning) aby ułatwić podjęcie decyzji o typie maszyny, na której mają być zainstalowane łączniki. Im większa maszyna, tym większy będzie bufor i będzie działać łącznik.

* **Ustawienia dostępu do sieci:** łączniki usługi Azure AD serwer proxy aplikacji łączą się z platformą Azure za pośrednictwem [protokołu HTTPS (port TCP 443) i protokołu HTTP (port TCP 80).](application-proxy-add-on-premises-application.md)

   * Przerywanie ruchu TLS łącznika nie jest obsługiwane i uniemożliwi łącznikom ustanawianie bezpiecznego kanału z odpowiednimi punktami końcowymi serwera proxy aplikacji platformy Azure.

   * Unikaj wszystkich form wbudowanej inspekcji wychodzącej komunikacji TLS między łącznikami a platformą Azure. Inspekcja wewnętrzna między łącznikiem a aplikacjami zaplecza jest możliwa, ale może obniżyć jakość obsługi użytkownika i w związku z tym nie jest zalecana.

   * Równoważenie obciążenia samych łączników również nie jest obsługiwane, a nawet konieczne.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Ważne zagadnienia przed skonfigurowaniem usługi Azure AD serwer proxy aplikacji

Aby skonfigurować i wdrożyć usługę Azure AD, należy spełnić następujące podstawowe serwer proxy aplikacji.

*  **Dołączanie do platformy Azure:** przed wdrożeniem serwera proxy aplikacji tożsamości użytkowników muszą być synchronizowane z katalogu lokalnego lub tworzone bezpośrednio w dzierżawach usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnianie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji serwera proxy aplikacji oraz uzyskanie informacji o identyfikatorze użytkownika niezbędnych do wykonania logowania jednokrotnego.

* **Wymagania dotyczące dostępu warunkowego:** nie zalecamy używania usługi serwer proxy aplikacji do uzyskiwania dostępu do intranetu, ponieważ zwiększa to opóźnienie, które będzie miało wpływ na użytkowników. Zalecamy używanie serwer proxy aplikacji z uwierzytelnianiem wstępnym i zasadami dostępu warunkowego w celu uzyskania dostępu zdalnego z Internetu.  Podejściem do zapewnienia dostępu warunkowego do użytku w intranecie jest modernizacja aplikacji, aby można było bezpośrednio uwierzytelniać je w u usługi AAD. Aby uzyskać więcej informacji, zapoznaj się z tematem Resources [for migrating applications to AAD (Zasoby](./migration-resources.md) dotyczące migrowania aplikacji do tej aplikacji).

* **Limity** usługi: aby zapewnić ochronę przed zbytnią wydajnością zasobów poszczególnych dzierżawców, istnieją limity ograniczania przepustowości ustawione dla poszczególnych aplikacji i dzierżawców. Aby wyświetlić te limity, zobacz [Azure AD service limits and restrictions (Limity](../enterprise-users/directory-service-limits-restrictions.md)i ograniczenia usługi Azure AD). Te limity ograniczania są oparte na testach porównawczych znacznie powyżej typowego woluminu użycia i zapewniają dużych bufor dla większości wdrożeń.

* **Certyfikat publiczny:** jeśli używasz niestandardowych nazw domen, musisz pozyskać certyfikat TLS/SSL. W zależności od wymagań organizacji uzyskanie certyfikatu może trochę potrwać i zalecamy jak najwcześniejsze rozpoczęcie procesu. Usługa Azure serwer proxy aplikacji obsługuje certyfikaty standardowe, [wieloznaczne](application-proxy-wildcard.md)lub oparte na sieci SAN. Aby uzyskać więcej informacji, [zobacz Konfigurowanie domen niestandardowych za pomocą usługi Azure AD serwer proxy aplikacji](application-proxy-configure-custom-domain.md).

* **Wymagania** dotyczące domeny: logowanie się do opublikowanych aplikacji przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) wymaga, aby serwer z uruchomionym łącznikiem i serwer z aplikacją był przyłączony do domeny i był częścią tej samej domeny lub domen ufających.
Aby uzyskać szczegółowe informacje na ten temat, zobacz [KCD for single sign-on](application-proxy-configure-single-sign-on-with-kcd.md) with serwer proxy aplikacji (KCD for single sign-on with serwer proxy aplikacji (KCD — KCD for single sign-on with serwer proxy aplikacji (KCD — logowanie serwer proxy aplikacji). Usługa łącznika działa w kontekście systemu lokalnego i nie powinna być skonfigurowana do używania tożsamości niestandardowej.

* **Rekordy DNS dla adresów URL**

   * Przed użyciem domen niestandardowych w serwer proxy aplikacji należy utworzyć rekord CNAME w publicznym systemie DNS, umożliwiając klientom rozpoznawanie niestandardowego zdefiniowanego zewnętrznego adresu URL do wstępnie zdefiniowanego serwer proxy aplikacji adresu. Nie można utworzyć rekordu CNAME dla aplikacji, która używa domeny niestandardowej, uniemożliwi użytkownikom zdalnym łączenie się z aplikacją. Kroki wymagane do dodawania rekordów CNAME mogą się różnić w zależności od dostawcy DNS, więc dowiedz się, jak zarządzać rekordami i zestawami rekordów DNS przy użyciu [Azure Portal](../../dns/dns-operations-recordsets-portal.md).

   * Podobnie hosty łączników muszą być w stanie rozpoznać wewnętrzny adres URL publikowanych aplikacji.

* **Prawa administracyjne i role**

   * **Instalacja łącznika** wymaga uprawnień administratora lokalnego do serwera z systemem Windows, na którym jest instalowany. Wymaga to również co najmniej roli *administratora aplikacji* do uwierzytelniania i rejestrowania wystąpienia łącznika w dzierżawie usługi Azure AD.

   * **Publikowanie i administrowanie aplikacją** wymaga *roli Administratora* aplikacji. Administratorzy aplikacji mogą zarządzać wszystkimi aplikacjami w katalogu, w tym rejestracjami, ustawieniami logowania jednokrotnego, przypisaniami użytkowników i grup oraz licencjonowaniem, serwer proxy aplikacji ustawieniami rejestracji oraz zgodą. Nie zapewnia możliwości zarządzania dostępem warunkowym. Rola *administratora aplikacji w* chmurze ma wszystkie możliwości administratora aplikacji, z tą różnicą, że nie zezwala na zarządzanie serwer proxy aplikacji ustawieniami.

* **Licencjonowanie:** serwer proxy aplikacji jest dostępna za pośrednictwem Azure AD — wersja Premium subskrypcji. Pełną listę [opcji Azure Active Directory licencjonowania można znaleźć](https://azure.microsoft.com/pricing/details/active-directory/) na stronie cennika.

### <a name="application-discovery"></a>Odnajdywanie aplikacji

Skompiluj spis wszystkich aplikacji w zakresie publikowanych za pośrednictwem serwer proxy aplikacji, zbierając następujące informacje:

| Typ informacji| Informacje do zebrania |
|---|---|
| Typ usługi| Na przykład: SharePoint, SAP, CRM, Niestandardowa aplikacja internetowa, Interfejs API |
| Platforma aplikacji | Na przykład: Windows IIS, Apache on Linux, Tomcat, NGINX |
| Członkostwo w domenie| W pełni kwalifikowana nazwa domeny (FQDN) serwera sieci Web |
| Lokalizacja aplikacji | Gdzie serwer sieci Web lub farma znajduje się w infrastrukturze |
| Dostęp wewnętrzny | Dokładny adres URL używany podczas uzyskiwania dostępu do aplikacji wewnętrznie. <br> Jeśli farma, jakiego typu równoważenia obciążenia jest w użyciu? <br> Określa, czy aplikacja pobiera zawartość ze źródeł innych niż sama aplikacja.<br> Ustal, czy aplikacja działa za pośrednictwem sieci WebSocket. |
| Dostęp zewnętrzny | Rozwiązanie dostawcy, dla których aplikacja jest już narażona na działanie zewnętrzne. <br> Adres URL, którego chcesz użyć na użytek dostępu zewnętrznego. Jeśli program SharePoint, upewnij się, że alternatywne mapowania dostępu są skonfigurowane na te [wskazówki.](/SharePoint/administration/configure-alternate-access-mappings) Jeśli nie, należy zdefiniować zewnętrzne adresy URL. |
| Certyfikat publiczny | W przypadku korzystania z domeny niestandardowej należy pozyskać certyfikat z odpowiednią nazwą podmiotu. Jeśli certyfikat istnieje, zanotuj numer seryjny i lokalizację, z której można go uzyskać. |
| Typ uwierzytelniania| Typ uwierzytelniania obsługiwany przez obsługę aplikacji, taki jak Podstawowe, Uwierzytelnianie integracji systemu Windows, oparte na formularzach, oparte na nagłówku i oświadczenia. <br>Jeśli aplikacja jest skonfigurowana do uruchamiania w ramach określonego konta domeny, zanotuj w pełni kwalifikowaną nazwę domeny (FQDN) konta usługi.<br> Jeśli jest to oparte na saml, identyfikator i adresy URL odpowiedzi. <br> Jeśli jest oparty na nagłówku, rozwiązanie dostawcy i określone wymaganie dotyczące obsługi typu uwierzytelniania. |
| Nazwa grupy łączników | Nazwa logiczna grupy łączników, która zostanie wyznaczona w celu zapewnienia połączenia i logowania jednokrotnego do tej aplikacji zaplecza. |
| Dostęp użytkowników/grup | Użytkownicy lub grupy użytkowników, którzy uzyskają dostęp zewnętrzny do aplikacji. |
| Wymagania dodatkowe | Zwróć uwagę na wszelkie dodatkowe wymagania dotyczące dostępu zdalnego lub zabezpieczeń, które należy wziąć pod uwagę podczas publikowania aplikacji. |

Możesz pobrać ten arkusz kalkulacyjny [spisu aplikacji,](https://aka.ms/appdiscovery) aby utworzyć spis aplikacji.

### <a name="define-organizational-requirements"></a>Definiowanie wymagań organizacyjnych

Poniżej przedstawiono obszary, dla których należy zdefiniować wymagania biznesowe organizacji. Każdy obszar zawiera przykłady wymagań

 **Dostęp**

* Użytkownicy zdalni z urządzeniami przyłączonych do domeny lub urządzeniami przyłączonych do usługi Azure AD mogą bezpiecznie uzyskać dostęp do opublikowanych aplikacji za pomocą bezproblemowego logowania jednokrotnego.

* Użytkownicy zdalni z zatwierdzonymi urządzeniami osobistymi mogą bezpiecznie uzyskać dostęp do opublikowanych aplikacji, pod warunkiem że są zarejestrowani w usłudze MFA i zarejestrują aplikację Microsoft Authenticator na telefonie komórkowym jako metodę uwierzytelniania.

**Nadzór**

* Administratorzy mogą definiować i monitorować cykl życia przypisań użytkowników do aplikacji publikowanych za pośrednictwem serwer proxy aplikacji.

**Bezpieczeństwo**

* Tylko użytkownicy przypisani do aplikacji za pośrednictwem członkostwa w grupie lub indywidualnie mogą uzyskać dostęp do tych aplikacji.

**Wydajność**

* Nie ma spadku wydajności aplikacji w porównaniu do uzyskiwania dostępu do aplikacji z sieci wewnętrznej.

**Środowisko użytkownika**

* Użytkownicy wiedzą, jak uzyskać dostęp do swoich aplikacji przy użyciu znanych firmowych adresów URL na dowolnej platformie urządzeń.

**Inspekcja**
* Administratorzy mogą inspekcji działań dostępu użytkowników.


### <a name="best-practices-for-a-pilot"></a>Najlepsze rozwiązania dotyczące pilotażu

Określ ilość czasu i nakładu pracy potrzebnego do pełnego zlecić jednej aplikacji na potrzeby dostępu zdalnego przy użyciu logowania jednokrotnego( SSO). W tym celu należy wykonać pilotaż, który uwzględnia początkowe odnajdywanie, publikowanie i testowanie ogólne. Użycie prostej aplikacji internetowej opartej na usługach IIS, która jest już wstępnie skonfigurowana dla usługi Integrated Windows Authentication (IWA), pomoże utworzyć punkt odniesienia, ponieważ ta konfiguracja wymaga minimalnego nakładu pracy w celu pomyślnego pilotażu dostępu zdalnego i logowania jednokrotnego.

Następujące elementy projektu powinny zwiększyć powodzenie wdrożenia pilotażowego bezpośrednio w dzierżawie produkcyjnej.

**Zarządzanie łącznikami:**

* Łączniki odgrywają kluczową rolę w dostarczaniu lokalnego połączenia z aplikacjami. Użycie **domyślnej grupy** łączników jest odpowiednie do wstępnego testowania pilotażowego opublikowanych aplikacji przed ich zlecenie do produkcji. Pomyślnie przetestowane aplikacje można następnie przenieść do grup łączników produkcyjnych.

**Zarządzanie aplikacją:**

* Pracownicy najprawdopodobniej zapamiętają, że zewnętrzny adres URL jest znany i odpowiedni. Unikaj publikowania aplikacji przy użyciu wstępnie zdefiniowanych msappproxy.net lub onmicrosoft.com sufiksów. Zamiast tego podaj znajomą zweryfikowaną domenę najwyższego poziomu poprzedzoną nazwą hosta logicznego, taką jak *intranet.<customers_domain>.com.*

* Ogranicz widoczność ikony aplikacji pilotażowej do grupy pilotażowej, ukrywając jej ikonę startu w portalu Azure MyApps. Gdy aplikacja jest gotowa do produkcji, można określić jej zakres dla odpowiednich odbiorców docelowych w tej samej dzierżawie przedprodukcyjną lub przez opublikowanie aplikacji w dzierżawie produkcyjnej.

**Ustawienia logowania jednokrotnego:** Niektóre ustawienia logowania jednokrotnego mają określone zależności, które mogą zająć trochę czasu, dlatego należy unikać opóźnień kontroli zmian, zapewniając, że zależności zostaną rozwiązane z wyprzedzeniem. Obejmuje to dołączanie hostów łącznika do wykonywania logowania jednokrotnego przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) i wykonywania innych czasochłonnych działań. Na przykład konfigurowanie wystąpienia programu PING Access, jeśli potrzebne jest logowanie jednokrotne oparte na nagłówku.

**Zabezpieczenia TLS między hostem łącznika** i aplikacją docelową: bezpieczeństwo jest najważniejsze, dlatego należy zawsze używać zabezpieczeń TLS między hostem łącznika a aplikacjami docelowymi. Szczególnie jeśli aplikacja internetowa jest skonfigurowana do uwierzytelniania opartego na formularzach (FBA), ponieważ poświadczenia użytkownika są następnie skutecznie przesyłane w postaci uwierzytelniania w postaci pospolitej.

**Implementuj przyrostowo i testuj każdy krok.**
Po opublikowaniu aplikacji przećwczaj podstawowe testy funkcjonalne, aby upewnić się, że wszystkie wymagania dotyczące użytkowników i firm zostały spełnione, zgodnie z poniższymi instrukcjami:

1. Przetestuj i zweryfikuj ogólny dostęp do aplikacji internetowej z wyłączonym uwierzytelnianiem wstępnym.
2. Jeśli to się powiedzie, włącz uwierzytelnianie wstępne i przypisz użytkowników i grupy. Przetestuj i zweryfikuj dostęp.
3. Następnie dodaj metodę logowania jednokrotnego dla aplikacji i ponownie przetestuj ją, aby zweryfikować dostęp.
4. Zastosuj zasady dostępu warunkowego i uwierzytelniania wieloskładnikowego zgodnie z potrzebami. Przetestuj i zweryfikuj dostęp.

**Narzędzia do rozwiązywania** problemów: Podczas rozwiązywania problemów zawsze rozpocznij od sprawdzania dostępu do opublikowanej aplikacji z przeglądarki na hoście łącznika i upewnij się, że aplikacja działa zgodnie z oczekiwaniami. Prostsza konfiguracja, łatwiejsza do określenia głównej przyczyny, dlatego rozważ odtworzenie problemów z minimalną konfiguracją, takich jak używanie tylko jednego łącznika i bez logowania jednokrotnego. W niektórych przypadkach narzędzia do debugowania w Internecie, takie jak Fiddler firmy Telerik, mogą okazać się niezbędne do rozwiązywania problemów z dostępem lub zawartością w aplikacjach, do których dostęp jest uzyskiwany za pośrednictwem serwera proxy. Program Fiddler może również działać jako serwer proxy, aby ułatwić śledzenie i debugowanie ruchu dla platform mobilnych, takich jak iOS i Android, oraz praktycznie wszystkiego, co można skonfigurować do przekierowywu za pośrednictwem serwera proxy. Aby uzyskać więcej [informacji, zobacz przewodnik](application-proxy-troubleshoot.md) rozwiązywania problemów.

## <a name="implement-your-solution"></a>Implementowanie rozwiązania

### <a name="deploy-application-proxy"></a>Wdrażanie serwer proxy aplikacji

Kroki wdrażania aplikacji serwer proxy aplikacji zostały opisane w tym samouczku dotyczącym dodawania aplikacji [lokalnej w celu uzyskania dostępu zdalnego.](application-proxy-add-on-premises-application.md) Jeśli instalacja nie powiedzie się, wybierz pozycję Rozwiązywanie problemów serwer proxy aplikacji w portalu lub skorzystaj z przewodnika rozwiązywania problemów w przypadku problemów z instalowaniem łącznika [serwer proxy aplikacji Agent Connector.](application-proxy-connector-installation-problem.md) 

### <a name="publish-applications-via-application-proxy"></a>Publikowanie aplikacji za pośrednictwem serwer proxy aplikacji

Publikowanie aplikacji zakłada, że spełniliśmy wszystkie wymagania wstępne i że masz kilka łączników, które są wyświetlane jako zarejestrowane i aktywne na serwer proxy aplikacji aplikacji.

Aplikacje można również publikować przy użyciu [programu PowerShell.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)

Poniżej przedstawiono kilka najlepszych rozwiązań, które należy stosować podczas publikowania aplikacji:

* **Użyj grup łączników:** przypisz grupę łączników wyznaczoną do publikowania poszczególnych aplikacji. Zalecamy, aby każda grupa łączników ma co najmniej dwa łączniki w celu zapewnienia wysokiej dostępności i skali. Posiadanie trzech łączników jest optymalne w przypadku, gdy w dowolnym momencie może być konieczne serwisienie maszyny. Ponadto zobacz [](application-proxy-connector-groups.md) Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników, aby zobaczyć, jak można również używać grup łączników do segmentacji łączników według sieci lub lokalizacji.

* **Ustaw limit czasu aplikacji zaplecza:** to ustawienie jest przydatne w scenariuszach, w których przetwarzanie transakcji klienta przez aplikację może trwać dłużej niż 75 sekund. Na przykład gdy klient wysyła zapytanie do aplikacji internetowej, która działa jako frontonie do bazy danych. Fronteń wysyła to zapytanie do serwera bazy danych swojej bazy danych na zadomowień i czeka na odpowiedź, ale do czasu otrzymania odpowiedzi po stronie klienta w konwersacji urócą przekrój czasu. Ustawienie limitu czasu na Long zapewnia 180 sekund na zakończenie dłuższych transakcji.

* **Używanie odpowiednich typów plików cookie**

   * **Plik cookie tylko dla protokołu HTTP:** zapewnia dodatkowe zabezpieczenia dzięki serwer proxy aplikacji flagi HTTPOnly w nagłówkach odpowiedzi HTTP set-cookie. To ustawienie pomaga ograniczyć wykorzystanie luk w zabezpieczeniach, takich jak skrypty między witrynami (XSS). Pozostaw wartość Nie dla klientów/agentów użytkowników, którzy wymagają dostępu do pliku cookie sesji. Na przykład klient RDP/MTSC łączący się z bramą usługi Pulpit zdalny opublikowany za pośrednictwem serwera proxy aplikacji.

   * **Bezpieczny plik cookie:** jeśli plik cookie zostanie ustawiony przy użyciu atrybutu Secure, agent użytkownika (aplikacja po stronie klienta) będzie dołączać plik cookie do żądań HTTP tylko wtedy, gdy żądanie zostanie przesłane za pośrednictwem kanału zabezpieczonego przez protokół TLS. Pomaga to zmniejszyć ryzyko naruszenia plików cookie za pośrednictwem kanałów w czytelnym tekście, dlatego należy je włączyć.

   * **Trwały plik cookie:** umożliwia serwer proxy aplikacji cookie sesji między zamknięciami przeglądarki przez pozostałą ważność, dopóki nie wygaśnie lub nie zostanie usunięty. Używany w scenariuszach, w których bogata aplikacja, taka jak office, uzyskuje dostęp do dokumentu w opublikowanej aplikacji internetowej, bez ponownego monitowania użytkownika o uwierzytelnienie. Należy jednak zachować ostrożność, ponieważ trwałe pliki cookie mogą ostatecznie pozostawić usługę na ryzyko nieautoryzowanego dostępu, jeśli nie są używane w połączeniu z innymi kontrolkami wyrównowania. To ustawienie powinno być używane tylko w przypadku starszych aplikacji, które nie mogą udostępniać plików cookie między procesami. Lepszym rozwiązaniem jest zaktualizowanie aplikacji w celu obsługi plików cookie udostępniania między procesami zamiast używania tego ustawienia.

* **Przetłumacz adresy URL** w nagłówkach: włącz tę opcję dla scenariuszy, w których nie można skonfigurować wewnętrznego systemu DNS tak, aby dopasować go do publicznej przestrzeni nazw organizacji (czyli split DNS). Jeśli aplikacja nie wymaga oryginalnego nagłówka hosta w żądaniu klienta, pozostaw tę wartość ustawioną na Tak. Alternatywnie łącznik używa nazwy FQDN w wewnętrznym adresie URL do routingu rzeczywistego ruchu i nazwy FQDN w zewnętrznym adresie URL jako nagłówka hosta. W większości przypadków ta alternatywa powinna umożliwić aplikacji normalne działanie, gdy jest uzyskiwany dostęp zdalny, ale użytkownicy tracą korzyści wynikające z dopasowania wewnątrz & adresu URL.

* **Przetłumacz adresy URL** w treści aplikacji: włącz tłumaczenie linków treści aplikacji dla aplikacji, jeśli chcesz, aby linki z tej aplikacji były tłumaczone w odpowiedziach z powrotem na klienta. Jeśli ta funkcja jest włączona, ta funkcja zapewnia najlepszą próbę tłumaczenia wszystkich linków wewnętrznych, które serwer proxy aplikacji znajduje w odpowiedziach HTML i CSS zwracanych do klientów. Jest to przydatne w przypadku publikowania aplikacji, które zawierają w zawartości zakodowane linki bezwzględne lub skrócone nazwy NetBIOS, lub aplikacji z zawartością, która zawiera linki do innych aplikacji lokalnych.

W scenariuszach, w których opublikowana aplikacja łączy się z innymi opublikowanymi aplikacjami, włącz tłumaczenie linków dla każdej aplikacji, aby mieć kontrolę nad środowisko użytkownika na poziomie poszczególnych aplikacji.

Załóżmy na przykład, że masz trzy aplikacje opublikowane za pośrednictwem usługi serwer proxy aplikacji, które są ze sobą linkami: Korzyści, Wydatki i Podróż oraz czwarta aplikacja Opinia, która nie jest opublikowana za pośrednictwem serwer proxy aplikacji.

![Ilustracja 1](media/App-proxy-deployment-plan/link-translation.png)

Po włączeniu tłumaczenia linków dla aplikacji Korzyści linki do usług Expenses i Travel są przekierowywany do zewnętrznych adresów URL dla tych aplikacji, dzięki czemu użytkownicy korzystający z aplikacji spoza sieci firmowej mogą uzyskać do nich dostęp. Linki z funkcji Wydatki i Podróż do strony Korzyści nie działają, ponieważ tłumaczenie linków nie zostało włączone dla tych dwóch aplikacji. Link do opinii nie jest przekierowywany, ponieważ nie ma zewnętrznego adresu URL, dlatego użytkownicy korzystający z aplikacji Korzyści nie będą mogli uzyskać dostępu do aplikacji opinii spoza sieci firmowej. Zobacz szczegółowe informacje na [temat tłumaczenia linków i innych opcji przekierowania.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Uzyskiwanie dostępu do aplikacji

Istnieje kilka opcji zarządzania dostępem do opublikowanych zasobów serwera proxy aplikacji, więc wybierz najbardziej odpowiednie dla danego scenariusza i potrzeb w zakresie skalowalności. Typowe podejścia obejmują: używanie grup lokalnych synchronizowanych za pośrednictwem programu Azure AD Connect, tworzenie grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika, używanie grup samoobsługi zarządzanych przez właściciela zasobu lub kombinację tych wszystkich. Zapoznaj się z połączonymi zasobami, aby uzyskać informacje o zaletach każdego z nich.

Najbardziej prosty sposób przypisywania użytkownikom dostępu do aplikacji  to dostęp do opcji Użytkownicy i grupy w okienku po lewej stronie opublikowanej aplikacji i bezpośrednie przypisywanie grup lub osób.

![Ilustracja 24](media/App-proxy-deployment-plan/add-user.png)

Możesz również zezwolić użytkownikom na samoobsługowy dostęp do aplikacji, przypisując grupę, do których nie są obecnie członkami, i konfigurując opcje samoobsługi.

![Ilustracja 25](media/App-proxy-deployment-plan/allow-access.png)

Jeśli ta opcja jest włączona, użytkownicy będą mogli zalogować się do portalu MyApps i zażądać dostępu, a także zostać automatycznie zatwierdzeni i dodani do już dozwolonej grupy samoobsługi lub potrzebują zatwierdzenia od wyznaczonej osoby zatwierdzającej.

Użytkowników-gości można również zaprosić do uzyskiwania dostępu do aplikacji wewnętrznych opublikowanych za [pośrednictwem serwer proxy aplikacji za pośrednictwem usługi Azure AD B2B.](../external-identities/add-users-information-worker.md)

W przypadku aplikacji lokalnych, które są zwykle dostępne anonimowo i nie wymagają uwierzytelniania, można wyłączyć opcję znajdującą się w oknie **Właściwości aplikacji.**

![Ilustracja 26](media/App-proxy-deployment-plan/assignment-required.png)


Pozostawienie tej opcji na wartość Nie umożliwia użytkownikom dostęp do aplikacji lokalnej za pośrednictwem serwera aplikacja usługi Azure AD proxy bez uprawnień, dlatego należy zachować ostrożność.

Po opublikowaniu aplikacja powinna być dostępna, wpisując jej zewnętrzny adres URL w przeglądarce lub za pomocą ikony pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Włączanie uwierzytelniania wstępnego

Sprawdź, czy aplikacja jest dostępna za pośrednictwem serwer proxy aplikacji uzyskać do jej dostępu za pośrednictwem zewnętrznego adresu URL.

1. Przejdź do **Azure Active Directory**  >  **dla przedsiębiorstw** Wszystkie  >  **aplikacje** i wybierz aplikację, którą chcesz zarządzać.

2. Wybierz **serwer proxy aplikacji**.

3. W polu **Wstępne uwierzytelnianie** użyj listy rozwijanej, aby wybrać pozycję Azure Active Directory , **a** następnie wybierz pozycję **Zapisz.**

Po włączeniu uwierzytelniania wstępnego usługa Azure AD będzie najpierw wymagać od użytkowników uwierzytelnienia, a jeśli logowanie jest skonfigurowane, aplikacja back-end zweryfikuje również użytkownika przed uzyskaniem dostępu do aplikacji. Zmiana trybu uwierzytelniania wstępnego z Przekazania do usługi Azure AD konfiguruje również zewnętrzny adres URL przy użyciu protokołu HTTPS, więc każda aplikacja początkowo skonfigurowana dla protokołu HTTP będzie teraz zabezpieczona za pomocą protokołu HTTPS.

### <a name="enable-single-sign-on"></a>Włączanie pojedynczego Sign-On

Logowanie jednokrotne zapewnia najlepsze możliwe środowisko użytkownika i bezpieczeństwo, ponieważ użytkownicy muszą zalogować się tylko raz podczas uzyskiwania dostępu do usługi Azure AD. Po wstępnym uwierzytelnieniu użytkownika logowanie jednokrotne jest wykonywane przez łącznik usługi serwer proxy aplikacji uwierzytelnianie do aplikacji lokalnej w imieniu użytkownika. Aplikacja zaplecza przetwarza dane logowania tak, jakby to był sam użytkownik.

Wybranie opcji **Przekaż umożliwia** użytkownikom dostęp do opublikowanej aplikacji bez konieczności uwierzytelniania w usłudze Azure AD.

Logowanie jednokrotne jest możliwe tylko wtedy, gdy usługa Azure AD może zidentyfikować użytkownika żądające dostępu do zasobu, dlatego aplikacja musi być skonfigurowana do wstępnego uwierzytelniania użytkowników w usłudze Azure AD przy dostępie do logowania jednokrotnego w celu działania. W przeciwnym razie opcje logowania jednokrotnego zostaną wyłączone.

Przeczytaj [temat Single sign-on to applications in Azure AD](what-is-single-sign-on.md) (Logowanie jednokrotne do aplikacji w usłudze Azure AD), aby ułatwić wybór najbardziej odpowiedniej metody logowania jednokrotnego podczas konfigurowania aplikacji.

###  <a name="working-with-other-types-of-applications"></a>Praca z innymi typami aplikacji

Usługa Azure AD serwer proxy aplikacji może również obsługiwać aplikacje opracowane do korzystania z [biblioteki Microsoft Authentication Library (MSAL).](../develop/v2-overview.md) Obsługuje ona natywne aplikacje klienckie przez korzystanie z tokenów wystawionych przez usługę Azure AD odebranych w informacjach nagłówka żądania klienta w celu przeprowadzenia wstępnego uwierzytelniania w imieniu użytkowników.

Przeczytaj [publikowanie natywnych i mobilnych aplikacji klienckich](./application-proxy-configure-native-client-application.md) i [aplikacji](./application-proxy-configure-for-claims-aware-applications.md) opartych na oświadczeniach, aby dowiedzieć się więcej o dostępnych konfiguracjach serwer proxy aplikacji.

### <a name="use-conditional-access-to-strengthen-security"></a>Używanie dostępu warunkowego do wzmacniania zabezpieczeń

Zabezpieczenia aplikacji wymagają zaawansowanego zestawu funkcji zabezpieczeń, które mogą chronić przed złożonymi zagrożeniami lokalnie i w chmurze oraz reagować na nie. Osoby atakujące najczęściej uzyskują dostęp do sieci firmowej za pośrednictwem słabych, domyślnych lub skradzionych poświadczeń użytkownika.  Zabezpieczenia oparte na tożsamościach firmy Microsoft zmniejszają liczbę skradzionych poświadczeń dzięki zarządzaniu tożsamościami uprzywilejowanymi i nieuuwistniających oraz ich ochronie.

Następujące możliwości mogą służyć do obsługi usługi Azure AD serwer proxy aplikacji:

* Dostęp warunkowy oparty na użytkownikach i lokalizacjach: zapewnianie ochrony poufnych danych przez ograniczenie dostępu użytkowników na podstawie lokalizacji geograficznej lub adresu IP z zasadami dostępu warunkowego opartego [na lokalizacji.](../conditional-access/location-condition.md)

* Dostęp warunkowy oparty na urządzeniach: Upewnij się, że tylko zarejestrowane, zatwierdzone i zgodne urządzenia mogą uzyskać dostęp do danych firmowych przy użyciu dostępu [warunkowego opartego na urządzeniach.](../conditional-access/require-managed-devices.md)

* Dostęp warunkowy oparty na aplikacji: praca nie musi być zatrzymywana, gdy użytkownik nie znajduje się w sieci firmowej. [Zabezpiecz dostęp do chmury firmowej](../conditional-access/app-based-conditional-access.md) i aplikacji lokalnych oraz zachowaj kontrolę przy użyciu dostępu warunkowego.

* Dostęp warunkowy oparty na ryzyku: chroń dane [](https://www.microsoft.com/cloud-platform/conditional-access) przed złośliwymi hakerami za pomocą zasad dostępu warunkowego opartego na ryzyku, które można zastosować do wszystkich aplikacji i wszystkich użytkowników, zarówno lokalnych, jak i w chmurze.

* Azure AD Moje aplikacje: po wdrożeniu usługi serwer proxy aplikacji i bezpiecznie opublikowanych aplikacjach oferują użytkownikom proste centrum do odnajdywania wszystkich swoich aplikacji i uzyskiwania do nich dostępu. Zwiększ produktywność dzięki możliwościom samoobsługi, takim jak możliwość żądania dostępu do nowych aplikacji i grup lub zarządzania dostępem do tych zasobów w imieniu innych osób za pośrednictwem [Moje aplikacje](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Zarządzanie implementacją

### <a name="required-roles"></a>Wymagane role

Firma Microsoft opowiada się za zasadą przyznawania możliwie najmniejszych uprawnień do wykonywania wymaganych zadań w usłudze Azure AD. [Zapoznaj się z różnymi dostępnymi rolami platformy Azure](../roles/permissions-reference.md) i wybierz odpowiednią rolę, która będzie odpowiednie do potrzeb poszczególnych osób. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Rola biznesowa| Zadania biznesowe| Role usługi Azure Active Directory |
|---|---|---|
| Administrator pomocy technicznej | Zwykle ograniczone do kwalifikowania zgłoszonych problemów przez użytkownika końcowego i wykonywania ograniczonych zadań, takich jak zmienianie haseł użytkowników, unieważnianie tokenów odświeżania i monitorowanie kondycji usługi. | Administrator pomocy technicznej |
| Administrator tożsamości| Przeczytaj raporty logowania i dzienniki inspekcji usługi Azure AD, aby debugować problemy związane z serwerem proxy aplikacji.| Czytelnik zabezpieczeń |
| Właściciel aplikacji| Tworzenie wszystkich aspektów aplikacji dla przedsiębiorstw, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzanie nimi.| Administrator aplikacji |
| Administrator infrastruktury | Właściciel przechowania certyfikatu | Administrator aplikacji |

Minimalizacja liczby osób, które mają dostęp do zabezpieczonych informacji lub zasobów, pomoże zmniejszyć prawdopodobieństwo uzyskania nieautoryzowanego dostępu przez złośliwego aktora lub przypadkowego wpływu na poufny zasób przez autoryzowanego użytkownika.

Jednak użytkownicy nadal muszą wykonywać codziennie uprzywilejowane operacje, dlatego wymuszanie zasad ji-in-time (JIT) opartych na usłudze Privileged Identity Management w celu zapewnienia uprzywilejowanego dostępu na żądanie do zasobów platformy [Azure, a](../privileged-identity-management/pim-configure.md) usługa Azure AD jest zalecanym podejściem do efektywnego zarządzania dostępem administracyjnym i inspekcją.

### <a name="reporting-and-monitoring"></a>Raportowanie i monitorowanie

Usługa Azure AD zapewnia dodatkowe szczegółowe informacje na temat użycia aplikacji i kondycji operacyjnej organizacji za [pośrednictwem dzienników inspekcji i raportów](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). serwer proxy aplikacji bardzo ułatwia również monitorowanie łączników z portalu usługi Azure AD i dzienników zdarzeń systemu Windows.

#### <a name="application-audit-logs"></a>Dzienniki inspekcji aplikacji

Te dzienniki zawierają szczegółowe informacje o logowaniach do aplikacji skonfigurowanych przy użyciu serwer proxy aplikacji i urządzenia oraz użytkownika, który uzyskuje dostęp do aplikacji. [Dzienniki inspekcji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) znajdują się w Azure Portal i w interfejsie [API inspekcji](/graph/api/resources/directoryaudit) w celu eksportowania. Ponadto [raporty użycia i szczegółowe informacje](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) są również dostępne dla aplikacji.

#### <a name="application-proxy-connector-monitoring"></a>serwer proxy aplikacji monitorowania łącznika

Łączniki i usługa dbają o wszystkie zadania o wysokiej dostępności. Stan łączników można monitorować na stronie serwer proxy aplikacji portalu usługi Azure AD. Aby uzyskać więcej informacji na temat konserwacji łączników, zobacz Understand Azure AD serwer proxy aplikacji Connectors (Informacje o [łącznikach łączników usługi Azure AD).](./application-proxy-connectors.md#maintenance)

![Przykład: łączniki serwer proxy aplikacji Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Dzienniki zdarzeń systemu Windows i liczniki wydajności

Łączniki mają dzienniki administratora i sesji. Dzienniki administratora zawierają kluczowe zdarzenia i ich błędy. Dzienniki sesji zawierają wszystkie transakcje i ich szczegóły przetwarzania. Dzienniki i liczniki znajdują się w dziennikach zdarzeń systemu Windows, aby uzyskać więcej informacji, zobacz Understand Azure AD serwer proxy aplikacji Connectors (Informacje o łącznikach usługi [Azure AD serwer proxy aplikacji).](./application-proxy-connectors.md#under-the-hood) Postępuj zgodnie z tym [samouczkiem, aby skonfigurować źródła](../../azure-monitor/agents/data-sources-windows-events.md)danych dziennika zdarzeń w Azure Monitor .

### <a name="troubleshooting-guide-and-steps"></a>Przewodnik rozwiązywania problemów i kroki

Dowiedz się więcej na temat typowych problemów i sposobu ich rozwiązywania, nasz przewodnik [rozwiązywania problemów z komunikatami](application-proxy-troubleshoot.md) o błędach.

Poniższe artykuły obejmują typowe scenariusze, których można również użyć do utworzenia przewodników rozwiązywania problemów dla organizacji pomocy technicznej.

* [Problem z wyświetlaniem strony aplikacji](application-proxy-page-appearance-broken-problem.md)
* [Ładowanie aplikacji trwa zbyt długo](application-proxy-page-load-speed-problem.md)
* [Linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)
* [Jakie porty należy otworzyć dla mojej aplikacji](application-proxy-add-on-premises-application.md)
* [Brak działającego łącznika w grupie łączników mojej aplikacji](application-proxy-connectivity-no-working-connector.md)
* [Konfigurowanie w portalu administracyjnym](application-proxy-config-how-to.md)
* [Konfigurowanie logowania jednokrotnego w mojej aplikacji](application-proxy-config-sso-how-to.md)
* [Problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)
* [Konfigurowanie ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurowanie przy użyciu usługi PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)
* [Problem z logowaniem](application-sign-in-problem-on-premises-application-proxy.md)

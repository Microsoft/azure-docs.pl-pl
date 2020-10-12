---
title: Planowanie wdrożenia serwer proxy aplikacji usługi Azure Active Directory
description: Kompleksowy przewodnik planowania wdrażania serwera proxy aplikacji w organizacji
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 09a930778e35897671d10f14a95f3fa48ea5e9eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642422"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planowanie wdrożenia serwera proxy aplikacji usługi Azure AD

Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) to bezpieczne i ekonomiczne rozwiązanie dostępu zdalnego dla aplikacji lokalnych. Zawiera natychmiastową ścieżkę przejścia dla organizacji "Cloud First" w celu zarządzania dostępem do starszych aplikacji lokalnych, które nie mogą jeszcze korzystać z nowoczesnych protokołów. Aby uzyskać dodatkowe informacje wprowadzające, zobacz [co to jest serwer proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Serwer proxy aplikacji jest zalecany do zapewniania użytkownikom zdalnym dostępu do zasobów wewnętrznych. Serwer proxy aplikacji zastępuje potrzebę sieci VPN lub zwrotnego serwera proxy dla tych przypadków użycia dostępu zdalnego. Nie jest ona przeznaczona dla użytkowników, którzy znajdują się w sieci firmowej. Ci użytkownicy, którzy korzystają z serwera proxy aplikacji w celu dostępu do sieci intranet, mogą napotkać niepożądane problemy z wydajnością.

Ten artykuł zawiera zasoby potrzebne do planowania i obsługi serwer proxy aplikacji usługi Azure AD platformy Azure oraz zarządzania nimi.

## <a name="plan-your-implementation"></a>Planowanie implementacji

Poniższa sekcja zawiera szeroki widok najważniejszych elementów planowania, które spowodują skonfigurowanie wydajnego środowiska wdrażania.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania należy spełnić następujące wymagania wstępne. Więcej informacji na temat konfigurowania środowiska, w tym wymagań wstępnych, można znaleźć w tym [samouczku](application-proxy-add-on-premises-application.md).

* **Łączniki**: Łączniki to lekkie agenci, których można wdrożyć na:
   * Sprzęt fizyczny lokalnie
   * Maszyna wirtualna hostowana w ramach dowolnego rozwiązania funkcji hypervisor
   * Maszyna wirtualna hostowana na platformie Azure, aby umożliwić wychodzące połączenie z usługą serwera proxy aplikacji.

* Więcej szczegółowych informacji można znaleźć w temacie Omówienie [łączników serwera Proxy aplikacja usługi Azure AD](application-proxy-connectors.md) .

     * Przed zainstalowaniem łączników należy [włączyć obsługę protokołu TLS 1,2 dla](application-proxy-add-on-premises-application.md) komputerów łączników.

     * Jeśli to możliwe, wdróż łączniki w [tej samej sieci](application-proxy-network-topology.md) i segmencie co serwery aplikacji sieci Web zaplecza. Najlepszym rozwiązaniem jest wdrożenie łączników po zakończeniu odnajdywania aplikacji.
     * Zalecamy, aby każda grupa łączników miała co najmniej dwa łączniki zapewniające wysoką dostępność i skalowanie. W przypadku, gdy trzy łączniki są optymalne na wypadek, może być konieczne obsługę komputera w dowolnym momencie. Przejrzyj [tabelę pojemności łącznika](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) , aby pomóc w wyborze typu maszyny, na której mają zostać zainstalowane łączniki. Im większa jest pojemność bufora i wykonanie łącznika.

* **Ustawienia dostępu do sieci**: Łączniki usługi Azure serwer proxy aplikacji usługi Azure AD [nawiązują połączenie z platformą Azure za pośrednictwem protokołu HTTPS (port TCP 443) i http (port TCP 80)](application-proxy-add-on-premises-application.md).

   * Przerwanie ruchu TLS łącznik nie jest obsługiwane i uniemożliwi łącznikom nawiązywanie bezpiecznego kanału z odpowiednimi punktami końcowymi serwera proxy aplikacji platformy Azure.

   * Unikaj wszystkich form inspekcji wbudowanej na wychodzącej komunikacji TLS między łącznikami i platformą Azure. Możliwa jest wewnętrzna Inspekcja między aplikacjami łącznika i zaplecza, ale może to obniżyć poziom środowiska użytkownika, a w związku z tym nie jest to zalecane.

   * Równoważenie obciążenia samych łączników jest również nieobsługiwane lub nawet niepotrzebne.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Ważne zagadnienia przed skonfigurowaniem usługi Azure serwer proxy aplikacji usługi Azure AD

Następujące podstawowe wymagania muszą zostać spełnione, aby można było skonfigurować i wdrożyć platformę Azure serwer proxy aplikacji usługi Azure AD.

*  **Azure**— dołączanie: przed wdrożeniem serwera proxy aplikacji tożsamości użytkowników muszą być synchronizowane z katalogu lokalnego lub tworzone bezpośrednio w dzierżawach usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnienie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji serwera proxy aplikacji i posiadanie informacji o identyfikatorze użytkownika w celu przeprowadzenia rejestracji jednokrotnej (SSO).

* **Wymagania dotyczące dostępu warunkowego**: nie zaleca się używania serwera proxy aplikacji do dostępu do intranetu, ponieważ powoduje to dodanie opóźnień, które mają wpływ na użytkowników. Zalecamy używanie serwera proxy aplikacji z zasadami wstępnego uwierzytelniania i dostępu warunkowego dla dostępu zdalnego z Internetu.  Podejście do zapewnienia dostępu warunkowego do użytku w intranecie polega na modernizacji aplikacji, aby mogły one być bezpośrednio uwierzytelniane za pomocą usługi AAD. Aby uzyskać więcej informacji, zapoznaj się z [zasobami dotyczącymi migrowania aplikacji do usługi AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) .

* **Limity usługi**: aby chronić przed zużyciem zasobów przez poszczególne dzierżawy, istnieją limity ograniczania ustawione na aplikację i dzierżawcę. Aby sprawdzić te limity, odnoszą się do [limitów i ograniczeń usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Te limity ograniczania są oparte na teście porównawczym znacznie powyżej typowego woluminu użycia i zapewniają szeroki bufor dla większości wdrożeń.

* **Certyfikat publiczny**: Jeśli używasz niestandardowych nazw domen, musisz uzyskać certyfikat TLS/SSL. W zależności od wymagań organizacyjnych uzyskanie certyfikatu może zająć trochę czasu, a firma Microsoft zaleca rozpoczęcie procesu tak szybko, jak to możliwe. Serwer proxy aplikacji platformy Azure obsługuje certyfikaty standardowego, [wieloznacznego](application-proxy-wildcard.md)lub opartego na sieci SAN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie domen niestandardowych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

* **Wymagania dotyczące domeny**: Logowanie jednokrotne do opublikowanych aplikacji przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) wymaga, aby serwer z uruchomionym łącznikiem i serwerem, na którym działa aplikacja, są przyłączone do domeny i częścią tej samej domeny lub domen ufających.
Aby uzyskać szczegółowe informacje na temat tego tematu, zobacz [KCD for Single Signing](application-proxy-configure-single-sign-on-with-kcd.md) in with Application proxy. Usługa łącznika jest uruchamiana w kontekście systemu lokalnego i nie należy jej konfigurować do korzystania z tożsamości niestandardowej.

* **Rekordy DNS dla adresów URL**

   * Przed użyciem domen niestandardowych w serwerze proxy aplikacji należy utworzyć rekord CNAME w publicznej usłudze DNS, umożliwiając klientom rozpoznawanie niestandardowego zdefiniowanego zewnętrznego adresu URL do wstępnie zdefiniowanego adresu serwera proxy aplikacji. Nie można utworzyć rekordu CNAME dla aplikacji, która używa domeny niestandardowej uniemożliwi użytkownikom zdalnym łączenie się z aplikacją. Kroki wymagane do dodania rekordów CNAME mogą się różnić od dostawcy DNS do dostawcy, aby dowiedzieć się, jak [zarządzać rekordami i zestawami rekordów DNS przy użyciu Azure Portal](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Podobnie hosty łączników muszą mieć możliwość rozpoznania wewnętrznego adresu URL publikowanych aplikacji.

* **Prawa i role administracyjne**

   * **Instalacja łącznika** wymaga uprawnień administratora lokalnego dla systemu Windows Server, na którym jest instalowany. Wymaga również co najmniej roli *administratora aplikacji* do uwierzytelniania i rejestrowania wystąpienia łącznika w dzierżawie usługi Azure AD.

   * **Publikowanie i administrowanie aplikacjami** wymaga roli *administratora aplikacji* . Administratorzy aplikacji mogą zarządzać wszystkimi aplikacjami w katalogu, takimi jak rejestracje, ustawienia rejestracji jednokrotnej, przypisania użytkowników i grup oraz Licencjonowanie, ustawienia serwera proxy aplikacji i zgodę. Nie przyznaje możliwości zarządzania dostępem warunkowym. Rola *administrator aplikacji w chmurze* ma wszystkie możliwości administratora aplikacji, z tą różnicą, że nie zezwala na zarządzanie ustawieniami serwera proxy aplikacji.

* **Licencjonowanie**: serwer proxy aplikacji jest dostępny w ramach subskrypcji Azure AD — wersja Premium. Zapoznaj się z pełną listą opcji licencjonowania i funkcji na [stronie z cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="application-discovery"></a>Odnajdywanie aplikacji

Kompiluj spis wszystkich aplikacji w zakresie, które są publikowane za pośrednictwem serwera proxy aplikacji, zbierając następujące informacje:

| Typ informacji| Informacje do zebrania |
|---|---|
| Typ usługi| Na przykład: SharePoint, SAP, CRM, niestandardowa aplikacja sieci Web, interfejs API |
| Platforma aplikacji | Na przykład: Windows IIS, Apache w systemie Linux, Tomcat, NGINX |
| Członkostwo w domenie| W pełni kwalifikowana nazwa domeny serwera sieci Web (FQDN) |
| Lokalizacja aplikacji | Gdzie serwer sieci Web lub farma znajduje się w infrastrukturze |
| Dostęp wewnętrzny | Dokładny adres URL używany podczas wewnętrznego uzyskiwania dostępu do aplikacji. <br> Jeśli w farmie jest używany typ równoważenia obciążenia? <br> Czy aplikacja rysuje zawartość ze źródeł innych niż sama.<br> Ustal, czy aplikacja działa za pośrednictwem obiektów WebSockets. |
| Dostęp zewnętrzny | Rozwiązanie dostawcy, dla którego aplikacja została już udostępniona zewnętrznie. <br> Adres URL, który ma być używany na potrzeby dostępu zewnętrznego. Jeśli program SharePoint, upewnij się, że zostały skonfigurowane alternatywne mapowania dostępu na [te wskazówki](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). W przeciwnym razie należy zdefiniować zewnętrzne adresy URL. |
| Certyfikat publiczny | Jeśli używasz domeny niestandardowej, Pozyskaj certyfikat z odpowiednią nazwą podmiotu. Jeśli certyfikat istnieje, należy zwrócić uwagę na numer seryjny i lokalizację, z której można uzyskać. |
| Typ uwierzytelniania| Typ uwierzytelniania obsługiwany przez aplikację, na przykład podstawowe, uwierzytelnianie integracji systemu Windows, oparte na formularzach, oparte na nagłówkach i oświadczeniach. <br>Jeśli aplikacja jest skonfigurowana do uruchamiania w ramach określonego konta domeny, zanotuj w pełni kwalifikowaną nazwę domeny (FQDN) konta usługi.<br> Identyfikator i adres URL odpowiedzi w przypadku protokołu SAML. <br> Jeśli oparty na nagłówkach, rozwiązanie dostawcy i specyficzne wymagania dotyczące obsługi typu uwierzytelniania. |
| Nazwa grupy łączników | Nazwa logiczna grupy łączników, która zostanie wyoznaczona w celu zapewnienia przewodu i logowania jednokrotnego do tej aplikacji zaplecza. |
| Dostęp użytkowników/grup | Użytkownicy lub grupy użytkowników, którym zostanie udzielony dostęp zewnętrzny do aplikacji. |
| Wymagania dodatkowe | Zwróć uwagę na wszelkie dodatkowe wymagania dotyczące dostępu zdalnego lub zabezpieczeń, które należy wziąć pod uwagę przy publikowaniu aplikacji. |

Ten [Arkusz spisu aplikacji](https://aka.ms/appdiscovery) można pobrać do spisu aplikacji.

### <a name="define-organizational-requirements"></a>Zdefiniuj wymagania organizacyjne

Poniżej znajdują się obszary, dla których należy zdefiniować wymagania biznesowe organizacji. Każdy obszar zawiera przykłady wymagań

 **Dostęp**

* Użytkownicy zdalni z przyłączonymi do domeny lub urządzeniami przyłączonymi do usługi Azure AD mogą bezpiecznie uzyskiwać dostęp do opublikowanych aplikacji przy użyciu bezproblemowego logowania jednokrotnego.

* Użytkownicy zdalni z zatwierdzonymi urządzeniami osobistymi mogą bezpiecznie uzyskiwać dostęp do opublikowanych aplikacji, pod warunkiem, że są one zarejestrowane w usłudze MFA i zarejestrowali aplikację Microsoft Authenticator na swoim telefonie komórkowym jako metoda uwierzytelniania.

**Nadzór**

* Administratorzy mogą definiować i monitorować cykl życia przypisań użytkowników do aplikacji publikowanych za pomocą serwera proxy aplikacji.

**Zabezpieczenia**

* Tylko użytkownicy przypisani do aplikacji za pośrednictwem członkostwa w grupie lub indywidualnie mogą uzyskiwać dostęp do tych aplikacji.

**Wydajność**

* Nie ma obniżenia wydajności aplikacji w porównaniu do uzyskiwania dostępu do aplikacji z sieci wewnętrznej.

**Środowisko użytkownika**

* Użytkownicy wiedzą, jak uzyskać dostęp do swoich aplikacji przy użyciu znanych adresów URL firmy na dowolnej platformie urządzeń.

**Inspekcja**
* Administratorzy mogą przeprowadzać inspekcję aktywności użytkowników.


### <a name="best-practices-for-a-pilot"></a>Najlepsze rozwiązania dla pilotażu

Określ ilość czasu i nakładów pracy wymaganych do pełnego zgłoszenia pojedynczej aplikacji do dostępu zdalnego przy użyciu logowania jednokrotnego (SSO). Należy to zrobić, uruchamiając pilotaż, który uważa, że początkowe odnajdywanie, publikowanie i testowanie ogólne. Korzystanie z prostej aplikacji sieci Web opartej na usługach IIS, która jest już wstępnie skonfigurowana na potrzeby zintegrowanego uwierzytelniania systemu Windows (IWA), pomaga ustalić linię bazową, ponieważ ta konfiguracja wymaga minimalnego wysiłku do pomyślnego pilotażowego dostępu zdalnego i logowania jednokrotnego.

Następujące elementy projektu powinny zwiększyć sukces implementacji pilotażowej bezpośrednio w dzierżawie produkcyjnej.

**Zarządzanie łącznikami**:

* Łączniki odgrywają kluczową rolę w dostarczaniu lokalnych danych do aplikacji. Użycie **domyślnej** grupy łączników jest wystarczające do wstępnego testowania pilotażowego opublikowanych aplikacji przed ich wprowadzeniem do środowiska produkcyjnego. Pomyślnie przetestowane aplikacje można przenieść do grup łączników produkcyjnych.

**Zarządzanie aplikacjami**:

* Pracownicy najprawdopodobniej zapamiętają, że zewnętrzny adres URL jest znany i istotny. Unikaj publikowania aplikacji przy użyciu wstępnie zdefiniowanych sufiksów msappproxy.net lub onmicrosoft.com. Zamiast tego należy podać znaną, zweryfikowaną domenę najwyższego poziomu, poprzedzoną logiczną nazwą hosta, taką jak *intranet. <customers_domain>. com*.

* Ogranicz widoczność ikony aplikacji pilotażowej do grupy pilotażowej, ukrywając jej ikonę uruchamiania w portalu Azure webapps. Gdy wszystko jest gotowe do środowiska produkcyjnego, można przekierować aplikację do odpowiednich docelowych odbiorców w tej samej dzierżawie przedprodukcyjnej lub przez również opublikowanie aplikacji w dzierżawie produkcyjnej.

**Ustawienia logowania**jednokrotnego: niektóre ustawienia rejestracji jednokrotnej mają określone zależności, które mogą wymagać czasu, aby uniknąć opóźnień kontroli zmian przez zapewnienie, że zależności są rozpatrywane przed czasem. Obejmuje to hosty łącznika przyłączania do domeny w celu przeprowadzenia logowania jednokrotnego przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) i poświęcają inne czasochłonne działania. Można na przykład skonfigurować wystąpienie dostępu do usługi PING, jeśli potrzebne jest logowanie jednokrotne oparte na nagłówkach.

**Protokół TLS między hostem łącznika a aplikacją docelową**: zabezpieczenia są najważniejsze, dlatego protokół TLS między hostem łącznika a aplikacją docelową powinien być zawsze używany. Szczególnie jeśli aplikacja sieci Web jest skonfigurowana do uwierzytelniania opartego na formularzach (FBA), w miarę jak poświadczenia użytkownika są efektywnie przesyłane w postaci zwykłego tekstu.

**Zaimplementuj przyrostowo i przetestuj każdy krok**.
Przeprowadzaj podstawowe testy funkcjonalne po opublikowaniu aplikacji, aby upewnić się, że wszystkie wymagania użytkownika i biznesowe zostały spełnione, postępując zgodnie z poniższymi instrukcjami:

1. Przetestuj i Zweryfikuj ogólny dostęp do aplikacji sieci Web z wyłączonym uwierzytelnianiem wstępnym.
2. W przypadku pomyślnego włączenia uwierzytelniania wstępnego i przypisania użytkowników i grup. Testowanie i weryfikowanie dostępu.
3. Następnie Dodaj metodę rejestracji Jednokrotnej dla aplikacji i przetestuj ją ponownie, aby sprawdzić dostęp.
4. Zastosuj zasady dostępu warunkowego i uwierzytelniania MFA zgodnie z wymaganiami. Testowanie i weryfikowanie dostępu.

**Narzędzia do rozwiązywania problemów**: w przypadku rozwiązywania problemów Zawsze uruchamiaj przez sprawdzenie poprawności dostępu do opublikowanej aplikacji z przeglądarki na hoście łącznika i sprawdź, czy aplikacja działa zgodnie z oczekiwaniami. Uproszczenie konfiguracji, łatwiejszy w ustaleniu przyczyny głównej, dlatego warto rozważyć próbę odtworzenia problemów z minimalną konfiguracją, taką jak użycie tylko jednego łącznika i bez rejestracji jednokrotnej. W niektórych przypadkach narzędzia debugowania sieci Web, takie jak Telerik programu Fiddler, mogą okazać się niezbędne do rozwiązywania problemów z dostępem do aplikacji lub zawartości w aplikacjach, do których dostęp odbywa się za pomocą serwera proxy. Programu Fiddler może również działać jako serwer proxy, aby pomóc w śledzeniu i debugowaniu ruchu dla platform mobilnych, takich jak iOS i Android, i praktycznie wszystko, co można skonfigurować do routingu za pośrednictwem serwera proxy. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>Implementowanie rozwiązania

### <a name="deploy-application-proxy"></a>Wdróż serwer proxy aplikacji

Kroki umożliwiające wdrożenie serwera proxy aplikacji zostały omówione w tym [samouczku w celu dodania aplikacji lokalnej dla dostępu zdalnego](application-proxy-add-on-premises-application.md). Jeśli instalacja nie powiedzie się, wybierz pozycję  **Rozwiązywanie problemów z serwerem proxy aplikacji**  w portalu lub Skorzystaj z przewodnika rozwiązywania problemów w [przypadku problemów z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikowanie aplikacji za pośrednictwem serwera proxy aplikacji

Publikowanie aplikacji zakłada, że spełniono wszystkie wymagania wstępne i że masz kilka łączników, które są wyświetlane jako zarejestrowane i aktywne na stronie serwera proxy aplikacji.

Możesz również publikować aplikacje za pomocą [programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Poniżej przedstawiono kilka najlepszych rozwiązań, które należy wykonać podczas publikowania aplikacji:

* **Korzystanie z grup łączników**: Przypisz grupę łączników, która została wyoznaczona do publikowania każdej odpowiedniej aplikacji. Zalecamy, aby każda grupa łączników miała co najmniej dwa łączniki zapewniające wysoką dostępność i skalowanie. W przypadku, gdy trzy łączniki są optymalne na wypadek, może być konieczne obsługę komputera w dowolnym momencie. Ponadto zobacz [publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md) , aby zobaczyć, jak można także użyć grup łączników do segmentacji łączników według sieci lub lokalizacji.

* **Ustaw limit czasu aplikacji zaplecza**: to ustawienie jest przydatne w scenariuszach, w których aplikacja może wymagać więcej niż 75 sekund, aby przetworzyć transakcję klienta. Na przykład gdy klient wysyła zapytanie do aplikacji sieci Web, która działa jako fronton do bazy danych. Fronton wysyła to zapytanie do serwera bazy danych zaplecza i czeka na odpowiedź, ale przez czas odebrania odpowiedzi po stronie klienta przekroczenie limitu czasu. Ustawienie limitu czasu na wartość Long zapewnia 180 sekund na ukończenie dłuższych transakcji.

* **Użyj odpowiednich typów plików cookie**

   * **Plik cookie dotyczący tylko protokołu HTTP**: zapewnia dodatkowe zabezpieczenia, gdy serwer proxy aplikacji zawiera flagę HTTPOnly w nagłówkach odpowiedzi HTTP Set-cookie. To ustawienie pomaga wyeliminować luki w zabezpieczeniach, takie jak wykonywanie skryptów między witrynami (XSS). Pozostaw ten zestaw jako wartość nie dla klientów/agentów użytkowników, którzy wymagają dostępu do pliku cookie sesji. Na przykład klient RDP/MTSC nawiązuje połączenie z bramą Pulpit zdalny opublikowaną za pośrednictwem serwera proxy aplikacji.

   * **Bezpieczny plik cookie**: Jeśli plik cookie jest ustawiony z bezpiecznym atrybutem, agent użytkownika (aplikacja po stronie klienta) będzie zawierać tylko plik cookie w żądaniach HTTP, jeśli żądanie zostanie przesłane przez zabezpieczony kanał TLS. Pozwala to ograniczyć ryzyko naruszenia bezpieczeństwa plików cookie za pośrednictwem kanałów tekstu czystego, dlatego należy je włączyć.

   * **Trwały plik cookie**: umożliwia utrwalanie plików cookie sesji serwera proxy aplikacji między zamknięciami w przeglądarce przez pozostały czas, dopóki nie wygaśnie lub zostanie usunięty. Używany w scenariuszach, w których rozbudowana aplikacja, taka jak Office uzyskuje dostęp do dokumentu w opublikowanej aplikacji sieci Web, bez ponownego monitowania użytkownika o uwierzytelnienie. Należy jednak włączyć z zachowaniem ostrożności, ponieważ trwałe pliki cookie mogą ostatecznie opuścić usługę narażoną na nieautoryzowany dostęp, jeśli nie są używane w połączeniu z innymi kontrolkami kompensacyjnymi. Tego ustawienia należy używać tylko w przypadku starszych aplikacji, które nie mogą udostępniać plików cookie między procesami. Lepiej jest zaktualizować swoją aplikację, aby obsługiwała udostępnianie plików cookie między procesami zamiast korzystać z tego ustawienia.

* **Tłumaczenie adresów URL w nagłówkach**: włącza się to dla scenariuszy, w których wewnętrzna usługa DNS nie może być skonfigurowana w taki sposób, aby była zgodna z publiczną przestrzenią nazw organizacji (a. k. a Split DNS). Jeśli aplikacja nie wymaga oryginalnego nagłówka hosta w żądaniu klienta, pozostaw tę wartość ustawioną na tak. Alternatywą jest, aby łącznik używał nazwy FQDN w wewnętrznym adresie URL routingu rzeczywistego ruchu, a nazwa FQDN w zewnętrznym adresie URL jako nagłówek hosta. W większości przypadków ta alternatywa powinna zezwalać aplikacji na normalne działanie, gdy uzyskuje się dostęp zdalnie, ale użytkownicy tracą korzyści z używania & poza adresem URL.

* **Tłumaczenie adresów URL w treści aplikacji**: Włącz opcję tłumaczenie treści aplikacji dla aplikacji, jeśli chcesz, aby linki z tej aplikacji były tłumaczone na odpowiedzi z powrotem do klienta. W przypadku włączenia tej funkcji Ta funkcja zapewnia najlepszą próbę przeprowadzenia translacji wszystkich linków wewnętrznych, które serwer proxy aplikacji znajdzie w kodzie HTML i w odpowiedziach CSS zwracanych do klientów. Jest to przydatne w przypadku publikowania aplikacji, które zawierają zakodowane jako stałe linki bezwzględne lub NetBIOS w zawartości, lub aplikacje z zawartością, która łączy się z innymi aplikacjami lokalnymi.

W przypadku scenariuszy, w których opublikowana aplikacja łączy się z innymi opublikowanymi aplikacjami, należy włączyć tłumaczenie linków dla każdej aplikacji, aby kontrolować środowisko użytkownika na poziomie aplikacji.

Załóżmy na przykład, że masz trzy aplikacje publikowane za pomocą serwera proxy aplikacji, które łączą się ze sobą: korzyści, wydatki i podróże oraz czwarta aplikacja, która nie jest publikowana za pomocą serwera proxy aplikacji.

![Obraz 1](media/App-proxy-deployment-plan/link-translation.png)

Po włączeniu tłumaczenia linku dla aplikacji korzyści linki do wydatków i podróży są przekierowywane do zewnętrznych adresów URL dla tych aplikacji, dzięki czemu użytkownicy uzyskujący dostęp do aplikacji spoza sieci firmowej będą mieli do nich dostępu. Linki z wydatków i podróż z powrotem do korzyści nie działają, ponieważ nie włączono tłumaczenia linków dla tych dwóch aplikacji. Link do opinii nie jest przekierowywany z powodu braku zewnętrznego adresu URL, dlatego użytkownicy korzystający z aplikacji korzyści nie będą mogli uzyskać dostępu do aplikacji z opiniami spoza sieci firmowej. Zapoznaj się ze szczegółowymi informacjami na temat [translacji łączy i innych opcji przekierowania](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Dostęp do aplikacji

Istnieje kilka opcji zarządzania dostępem do zasobów publikowanych przez serwer proxy aplikacji, dlatego należy wybrać najbardziej odpowiednie dla danego scenariusza i skalowalności. Typowe podejścia obejmują: używanie grup lokalnych, które są synchronizowane za pośrednictwem Azure AD Connect, tworzenie grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika, przy użyciu grup samoobsługi, które są zarządzane przez właściciela zasobu, lub kombinację wszystkich tych elementów. Zapoznaj się z powiązanymi zasobami, aby poznać zalety każdego z nich.

Najbardziej prostym sposobem przypisywania użytkownikom dostępu do aplikacji jest przejście do opcji **Użytkownicy i grupy** z okienka po lewej stronie opublikowanej aplikacji i bezpośrednie przypisanie grup lub użytkowników indywidualnych.

![Obraz 24](media/App-proxy-deployment-plan/add-user.png)

Możesz również pozwolić użytkownikom na samoobsługowe dostęp do aplikacji, przypisując grupie, która nie jest obecnie członkiem i skonfigurować opcje samoobsługowego.

![Obraz 25](media/App-proxy-deployment-plan/allow-access.png)

Jeśli ta funkcja jest włączona, użytkownicy będą mogli zalogować się do portalu usługi webapps i zażądać dostępu oraz zostać automatycznie zatwierdzona i dodana do już dozwolonej grupy samoobsługowej lub wymagać zatwierdzenia od wskazanej osoby zatwierdzającej.

Użytkownicy-Goście mogą również zostać [zaproszeni do dostępu do wewnętrznych aplikacji publikowanych za pośrednictwem serwera proxy aplikacji za pośrednictwem usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

W przypadku aplikacji lokalnych, które są normalnie dostępne anonimowo, nie wymagając uwierzytelniania, możesz chcieć wyłączyć opcję znajdującą się we **właściwościach**aplikacji.

![Obraz 26](media/App-proxy-deployment-plan/assignment-required.png)


Pozostawienie tej opcji na wartość nie pozwala użytkownikom na dostęp do aplikacji lokalnej za pośrednictwem aplikacja usługi Azure AD serwera proxy bez uprawnień, dlatego należy używać z zachowaniem ostrożności.

Po opublikowaniu aplikacji powinna ona być dostępna, wpisując jej zewnętrzny adres URL w przeglądarce lub przez jej ikonę pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Włącz uwierzytelnianie wstępne

Sprawdź, czy aplikacja jest dostępna za pośrednictwem serwera proxy aplikacji, do którego uzyskuje dostęp za pośrednictwem zewnętrznego adresu URL.

1. Przejdź do **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** i wybierz aplikację, którą chcesz zarządzać.

2. Wybierz pozycję **serwer proxy aplikacji**.

3. W polu **wstępnego uwierzytelniania** Użyj listy rozwijanej, aby wybrać **Azure Active Directory**, a następnie wybierz pozycję **Zapisz**.

Po włączeniu wstępnego uwierzytelniania usługa Azure AD będzie najpierw sprawdzać użytkowników w celu uwierzytelnienia. Jeśli skonfigurowano Logowanie jednokrotne, aplikacja zaplecza sprawdzi również, czy użytkownik uzyska dostęp do aplikacji. Zmiana trybu wstępnego uwierzytelniania z przekazywania do usługi Azure AD powoduje również skonfigurowanie zewnętrznego adresu URL przy użyciu protokołu HTTPS, więc każda aplikacja początkowo skonfigurowana dla protokołu HTTP będzie teraz zabezpieczona przy użyciu protokołu HTTPS.

### <a name="enable-single-sign-on"></a>Włącz pojedynczy Sign-On

Logowanie jednokrotne zapewnia najlepszą możliwą obsługę i bezpieczeństwo, ponieważ użytkownicy muszą logować się tylko raz podczas uzyskiwania dostępu do usługi Azure AD. Po wstępnym uwierzytelnieniu użytkownika Logowanie jednokrotne jest wykonywane przez łącznik serwera proxy aplikacji uwierzytelniany w aplikacji lokalnej w imieniu użytkownika. Aplikacja zaplecza przetwarza identyfikator logowania, tak jakby był on użytkownikiem.

Wybranie opcji **przekazywania** pozwala użytkownikom na dostęp do opublikowanej aplikacji bez konieczności uwierzytelniania w usłudze Azure AD.

Logowanie jednokrotne jest możliwe tylko wtedy, gdy usługa Azure AD może identyfikować użytkownika żądającego dostępu do zasobu, dlatego aplikacja musi być skonfigurowana do wstępnego uwierzytelniania użytkowników za pomocą usługi Azure AD, gdy dostęp do logowania jednokrotnego ma być wykonywany, w przeciwnym razie opcje logowania jednokrotnego zostaną wyłączone.

Odczytuj Logowanie jednokrotne [do aplikacji w usłudze Azure AD](what-is-single-sign-on.md) , aby pomóc w wyborze najbardziej odpowiedniej metody rejestracji jednokrotnej podczas konfigurowania aplikacji.

###  <a name="working-with-other-types-of-applications"></a>Praca z innymi typami aplikacji

Usługa Azure serwer proxy aplikacji usługi Azure AD może również obsługiwać aplikacje opracowane do korzystania z [biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview). Obsługuje natywne aplikacje klienckie przez konsumowanie tokenów wystawionych przez usługę Azure AD odebrane w nagłówku żądania klienta w celu przeprowadzenia wstępnego uwierzytelniania w imieniu użytkowników.

Przeczytaj artykuł [Publikowanie natywnych i mobilnych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) oraz [aplikacji opartych na oświadczeniach](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) , aby dowiedzieć się więcej o dostępnych konfiguracjach serwera proxy aplikacji.

### <a name="use-conditional-access-to-strengthen-security"></a>Korzystanie z dostępu warunkowego w celu wzmocnienia zabezpieczeń

Zabezpieczenia aplikacji wymagają zaawansowanego zestawu funkcji zabezpieczeń, które mogą chronić i reagować na złożone zagrożenia lokalnie i w chmurze. Osoby atakujące najczęściej uzyskują dostęp do sieci firmowej za pośrednictwem słabych, domyślnych lub skradzionych poświadczeń użytkownika.  Zabezpieczenia oparte na tożsamościach firmy Microsoft redukują korzystanie z skradzionych poświadczeń przez Zarządzanie tożsamościami uprzywilejowanymi i nieuprzywilejowanymi oraz ich ochronę.

Do obsługi serwer proxy aplikacji usługi Azure AD platformy Azure mogą być używane następujące funkcje:

* Dostęp warunkowy oparty na użytkowniku i lokalizacji: Zachowaj poufne dane chronione przez ograniczenie dostępu użytkowników na podstawie lokalizacji geograficznej lub adresu IP przy użyciu [zasad dostępu warunkowego opartego na lokalizacjach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Dostęp warunkowy oparty na urządzeniu: Upewnij się, że tylko zarejestrowane, zatwierdzone i zgodne urządzenia mogą uzyskiwać dostęp do danych firmowych przy użyciu [dostępu warunkowego opartego na urządzeniach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Dostęp warunkowy oparty na aplikacji: nie trzeba przerywać pracy, gdy użytkownik nie znajduje się w sieci firmowej. [Bezpieczny dostęp do firmowych aplikacji w chmurze i lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) oraz zachowanie kontroli z dostępem warunkowym.

* Dostęp warunkowy oparty na ryzyku: Chroń dane przed złośliwymi hakerami za pomocą [zasad dostępu warunkowego opartego na ryzyku](https://www.microsoft.com/cloud-platform/conditional-access) , które mogą być stosowane do wszystkich aplikacji i wszystkich użytkowników, zarówno lokalnych, jak i w chmurze.

* Moje aplikacje usługi Azure AD: wraz z wdrożoną usługą serwera proxy aplikacji i bezpiecznie opublikowanymi aplikacjami oferuje użytkownikom prosty centrum do odnajdywania i uzyskiwania dostępu do wszystkich swoich aplikacji. Zwiększ produktywność dzięki możliwościom samoobsługowym, takim jak możliwość żądania dostępu do nowych aplikacji i grup lub zarządzania dostępem do tych zasobów w imieniu innych użytkowników za pomocą [aplikacji](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Zarządzanie implementacją

### <a name="required-roles"></a>Wymagane role

Firma Microsoft zaleca, aby udzielić najmniejszego możliwego uprawnienia do wykonywania niezbędnych zadań w usłudze Azure AD. [Zapoznaj się z różnymi rolami platformy Azure, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , a następnie wybierz jedną z nich w celu rozwiązania potrzeb każdej osoby. Niektóre role mogą wymagać tymczasowego zastosowania i usunąć po zakończeniu wdrażania.

| Rola biznesowa| Zadania biznesowe| Role usługi Azure AD |
|---|---|---|
| Administrator pomocy technicznej | Zwykle ograniczone do kwalifikujących się problemów zgłoszonych przez użytkowników końcowych i wykonywania ograniczonych zadań, takich jak zmienianie haseł użytkowników, sprawdzanie poprawności tokenów odświeżania i kondycja usługi monitorowania. | Administrator pomocy technicznej |
| Administrator tożsamości| Aby debugować problemy związane z serwerem proxy aplikacji, zapoznaj się z tematem rejestrowanie w raportach i dziennikach inspekcji usługi Azure AD| Czytelnik zabezpieczeń |
| Właściciel aplikacji| Twórz wszystkie aspekty aplikacji przedsiębiorstwa, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzaj nimi.| Administrator aplikacji |
| Administrator infrastruktury | Właściciel przerzucania certyfikatu | Administrator aplikacji |

Zminimalizowanie liczby osób, które mają dostęp do zabezpieczanych informacji lub zasobów, może pomóc w zmniejszeniu prawdopodobieństwa uzyskania nieautoryzowanego dostępu lub autoryzowanemu użytkownikowi przypadkowo wpływającemu na poufne zasoby.

Jednak użytkownicy nadal muszą wykonać codzienne operacje uprzywilejowane, aby wymusić [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) zasady oparte na czasie just-in-Time (JIT), aby zapewnić uprzywilejowany dostęp na żądanie do zasobów platformy Azure, a usługa Azure AD to zalecane podejście do efektywnego zarządzania dostępem administracyjnym i inspekcją.

### <a name="reporting-and-monitoring"></a>Raportowanie i monitorowanie

Usługa Azure AD zapewnia dodatkowe informacje o użyciu aplikacji i kondycji operacyjnej w organizacji za pomocą [dzienników i raportów inspekcji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Serwer proxy aplikacji ułatwia również monitorowanie łączników z poziomu portalu usługi Azure AD i dzienników zdarzeń systemu Windows.

#### <a name="application-audit-logs"></a>Dzienniki inspekcji aplikacji

Te dzienniki zawierają szczegółowe informacje dotyczące logowań do aplikacji skonfigurowanych przy użyciu serwera proxy aplikacji oraz urządzenia i użytkownika, który uzyskują dostęp do aplikacji. [Dzienniki inspekcji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) znajdują się w Azure Portal i w [interfejsie API inspekcji](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) do eksportowania. Ponadto dostępne są również [raporty dotyczące użycia i wglądu](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w dane aplikacji.

#### <a name="application-proxy-connector-monitoring"></a>Monitorowanie łącznika serwera proxy aplikacji

Łączniki i usługi zadbają o wszystkie zadania wysokiej dostępności. Stan łączników można monitorować ze strony serwera proxy aplikacji w portalu usługi Azure AD. Aby uzyskać więcej informacji na temat łącznika maintainence, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Przykład: Łączniki serwer proxy aplikacji usługi Azure AD platformy Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Dzienniki zdarzeń systemu Windows i liczniki wydajności

Łączniki mają zarówno dzienniki administratora, jak i sesji. Dzienniki administracyjne obejmują kluczowe zdarzenia i ich błędy. Dzienniki sesji obejmują wszystkie transakcje i ich szczegóły przetwarzania. Dzienniki i liczniki znajdują się w dziennikach zdarzeń systemu Windows Aby uzyskać więcej informacji, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Postępuj zgodnie [z tym samouczkiem, aby skonfigurować źródła danych dziennika zdarzeń w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Przewodnik rozwiązywania problemów i kroki

Dowiedz się więcej o typowych problemach i sposobach ich rozwiązywania w celu [rozwiązywania problemów](application-proxy-troubleshoot.md) z komunikatami o błędach.

W poniższych artykułach omówiono typowe scenariusze, które mogą być również używane do tworzenia przewodników związanych z rozwiązywaniem problemów w organizacji pomocy technicznej.

* [Problem z wyświetlaniem strony aplikacji](application-proxy-page-appearance-broken-problem.md)
* [Ładowanie aplikacji trwa zbyt długo](application-proxy-page-load-speed-problem.md)
* [Linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)
* [Jakie porty należy otworzyć dla mojej aplikacji](application-proxy-add-on-premises-application.md)
* [Brak działającego łącznika w grupie łączników mojej aplikacji](application-proxy-connectivity-no-working-connector.md)
* [Konfigurowanie w portalu administracyjnym](application-proxy-config-how-to.md)
* [Konfigurowanie logowania jednokrotnego w mojej aplikacji](application-proxy-config-sso-how-to.md)
* [Problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)
* [Konfigurowanie ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurowanie przy użyciu usługi PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)
* [Problem z logowaniem](application-sign-in-problem-on-premises-application-proxy.md)

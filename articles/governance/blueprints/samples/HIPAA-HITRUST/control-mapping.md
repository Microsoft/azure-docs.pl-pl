---
title: Przykładowe kontrolki planu HIPAA HITRUST
description: Sterowanie mapowaniem przykładów planu HIPAA HITRUST. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209421"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu HIPAA HITRUST

W poniższym artykule szczegółowo opisano sposób, w jaki plan platformy Azure HIPAA HITRUST plan jest mapowany do kontrolek HITRUST HIPAA. Aby uzyskać więcej informacji na temat kontrolek, zobacz [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Następujące mapowania są do kontrolek **HIPAA HiTRUST** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję ** \[ wersja zapoznawcza \] : Inspekcja HIPAA HiTRUST kontroluje** wbudowaną inicjatywę Policy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontrola nad złośliwym kodem

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w Azure Security Center i wymuszają rozwiązanie Microsoft chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych z systemem Windows.

- Program Microsoft chroniący przed złośliwym kodem dla platformy Azure powinien zostać skonfigurowany do automatycznego aktualizowania sygnatur ochrony
- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji


## <a name="management-of-removable-media"></a>Zarządzanie nośnikiem wymiennym

W organizacji, na podstawie poziomu klasyfikacji danych, rejestruje nośniki (w tym laptopy) przed użyciem, umieszcza uzasadnione ograniczenia dotyczące tego nośnika, a także zapewnia odpowiedni poziom ochrony fizycznej i logicznej (w tym szyfrowania) na nośniku zawierającym informacje objęte usługą, dopóki nie zostaną odpowiednio zniszczone lub oczyszczone.

- Należy włączyć Transparent Data Encryption baz danych SQL
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Niedołączone dyski powinny być szyfrowane
- Wymagaj szyfrowania na kontach Data Lake Store
- Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza

## <a name="control-of-operational-software"></a>Kontrola oprogramowania operacyjnego 

Organizacja identyfikuje nieautoryzowane oprogramowanie w systemie informacyjnym, w tym serwery, stacje robocze i laptopy, korzysta z zasad zezwalania na wszystkie, Odmów na to-wyjątku, aby uniemożliwić wykonywanie znanego nieautoryzowanego oprogramowania w systemie informacyjnym, a następnie sprawdza i aktualizuje listę nieautoryzowanego oprogramowania okresowo, ale nie mniej niż rocznie.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="change-control-procedures"></a>Procedury kontroli zmian

Integralność wszystkich obrazów maszyn wirtualnych jest zapewniona przez rejestrowanie i wywoływanie alertów dotyczących wszelkich zmian wprowadzonych w obrazach maszyn wirtualnych i udostępnianie ich właścicielom i/lub klientom za pomocą metod elektronicznych (np. portali lub alertów) wyników zmian lub przenoszenia oraz kolejnej weryfikacji integralności obrazu.

- \[\]Wyświetl podgląd wyników inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "zasady inspekcji systemu — szczegółowe śledzenie"
- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "zasady inspekcji systemu — szczegółowe śledzenie"

## <a name="control-of-technical-vulnerabilities"></a>Kontrola nad lukami w zabezpieczeniach 

Istnieje zaostrzony Standard konfiguracji dla wszystkich składników systemowych i sieciowych.

- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- \[\]Ocena luk w zabezpieczeniach w wersji zapoznawczej powinna być włączona na Virtual Machines
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- \[Wersja zapoznawcza \] : w usługach Kubernetes Services należy zdefiniować zasady zabezpieczeń

## <a name="segregation-in-networks"></a>Dzielenie w sieci

Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokują nieautoryzowany dostęp i służą do obsługi rozdzielania między wewnętrzną przewodową, wewnętrzną siecią bezprzewodową i zewnętrznymi segmentami sieci (np. Internetem), w tym stref DMZ i wymuszanie zasad kontroli dostępu dla każdej z domen.

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną
- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń
- Service Bus powinien używać punktu końcowego usługi sieci wirtualnej
- App Service powinien używać punktu końcowego usługi sieci wirtualnej
- SQL Server powinien używać punktu końcowego usługi sieci wirtualnej
- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej
- Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej
- Key Vault powinien używać punktu końcowego usługi sieci wirtualnej
- Podsieci bramy nie należy konfigurować przy użyciu sieciowej grupy zabezpieczeń
- Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej
- \[Wersja zapoznawcza \] : Container Registry powinna używać punktu końcowego usługi sieci wirtualnej
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci należy stosować na wewnętrznych maszynach wirtualnych

## <a name="network-connection-control"></a>Sterowanie połączeniem sieciowym

Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu w organizacji przez zaporę i inne ograniczenia związane z siecią dla każdego punktu dostępu do sieci lub z zarządzanym zewnętrznym interfejsem usługi telekomunikacyjnej.

- Należy włączyć bezpieczny transfer na konta magazynu
- Najnowsza wersja protokołu TLS powinna być używana w interfejsie API AP
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL
- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web w usłudze IaaS powinny być zaostrzone
- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone
- Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną
- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń

## <a name="network-controls"></a>Kontrolki sieci

Organizacja używa zabezpieczonych i szyfrowanych kanałów komunikacyjnych podczas migrowania serwerów fizycznych, aplikacji lub danych na serwery zwirtualizowane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci należy stosować na wewnętrznych maszynach wirtualnych
- Service Bus powinien używać punktu końcowego usługi sieci wirtualnej
- App Service powinien używać punktu końcowego usługi sieci wirtualnej
- SQL Server powinien używać punktu końcowego usługi sieci wirtualnej
- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej
- Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej
- Key Vault powinien używać punktu końcowego usługi sieci wirtualnej
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej
- \[Wersja zapoznawcza \] : Container Registry powinna używać punktu końcowego usługi sieci wirtualnej

## <a name="security-of-network-services"></a>Bezpieczeństwo Network Services

Zatwierdzone usługi udostępniane przez dostawcę/Menedżera usług sieciowych są zarządzane i monitorowane w celu zapewnienia, że są one bezpieczne.

- \[Wersja zapoznawcza \] : Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Windows
- \[Wersja zapoznawcza \] : Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Linux Network Watcher powinna być włączona

## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

Organizacja ogranicza użycie przenośnych nośników magazynu chronionych przez organizację przez autoryzowanych użytkowników w zewnętrznych systemach informacyjnych.

- Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji funkcji
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- Zdalne debugowanie powinno zostać wyłączone dla API Apps

## <a name="on-line-transactions"></a>Transakcje online

Organizacja wymaga użycia szyfrowania między, a korzystanie z podpisów elektronicznych przez, każda ze stron uwzględnionych w transakcji. Organizacja gwarantuje, że magazyn informacji o transakcji znajduje się poza wszystkimi publicznie dostępnymi środowiskami (np. na platformie magazynu istniejącej w intranecie organizacji i nie jest on przechowywany i ujawniany na nośniku magazynującym bezpośrednio dostępnym z Internetu. w przypadku używania zaufanego urzędu (np. do celów wystawiania i obsługiwania podpisów cyfrowych i/lub certyfikatów cyfrowych) zabezpieczenia są zintegrowane i osadzane w całym całym kompleksowym procesie zarządzania certyfikatami/sygnaturami.

- Należy włączyć bezpieczny transfer na konta magazynu
- Najnowsza wersja protokołu TLS powinna być używana w interfejsie API AP
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL
- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- \[Wersja zapoznawcza \] : Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym
- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym

## <a name="user-password-management"></a>Zarządzanie hasłami użytkowników

Hasła są szyfrowane podczas przesyłania i przechowywania na wszystkich składnikach systemu.

- \[Podgląd \] maszyn wirtualnych inspekcji z niezabezpieczonymi ustawieniami zabezpieczeń haseł

## <a name="user-authentication-for-external-connections"></a>Uwierzytelnianie użytkowników dla połączeń zewnętrznych

Metody silnego uwierzytelniania, takie jak uwierzytelnianie wieloskładnikowe, usługa RADIUS lub Kerberos (dla dostępu uprzywilejowanego) i CHAP (w przypadku szyfrowania poświadczeń dla metod telefonicznych) są implementowane dla wszystkich połączeń zewnętrznych z siecią organizacji.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="user-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie użytkownika

Użytkownicy, którzy wykonali funkcje uprzywilejowane (np. Administracja systemu), używają osobnych kont podczas wykonywania tych funkcji uprzywilejowanych. Metody uwierzytelniania wieloskładnikowego są używane zgodnie z zasadami organizacyjnymi (np. w przypadku dostępu zdalnego do sieci).

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera tylko określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera tylko określonych elementów członkowskich

## <a name="privilege-management"></a>Zarządzanie uprawnieniami

Dostęp do funkcji zarządzania lub konsol administracyjnych dla systemów obsługujących systemy wirtualne są ograniczone do pracowników opartych na zasadzie najniższych uprawnień i obsługiwanych przez kontrolę techniczną.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Porty zarządzania powinny być zamknięte na maszynach wirtualnych
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Inspekcja użycia niestandardowych reguł RBAC
- Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services

## <a name="review-of-user-access-rights"></a>Przegląd praw dostępu użytkownika

Organizacja przechowuje udokumentowaną listę autoryzowanych użytkowników zasobów informacji.

- Inspekcja użycia niestandardowych reguł RBAC

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Zdalna Ochrona danych diagnostycznych i konfiguracji

Porty, usługi i podobne aplikacje zainstalowane na komputerze lub w systemach sieciowych, które nie są wymagane w celu zapewnienia funkcjonalności biznesowej, są wyłączone lub usuwane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Porty zarządzania powinny być zamknięte na maszynach wirtualnych
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- Zdalne debugowanie powinno zostać wyłączone dla API Apps
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="audit-logging"></a>Rejestrowanie inspekcji

Dzienniki komunikatów wysłanych i odebranych są obsługiwane, w tym data, godzina, źródło i miejsce docelowe wiadomości, ale nie jej zawartość. Inspekcja jest zawsze dostępna, gdy system jest aktywny i śledzi najważniejsze zdarzenia, dostęp do danych zakończonych sukcesem/zakończonych niepowodzeniem, zmiany konfiguracji zabezpieczeń systemu, uprawnienia uprzywilejowane lub użycie narzędzi, wszelkie alarmy zgłoszone, aktywację i cofnięcie aktywacji systemów ochrony (np. a/V i identyfikatorów), aktywacji i dezaktywacji mechanizmów identyfikacji i uwierzytelniania oraz tworzenia i usuwania obiektów poziomu systemu.

- Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone
- Dzienniki diagnostyczne w Logic Apps powinny być włączone 
- Dzienniki diagnostyczne w IoT Hub powinny być włączone 
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych 
- Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone 
- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone 
- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone 
- Dzienniki diagnostyczne w App Services powinny być włączone 
- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone 
- Dzienniki diagnostyczne w Key Vault powinny być włączone 
- Dzienniki diagnostyczne w Service Bus powinny być włączone
- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone
- Inspekcja w programie SQL Server powinna być włączona
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów

## <a name="monitoring-system-use"></a>Monitorowanie użycia systemu

Zautomatyzowane systemy wdrożone w środowisku organizacji są używane do monitorowania najważniejszych zdarzeń i nietypowej aktywności oraz analizowania dzienników systemowych, których wyniki są regularnie przeglądane. Monitorowanie obejmuje operacje uprzywilejowane, autoryzowany dostęp lub próby nieautoryzowanego dostępu, w tym próby dostępu do zdezaktywowanych kont, a także alerty lub błędy systemowe.

- Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów
- Na maszynach wirtualnych należy zainstalować agenta Log Analytics
- Agenta Log Analytics należy zainstalować na Virtual Machine Scale Sets
- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji maszyn wirtualnych z systemem Windows, na których agent log Analytics nie jest połączony zgodnie z oczekiwaniami
- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, na których agent log Analytics nie jest połączony zgodnie z oczekiwaniami
- Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"
- Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji

## <a name="segregation-of-duties"></a>Rozdzielenie obowiązków

Rozdzielenie obowiązków jest stosowane w celu ograniczenia ryzyka nieautoryzowanej lub przypadkowej modyfikacji informacji i systemów. Żadna osoba nie może uzyskać dostępu do systemów informacyjnych ani ich modyfikować ani korzystać z nich bez autoryzacji i wykrywania. Dostęp do osób odpowiedzialnych za administrowanie i kontrola dostępu jest ograniczony do minimalnych potrzeb w zależności od roli i odpowiedzialności poszczególnych użytkowników, a te osoby nie mogą uzyskać dostępu do funkcji inspekcji związanych z tymi kontrolkami.

- Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services
- Inspekcja użycia niestandardowych reguł RBAC
- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w temacie
- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "przypisanie praw użytkownika"
- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-Kontrola konta użytkownika"
- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń-Kontrola konta użytkownika"
- Role niestandardowego właściciela subskrypcji nie powinny istnieć

## <a name="administrator-and-operator-logs"></a>Dzienniki administratorów i operatorów

Organizacja zapewnia, że w celu inspekcji działań administratorów jest włączone odpowiednie rejestrowanie; i regularnie przegląda dzienniki i administratorów systemu.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="identification-of-risks-related-to-external-parties"></a>Identyfikacja zagrożeń związanych ze stronami zewnętrznymi

Połączenia dostępu zdalnego między organizacją a zewnętrznymi stronami są szyfrowane

- Należy włączyć bezpieczny transfer na konta magazynu
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL
- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL
- Należy włączyć tylko bezpieczne połączenia z Redis Cache

## <a name="business-continuity-and-risk-assessment"></a>Ciągłość działania i Ocena ryzyka

Organizacja identyfikuje swoje krytyczne procesy biznesowe i integruje wymagania w zakresie zarządzania zabezpieczeniami informacji o ciągłości biznesowej z innymi wymaganiami w zakresie ciągłości, które dotyczą takich aspektów, jak operacje, personel, materiały, transport i zakłady.

- Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii
- Key Vault obiektów powinno być możliwe do odzyskania
- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń — Konsola odzyskiwania"
- \[\]Wyświetl podgląd wyników inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — Konsola odzyskiwania"

## <a name="back-up"></a>Utwórz kopię zapasową

Ten plan przypisuje definicje Azure Policy, które Przeprowadź inspekcję informacji o kopii zapasowej systemu w organizacji w alternatywnej witrynie magazynowej. W przypadku fizycznej wysyłki metadanych magazynu należy rozważyć użycie Azure Data Box.

- Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB
- Azure Backup powinna być włączona dla Virtual Machines

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Sprawdzono Mapowanie formantów przykładu planu HIPAA HITRUST. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="next step action"]
> [HIPAA HiTRUST — Omówienie](./control-mapping.md) 
>  [HIPAA HiTRUST — etapy wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

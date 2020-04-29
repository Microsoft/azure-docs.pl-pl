---
title: Przykładowe kontrolki planu HIPAA HITRUST
description: Sterowanie mapowaniem przykładów planu HIPAA HITRUST. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472310"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu HIPAA HITRUST

W poniższym artykule szczegółowo opisano sposób, w jaki plan platformy Azure HIPAA HITRUST plan jest mapowany do kontrolek HITRUST HIPAA. Aby uzyskać więcej informacji na temat kontrolek, zobacz [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Następujące mapowania są do kontrolek **HIPAA HiTRUST** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję ** \[wersja zapoznawcza\]: Inspekcja HIPAA HiTRUST kontroluje** wbudowaną inicjatywę Policy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontrola nad złośliwym kodem

Ten plan ułatwia zarządzanie programem Endpoint Protection, w tym złośliwą ochroną kodu, przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brak ochrony punktu końcowego na maszynach wirtualnych w Azure Security Center i wymuszają rozwiązanie Microsoft chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych z systemem Windows.

- Wdróż domyślne rozszerzenie programu Microsoft IaaS chroniące przed złośliwym kodem dla systemu Windows Server
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych
- Należy zainstalować aktualizacje systemu na maszynach


## <a name="management-of-removable-media"></a>Zarządzanie nośnikiem wymiennym

W organizacji, na podstawie poziomu klasyfikacji danych, rejestruje nośniki (w tym laptopy) przed użyciem, umieszcza uzasadnione ograniczenia dotyczące tego nośnika, a także zapewnia odpowiedni poziom ochrony fizycznej i logicznej (w tym szyfrowania) na nośniku zawierającym informacje objęte usługą, dopóki nie zostaną odpowiednio zniszczone lub oczyszczone.

- Wymagaj szyfrowania na kontach Data Lake Store
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Należy włączyć Transparent Data Encryption baz danych SQL


## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

Dostawcy usług w chmurze wykorzystują platformę wirtualizacji rozpoznaną przez branżę i standardowe formaty wirtualizacji (np. Open Virtualization Format, OVF), aby pomóc w zapewnieniu współdziałania i udokumentowania niestandardowymi zmianami wprowadzonymi do dowolnych funkcji hypervisor i wszystkich punktów zaczepienia wirtualizacji określonych dla danego rozwiązania.

- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, na których nie zainstalowano określonych aplikacji

## <a name="control-of-operational-software"></a>Kontrola oprogramowania operacyjnego 

Organizacja identyfikuje nieautoryzowane oprogramowanie w systemie informacyjnym, w tym serwery, stacje robocze i laptopy, korzysta z zasad zezwalania na wszystkie, Odmów na to-wyjątku, aby uniemożliwić wykonywanie znanego nieautoryzowanego oprogramowania w systemie informacyjnym, a następnie sprawdza i aktualizuje listę nieautoryzowanego oprogramowania okresowo, ale nie mniej niż rocznie.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-inspekcja"
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "zasady inspekcji systemu — Zarządzanie kontem"

## <a name="change-control-procedures"></a>Procedury kontroli zmian

Integralność wszystkich obrazów maszyn wirtualnych jest zapewniona przez rejestrowanie i wywoływanie alertów dotyczących wszelkich zmian wprowadzonych w obrazach maszyn wirtualnych i udostępnianie ich właścicielom i/lub klientom za pomocą metod elektronicznych (np. portali lub alertów) wyników zmian lub przenoszenia oraz kolejnej weryfikacji integralności obrazu.

- \[Wyświetl\] Podgląd wyników inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "zasady inspekcji systemu — szczegółowe śledzenie"

## <a name="inventory-of-assets"></a>Spis zasobów 

Jest zachowywany spis zasobów i usług

- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone.
- \[Podgląd\] wymagań dotyczących wdrażania w celu przeprowadzenia inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń — serwer sieci Microsoft"
- \[Wersja\] zapoznawcza wymagania dotyczące konfigurowania maszyn wirtualnych z systemem Windows w systemie "Szablony administracyjne-Network"

## <a name="control-of-technical-vulnerabilities"></a>Kontrola nad lukami w zabezpieczeniach 

Istnieje zaostrzony Standard konfiguracji dla wszystkich składników systemowych i sieciowych.

- Inspekcja Virtual Machines bez skonfigurowanego odzyskiwania po awarii
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Usterka powinna zostać skorygowana przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="segregation-in-networks"></a>Dzielenie w sieci

Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokują nieautoryzowany dostęp i służą do obsługi rozdzielania między wewnętrzną przewodową, wewnętrzną siecią bezprzewodową i zewnętrznymi segmentami sieci (np. Internetem), w tym stref DMZ i wymuszanie zasad kontroli dostępu dla każdej z domen.

- Automatyczna obsługa administracyjna agenta monitorowania zabezpieczeń
- Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych

## <a name="input-data-validation"></a>Sprawdzanie poprawności danych wejściowych

W przypadku wszystkich publicznych aplikacji sieci Web zapory na poziomie aplikacji są implementowane w celu sterowania ruchem. W przypadku aplikacji publicznych, które nie są oparte na sieci Web, organizacja zaimplementował zaporę opartą na sieci specyficzną dla typu aplikacji. Jeśli ruch do aplikacji publicznej jest szyfrowany, urządzenie znajduje się za szyfrowaniem lub może odszyfrować ruch przed analizą.

- \[Wyświetl\] Podgląd wyników inspekcji z konfiguracji maszyn wirtualnych z systemem Windows we właściwościach zapory systemu Windows


## <a name="network-connection-control"></a>Sterowanie połączeniem sieciowym

Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu w organizacji przez zaporę i inne ograniczenia związane z siecią dla każdego punktu dostępu do sieci lub z zarządzanym zewnętrznym interfejsem usługi telekomunikacyjnej.

- Dostęp za poorednictwem punktów końcowych mających połączenie z Internetem powinien być ograniczony
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza
- Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API

## <a name="network-controls"></a>Kontrolki sieci

Organizacja używa zabezpieczonych i szyfrowanych kanałów komunikacyjnych podczas migrowania serwerów fizycznych, aplikacji lub danych na serwery zwirtualizowane.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w temacie "Opcje zabezpieczeń-dostęp sieciowy"
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows we właściwościach zapory systemu Windows
- Wdróż ustawienia diagnostyki z sieciowych grup zabezpieczeń
- Dostęp za poorednictwem punktów końcowych mających połączenie z Internetem powinien być ograniczony

## <a name="sensitive-system-isolation"></a>Izolacja systemu poufnego

Udostępnione zasoby systemu (np. rejestry, pamięć główna, magazyn pomocniczy) są zwracane z powrotem do systemu, chronione przed ujawnieniem w innych systemach/aplikacjach/użytkowników, a użytkownicy nie mogą celowo lub przypadkowo uzyskać dostępu do pozostałej informacji.

- Virtual Machines należy migrować do nowych zasobów Azure Resource Manager

## <a name="security-of-network-services"></a>Bezpieczeństwo Network Services

Zatwierdzone usługi udostępniane przez dostawcę/Menedżera usług sieciowych są zarządzane i monitorowane w celu zapewnienia, że są one bezpieczne.

- Virtual Machines należy migrować do nowych zasobów Azure Resource Manager

## <a name="network-routing-control"></a>Kontrola routingu sieciowego

Kontrolki routingu są implementowane za pomocą bram zabezpieczeń (np. zapór) używanych między sieciami wewnętrznymi i zewnętrznymi (np. Internet i sieci innych firm).

- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

Organizacja ogranicza użycie przenośnych nośników magazynu chronionych przez organizację przez autoryzowanych użytkowników w zewnętrznych systemach informacyjnych.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web
- Aplikacja interfejsu APi powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="electronic-messaging"></a>Wiadomości elektroniczne

Zatwierdzenia są uzyskiwane przed użyciem zewnętrznych usług publicznych, w tym do obsługi wiadomości błyskawicznych lub udostępniania plików.

- \[Wyświetl\] Podgląd wyników inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku hasła ustawione na 0644

## <a name="on-line-transactions"></a>Transakcje online

Organizacja wymaga użycia szyfrowania między, a korzystanie z podpisów elektronicznych przez, każda ze stron uwzględnionych w transakcji. Organizacja gwarantuje, że magazyn informacji o transakcji znajduje się poza wszystkimi publicznie dostępnymi środowiskami (np. na platformie magazynu istniejącej w intranecie organizacji i nie jest on przechowywany i ujawniany na nośniku magazynującym bezpośrednio dostępnym z Internetu. w przypadku używania zaufanego urzędu (np. do celów wystawiania i obsługiwania podpisów cyfrowych i/lub certyfikatów cyfrowych) zabezpieczenia są zintegrowane i osadzane w całym całym kompleksowym procesie zarządzania certyfikatami/sygnaturami.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- \[Wyświetl\] Podgląd wyników inspekcji z maszyn wirtualnych z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym

## <a name="password-management"></a>Zarządzanie hasłami

Hasła są szyfrowane podczas przesyłania i przechowywania na wszystkich składnikach systemu.

- \[Wyświetl\] Podgląd wyników inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła

## <a name="user-authentication-for-external-connections"></a>Uwierzytelnianie użytkowników dla połączeń zewnętrznych

Metody silnego uwierzytelniania, takie jak uwierzytelnianie wieloskładnikowe, usługa RADIUS lub Kerberos (dla dostępu uprzywilejowanego) i CHAP (w przypadku szyfrowania poświadczeń dla metod telefonicznych) są implementowane dla wszystkich połączeń zewnętrznych z siecią organizacji.

- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Kontrola dostępu just in Time do sieci maszyn wirtualnych

## <a name="user-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie użytkownika

Użytkownicy, którzy wykonali funkcje uprzywilejowane (np. Administracja systemu), używają osobnych kont podczas wykonywania tych funkcji uprzywilejowanych. Metody uwierzytelniania wieloskładnikowego są używane zgodnie z zasadami organizacyjnymi (np. w przypadku dostępu zdalnego do sieci).

- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Kontrola dostępu just in Time do sieci maszyn wirtualnych

## <a name="privilege-management"></a>Zarządzanie uprawnieniami

Dostęp do funkcji zarządzania lub konsol administracyjnych dla systemów obsługujących systemy wirtualne są ograniczone do pracowników opartych na zasadzie najniższych uprawnień i obsługiwanych przez kontrolę techniczną.

- Kontrola dostępu just in Time do sieci maszyn wirtualnych
- \[W\] przypadku usług Kubernetes należy używać Access Control na podstawie ról (RBAC).

## <a name="review-of-user-access-rights"></a>Przegląd praw dostępu użytkownika

Organizacja przechowuje udokumentowaną listę autoryzowanych użytkowników zasobów informacji.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w temacie "Opcje zabezpieczeń-konta"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Zdalna Ochrona danych diagnostycznych i konfiguracji

Porty, usługi i podobne aplikacje zainstalowane na komputerze lub w systemach sieciowych, które nie są wymagane w celu zapewnienia funkcjonalności biznesowej, są wyłączone lub usuwane.

- Porty zarządzania powinny być zamknięte na maszynach wirtualnych
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować

## <a name="audit-logging"></a>Rejestrowanie inspekcji

Dzienniki komunikatów wysłanych i odebranych są obsługiwane, w tym data, godzina, źródło i miejsce docelowe wiadomości, ale nie jej zawartość. Inspekcja jest zawsze dostępna, gdy system jest aktywny i śledzi najważniejsze zdarzenia, dostęp do danych zakończonych sukcesem/zakończonych niepowodzeniem, zmiany konfiguracji zabezpieczeń systemu, uprawnienia uprzywilejowane lub użycie narzędzi, wszelkie alarmy zgłoszone, aktywację i cofnięcie aktywacji systemów ochrony (np. a/V i identyfikatorów), aktywacji i dezaktywacji mechanizmów identyfikacji i uwierzytelniania oraz tworzenia i usuwania obiektów poziomu systemu.

- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych

## <a name="monitoring-system-use"></a>Monitorowanie użycia systemu

Zautomatyzowane systemy wdrożone w środowisku organizacji są używane do monitorowania najważniejszych zdarzeń i nietypowej aktywności oraz analizowania dzienników systemowych, których wyniki są regularnie przeglądane. Monitorowanie obejmuje operacje uprzywilejowane, autoryzowany dostęp lub próby nieautoryzowanego dostępu, w tym próby dostępu do zdezaktywowanych kont, a także alerty lub błędy systemowe.

- Dzienniki diagnostyczne w zestawach skalowania maszyn wirtualnych powinny być włączone

## <a name="segregation-of-duties"></a>Rozdzielenie obowiązków

Rozdzielenie obowiązków jest stosowane w celu ograniczenia ryzyka nieautoryzowanej lub przypadkowej modyfikacji informacji i systemów. Żadna osoba nie może uzyskać dostępu do systemów informacyjnych ani ich modyfikować ani korzystać z nich bez autoryzacji i wykrywania. Dostęp do osób odpowiedzialnych za administrowanie i kontrola dostępu jest ograniczony do minimalnych potrzeb w zależności od roli i odpowiedzialności poszczególnych użytkowników, a te osoby nie mogą uzyskać dostępu do funkcji inspekcji związanych z tymi kontrolkami.

- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="administrator-and-operator-logs"></a>Dzienniki administratorów i operatorów

Organizacja zapewnia, że w celu inspekcji działań administratorów jest włączone odpowiednie rejestrowanie; i regularnie przegląda dzienniki i administratorów systemu.

- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="identification-of-risks-related-to-external-parties"></a>Identyfikacja zagrożeń związanych ze stronami zewnętrznymi

Połączenia dostępu zdalnego między organizacją a zewnętrznymi stronami są szyfrowane

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="business-continuity-and-risk-assessment"></a>Ciągłość działania i Ocena ryzyka

Organizacja identyfikuje swoje krytyczne procesy biznesowe i integruje wymagania w zakresie zarządzania zabezpieczeniami informacji o ciągłości biznesowej z innymi wymaganiami w zakresie ciągłości, które dotyczą takich aspektów, jak operacje, personel, materiały, transport i zakłady.

- \[Wyświetl\] Podgląd wyników inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — Konsola odzyskiwania"

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Sprawdzono Mapowanie formantów przykładu planu HIPAA HITRUST. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="next step action"]
> [HIPAA HiTRUST plan — przegląd](./control-mapping.md)
> [HIPAA HiTRUST plan — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

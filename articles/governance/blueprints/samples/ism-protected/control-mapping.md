---
title: Kontrolki dotyczące przykładowej strategii CHRONIONEj przez rząd ISM
description: Kontrolowanie mapowania australijskiego planu ochrony instytucji rządowych z instytucją ISM. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: df143a871db3d871fe6354eab23b7ca6dc012b78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680000"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Sterowanie mapowaniem przykładowej strategii CHRONIONEj przez rząd ISM

W tym artykule szczegółowo przedstawiono sposób, w jaki plany platformy Azure australijskiego planu ochrony instytucji rządowych są mapowane na kontrolki chronione ISM. Aby uzyskać więcej informacji na temat kontrolek, zobacz Ochrona za pomocą usługi [ISM](https://www.cyber.gov.au/ism).

Następujące mapowania są do formantów **chronionych przez ISM** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz wersję **\[ zapoznawczą \] : Inspekcja australijskiego nadzoru rządowego centrum kontroli ochrony i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu obsługi inspekcji wbudowanych wymagań** zasad.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Ograniczenia lokalizacji

Ten plan pomaga ograniczyć lokalizację wdrożenia wszystkich zasobów i grup zasobów do "Australii środkowe", "Australia Central2", "Australia Wschodnia" i "Australia Południowo-Wschodnia", przypisując następujące definicje Azure Policy:

- Dozwolone lokalizacje (zostały trwale zakodowane na "Australia Środkowa", "Australia Central2", "Australia Wschodnia" i "Australia Południowo-Wschodnia")
- Dozwolone lokalizacje dla grup zasobów (zostały trwale zakodowane na "Australia Środkowa", "Australia Central2", "Australia Wschodnia" i "Australia Południowo-Wschodnia")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Wskazówki dotyczące bezpieczeństwa personelu — dostęp do systemów i ich zasobów

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 pracowników przyznających dostęp do systemu i jego zasobów można jednoznacznie zidentyfikować

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 standardowy dostęp do systemów, aplikacji i repozytoriów danych jest ograniczony do tych, które są wymagane przez personel do podejmowania obowiązków

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 uprzywilejowany dostęp do systemów, aplikacje i repozytoria danych są weryfikowane podczas pierwszego żądania i ponownie weryfikowane w ciągu roku lub częściej.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 uprzywilejowany dostęp do systemów, aplikacji i repozytoriów danych jest ograniczony do tych, które są wymagane dla personelu do podejmowania obowiązków

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 korzystanie ze współużytkowanych kont użytkowników jest ściśle kontrolowane i personel korzystający z takich kont można jednoznacznie zidentyfikować

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 uprzywilejowani użytkownicy mają przypisane specjalne konto uprzywilejowane, które ma być używane wyłącznie do zadań wymagających dostępu uprzywilejowanego

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 dostęp do systemów, aplikacji i repozytoriów danych jest usuwany lub zawieszany w tym samym dniu pracownicy nie mają już uzasadnionego wymogu dostępu

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441, gdy personel otrzymuje tymczasowy dostęp do systemu, obowiązujące środki kontroli bezpieczeństwa są stosowane w celu ograniczenia ich dostępu tylko do informacji wymaganych do podjęcia ich obowiązków

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Wskazówki dotyczące ograniczania funkcjonalności systemu operacyjnego

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 Najnowsza wersja (N) lub wersja N-1 systemu operacyjnego jest używana w standardowych środowiskach operacyjnych (SOEs)

- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 niepotrzebne konta systemu operacyjnego, oprogramowanie, składniki, usługi i funkcje są usuwane lub wyłączone

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 rozwiązanie listy dozwolonych aplikacji jest zaimplementowane na wszystkich serwerach, aby ograniczyć wykonywanie plików wykonywalnych, bibliotek oprogramowania, skryptów i instalatorów do zatwierdzonego zestawu

- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 oprogramowanie antywirusowe jest zaimplementowane na stacjach roboczych i serwerach i skonfigurowane z: wykrywanie oparte na sygnaturze włączone i ustawione na wysoki poziom, oparte na heurystyki wykrywanie włączone i ustawione na wysoki poziom, sygnatury wykrywania sprawdzane dla waluty i aktualizowane co najmniej codziennie, automatyczne i regularne skanowanie skonfigurowane dla wszystkich dysków stałych i nośników wymiennych

- Rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Wskazówki dotyczące ograniczania funkcjonalności systemu — Ograniczanie funkcjonalności uwierzytelniania

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 użytkowników są uwierzytelniani przed przyznaniem im dostępu do systemu i jego zasobów

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które mają konta bez hasła
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które mają konta bez hasła

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 uwierzytelnianie wieloskładnikowe służy do uwierzytelniania użytkowników standardowych

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 uwierzytelnianie wieloskładnikowe służy do uwierzytelniania wszystkich uprzywilejowanych użytkowników i innych pozycji zaufania

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>hasła 0421 używane do uwierzytelniania jednoskładnikowego są co najmniej 14 znakami ze złożonością, najlepiej w przypadku 4 losowych słów

- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Ustawienia zabezpieczeń-Zasady konta"
- Wdróż wymagania wstępne w celu przeprowadzenia inspekcji konfiguracji maszyn wirtualnych z systemem Windows w sekcji "Ustawienia zabezpieczeń — Zasady konta"

## <a name="guidelines-for-system-management---system-administration"></a>Wskazówki dotyczące zarządzania systemem — Administracja systemu

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 uwierzytelnianie wieloskładnikowe służy do uwierzytelniania użytkowników za każdym razem, gdy wykonują uprzywilejowane akcje

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 ruch związany z zarządzaniem jest dozwolony tylko w strefach sieciowych, które są używane do administrowania systemami i aplikacjami

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Zdalne debugowanie powinno zostać wyłączone dla API Apps
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web

## <a name="guidelines-for-system-management---system-patching"></a>Wskazówki dotyczące zarządzania systemem — stosowanie poprawek systemu

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 luki w zabezpieczeniach w aplikacjach i sterownikach, które zostały ocenione jako skrajne ryzyko, zostały poprawione, zaktualizowane lub skorygowane w ciągu 48 godzin od wykrytych luk w zabezpieczeniach przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na maszynach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 luki w zabezpieczeniach w aplikacjach i sterownikach, które zostały ocenione jako wysokie ryzyko, zostały zaktualizowane lub skorygowane w ciągu dwóch tygodni od luki w zabezpieczeniach, które są identyfikowane przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 luki w zabezpieczeniach w aplikacjach i sterownikach, które zostały ocenione jako umiarkowane lub niskie ryzyko, zostały poprawione, zaktualizowane lub skorygowane w ciągu miesiąca o luki w zabezpieczeniach identyfikowane przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 luki w zabezpieczeniach w systemach operacyjnych i oprogramowaniu układowym oceniane jako skrajne ryzyko są poprawiane, aktualizowane lub rozwiązywane przez 48 godz. luk w zabezpieczeniach identyfikowanych przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 luki w zabezpieczeniach w systemach operacyjnych i oprogramowaniu układowym są oceniane w ramach wysokiego ryzyka, aktualizowane lub zmniejszane w ciągu dwóch tygodni od luki w zabezpieczeniach identyfikowanej przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 luki w zabezpieczeniach systemów operacyjnych i oprogramowania układowego, które zostały ocenione jako umiarkowane lub niskie ryzyko, zostały poprawione, zaktualizowane lub skorygowane w ciągu miesiąca o luki w zabezpieczeniach identyfikowane przez dostawców, niezależnych stron trzecich, menedżerów systemu lub użytkowników

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Wskazówki dotyczące zarządzania systemem — tworzenie kopii zapasowych i przywracanie danych

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 kopie zapasowe ważnych informacji, oprogramowania i ustawień konfiguracji są wykonywane co najmniej codziennie

- Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Wskazówki dotyczące monitorowania systemu — rejestrowanie i Inspekcja zdarzeń

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 zaimplementowano scentralizowaną funkcję rejestrowania, a systemy są skonfigurowane do zapisywania dzienników zdarzeń w scentralizowanym obiekcie rejestrowania, jak najszybciej po wystąpieniu każdego zdarzenia

- Subskrypcje platformy Azure powinny mieć profil dziennika aktywności

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 następujące zdarzenia są rejestrowane w systemach operacyjnych: dostęp do ważnych danych i procesów, awarii aplikacji i wszystkich komunikatów o błędach, próbuje użyć specjalnych uprawnień, zmian kont, zmian w zasadach zabezpieczeń, zmianach konfiguracji systemu, systemu nazw domen (DNS) i żądań protokołu HTTP (Hypertext Transfer Protocol), nieudanych prób dostępu do danych i zasobów systemowych, niepowodzeń usługi i ponownego uruchomienia systemu. , transfer danych do nośników zewnętrznych, zarządzanie użytkownikami lub grupami, używanie specjalnych uprawnień

- \[Wersja zapoznawcza \] : Inspekcja wdrożenia agenta log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Inspekcja wdrożenia agenta Log Analytics w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- Obszar roboczy Log Analytics inspekcji dla maszyny wirtualnej — niezgodność raportów
- Przeprowadzanie inspekcji ustawienia diagnostyki

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 następujące zdarzenia są rejestrowane dla baz danych: dostęp do szczególnie ważnych informacji, dodawanie nowych użytkowników, szczególnie uprzywilejowanych użytkowników, dowolnych zapytań zawierających komentarze, wszelkie zapytania zawierające wiele osadzonych zapytań, wszelkie zapytania lub alerty bazy danych, próby podniesienia uprawnień, próba uzyskania dostępu zakończony powodzeniem lub niepowodzeniem, zmiany struktury bazy danych, zmiany ról użytkownika lub uprawnienia do bazy danych, akcje administratora bazy danych , logowania do bazy danych i wylogowywania, modyfikacje danych, użycie poleceń wykonywalnych

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Wskazówki dotyczące monitorowania systemu — zarządzanie lukami

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>oceny luk w zabezpieczeniach (0911) i testy penetracji są przeprowadzane przez odpowiednio wykwalifikowany personel przed wdrożeniem systemu, po znacznej zmianie systemu i co najmniej raz na określony przez właściciela systemu

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować

## <a name="guidelines-for-database-systems-management---database-servers"></a>Wskazówki dotyczące zarządzania systemami bazy danych — serwery baz danych

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>dyski twarde serwerów baz danych 1425 są szyfrowane przy użyciu pełnego szyfrowania dysków

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Należy włączyć Transparent Data Encryption baz danych SQL

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 informacje przesyłane między serwerami baz danych i aplikacjami sieci Web są szyfrowane

- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Należy włączyć bezpieczny transfer na konta magazynu
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdróż wymagania wstępne w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Wskazówki dotyczące zarządzania systemami bazy danych — oprogramowanie systemowe zarządzania bazami danych

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 domyślne konta administratora bazy danych są wyłączone, zmieniono ich nazwy lub zmieniono ich hasła

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>Administratorzy baz danych 1262 mają unikatowe i identyfikowalne konta

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>konta administratorów bazy danych 1261 nie są współużytkowane przez różne bazy danych

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>konta administratorów bazy danych 1263 są używane wyłącznie na potrzeby zadań administracyjnych, a standardowe konta bazy danych używane na potrzeby interakcji ogólnego przeznaczenia z bazą danych

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 dostęp administratora bazy danych jest ograniczony do zdefiniowanych ról, a nie kont z domyślnymi uprawnieniami administracyjnymi lub wszystkie uprawnienia

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Wskazówki dotyczące korzystania z podstaw kryptografii kryptograficznej

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 oprogramowanie do szyfrowania używane do przechowywania danych w spoczynku implementuje pełne szyfrowanie dysków lub częściowe szyfrowanie, w przypadku gdy kontrolki dostępu będą zezwalać na zapisywanie na partycji zaszyfrowanej

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Wskazówki dotyczące korzystania z kryptografii Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 jest używana tylko Najnowsza wersja protokołu TLS

- Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji
- Wdróż wymagania wstępne w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Wskazówki dotyczące transferów danych i filtrowania zawartości — filtrowanie zawartości

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 skanowanie antywirusowe przy użyciu wielu różnych aparatów skanowania jest wykonywane na całej zawartości

- Rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Wskazówki dotyczące transferów danych i filtrowania zawartości — tworzenie aplikacji sieci Web

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 cała zawartość aplikacji sieci Web jest oferowana wyłącznie przy użyciu protokołu HTTPS

- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć tylko bezpieczne połączenia z Redis Cache

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>w przypadku aplikacji sieci Web, które są przeznaczone do ochrony zarówno aplikacji sieci Web, jak i ich użytkowników, są implementowane formanty zabezpieczeń oparte na przeglądarce sieci Web 1424.

- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Wskazówki dotyczące zarządzania siecią — projektowanie i konfiguracja sieci

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 kontroli dostępu do sieci są implementowane w sieciach, aby zapobiec połączeniu nieautoryzowanych urządzeń sieciowych

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 kontroli dostępu do sieci są implementowane w celu ograniczenia ruchu wewnątrz i między segmentami sieci tylko do tych, które są wymagane do celów służbowych

- Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Wskazówki dotyczące zarządzania siecią — ciągłość usługi dla Usługi online

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 atak typu "odmowa usługi" ataku i strategie zaradcze są omawiane z dostawcami usług, w odróżnieniu od ich zdolności do wydzielenia ataków typu "odmowa usługi", wszelkich kosztów, które mogą być naliczane przez klientów wynikających z ataków typu "odmowa usługi". progi dla powiadamiania klientów lub wyłączania ich Usługi online podczas ataków typu "odmowa usługi", wstępnie zatwierdzonych działań, które mogą być podejmowane w trakcie ataków typu "odmowa usługi", procedury zapobiegania atakom typu "odmowa usługi" z dostawcami nadrzędnymi w celu zablokowania złośliwego ruchu jako możliwie najbliżej strumienia

- Należy włączyć Standard DDoS Protection


> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

> [!div class="nextstepaction"]
> Strategia [chroniona przez ISM — Omówienie](./index.md) 
>  [Plan chroniony przez usługę ISM — kroki wdrażania](./deploy.md)

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

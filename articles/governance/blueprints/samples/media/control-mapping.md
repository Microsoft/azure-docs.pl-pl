---
title: Przykładowe kontrolki multimediów
description: Sterowanie mapowaniem przykładów planu multimediów. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035040"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu multimediów

W tym artykule szczegółowo przedstawiono sposób mapowania przykładu planu nośnika na platformę Azure do kontrolek multimediów. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Następujące mapowania znajdują się w kontrolkach **multimediów** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję **\[ Podgląd \] : Inspekcja nośnika** — wbudowana inicjatywa Policy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Kontrola dostępu

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1 — Upewnij się, że nie istnieje główny klucz dostępu

- \[Wersja zapoznawcza \] : Wdróż wymagania wstępne w celu inspekcji maszyn wirtualnych z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2 — hasła, numery PIN i tokeny muszą być chronione

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8 — dostęp do udostępnionego konta jest zabroniony

- Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw Service Bus

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9 — system musi ograniczyć dostęp do autoryzowanych użytkowników.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14 — system musi wymusić prawa dostępu.

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w temacie

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15 — Zapobiegaj nieautoryzowanemu dostępowi do istotnych informacji lub funkcji zabezpieczeń.

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń-ustawienia systemu"

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 — separacja obowiązków musi być wymuszana przez odpowiednie przypisanie roli.

- [Wersja zapoznawcza \] : Role-Based Access Control (RBAC) powinna być używana w usługach Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40 — upewnij się, że systemy nie łączą zaufanych sieci i niezaufanych sieci w tym samym czasie.

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-dostęp sieciowy"

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43 — dostęp zdalny do osób nienależących do pracowników musi być ograniczony, aby zezwalać na dostęp tylko do zatwierdzonych systemów informacji

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50 — zdarzenia związane z zabezpieczeniami dzienników dla wszystkich składników systemu informacji.

- Dzienniki diagnostyczne w Logic Apps powinny być włączone

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54 — upewnij się, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone dla wszystkich użytkowników konsoli w chmurze.

- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów.

## <a name="auditing--logging"></a>Inspekcja & rejestrowanie

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1 — zdarzenia zakończone powodzeniem i niepowodzeniem muszą być rejestrowane.

- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16 — urządzenia sieciowe/wystąpienia muszą rejestrować wszelkie zdarzenia sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez to urządzenie/wystąpienie sieciowe (ELBs, zapory aplikacji sieci Web itp.)

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-konta"

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17-serwery/wystąpienia muszą rejestrować wszelkie zdarzenia sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez ten serwer/wystąpienie

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-konta"

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19 — zdarzenia domeny muszą rejestrować wszelkie zdarzenia sklasyfikowane jako krytyczne lub wysokie zdarzenie zabezpieczeń przez oprogramowanie do zarządzania domeną

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-konta"
- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-Klient sieci Microsoft"

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20 — zdarzenia domeny muszą rejestrować wszelkie zdarzenia sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez kontrolę zabezpieczeń domeny

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-konta"

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21 — zdarzenia domeny muszą rejestrować dowolny dostęp lub zmiany w dzienniku domeny

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — Konsola odzyskiwania"

## <a name="cryptographic-controls"></a>Formanty kryptograficzne

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2 — aplikacje i systemy muszą używać bieżących rozwiązań kryptograficznych do ochrony danych.

- Należy włączyć Transparent Data Encryption baz danych SQL
- Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności.

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5 — certyfikaty cyfrowe muszą być podpisane przez zatwierdzony urząd certyfikacji.

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zawierają certyfikaty, których ważność wygaśnie w ciągu określonej liczby dni

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6 — Certyfikaty cyfrowe muszą być przypisane do użytkownika lub urządzenia.

- \[Wersja zapoznawcza \] : Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które zawierają certyfikaty wygasające w ciągu określonej liczby dni

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>DW-4,7-materiał kryptograficzny musi być przechowywany, aby umożliwić odszyfrowywanie rekordów przez czas, gdy rekordy są zachowywane.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>Klucze DW-4,8-tajne i prywatne muszą być przechowywane bezpiecznie.

- Należy włączyć Transparent Data Encryption baz danych SQL
- Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności.

## <a name="change--config-management"></a>Zmień zarządzanie konfiguracją &

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5,2 — tylko autoryzowani użytkownicy mogą wdrażać zatwierdzone zmiany w systemie.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12 — utrzymuje aktualną, kompletną, dokładną i łatwo dostępną konfigurację bazową systemu informacji.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5,13 — wykorzystuj zautomatyzowane narzędzia do obsługi konfiguracji linii bazowej systemu informacji.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14 — identyfikowanie i wyłączanie niepotrzebnych i/lub niezabezpieczonych funkcji, portów, protokołów i usług.

- Interfejsy sieciowe powinny wyłączyć przekazywanie adresów IP
- \[Wersja zapoznawcza \] : przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19 — Monitoruj zmiany ustawień konfiguracji zabezpieczeń.

- Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5,22 — upewnij się, że tylko autoryzowane oprogramowanie i aktualizacje są zainstalowane w systemach firmy.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia

## <a name="identity--authentication"></a>Uwierzytelnianie & tożsamości

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1 — konta użytkowników muszą być jednoznacznie przypisane do osób w celu uzyskania dostępu do informacji, które nie są klasyfikowane jako publiczne. Identyfikatory kont muszą być zbudowane przy użyciu standardowego formatu logicznego.

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.

## <a name="network-security"></a>Bezpieczeństwo sieci

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2 — dostęp do funkcji zarządzania urządzeniami sieciowymi jest ograniczony do autoryzowanych użytkowników.

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-dostęp sieciowy"

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3 — wszystkie urządzenia sieciowe muszą być skonfigurowane przy użyciu najbezpieczniejszych konfiguracji.

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych dotyczących inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-dostęp sieciowy"

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5 — wszystkie połączenia sieciowe z systemem za pomocą zapory muszą być regularnie zatwierdzane i monitorowane.

- \[Wersja zapoznawcza \] : Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows we właściwościach zapory systemu Windows

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7-odpowiednie kontrolki muszą być obecne w dowolnej granicy między zaufaną siecią a niezaufaną lub publiczną siecią.

- \[Wersja zapoznawcza \] : wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows we właściwościach zapory systemu Windows

## <a name="security-planning"></a>Planowanie zabezpieczeń

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3 — zagrożenia muszą być zidentyfikowane, które mogą mieć negatywny wpływ na poufność, integralność lub dostępność informacji i zawartości firmy wraz z prawdopodobieństwem wystąpienia.

- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL

### <a name="security-continuity"></a>Ciągłość zabezpieczeń

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5 — dane w magazynie długoterminowym muszą być dostępne w całym okresie przechowywania i być chronione przed zmianami w odniesieniu do multimediów i technologiami.

- Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.
- Inspekcja serwerów SQL skonfigurowanych z okresem przechowywania inspekcji krótszym niż 90 dni.

## <a name="system-integrity"></a>Integralność systemu

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14,3 — tylko autoryzowani pracownicy mogą monitorować aktywność sieci i użytkowników.

- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Monitoruj wyniki skanowania oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14,4 — systemy połączone z Internetem muszą mieć wykrywanie włamania.

- Wdrażanie wykrywania zagrożeń na serwerach SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14,13 — standardowe zarządzane centralnie oprogramowanie chroniące przed złośliwym kodem powinno zostać wdrożone w całej firmie.

- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>Oprogramowanie SI-14,14-chroniące przed złośliwym oprogramowaniem musi skanować co najmniej dwa tygodnie komputerów i nośników.

- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4 — zapewnia comiesięczne skanowanie aplikacji w poszukiwaniu luk w zabezpieczeniach.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5 — Upewnij się, że luki są rozpoznawane, sparowane ze zagrożeniami i oceniane pod kątem ryzyka.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6 — Upewnij się, że zidentyfikowane luki w zabezpieczeniach zostały skorygowane w ramach wzajemnie uzgodnionych osi czasu.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7 — dostęp do systemów zarządzania lukami w zabezpieczeniach i korzystanie z nich musi być ograniczony do autoryzowanego personelu.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Sprawdzono Mapowanie formantów przykładu planu multimediów. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="next step action"]
> [Plany multimediów — Omówienie](./control-mapping.md) 
>  [Plany multimediów — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

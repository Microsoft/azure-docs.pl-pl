---
title: Przykładowe kontrolki strategii multimediów
description: Mapowanie kontrolek przykładów strategii Media. Każda kontrolka jest mapowana na co najmniej jedną Azure Policy, które pomagają w ocenie.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: 4c04c661d7dd8b2b07a05c20524258d6bb887e62
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376702"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapowanie kontrolek przykładowej strategii Media

W poniższym artykule szczegółowo opisano sposób mapowania przykładowej strategii Azure Blueprints Media na kontrolki Multimedia. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Media](https://www.motionpictures.org/best-practices).

Następujące mapowania są formanty **nośnika.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania kontrolek. Wiele zamapowanych kontrolek jest implementowanych za [pomocą Azure Policy](../../../policy/overview.md) inicjatywy. Aby przejrzeć pełną inicjatywę, otwórz pozycję **Zasady** w Azure Portal i wybierz **stronę Definicje.** Następnie znajdź i wybierz wbudowaną inicjatywę zasad **\[ \] Podgląd: inspekcja** multimediów.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co [najmniej jedną Azure Policy](../../../policy/overview.md) definicją. Te zasady mogą ułatwić [ocenę zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; Jednak często nie występuje dopasowanie "jeden do jednego" lub pełne dopasowanie między kontrolą a co najmniej jedną zasadą. W związku z **tym zgodność** w Azure Policy odnosi się tylko do samych zasad; Nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami kontrolki. Ponadto standard zgodności obejmuje mechanizmy kontroli, które nie są obecnie Azure Policy przez żadne definicje. Dlatego zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami Azure Policy definicjami dla tego przykładu strategii zgodności mogą zmieniać się z czasem. Aby wyświetlić historię zmian, zobacz [GitHub Commit History (Historia zatwierdzania w usłudze GitHub).](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)

## <a name="access-control"></a>Kontrola dostępu

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 — Upewnij się, że klucz dostępu głównego nie istnieje

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych z systemem Windows, które nie zawierają \] określonych certyfikatów w zaufanym katalogu głównym

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 — hasła, piny i tokeny muszą być chronione

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła \] do 14 znaków

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 — dostęp do kont udostępnionych jest zabroniony

- Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey należy usunąć z Service Bus nazw

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 — system musi ograniczyć dostęp do autoryzowanych użytkowników.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 - System musi wymuszać prawa dostępu.

- \[Wersja \] zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w ramach przypisania praw użytkownika

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 - Zapobiegaj nieautoryzowanemu dostępowi do istotnych dla bezpieczeństwa informacji lub funkcji.

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — ustawienia systemowe"

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 — podział obowiązków musi zostać wymuszony przez odpowiednie przypisanie roli.

- [Wersja \] zapoznawcza: Role-Based Access Control (RBAC) powinna być używana w usługach Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 — upewnij się, że systemy nie podłączają jednocześnie zaufanych i niezaufanych sieci.

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w opcji \] "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC-1.43 — dostęp zdalny dla osób niebędących pracownikami musi być ograniczony do zezwalania na dostęp tylko do specjalnie zatwierdzonych systemów informacyjnych

- \[Wersja \] zapoznawcza: pokaż wyniki inspekcji z maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez haseł

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 — rejestruj zdarzenia związane z zabezpieczeniami dla wszystkich składników systemu informacji.

- Dzienniki diagnostyczne w Logic Apps powinny być włączone

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 — Upewnij się, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone dla wszystkich użytkowników konsoli w chmurze.

- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów.

## <a name="auditing--logging"></a>Rejestrowanie & inspekcji

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 — zdarzenia pomyślne i niepomyślne muszą być rejestrowane.

- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16 — urządzenia sieciowe/wystąpienia muszą rejestrować wszystkie zdarzenia sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez to urządzenie/wystąpienie sieciowe (elb, zapory aplikacji internetowych itp.)

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — konta"

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 — Serwery/wystąpienia muszą rejestrować wszystkie zdarzenia sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez ten serwer/wystąpienie

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — konta"

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 — zdarzenia domeny muszą rejestrować wszystkie zdarzenia sklasyfikowane jako krytyczne lub wysokie zdarzenia zabezpieczeń przez oprogramowanie do zarządzania domenami

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — konta"
- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w \] opcji "Opcje zabezpieczeń — klient sieci firmy Microsoft"

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 — zdarzenia domeny muszą rejestrować wszystkie zdarzenia sklasyfikowane jako krytyczne zdarzenia zabezpieczeń przez mechanizmy kontroli zabezpieczeń domeny

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — konta"

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 — zdarzenia domeny muszą rejestrować wszelkie zmiany lub dostęp do dziennika domeny

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows w opcji "Opcje zabezpieczeń — konsola odzyskiwania"

## <a name="cryptographic-controls"></a>Formanty kryptograficzne

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 — Aplikacje i systemy muszą używać bieżących rozwiązań kryptograficznych do ochrony danych.

- Transparent Data Encryption w bazach danych SQL powinna być włączona
- Należy włączyć funkcję Transparent Data Encryption, aby chronić dane w spoczynku i spełniać wymagania dotyczące zgodności

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 — Certyfikaty cyfrowe muszą być podpisane przez zatwierdzony urząd certyfikacji.

- \[Wersja zapoznawcza: pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zawierają certyfikaty wygasające \] w ciągu określonej liczby dni

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 — Certyfikaty cyfrowe muszą być jednoznacznie przypisane do użytkownika lub urządzenia.

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych z systemem Windows zawierających certyfikaty wygasające \] w ciągu określonej liczby dni

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 — Materiał kryptograficzny musi być przechowywany, aby umożliwić odszyfrowywanie rekordów przez czas przechowywania rekordów.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center zgodnie z zaleceniami

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 — Klucze tajne i prywatne muszą być przechowywane w bezpieczny sposób.

- Transparent Data Encryption w bazach danych SQL powinny być włączone
- Należy włączyć funkcję Transparent Data Encryption, aby chronić dane w spoczynku i spełniać wymagania dotyczące zgodności

## <a name="change--config-management"></a>Zarządzanie & zmianą konfiguracji

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 — tylko autoryzowani użytkownicy mogą implementować zatwierdzone zmiany w systemie.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 — Zapewnianie aktualnej, kompletnej, dokładnej i łatwo dostępnej konfiguracji odniesienia systemu informacyjnego.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 — Do obsługi konfiguracji odniesienia systemu informacyjnego należy stosować zautomatyzowane narzędzia.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 — Identyfikowanie i wyłączanie niepotrzebnych i/lub niezabezpieczonych funkcji, portów, protokołów i usług.

- Interfejsy sieciowe powinny wyłączać przekazywanie IP
- \[Wersja \] zapoznawcza: przekazywanie IP na maszynie wirtualnej powinno być wyłączone

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 — Monitoruj zmiany ustawień konfiguracji zabezpieczeń.

- Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 — Upewnij się, że w systemach firmy są zainstalowane tylko autoryzowane oprogramowanie i aktualizacje.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami

## <a name="identity--authentication"></a>Uwierzytelnianie & tożsamości

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 — konta użytkowników muszą być jednoznacznie przypisane do użytkowników w celu uzyskania dostępu do informacji, które nie są sklasyfikowane jako publiczne. Identyfikatory kont muszą być konstruowane przy użyciu standardowego formatu logicznego.

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.

## <a name="network-security"></a>Bezpieczeństwo sieci

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 — dostęp do funkcji zarządzania urządzeniami sieciowymi jest ograniczony do autoryzowanych użytkowników.

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w \] opcji "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 — wszystkie urządzenia sieciowe muszą być skonfigurowane przy użyciu najbezpieczniejszych konfiguracji.

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w \] opcji "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 — wszystkie połączenia sieciowe z systemem za pośrednictwem zapory muszą być regularnie zatwierdzane i inspekcji.

- \[Wersja zapoznawcza: wyświetlanie wyników inspekcji z konfiguracji maszyn wirtualnych z systemem \] Windows we właściwościach Zapory systemu Windows

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 — odpowiednie mechanizmy kontroli muszą znajdować się na dowolnej granicy między zaufaną siecią a dowolną niezaufaną lub publiczną siecią.

- \[Wersja zapoznawcza: wdrażanie wymagań wstępnych w celu inspekcji konfiguracji maszyn wirtualnych z systemem \] Windows we właściwościach Zapory systemu Windows

## <a name="security-planning"></a>Planowanie zabezpieczeń

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 — Należy zidentyfikować zagrożenia, które mogą negatywnie wpłynąć na poufność, integralność lub dostępność informacji i zawartości firmy wraz z prawdopodobieństwem ich wystąpienia.

- Typy usługi Advanced Threat Protection powinny być ustawione na wartość "Wszystkie" w ustawieniach usługi SQL Managed Instance Advanced Data Security ustawieniach

### <a name="security-continuity"></a>Ciągłość zabezpieczeń

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 — dane w magazynie długoterminowym muszą być dostępne przez cały okres przechowywania i być chronione przed obniżeniem wydajności nośników i zmianami technologicznymi.

- Serwery SQL powinny mieć skonfigurowane dni przechowywania inspekcji większe niż 90 dni.
- Inspekcja serwerów SQL skonfigurowanych z okresem przechowywania inspekcji krótszym niż 90 dni.

## <a name="system-integrity"></a>Integralność systemu

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 — tylko autoryzowany personel może monitorować działania sieci i użytkowników.

- Należy skorygować luki w zabezpieczeniach baz danych SQL
- Monitoruj wyniki skanowania w ramach oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 — Systemy internetowe muszą mieć wykrywanie włamań.

- Wdrażanie wykrywania zagrożeń na serwerach SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 — Standardowe, centralnie zarządzane oprogramowanie chroniące przed złośliwym oprogramowaniem powinno być wdrażane w całej firmie.

- Wdrażanie domyślnego rozszerzenia IaaSAntimalware firmy Microsoft dla systemu Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 — Oprogramowanie chroniące przed złośliwym oprogramowaniem musi co najmniej raz w tygodniu skanować komputery i multimedia.

- Wdrażanie domyślnego rozszerzenia IaaSAntimalware firmy Microsoft dla systemu Windows Server

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 — co miesiąc upewnij się, że aplikacje są skanowane w celu skanowania pod uwagę luk w zabezpieczeniach.

- Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych
- Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 — Upewnij się, że luki w zabezpieczeniach są identyfikowane, sparowane z zagrożeniami i oceniane pod uwagę.

- Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych
- Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 — upewnij się, że zidentyfikowane luki w zabezpieczeniach zostały skorygowane w ramach wzajemnie uzgadnianego harmonogramu.

- Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych
- Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 — dostęp do systemów zarządzania lukami w zabezpieczeniach i korzystanie z nich musi być ograniczony do autoryzowanego personelu.

- Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych
- Przejmij luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami.

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w Azure Government i innych chmurach krajowych. 

## <a name="next-steps"></a>Następne kroki

Przejrzeno mapowanie kontrolek przykładowej strategii Media. Następnie zapoznaj się z następującymi artykułami, aby poznać omówienie i sposób wdrażania tego przykładu:

> [!div class="next step action"]
> [Strategia media — omówienie](./control-mapping.md) 
>  [Strategia media — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

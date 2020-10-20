---
title: Podstawa zabezpieczeń platformy Azure dla Cloud Shell
description: Linia bazowa zabezpieczeń Cloud Shell zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 463bbe637eee26ab098d1531976a18999497d12f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210242"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Podstawa zabezpieczeń platformy Azure dla Cloud Shell

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Cloud Shell. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Cloud Shell. **Kontrolki** nie mają zastosowania do Cloud Shell zostały wykluczone.

 
Aby dowiedzieć się, jak Cloud Shell całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Cloud Shell pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: klienci mogą wdrażać Azure Cloud Shell w Virtual Network należącej do klienta.

Po wdrożeniu Azure Cloud Shell w Virtual Network należącej do klienta należy utworzyć lub użyć istniejącej sieci wirtualnej. Upewnij się, że wybrana Sieć wirtualna ma zastosowana sieciową grupę zabezpieczeń do jej podsieci i kontroli dostępu do sieci skonfigurowanych specyficznie dla zaufanych portów i źródeł aplikacji.

- [Wdrażanie Cloud Shell w sieci wirtualnej platformy Azure](private-vnet.md)

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Azure Cloud Shell to oparta na przeglądarce funkcja środowiska wiersza polecenia, która używa tej samej autoryzacji, która jest używana do uzyskiwania dostępu do Azure Portal, w tym przypadku Logowanie jednokrotne do Azure Portal także uwierzytelnia użytkownika za pomocą Cloud Shell. 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Azure Cloud Shell to oparta na przeglądarce funkcja środowiska wiersza polecenia, która używa tej samej autoryzacji, która jest używana do uzyskiwania dostępu do Azure Portal, w tym przypadku wszelkie uwierzytelnianie wieloskładnikowe wymagane do nawiązania połączenia z Azure Portal również będzie wymagane dla Cloud Shell. 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy kontenerów są monitorowane i aktualizowane przez zespół Cloud Shell.

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za uruchamianie zautomatyzowanych narzędzi do skanowania oprogramowania, które są uruchomione w środowisku programu.  

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy kontenerów są monitorowane i aktualizowane przez zespół Cloud Shell.

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za zarządzanie poprawkami oprogramowania działającymi w ich środowisku.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy kontenerów są monitorowane i aktualizowane przez zespół Cloud Shell.

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za korygowanie luk w zabezpieczeniach, które zostały wykryte za pośrednictwem ich skanów. Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki z poprzednimi skanami, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z zaleceń dotyczących zarządzania lukami zalecanymi przez Azure Security Center można przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy kontenerów są monitorowane i aktualizowane przez zespół Cloud Shell.

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za korygowanie luk w zabezpieczeniach, które zostały wykryte za pośrednictwem ich skanów.  Użyj typowego programu oceny ryzyka (na przykład typowego rozwiązania do oceny luk w zabezpieczeniach) lub domyślnych ocen ryzyka zapewnianych przez narzędzie skanowania innej firmy. 

- [Publikacja NIST — Typowy system oceniania luk w zabezpieczeniach](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell.  Klient może instalować własne narzędzia z własnych obrazów zgodnie z ich potrzebami organizacyjnymi, a narzędzia nie wymagają `sudo` uprawnień podczas instalacji.

Zalecane jest utworzenie spisu zatwierdzonego oprogramowania, które jest instalowane za Azure Cloud Shell zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Azure Cloud Shell to bezpłatna usługa bez zasobów należących do klienta.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell. 

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za monitorowanie aplikacji uruchamianych w środowisku, aby upewnić się, że są one zatwierdzone zgodnie z zasadami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Azure Cloud Shell to bezpłatna usługa bez zasobów należących do klienta.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell. 

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za monitorowanie aplikacji uruchamianych w środowisku, aby upewnić się, że niezatwierdzone oprogramowanie jest zarządzane zgodnie z zasadami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: Azure Cloud Shell to bezpłatna usługa bez zasobów należących do klienta.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell.  Określone narzędzia nie mogą zostać usunięte przez klienta.

Azure Cloud Shell umożliwia klientom zainstalowanie własnych narzędzi lub aplikacji na własny obraz zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za monitorowanie aplikacji działających w środowisku, aby upewnić się, że są one zatwierdzone zgodnie z zasadami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy kontenerów są monitorowane i aktualizowane przez zespół Cloud Shell.

Azure Cloud Shell umożliwia klientom instalowanie własnych narzędzi lub oprogramowania w swoich własnych obrazach zgodnie z potrzebami organizacji.

Klienci są odpowiedzialni za zachowanie spisu zatwierdzonego oprogramowania działającego w środowisku, aby upewnić się, że są one zatwierdzone dla zasad organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Akcje wykonywane w ramach funkcji Cloud Shell są takie same jak akcje wykonywane z tych samych narzędzi lub języków, które są uruchamiane w środowisku lokalnym.  Akcje z poszczególnych narzędzi i języków powinny być ograniczone, klienci nie mogą ograniczyć dostępu do Cloud Shell ani ograniczyć możliwości dostępnych dla użytkownika.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: Azure Cloud Shell mogą być izolowane w sieci wirtualnej klienta.

- [Wdrażanie Cloud Shell w sieci wirtualnej platformy Azure](private-vnet.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Cloud Shell umożliwia uruchamianie skryptów w programie, tworzenie w programie i przekazywanie ich do środowiska Cloud Shell.  To zalecenie jest przenoszone do Azure Key Vault.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell.  Klient może instalować własne narzędzia z własnych obrazów zgodnie z ich potrzebami organizacyjnymi, a narzędzia nie wymagają `sudo` uprawnień podczas instalacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Azure Cloud Shell to środowisko wiersza polecenia oparte na przeglądarce, które służy do interaktywnego zarządzania zasobami w chmurze.  Każdy kontener klienta jest nieulotny dla każdej sesji jest używany nowy kontener.  Obrazy i narzędzia kontenera są monitorowane i aktualizowane przez zespół Cloud Shell.  Klient może instalować własne narzędzia z własnych obrazów zgodnie z ich potrzebami organizacyjnymi, a narzędzia nie wymagają `sudo` uprawnień podczas instalacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.
- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 
- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.
Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.
- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 
- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.
- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.
- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 
- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.
- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.
- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

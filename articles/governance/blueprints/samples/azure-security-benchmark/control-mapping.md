---
title: Przykładowe kontrole planu wzoru zabezpieczeń platformy Azure
description: Kontrolowanie mapowania przykładowego planu testów zabezpieczeń platformy Azure do zasad platformy Azure.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538733"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Przykład planu wzoru zabezpieczeń platformy Azure

W poniższym artykule opisano, jak przykładowy plan wzoru zabezpieczeń platformy Azure Plany azure na poziomie zabezpieczeń jest mapą do kontrolek testów porównawczych zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat formantów, zobacz [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview).

Poniższe mapowania są do kontroli **usługi Azure Security Benchmark.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Zamapowane formanty są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[w wersji zapoznawczej:\]Inspekcja zaleceń dotyczących testów porównawczych zabezpieczeń platformy Azure i wdrożenie określonej pomocniczej inicjatywy dotyczącej rozszerzeń maszyn wirtualnych.**

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności może zawierać formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Adaptacyjne zalecenia dotyczące hartowania sieci powinny być stosowane w przypadku maszyn wirtualnych skierowanych do Internetu
- Maszyny wirtualne powinny być podłączone do zatwierdzonej sieci wirtualnej
- Maszyny wirtualne z internetem powinny być chronione za pomocą grup zabezpieczeń sieci
- Usługa Service Bus powinna używać punktu końcowego usługi sieci wirtualnej
- Usługa app service powinna używać punktu końcowego usługi sieci wirtualnej
- Program SQL Server powinien używać punktu końcowego usługi sieci wirtualnej
- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej
- Usługa Cosmos DB powinna używać punktu końcowego usługi sieci wirtualnej
- Usługa Key Vault powinna używać punktu końcowego usługi sieci wirtualnej
- Inspekcja nieograniczonego dostępu do kont magazynu
- Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej
- Rejestr kontenerów powinien używać punktu końcowego usługi sieci wirtualnej
- Sieci wirtualne powinny używać określonej bramy sieci wirtualnej
- Autoryzowane zakresy adresów IP powinny być definiowane w usługach Kubernetes
- \[Podgląd:\]Przekazywanie ip na maszynie wirtualnej powinno być wyłączone
- Maszyny wirtualne z internetem powinny być chronione za pomocą grup zabezpieczeń sieci
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Porty zarządzania powinny być zamykane na maszynach wirtualnych

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

- Obserwator sieci powinien być włączony

## <a name="13-protect-critical-web-applications"></a>1.3 Ochrona krytycznych aplikacji internetowych

- Upewnij się, że aplikacja WEB ma ustawioną na "Włączone certyfikaty klientów (certyfikaty klientów przychodzących)"
- CORS nie powinien zezwalać każdemu zasobowi na dostęp do aplikacji sieci Web
- CORS nie powinien zezwalać każdemu zasobom na dostęp do aplikacji funkcji
- Usługa CORS nie powinna zezwalać każdemu zasobom na dostęp do aplikacji interfejsu API
- Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno być wyłączone dla aplikacji funkcyjnych
- Zdalne debugowanie powinno być wyłączone dla aplikacji interfejsu API

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Odmowa komunikacji ze znanymi złośliwymi adresami IP

- Standard ochrony przed atakami DDoS powinien być włączony
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Adaptacyjne zalecenia dotyczące hartowania sieci powinny być stosowane w przypadku maszyn wirtualnych skierowanych do Internetu

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Rejestrowanie pakietów sieciowych i dzienników przepływu

- Obserwator sieci powinien być włączony

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Używaj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

- Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — dostęp do sieci"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w "Opcje zabezpieczeń — klient sieciowy firmy Microsoft"
- Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — zabezpieczenia sieci"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w "Opcje zabezpieczeń — zabezpieczenia sieci"
- Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — Microsoft Network Server"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w "Opcje zabezpieczeń — Microsoft Network Server"
- Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w "Szablony administracyjne — sieć"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Szablony administracyjne - sieć"

## <a name="22-configure-central-security-log-management"></a>2.2 Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

- Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych
- Agent usługi Log Analytics powinien być zainstalowany w zestawach skalowania maszyny wirtualnej
- Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, z którymi agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, z którymi agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Profil dziennika usługi Azure Monitor powinien zbierać dzienniki dla kategorii "napisz", "usuń" i "akcja"
- Usługa Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów
- Automatyczne inicjowanie obsługi administracyjnej agenta monitorowania usługi Log Analytics powinno być włączone w ramach subskrypcji

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Włączanie rejestrowania inspekcji zasobów platformy Azure

- Dzienniki diagnostyczne w magazynie usługi Azure Data Lake powinny być włączone
- Dzienniki diagnostyczne w aplikacjach logiki powinny być włączone
- Dzienniki diagnostyczne w Centrum IoT powinny być włączone
- Dzienniki diagnostyczne na kontach usługi Batch powinny być włączone
- Dzienniki diagnostyczne w zestawach skalowania maszyny wirtualnej powinny być włączone
- Dzienniki diagnostyczne w Centrum zdarzeń powinny być włączone
- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone
- Dzienniki diagnostyczne w usługach app services powinny być włączone
- Dzienniki diagnostyczne w usłudze Data Lake Analytics powinny być włączone
- Dzienniki diagnostyczne w magazynie kluczy powinny być włączone
- Dzienniki diagnostyczne w usłudze Service Bus powinny być włączone
- Dzienniki diagnostyczne w usłudze Azure Stream Analytics powinny być włączone
- Inspekcja na serwerze SQL powinna być włączona
- Przeprowadzanie inspekcji ustawienia diagnostyki

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Zbieranie dzienników zabezpieczeń z systemów operacyjnych

- Automatyczne inicjowanie obsługi administracyjnej agenta monitorowania usługi Log Analytics powinno być włączone w ramach subskrypcji
- Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych
- Agent usługi Log Analytics powinien być zainstalowany w zestawach skalowania maszyny wirtualnej
- Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, z którymi agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, z którymi agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Włączanie alertów o nietypowej aktywności

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych serwera SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w przypadku zarządzanym SQL ustawienia zaawansowanego zabezpieczenia danych

## <a name="28-centralize-anti-malware-logging"></a>2.8 Scentralizuj rejestrowanie ochrony przed złośliwym oprogramowaniem

- Microsoft Antimalware for Azure powinien być skonfigurowany do automatycznego aktualizowania podpisów ochrony
- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center
- Rozwiązanie ochrony punktów końcowych powinno być zainstalowane w zestawach skalowania maszyny wirtualnej

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Prowadzenie spisu rachunków administracyjnych

- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Korzystanie z dedykowanych kont administracyjnych

- Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera tylko określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera tylko określonych elementów członkowskich
- Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Korzystanie z uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń

## <a name="39-use-azure-active-directory"></a>3.9 Korzystanie z usługi Azure Active Directory

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL
- Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta
- Upewnij się, że usługa Zarejestruj się w usłudze Azure Active Directory jest włączona w aplikacji interfejsu API
- Upewnij się, że usługa Zarejestruj się w usłudze Azure Active Directory jest włączona w aplikacji WEB
- Upewnij się, że rejestracja w usłudze Azure Active Directory jest włączona w aplikacji function

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Regularne przeglądanie i uzgadnianie dostępu użytkowników

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Prowadzenie spisu informacji poufnych

- Poufne dane w bazach danych SQL powinny być klasyfikowane

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Szyfrowanie wszystkich poufnych informacji podczas przesyłania

- Należy włączyć bezpieczny transfer na konta magazynu
- Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja TLS powinna być używana w aplikacji funkcji
- Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Wymuszanie połączenia SSL powinno być włączone dla serwerów bazy danych MySQL
- Wymuszanie połączenia SSL powinno być włączone dla serwerów bazy danych PostgreSQL
- Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

- Poufne dane w bazach danych SQL powinny być klasyfikowane
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

- Kontrola dostępu oparta na rolach (RBAC) powinna być używana w usługach Kubernetes
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Szyfrowanie poufnych informacji w stanie spoczynku

- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Dyski niezałączone powinny być szyfrowane
- Ochrona TDE serwera SQL powinna być szyfrowana za pomocą własnego klucza
- Ochrona TDE wystąpienia zarządzanego SQL powinna być szyfrowana za pomocą własnego klucza
- Zmienne konta automatyzacji powinny być szyfrowane
- Klastry sieci szkieletowej usług powinny mieć właściwość ClusterProtectionLevel ustawiona na EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

- Usługa Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- \[W\] przypadku maszyn wirtualnych powinna być włączona funkcja oceny luk w zabezpieczeniach w wersji zapoznawczej
- Ustawienia oceny luk w zabezpieczeniach serwera SQL powinny zawierać adres e-mail umożliwiający otrzymywanie raportów skanowania

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu w zestawach skalowania maszyny wirtualnej
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji WEB
- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji Function
- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji Function
- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji Function
- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Usługi Kubernetes powinny zostać uaktualnione do wersji kubernetes niechdyskniejszą

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Stosowanie procesu oceny ryzyka w celu nadania priorytetu usuwaniu wykrytych luk w zabezpieczeniach

- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Luki w zabezpieczeniach kontenerów powinny zostać naprawione
- Luki w bazach danych SQL powinny zostać naprawione

## <a name="68-use-only-approved-applications"></a>6.8 Używaj wyłącznie zatwierdzonych aplikacji

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń
- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="69-use-only-approved-azure-services"></a>6.9 Korzystaj tylko z zatwierdzonych usług platformy Azure

- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager
- Konta magazynu powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager

## <a name="610-implement-approved-application-list"></a>6.10 Wdrożenie listy zatwierdzonych wniosków

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń
- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Obsługa bezpiecznych konfiguracji zasobów platformy Azure

- \[Wersja\]zapoznawcza : Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Obsługa bezpiecznych konfiguracji systemów operacyjnych

- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione
- Luki w zabezpieczeniach kontenerów powinny zostać naprawione
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

- \[Wersja\]zapoznawcza : Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione
- Luki w zabezpieczeniach kontenerów powinny zostać naprawione
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione

## <a name="711-manage-azure-secrets-securely"></a>7.11 Bezpieczne zarządzanie wpisami tajnymi platformy Azure

- Obiekty magazynu kluczy powinny być odzyskiwalne

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Bezpieczne i automatyczne zarządzanie tożsamościami

- Tożsamość zarządzana powinna być używana w aplikacji funkcji
- Tożsamość zarządzana powinna być używana w aplikacji sieci Web
- Tożsamość zarządzana powinna być używana w aplikacji interfejsu API

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center
- Rozwiązanie ochrony punktów końcowych powinno być zainstalowane w zestawach skalowania maszyny wirtualnej

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Pliki skanowania wstępnego do przesłania do zasobów platformy Azure nieoliczonych

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

- Microsoft Antimalware for Azure powinien być skonfigurowany do automatycznego aktualizowania podpisów ochrony

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Zapewnienie regularnych automatycznych kopii zapasowych

- Długoterminowa geograficzna nadmiarowa kopia zapasowa powinna być włączona dla baz danych SQL azure
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mysql
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla postgreSQL
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mariadb
- Usługa Azure Backup powinna być włączona dla maszyn wirtualnych

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

- Długoterminowa geograficzna nadmiarowa kopia zapasowa powinna być włączona dla baz danych SQL azure
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mysql
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla postgreSQL
- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mariadb
- Usługa Azure Backup powinna być włączona dla maszyn wirtualnych

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

- Obiekty magazynu kluczy powinny być odzyskiwalne

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Tworzenie procedury oceniania i ustalania priorytetów incydentów

- Należy wybrać standardową warstwę cenową Centrum zabezpieczeń

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów związanych z bezpieczeństwem

- Dla subskrypcji należy podać adres e-mail kontaktu z zabezpieczeniami
- Dla subskrypcji należy podać numer telefonu kontaktu z zabezpieczeniami
- Zaawansowane ustawienia zabezpieczeń danych dla serwera SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych serwera SQL server
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych w przypadku zarządzanym sql

## <a name="next-steps"></a>Następne kroki

Teraz, po przejrzeniu mapowania kontroli planu testów porównawczych zabezpieczeń platformy Azure, odwiedź witrynę Azure Policy w witrynie Azure portal, aby przypisać inicjatywę:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
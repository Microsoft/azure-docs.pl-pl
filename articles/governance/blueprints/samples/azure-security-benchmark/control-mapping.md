---
title: Przykładowe formanty usługi Azure Security test
description: Kontroluj mapowanie przykładowego planu usługi Azure Security test do Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691302"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Sterowanie mapowaniem przykładowego planu usługi Azure Security test

W tym artykule szczegółowo przedstawiono sposób, w jaki platforma Azure planuje przykład planu wydajności Azure Security, mapuje się na kontrolki testu porównawczego zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Azure Security Tests](../../../../security/benchmarks/overview.md).

Następujące mapowania znajdują się w formantach **testu porównawczego zabezpieczeń platformy Azure** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Zamapowane kontrolki są implementowane z inicjatywy [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz wersję ** \[\]zapoznawczą: Przeprowadź inspekcję zaleceń dotyczących usługi Azure Security test i Wdróż konkretną** inicjatywę z wbudowanymi rozszerzeniami maszyny wirtualnej.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto norma zgodności może zawierać kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1 Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.
- Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną
- Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń
- Service Bus powinien używać punktu końcowego usługi sieci wirtualnej
- App Service powinien używać punktu końcowego usługi sieci wirtualnej
- SQL Server powinien używać punktu końcowego usługi sieci wirtualnej
- Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej
- Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej
- Key Vault powinien używać punktu końcowego usługi sieci wirtualnej
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej
- Container Registry powinien używać punktu końcowego usługi sieci wirtualnej
- Sieci wirtualne powinny używać określonej bramy sieci wirtualnej
- Dozwolone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services
- \[Wersja\]zapoznawcza: przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone
- Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Porty zarządzania powinny być zamknięte na maszynach wirtualnych

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2 Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

- Network Watcher powinna być włączona

## <a name="13-protect-critical-web-applications"></a>1,3 Ochrona krytycznych aplikacji sieci Web

- Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji funkcji
- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- Zdalne debugowanie powinno zostać wyłączone dla API Apps

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4 odmowa komunikacji ze znanymi złośliwymi adresami IP

- Należy włączyć Standard DDoS Protection
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.

## <a name="15-record-network-packets-and-flow-logs"></a>1,5. rejestrowanie pakietów sieciowych i dzienników przepływów

- Network Watcher powinna być włączona

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11 używaj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

- Wdróż wymagania wstępne w celu inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-dostęp do sieci"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń-Klient sieci Microsoft"
- Wdróż wymagania wstępne w celu przeprowadzenia inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń-zabezpieczenia sieci"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń-zabezpieczenia sieci"
- Wdróż wymagania wstępne w celu przeprowadzenia inspekcji konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Opcje zabezpieczeń — serwer sieci Microsoft"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — serwer sieci Microsoft"
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję konfiguracji maszyn wirtualnych z systemem Windows w "Szablony administracyjne-Network"
- Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w obszarze "Szablony administracyjne-Network"

## <a name="22-configure-central-security-log-management"></a>2,2. Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

- Na maszynach wirtualnych należy zainstalować agenta Log Analytics
- Agenta Log Analytics należy zainstalować na Virtual Machine Scale Sets
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"
- Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów
- Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3 włączenie rejestrowania inspekcji dla zasobów platformy Azure

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

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4 zbieranie dzienników zabezpieczeń z systemów operacyjnych

- Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji
- Na maszynach wirtualnych należy zainstalować agenta Log Analytics
- Agenta Log Analytics należy zainstalować na Virtual Machine Scale Sets
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7 Włącz alerty dla nietypowej aktywności

- Należy wybrać Security Center warstwy cenowej Standard
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL

## <a name="28-centralize-anti-malware-logging"></a>2,8 scentralizowane rejestrowanie chroniące przed złośliwym oprogramowaniem

- Program Microsoft chroniący przed złośliwym kodem dla platformy Azure powinien zostać skonfigurowany do automatycznego aktualizowania sygnatur ochrony
- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1 przechowywanie spisu kont administracyjnych

- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="33-use-dedicated-administrative-accounts"></a>3,3 używaj dedykowanych kont administracyjnych

- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera tylko określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera tylko określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdróż wymagania wstępne, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolnego z określonych elementów członkowskich
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, w których Grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5 używanie uwierzytelniania wieloskładnikowego w przypadku wszystkich Azure Active Directory dostępu

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>Dziennik 3,7 i alert dotyczący podejrzanego działania z kont administracyjnych

- Należy wybrać Security Center warstwy cenowej Standard

## <a name="39-use-azure-active-directory"></a>3,9 Użyj Azure Active Directory

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- Upewnij się, że w aplikacji interfejsu API jest włączony rejestr z Azure Active Directory
- Upewnij się, że w aplikacji sieci WEB jest włączony rejestr z Azure Active Directory
- Upewnij się, że Azure Active Directory jest włączona na aplikacja funkcji

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10 regularne przeglądanie i uzgadnianie dostępu użytkowników

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1 przechowywanie spisu poufnych informacji

- Poufne dane w bazach danych SQL powinny być klasyfikowane

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4 Szyfruj wszystkie poufne informacje podczas przesyłania

- Należy włączyć bezpieczny transfer na konta magazynu
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL
- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL
- Należy włączyć tylko bezpieczne połączenia z Redis Cache

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5 Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

- Poufne dane w bazach danych SQL powinny być klasyfikowane
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6 użycie usługi Azure RBAC do kontrolowania dostępu do zasobów

- Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8. Szyfruj poufne informacje w stanie spoczynku

- Należy włączyć Transparent Data Encryption baz danych SQL
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Niedołączone dyski powinny być szyfrowane
- Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza
- Zmienne konta usługi Automation powinny być szyfrowane
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9 dziennik i alert dotyczący zmian krytycznych zasobów platformy Azure

- Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1 Uruchamianie narzędzi do skanowania zautomatyzowanej luki w zabezpieczeniach

- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- \[Ocena\] luk w zabezpieczeniach w wersji zapoznawczej powinna być włączona na Virtual Machines
- Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2 Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacja funkcji
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3 wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB
- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web
- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Usługi Kubernetes należy uaktualnić do niezagrożonej wersji Kubernetes

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5 użycie procesu oceny ryzyka w celu określenia priorytetów korygowania odkrytych luk w zabezpieczeniach

- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować

## <a name="68-use-only-approved-applications"></a>6,8 Używaj tylko zatwierdzonych aplikacji

- Należy wybrać Security Center warstwy cenowej Standard
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="69-use-only-approved-azure-services"></a>6,9 Używaj tylko zatwierdzonych usług platformy Azure

- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager

## <a name="610-implement-approved-application-list"></a>6,10 wdrożenie zatwierdzonej listy aplikacji

- Należy wybrać Security Center warstwy cenowej Standard
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3. Zachowaj bezpieczne konfiguracje zasobów platformy Azure

- \[Wersja\]zapoznawcza: w usługach Kubernetes Services należy zdefiniować zasady zabezpieczeń

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4. Zachowaj bezpieczne konfiguracje systemów operacyjnych

- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9 wdrożenie zautomatyzowanego monitorowania konfiguracji dla usług platformy Azure

- \[Wersja\]zapoznawcza: w usługach Kubernetes Services należy zdefiniować zasady zabezpieczeń

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10 wdrożenie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować

## <a name="711-manage-azure-secrets-securely"></a>7,11. Zarządzanie kluczami tajnymi platformy Azure bezpiecznie

- Key Vault obiektów powinno być możliwe do odzyskania

## <a name="712-manage-identities-securely-and-automatically"></a>7,12 — bezpieczne zarządzanie tożsamościami i automatyczne

- Tożsamość zarządzana powinna być używana w aplikacja funkcji
- Tożsamość zarządzana powinna być używana w aplikacji sieci Web
- Tożsamość zarządzana powinna być używana w aplikacji interfejsu API

## <a name="81-use-centrally-managed-anti-malware-software"></a>8,1 Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2 pliki skanowania przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

- Należy wybrać Security Center warstwy cenowej Standard

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3 Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

- Program Microsoft chroniący przed złośliwym kodem dla platformy Azure powinien zostać skonfigurowany do automatycznego aktualizowania sygnatur ochrony

## <a name="91-ensure-regular-automated-back-ups"></a>9,1 Upewnij się, że zwykła zautomatyzowana kopia zapasowa

- Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB
- Azure Backup powinna być włączona dla Virtual Machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2. wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

- Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB
- Azure Backup powinna być włączona dla Virtual Machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4 zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

- Key Vault obiektów powinno być możliwe do odzyskania

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2 Tworzenie oceny incydentu i procedury priorytetyzacji

- Należy wybrać Security Center warstwy cenowej Standard

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4 Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

- Należy podać adres e-mail kontaktu z zabezpieczeniami dla Twojej subskrypcji
- Należy podać numer telefonu kontaktu z zabezpieczeniami dla Twojej subskrypcji
- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli usługi Azure Security test, odwiedź Azure Policy w Azure Portal, aby przypisać inicjatywę:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
---
title: Usługa Azure Security Baseline dla bazy danych platformy Azure dla bazy danych MariaDB
description: Usługa Azure Security Baseline dla bazy danych platformy Azure dla bazy danych MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9df0de443e28e0eea3224e42178eddd46108ea64
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759162"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Usługa Azure Security Baseline dla bazy danych platformy Azure dla bazy danych MariaDB

Usługa Azure Security Baseline for Azure Database for MariaDB zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Konfigurowanie łącza prywatnego dla bazy danych platformy Azure dla bazy danych MariaDB z prywatnymi punktami końcowymi. Private Link umożliwia łączenie się z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo przynosi usługi platformy Azure wewnątrz prywatnej sieci wirtualnej (VNet). Ruch między siecią wirtualną a wystąpieniem MariaDB jest przesuwem sieci szkieletowej firmy Microsoft.

Alternatywnie można użyć punktów końcowych usługi sieci wirtualnej do ochrony i ograniczenia dostępu do sieci usługi Azure Database dla implementacji MariaDB. Reguły sieci wirtualnej to jedna funkcja zabezpieczeń zapory, która określa, czy usługa Azure Database for MariaDB akceptuje komunikację wysyłaną z określonych podsieci w sieciach wirtualnych.

Możesz również zabezpieczyć usługę Azure Database dla mariadb z regułami zapory. Zapora serwera uniemożliwia dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

Jak skonfigurować łącze prywatne dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Jak tworzyć punkty końcowe usługi sieci wirtualnej i zarządzać nimi w usłudze Azure Database dla serwera MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Jak skonfigurować reguły zapory usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Gdy serwer usługi Azure Database for MariaDB jest zabezpieczony do prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Aby zmniejszyć ryzyko eksfiltracji danych, można użyć sieciowej grupy zabezpieczeń(NSG). Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta magazynu dla inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak skonfigurować łącze prywatne dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Jak włączyć dzienniki przepływu nsg: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal jak włączyć i używać analizy ruchu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami dla bazy danych platformy Azure dla mariadb. Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Włącz standard ochrony przed atakami DDoS w sieciach wirtualnych skojarzonych z usługą Azure Database dla wystąpień MariaDB, aby chronić przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla bazy danych platformy Azure dla bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować ochronę przed atakami DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Gdy serwer usługi Azure Database for MariaDB jest zabezpieczony do prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Następnie można skonfigurować sieciową grupę zabezpieczeń (NSG), aby zmniejszyć ryzyko eksfiltracji danych. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta magazynu dla inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Log Analytics i użyć usługi Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów zapalnych, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal jak włączyć i używać analizy ruchu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Użyj zaawansowanej ochrony przed zagrożeniami dla bazy danych platformy Azure dla mariadb. Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.
Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla bazy danych platformy Azure dla bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do bazy danych platformy Azure dla wystąpień MariaDB, użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolki dostępu do sieci w sieciowych grupach zabezpieczeń lub zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. WestUs) w odpowiednim polu źródłowym lub docelowym reguły można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.
Uwaga: Usługa Azure Database for MariaDB używa tagu usługi "Microsoft.Sql".

Aby uzyskać więcej informacji na https://docs.microsoft.com/azure/virtual-network/service-tags-overview temat używania tagów usługi: Poznaj użycie tagu usługi dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych i zasobów sieciowych skojarzonych z usługą Azure Database dla wystąpień MariaDB za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.DBforMariaDB" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej bazy danych Azure dla wystąpień MariaDB. Można również korzystać z wbudowanych definicji zasad związanych z siecią lub usługi Azure Database dla wystąpień MariaDB, takich jak:

- Standard ochrony przed atakami DDoS powinien być włączony

- Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB

- Serwer MariaDB powinien używać punktu końcowego usługi sieci wirtualnej

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure dla sieci:https://docs.microsoft.com/azure/governance/policy/samples/

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień MariaDB, aby zapewnić metadane i organizację logiczną.

Użyj dowolnej z wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieoznakowanych zasobach.

Za pomocą narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można wyszukać lub wykonać akcje na podstawie zasobów na podstawie ich tagów.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z usługą Azure Database dla wystąpień MariaDB. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.
Jak wyświetlać i pobierać zdarzenia https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view dziennika aktywności platformy Azure: jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Firma Microsoft przechowuje źródło czasu używane dla zasobów platformy Azure, takich jak usługa Azure Database dla MariaDB dla znaczników czasu w dziennikach.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włącz ustawienia diagnostyczne i dzienniki serwera i dzienniki pozyskiwania, aby agregować dane zabezpieczeń generowane przez usługę Azure Database dla wystąpień MariaDB. W usłudze Azure Monitor użyj obszaru roboczego analizy dzienników do wykonywania zapytań i wykonywania analiz oraz użyj kont usługi Azure Storage do długoterminowego/archiwizacji magazynu. Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM.
Jak skonfigurować dzienniki serwera dla usługi Azure Database i uzyskiwać do nich dostęp:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować dzienniki inspekcji i uzyskać dostęp https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal do usługi Azure Database dla bazy danych MariaDB: jak przy użyciu usługi Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne w bazie danych platformy Azure dla wystąpień MariaDB, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Upewnij się, że w szczególności włączyć dziennik inspekcji MariaDB. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. Można również włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać dzienniki do tego samego obszaru roboczego usługi Log Analytics lub konta magazynu.

Jak skonfigurować dzienniki serwera i uzyskać dostęp https://docs.microsoft.com/azure/mariadb/concepts-server-logs do niej dla bazy danych platformy Azure dla https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal bazy danych MariaDB: jak skonfigurować dzienniki inspekcji usługi Azure Database dla usługi Azure Database i uzyskiwać do nich dostęp: jak skonfigurować ustawienia diagnostyczne dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor obszar roboczy usługi Log Analytics używany do przechowywania dzienników usługi Azure Database dla mariadb ustaw okres przechowywania zgodnie z przepisami dotyczącymi zgodności organizacji. Użyj kont usługi Azure Storage dla magazynu długoterminowego/archiwizacji.
Jak ustawić parametry przechowywania dzienników dla https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period obszarów roboczych usługi Log Analytics: przechowywanie dzienników zasobów na koncie usługi Azure Storage:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i monitorowanie dzienników z wystąpień MariaDB pod kątem nietypowego zachowania. Użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przeglądać dzienniki i wykonywać kwerendy dotyczące danych dziennika. Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM.

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Aby uzyskać więcej informacji na temat obszaru roboczego usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** Włącz zaawansowaną ochronę przed zagrożeniami dla mariadb. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla mariadb wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Ponadto można włączyć dzienniki serwera i ustawienia diagnostyczne dla MariaDB i wysłać dzienniki do obszaru roboczego analizy dzienników. Dołączanie obszaru roboczego usługi Log Analytics do usługi Azure Sentinel, ponieważ zapewnia rozwiązanie automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR). Pozwala to na tworzenie podręczników (zautomatyzowanych rozwiązań) i ich wykorzystanie do rozwiązywania problemów z zabezpieczeniami.

Jak włączyć zaawansowaną ochronę przed zagrożeniami dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować i uzyskać dostęp do dzienników serwera dla MariDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować dzienniki inspekcji i uzyskać do nich dostęp do bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; MariaDB nie przetwarza ani nie produkuje dzienników związanych ze złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; MariaDB nie przetwarza ani nie tworzy dzienników związanych z systemem DNS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Obsługa spisu kont użytkowników, które mają dostęp administracyjny do płaszczyzny zarządzania (Azure portal/Azure Resource Manager) wystąpień MariaDB. Ponadto należy prowadzić spis kont administracyjnych, które mają dostęp do płaszczyzny danych wystąpień MariaDB. (Podczas tworzenia serwera MariaDB należy podać poświadczenia dla użytkownika administratora. Tego administratora można użyć do utworzenia dodatkowych użytkowników MariaDB.)

Poznaj zarządzanie dostępem dla MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Poznaj wbudowane role RBAC dla subskrypcji platformy Azure:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Usługa Azure Active Directory nie ma pojęcia haseł domyślnych.

Po utworzeniu samego zasobu MariaDB azure wymusza utworzenie użytkownika administracyjnego z silnym hasłem. Jednak po utworzeniu wystąpienia MariaDB można użyć pierwszego konta administratora serwera utworzonego konta, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administracyjnego. Podczas tworzenia tych kont upewnij się, że konfigurujesz inne, silne hasło dla każdego konta.

Jak utworzyć dodatkowe konta dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do wystąpień MariaDB. Zarządzanie tożsamościami i dostępem usługi Azure Security Center umożliwia monitorowanie liczby kont administracyjnych.

Poznaj tożsamość i dostęp usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Dostęp płaszczyzny danych do MariaDB jest kontrolowany przez tożsamości przechowywane w bazie danych i nie obsługuje samego i przesuwu. Dostęp do płaszczyzny sterowania dla MariaDB jest dostępny za pośrednictwem interfejsu API REST i obsługuje funkcję SSO. Aby uwierzytelnić, ustaw nagłówek autoryzacji żądań na token sieci Web JSON, który można uzyskać z usługi Azure Active Directory.

Poznaj interfejs API usługi Azure Database dla systemu MariaDB REST:https://docs.microsoft.com/rest/api/mariadb/

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi tożsamości i zarządzania dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj paws (uprzywilejowanych stacji roboczych dostępu) z usługi MFA skonfigurowany do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Włącz zaawansowaną ochronę przed zagrożeniami dla MariaDB, aby generować alerty o podejrzanych działaniach.

Ponadto można użyć usługi Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów, gdy podejrzane lub niebezpieczne działania występuje w środowisku. Użyj wykrywania ryzyka usługi Azure AD, aby wyświetlić alerty i raporty dotyczące ryzykownych zachowań użytkowników.

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak wdrożyć zarządzanie tożsamościami uprzywilejowanymi (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Poznaj wykrywanie ryzyka usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwane lokalizacje, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów, aby ograniczyć dostęp do zasobów platformy Azure, takich jak MariaDB.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa AAD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. AAD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Nie można użyć uwierzytelniania usługi Azure AD do bezpośredniego dostępu do płaszczyzny danych MariaDB, jednak poświadczenia usługi Azure AD mogą być używane do administrowania na poziomie płaszczyzny zarządzania (np. w witrynie Azure portal) do kontrolowania kont administratora MariaDB.

Jak zaktualizować hasło administratora mariadb:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Przejrzyj dzienniki usługi Azure Active Directory, aby ułatwić odnajdywanie starych kont, które mogą obejmować te z rolami administracyjnymi MariaDB. Ponadto użyj przeglądów dostępu tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw, które mogą być używane do uzyskiwania dostępu do mariadb, i przypisaniami ról. Dostęp użytkowników powinien być regularnie sprawdzany, na przykład co 90 dni, aby upewnić się, że tylko właściwi Użytkownicy mają stały dostęp.

Opis raportowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Włącz ustawienia diagnostyczne dla MariaDB i usługi Azure Active Directory, wysyłając wszystkie dzienniki do obszaru roboczego usługi Log Analytics. Skonfiguruj żądane alerty (takie jak nieudane próby uwierzytelniania) w obszarze roboczym usługi Log Analytics.

Jak skonfigurować i uzyskać dostęp do dzienników serwera dla MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować dzienniki inspekcji i uzyskać do nich dostęp do bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Włącz zaawansowaną ochronę przed zagrożeniami dla mariadb. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla mariadb wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Użyj funkcji ochrony tożsamości usługi Azure Active Directory i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi do wykrytych podejrzanych akcji. Można włączyć automatyczne odpowiedzi za pośrednictwem usługi Azure Sentinel do zaimplementowania odpowiedzi zabezpieczeń organizacji.

Jak włączyć zaawansowaną ochronę przed zagrożeniami dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak wyświetlić ryzykowne logowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Nie dotyczy; Skrytka klienta nie jest jeszcze obsługiwana dla usługi Azure Database dla mariadb.

Lista obsługiwanych usług skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu usługi Azure Database dla wystąpień MariaDB lub powiązanych zasobów, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Użyj kombinacji private link, punkty końcowe usługi i/lub MariaDB reguł zapory, aby wyizolować i ograniczyć dostęp do sieci do wystąpień MariaDB.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak skonfigurować łącze prywatne dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Jak skonfigurować punkty końcowe usługi dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Jak skonfigurować reguły zapory dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Podczas korzystania z maszyn wirtualnych platformy Azure w celu uzyskania dostępu do wystąpień MariaDB, użyj private link, konfiguracje sieci MariaDB, sieciowe grupy zabezpieczeń i tagi usług, aby ograniczyć możliwość eksfiltracji danych.

Firma Microsoft zarządza podstawową infrastrukturą mariadb i wdrożyła ścisłe kontrole, aby zapobiec utracie lub ekspozycji danych klientów.

Jak ograniczyć eksfiltrację danych dla usługi Azure Database dla mariadb:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Usługa Azure Database for MariaDB obsługuje łączenie serwera usługi Azure Database dla mariadb z aplikacjami klienckimi przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. W witrynie Azure portal upewnij się, że opcja "Wymuszanie połączenia SSL" jest włączona dla wszystkich wystąpień MariaDB.

Jak skonfigurować szyfrowanie podczas przesyłania dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania stratom danych nie są jeszcze dostępne dla usługi Azure Database for MariaDB. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Użyj usługi Azure AD RBAC do kontrolowania dostępu do bazy danych platformy Azure dla płaszczyzny zarządzania MariaDB (Azure portal/Azure Resource Manager). Aby uzyskać dostęp do płaszczyzny danych (w samej bazie danych), użyj zapytań SQL, aby utworzyć użytkowników i skonfigurować uprawnienia użytkowników.

Jak skonfigurować rbac na platformie Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Jak skonfigurować dostęp użytkownika za pomocą języka SQL dla MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą mariadb i wdrożyła ścisłe kontrole, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Usługa Azure Database for MariaDB używa modułu kryptograficznego fips 140-2 do szyfrowania danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku, z wyjątkiem plików tymczasowych utworzonych podczas uruchamiania kwerend. Usługa używa 256-bitowego szyfrowania AES dołączonego do szyfrowania magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

Opis szyfrowania w spoczynku dla MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące tego, kiedy zmiany mają miejsce w wystąpieniach produkcyjnych usługi Azure Database dla MariaDB i innych krytycznych lub powiązanych zasobów.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki**: Obecnie niedostępne; Usługa Azure Security Center nie obsługuje jeszcze oceny luk w zabezpieczeniach usługi Azure Database dla serwera MariaDB.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących usługę Azure Database dla serwera MariaDB.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym usługi Azure Database dla serwera MariaDB) w ramach subskrypcji. Upewnij się, że masz odpowiednie (odczytu) uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do usługi Azure Database dla serwera MariaDB i innych powiązanych zasobów, dając metadane logicznie zorganizować je w taksonomii.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Użyj tagowania, grup zarządzania i oddzielnych subskrypcji, w stosownych przypadkach, do organizowania i śledzenia usługi Azure Database dla serwera MariaDB i powiązanych zasobów. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj programu Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Ograniczanie możliwości interakcji użytkowników z usługą Azure Resources Manager za pomocą skryptów</div>

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i zmiany zasobów w środowisku o wysokim poziomie zabezpieczeń, takim jak usługa Azure Database dla serwera MariaDB zawierająca poufne informacje.

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure Database dla wystąpień MariaDB za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DBforMariaDB", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej bazy danych Azure dla serwerów MariaDB. Można również korzystać z wbudowanych definicji zasad związanych z usługą Azure Database dla serwerów MariaDB, takich jak:

- Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mariadb

Jak wyświetlić dostępne aliasy zasad platformy Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure dla usługi Azure Database dla serwerów MariaDB i powiązanych zasobów, użyj usługi Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DBforMariaDB", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.DBforMariaDB", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [inspekcja], [odmówić] i [wdrożyć, jeśli nie istnieje], aby automatycznie wymusić konfiguracje usługi Azure Database dla wystąpień MariaDB i powiązanych zasobów.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Dla maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do bazy danych platformy Azure dla serwerów MariaDB, użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć usługę Azure Database dla programu MariaDB zarządzania kluczem tajnym serwera. Upewnij się, że włączona jest funkcja usuwania nietrwałego przechowalni kluczy.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Usługa Azure Database dla serwera MariaDB obecnie nie obsługuje uwierzytelniania usługi Azure Active Directory w celu uzyskania dostępu do baz danych.  Podczas tworzenia usługi Azure Database dla serwera MariaDB, należy podać poświadczenia dla użytkownika administratora. Tego administratora można użyć do utworzenia dodatkowych użytkowników MariaDB.  

W przypadku maszyn wirtualnych platformy Azure lub aplikacji sieci web uruchomionych w usłudze Azure App Service używanych do uzyskiwania dostępu do bazy danych usługi Azure dla serwera MariaDB należy używać tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault do przechowywania i pobierania poświadczeń dla usługi Azure Database dla serwera MariaDB.  Upewnij się, że włączona jest funkcja usuwania nietrwałego przechowalni kluczy.

Użyj tożsamości zarządzanych, aby zapewnić usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie. Jak skonfigurować tożsamości zarządzane: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm. Jak zintegrować się z https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identitytożsamościami zarządzanymi platformy Azure: .



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure App Service), jednak nie jest uruchamiana na zawartości klienta.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure Database dla serwera MariaDB), jednak nie działa na zawartości klienta.

Wstępnie skanuj dowolną zawartość przekazywanych do nieobliczalnych zasobów platformy Azure, takich jak usługa App Service, magazyn usługi Data Lake, magazyn obiektów Blob, usługa Azure Database dla serwera MariaDB itp. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure Database dla serwera MariaDB), jednak nie działa na zawartości klienta.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Usługa Azure Database dla MariaDB wykonuje pełne, różnicowe i kopie zapasowe dziennika transakcji.  Usługa Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie skonfigurowanym lokalnie nadmiarowo lub geograficznie nadmiarowym. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią każdej strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.  Domyślny okres przechowywania kopii zapasowej wynosi siedem dni. Opcjonalnie można go skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania 256-bitowego AES.

Opis kopii zapasowych dla MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Poznaj początkową konfigurację MariaDB:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie skonfigurowanym lokalnie nadmiarowo lub geograficznie nadmiarowym. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie.  Tworzenie kopii zapasowych i przywracanie jest istotną częścią każdej strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

Jeśli używasz usługi Key Vault do szyfrowania danych po stronie klienta dla danych przechowywanych na serwerze MariaDB, upewnij się, że regularnieszykła automatyczne kopie zapasowe kluczy.

Opis kopii zapasowych dla MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Jak wykonać kopię zapasową kluczy magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** W usłudze Azure Database for MariaDB wykonaj przywracanie z oryginalnych kopii zapasowych serwera w celu okresowego testowania kopii zapasowych. Dostępne są dwa typy przywracania: przywracanie punktu w czasie i przywracanie geograficzne. Przywracanie w czasie jest dostępne z opcją nadmiarowości kopii zapasowej i tworzy nowy serwer w tym samym regionie co oryginalny serwer. Przywracanie geograficzne jest dostępne tylko wtedy, gdy serwer został skonfigurowany do magazynowania geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, w tym rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i całkowita liczba baz danych odzyskiwanie w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

Poznaj tworzenie kopii zapasowych i przywracanie w bazie danych platformy Azure dla bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Database dla MariaDB wykonuje pełne, różnicowe i kopie zapasowe dziennika transakcji. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w skonfigurowanym okresie przechowywania kopii zapasowej. Domyślny okres przechowywania kopii zapasowej wynosi siedem dni. Opcjonalnie można go skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania 256-bitowego AES.

Poznaj tworzenie kopii zapasowych i przywracanie w bazie danych platformy Azure dla bazy danych MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Użyj tagów do organizowania zasobów platformy Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki: Przeprowadzaj**ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach w regularnym rytmie, aby chronić zasoby platformy Azure. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną. Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center za pomocą funkcji ciągłego eksportowania, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń w celu ochrony zasobów platformy Azure.
    

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w infrastrukturze, usługach i aplikacjach zarządzanych przez firmę Microsoft można znaleźć tutaj:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

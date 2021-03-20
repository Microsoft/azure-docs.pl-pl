---
title: Podstawa zabezpieczeń platformy Azure dla Azure Database for MariaDB
description: Podstawa zabezpieczeń platformy Azure dla Azure Database for MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99c02644649e133a7634418a5edaaf228d52f0d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201284"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Podstawa zabezpieczeń platformy Azure dla Azure Database for MariaDB

Podstawą zabezpieczeń platformy Azure dla Azure Database for MariaDB są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: Konfigurowanie prywatnego linku dla Azure Database for MariaDB z prywatnymi punktami końcowymi. Usługa Private Link umożliwia łączenie z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo łączy usługi platformy Azure z Twoją prywatną siecią wirtualną. Ruch między siecią wirtualną i wystąpieniem MariaDB podróżuje z siecią szkieletową firmy Microsoft.

Alternatywnie możesz użyć punktów końcowych usługi Virtual Network do ochrony i ograniczania dostępu sieciowego do implementacji Azure Database for MariaDB. Reguły sieci wirtualnej to jedna funkcja zabezpieczeń zapory, która kontroluje, czy Azure Database for MariaDB akceptuje wiadomości wysyłane z określonych podsieci w sieciach wirtualnych.

Możesz również zabezpieczyć Azure Database for MariaDB przy użyciu reguł zapory. Zapora serwera uniemożliwia dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

Jak skonfigurować link prywatny dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Jak utworzyć i zarządzać punktami końcowymi usługi sieci wirtualnej i regułami sieci wirtualnej w programie Azure Database for MariaDB Server: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Jak skonfigurować reguły zapory Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: gdy serwer Azure Database for MariaDB jest zabezpieczony za pomocą prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) można ograniczyć ryzyko związane z eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jak skonfigurować link prywatny dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal sposób włączania i używania Analiza ruchu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami dla Azure Database for MariaDB. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Włącz DDoS Protection standard w sieciach wirtualnych skojarzonych z wystąpieniami Azure Database for MariaDB, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować ochronę DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: gdy serwer Azure Database for MariaDB jest zabezpieczony za pomocą prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Następnie można skonfigurować grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) w celu zmniejszenia ryzyka związanego z eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal sposób włączania i używania Analiza ruchu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami dla Azure Database for MariaDB. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.
Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do wystąpień Azure Database for MariaDB, użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. SQL. Zachodnie) w odpowiednim polu źródłowym lub docelowym reguły można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.
Uwaga: Azure Database for MariaDB używa znacznika usługi "Microsoft. SQL".

Aby uzyskać więcej informacji na temat używania tagów usługi: https://docs.microsoft.com/azure/virtual-network/service-tags-overview Opis użycia tagu usługi dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych i zasobów sieciowych skojarzonych z wystąpieniami Azure Database for MariaDB przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. DBforMariaDB" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Database for MariaDB. Możesz również korzystać z wbudowanych definicji zasad związanych z siecią lub Azure Database for MariaDB wystąpieniami, takimi jak:

- Należy włączyć Standard DDoS Protection

- Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB

- Serwer MariaDB powinien używać punktu końcowego usługi sieci wirtualnej

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady Azure Policy dla sieci: https://docs.microsoft.com/azure/governance/policy/samples/

Jak utworzyć Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień MariaDB, aby zapewnić metadane i organizację logiczną.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Database for MariaDB. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.
Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view jak tworzyć alerty w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Database for MariaDB sygnatur czasowych w dziennikach.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych i dzienników serwera i dzienników pozyskiwania w celu agregowania danych zabezpieczeń wygenerowanych przez wystąpienia Azure Database for MariaDB. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.
Jak skonfigurować i uzyskać dostęp do dzienników serwera dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych w wystąpieniach Azure Database for MariaDB, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Upewnij się, że został jawnie włączony dziennik inspekcji MariaDB. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. Możesz również włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać dzienniki do tego samego obszaru roboczego Log Analytics lub konta magazynu.

Jak skonfigurować i uzyskać dostęp do dzienników serwera dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal jak skonfigurować ustawienia diagnostyczne dla dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor w obszarze roboczym log Analytics używanym do przechowywania dzienników Azure Database for MariaDB należy ustawić okres przechowywania zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.
Jak ustawić parametry przechowywania dzienników dla Log Analytics obszarów roboczych: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period przechowywanie dzienników zasobów na koncie usługi Azure Storage: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników z wystąpień usługi MariaDB w celu nietypowego zachowania. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Aby uzyskać więcej informacji na temat obszaru roboczego Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać niestandardowe zapytania w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla MariaDB. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Ponadto można włączyć Dzienniki serwera i ustawienia diagnostyczne dla MariaDB i wysłać dzienniki do obszaru roboczego Log Analytics. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

Jak włączyć zaawansowaną ochronę przed zagrożeniami dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować i uzyskać dostęp do dzienników serwera dla MariDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: N/A; Usługa MariaDB nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: N/A; MariaDB nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: N/A; testy porównawcze są przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu kont użytkowników, które mają dostęp administracyjny do płaszczyzny zarządzania (Azure Portal/Azure Resource Manager) wystąpień MariaDB. Ponadto należy zachować spis kont administracyjnych, które mają dostęp do płaszczyzny danych wystąpień MariaDB. (Podczas tworzenia serwera MariaDB należy podać poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników programu MariaDB).

Informacje o zarządzaniu dostępem dla MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Zapoznaj się z wbudowanymi rolami platformy Azure dla subskrypcji platformy Azure: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory nie ma koncepcji domyślnych haseł.

Po utworzeniu samego zasobu MariaDB platforma Azure wymusza tworzenie użytkownika administracyjnego przy użyciu silnego hasła. Jednak po utworzeniu wystąpienia MariaDB można użyć pierwszego konta administratora serwera, które zostało utworzone, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administracyjnego. Podczas tworzenia tych kont należy skonfigurować inne, silne hasło dla każdego konta.

Jak utworzyć dodatkowe konta dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do wystąpień MariaDB. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Informacje o tożsamości i dostępie Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: dostęp do płaszczyzny danych do MariaDB jest kontrolowany przez tożsamości przechowywane w bazie danych i nie obsługuje logowania jednokrotnego. Dostęp do płaszczyzny kontroli dla MariaDB jest dostępny za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby przeprowadzić uwierzytelnianie, należy ustawić nagłówek autoryzacji dla żądań na token sieci Web JSON uzyskany z Azure Active Directory.

Informacje o interfejsie API REST Azure Database for MariaDB: https://docs.microsoft.com/rest/api/mariadb/

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Jak włączyć usługę MFA na platformie Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla MariaDB, aby generować alerty dla podejrzanych działań.

Ponadto można użyć Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak wdrożyć Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informacje o wykrywaniu ryzyka usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów, aby ograniczyć dostęp do zasobów platformy Azure, takich jak MariaDB.

Jak skonfigurować nazwane lokalizacje na platformie Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. W usłudze AAD dane są chronione przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. W usłudze AAD są również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Uwierzytelniania usługi Azure AD nie można używać do bezpośredniego dostępu do płaszczyzny danych MariaDB, jednak poświadczenia usługi Azure AD mogą być używane do administrowania na poziomie płaszczyzny zarządzania (np. Azure Portal) do kontrolowania kont administratorów MariaDB.

Jak zaktualizować hasło administratora dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Zapoznaj się z dziennikami Azure Active Directory, aby ułatwić odnalezienie starych kont, które mogą obejmować role administracyjne MariaDB. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do MariaDB i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

Informacje o raportowaniu usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Włączanie ustawień diagnostycznych dla MariaDB i Azure Active Directory, wysyłanie wszystkich dzienników do log Analytics obszaru roboczego. Skonfiguruj żądane alerty (takie jak nieudane próby uwierzytelniania) w obszarze roboczym Log Analytics.

Jak skonfigurować i uzyskać dostęp do dzienników serwera dla MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla MariaDB. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania. Automatyczne odpowiedzi można włączyć za pomocą wskaźnikowego platformy Azure, aby zaimplementować odpowiedzi na zabezpieczenia organizacji.

Jak włączyć zaawansowaną ochronę przed zagrożeniami dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak wyświetlić ryzykowne logowania usługi Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak dołączyć wskaźnik na platformie Azure: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Azure Database for MariaDB nie Skrytka klienta jeszcze obsługiwane.

Lista obsługiwanych usług Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu wystąpień Azure Database for MariaDB lub związanych z nimi zasobów, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Użyj połączenia prywatnego, punktów końcowych usługi i/lub MariaDB reguły zapory, aby wyizolować i ograniczyć dostęp sieciowy do wystąpień MariaDB.

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Jak skonfigurować link prywatny dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Jak skonfigurować punkty końcowe usługi dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Jak skonfigurować reguły zapory dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: podczas korzystania z maszyn wirtualnych platformy Azure w celu uzyskiwania dostępu do wystąpień usługi MariaDB należy użyć prywatnego linku, konfiguracji sieci MariaDB, sieciowych grup zabezpieczeń i tagów usług, aby ograniczyć możliwość eksfiltracji danych.

Firma Microsoft zarządza podstawową infrastrukturą dla MariaDB i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Jak wyeliminować eksfiltracji danych dla Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure Database for MariaDB obsługuje łączenie serwera Azure Database for MariaDB z aplikacjami klienckimi przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako Secure SOCKETS Layer (SSL). Wymuszanie połączeń TLS między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. W Azure Portal upewnij się, że dla wszystkich wystąpień usługi MariaDB jest włączone polecenie Wymuszaj połączenie SSL.

Jak skonfigurować szyfrowanie podczas przesyłania dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Database for MariaDB. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie Azure Security Center**: niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do usługi Azure Database dla płaszczyzny zarządzania MariaDB (Azure Portal/Azure Resource Manager). Aby uzyskać dostęp do płaszczyzny danych (w samej bazie danych), należy użyć zapytań SQL do tworzenia użytkowników i konfigurowania uprawnień użytkownika.

Jak skonfigurować usługę Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Jak skonfigurować dostęp użytkowników przy użyciu programu SQL dla MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: N/A; te wytyczne są przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla MariaDB i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Zrozumienie ochrony danych klientów na platformie Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: usługa Azure Database for MariaDB używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu przechowywanych danych. Dane, w tym kopie zapasowe, są szyfrowane na dysku, z wyjątkiem plików tymczasowych utworzonych podczas wykonywania zapytań. Usługa używa szyfru AES 256-bit zawartego w szyfrowaniu usługi Azure Storage, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

Poznaj szyfrowanie MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Azure Database for MariaDB i innych krytycznych lub powiązanych zasobów.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: obecnie niedostępne; Azure Security Center nie obsługuje jeszcze oceny luk w zabezpieczeniach dla Azure Database for MariaDB Server.


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach bazowych, które obsługują serwer Azure Database for MariaDB.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym Azure Database for MariaDB Server) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Opis kontroli RBAC platformy Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do serwera Azure Database for MariaDB i innych powiązanych zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia Azure Database for MariaDB serwera i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania: https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6,11: <div>Ograniczanie możliwości korzystania przez użytkowników z usługi Azure zasobów Manager za pomocą skryptów</div>

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich Azure Database for MariaDB serwer zawierający informacje poufne.

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Database for MariaDB przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DBforMariaDB", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci serwerów Azure Database for MariaDB. Możesz również wykorzystać wbudowane definicje zasad powiązane z serwerami Azure Database for MariaDB, na przykład:

- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB

Jak wyświetlić dostępne aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla serwerów Azure Database for MariaDB i powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

[Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DBforMariaDB", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DBforMariaDB", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla wystąpień Azure Database for MariaDB i powiązanych zasobów.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: w przypadku usługi Azure Virtual Machines lub aplikacji sieci Web działających na Azure App Service używanych do uzyskiwania dostępu do serwerów Azure Database for MariaDB należy użyć tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Azure Database for MariaDB serwer tajny zarządzania serwerem. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: serwer Azure Database for MariaDB obecnie nie obsługuje uwierzytelniania Azure Active Directory w celu uzyskiwania dostępu do baz danych.  Podczas tworzenia serwera Azure Database for MariaDB podajesz poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników MariaDB.  

W przypadku usługi Azure Virtual Machines lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do serwera Azure Database for MariaDB należy użyć tożsamość usługi zarządzanej w połączeniu z Azure Key Vault do przechowywania i pobierania poświadczeń dla serwera Azure Database for MariaDB.  Upewnij się, że Key Vault usuwanie trwałe jest włączone.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie. Jak skonfigurować tożsamości zarządzane: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm . Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity .



**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service), ale nie jest uruchamiane w treści klienta.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład serwer Azure Database for MariaDB), ale nie jest on uruchamiany w treści klienta.

Przed przeskanowaniem zawartość przekazywana do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB Server itp. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład serwer Azure Database for MariaDB), ale nie jest on uruchamiany w treści klienta.


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Azure Database for MariaDB pobiera pełne, różnicowe i transakcyjne kopie zapasowe dziennika.  Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub geograficznie nadmiarowy. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.  Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Informacje o kopiach zapasowych dla MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Opis początkowej konfiguracji MariaDB: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Azure Database for MariaDB automatycznie tworzy kopie zapasowe serwera i przechowuje je w użytkowniku skonfigurowanym lokalnie nadmiarowy lub geograficznie nadmiarowy. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie.  Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem.

W przypadku używania Key Vault do szyfrowania danych po stronie klienta dla danych przechowywanych na serwerze MariaDB należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy.

Informacje o kopiach zapasowych dla MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Jak utworzyć kopię zapasową kluczy Key Vault:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: w Azure Database for MariaDB wykonaj przywracanie z kopii zapasowych oryginalnego serwera, aby okresowo przetestować kopie zapasowe. Dostępne są dwa typy przywracania: Przywracanie do punktu w czasie i przywracanie geograficzne. Przywracanie do punktu w czasie jest dostępne z opcją nadmiarowości kopii zapasowych i tworzy nowy serwer w tym samym regionie, w którym znajduje się oryginalny serwer. Przywracanie geograficzne jest dostępne tylko wtedy, gdy skonfigurowano serwer dla magazynu geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

Informacje na temat tworzenia kopii zapasowych i przywracania w Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Azure Database for MariaDB pobiera pełne, różnicowe i transakcyjne kopie zapasowe dziennika. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Informacje na temat tworzenia kopii zapasowych i przywracania w Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Anatomia w centrum Microsoft Security Response: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- Klient może także skorzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu. 

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- Alerty zabezpieczeń w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Użyj tagów do organizowania zasobów platformy Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- Jak ustawić Azure Security Center kontaktu zabezpieczeń: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- Jak skonfigurować eksport ciągły: https://docs.microsoft.com/azure/security-center/continuous-export
- Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.
    

Jak skonfigurować automatyzację przepływu pracy i Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w oparciu o infrastrukturę, usługi i aplikacje chmurowe zarządzane przez firmę Microsoft, należy tutaj:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
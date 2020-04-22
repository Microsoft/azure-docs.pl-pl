---
title: Plan bazowy zabezpieczeń platformy Azure dla usługi HDInsight
description: Plan bazowy zabezpieczeń platformy Azure dla usługi HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2ddb9a9c205135707d7d96beb106074baa1ce324
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758267"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Plan bazowy zabezpieczeń platformy Azure dla usługi HDInsight

Usługa Azure Security Baseline for HDInsight zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Zabezpieczenia obwodowe w usłudze Azure HDInsight są osiągane za pośrednictwem sieci wirtualnych. Administrator przedsiębiorstwa może utworzyć klaster wewnątrz sieci wirtualnej i użyć sieciowej grupy zabezpieczeń (NSG) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w regułach przychodzącej sieciowej grupy zabezpieczeń będą mogły komunikować się z klastrem usługi Azure HDInsight. Ta konfiguracja zapewnia zabezpieczenia obwodowe. Wszystkie klastry wdrożone w sieci wirtualnej będą również miały prywatny punkt końcowy, który jest rozpoznawany na prywatny adres IP wewnątrz sieci wirtualnej dla prywatnego dostępu HTTP do bram klastra.

Aby zmniejszyć ryzyko utraty danych za pomocą eksfiltracji, należy ograniczyć ruch sieciowy wychodzący dla klastrów usługi Azure HDInsight przy użyciu zapory azure.

Jak wdrożyć usługę Azure HDInsight w sieci wirtualnej i bezpieczne z sieciowej grupy zabezpieczeń:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight z Zaporą platformy Azure:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Użyj usługi Azure Security Center i korygować zalecenia dotyczące ochrony sieci dla sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń używanej do zabezpieczania klastra usługi Azure HDInsight. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) i wysyłaj dzienniki do konta usługi Azure Storage do inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Azure Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów wyszukiwania, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z usługi Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; benchmark jest przeznaczony dla usługi Azure Apps Service lub zasobów obliczeniowych hosting aplikacji sieci web.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Aby uzyskać ochronę przed atakami DDoS, włącz standardową ochronę usługi Azure DDoS w sieci wirtualnej, w której jest wdrażany usługa Azure HDInsight. Zintegrowana analiza zagrożeń usługi Azure Security Center umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować ochronę przed atakami DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Włącz dzienniki flog grupy zabezpieczeń sieciowej (NSG) dla sieciowej grupy zabezpieczeń dołączonej do podsieci używanej do ochrony klastra usługi Azure HDInsight. Rejestrowanie dzienników przepływu nsg na koncie usługi Azure Storage w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowej aktywności, włącz przechwytywanie pakietów usługi Azure Network Watcher.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć funkcję Obserwatora sieciowego:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Wymagania mogą być spełnione identyfikator kontroli zabezpieczeń platformy Azure 1.1; Wdrażanie klastra usługi Azure HDInsight w sieci wirtualnej i bezpieczne z sieciowej grupy zabezpieczeń (NSG).

Istnieje kilka zależności dla usługi Azure HDInsight, które wymagają ruchu przychodzącego. Przychodzącego ruchu zarządzania nie można wysyłać za pośrednictwem urządzenia zapory. Znane i publikowane są adresy źródłowe wymaganego ruchu zarządzania. Utwórz reguły sieciowej grupy zabezpieczeń z tymi informacjami, aby zezwolić na ruch tylko z zaufanych lokalizacji, zabezpieczając ruch przychodzący do klastrów.

Aby zmniejszyć ryzyko utraty danych za pomocą eksfiltracji, należy ograniczyć ruch sieciowy wychodzący dla klastrów usługi Azure HDInsight przy użyciu zapory azure.

Jak wdrożyć usługi HDInsight w sieci wirtualnej i bezpieczne z sieciowej grupy zabezpieczeń:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Opis zależności hdinsight i użycia zapory:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Adresy IP zarządzania hdinsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; benchmark jest przeznaczony dla usługi Azure Apps Service lub zasobów obliczeniowych hosting aplikacji sieci web.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Użyj tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń (NSG), które są dołączone do podsieci, w której jest wdrażany klaster usługi Azure HDInsight. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Zrozumienie i używanie tagów usługi dla usługi Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z klastrem usługi Azure HDInsight. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.HDInsight" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej klastra usługi Azure HDInsight.

Można również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Manager, formanty RBAC i zasady, w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i środowisk i dostosować kontroli i zarządzania poprzez przechowywanie wersji.

Jak wyświetlić dostępne aliasy zasad platformy Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć plan platformy Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla sieciowej grupy zabezpieczeń (NSG) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu skojarzonymi z klastrem usługi Azure HDInsight. W przypadku poszczególnych reguł sieciowej grupy danych sieciowych należy użyć pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł umożliwiających ruch do/z sieci.

Użyj dowolnej z wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieoznakowanych zasobach.

Można użyć interfejsu wiersza polecenia platformy Azure lub platformy Azure (CLI) do wyszukiwania lub wykonywania akcji na podstawie zasobów na podstawie ich tagów.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć nsg z configiem zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wdrożeniami usługi Azure HDInsight. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Firma Microsoft przechowuje źródła czasu dla składników klastra Usługi Azure HDInsight, można zaktualizować synchronizację czasu dla wdrożeń obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Można dołączać klaster usługi Azure HDInsight do usługi Azure Monitor do agregowania danych zabezpieczeń generowanych przez klaster. Korzystać z niestandardowych zapytań do wykrywania zagrożeń i reagowania na nie w środowisku. 

Jak przywdzielić klaster usługi Azure HDInsight do usługi Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak utworzyć niestandardowe kwerendy dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz usługę Azure Monitor dla klastra HDInsight, kieruj go do obszaru roboczego usługi Log Analytics. Spowoduje to rejestrowanie istotnych informacji klastra i metryk systemu operacyjnego dla wszystkich węzłów klastra usługi Azure HDInsight.

Jak włączyć rejestrowanie dla klastra HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak wysyłać zapytania do dzienników usługi HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** Wbudowany klaster usługi Azure HDInsight w usłudze Azure Monitor. Upewnij się, że używany obszar roboczy usługi Log Analytics ma ustawiony okres przechowywania dzienników zgodnie z przepisami dotyczącymi zgodności w organizacji.

Jak przywdzielić klaster usługi Azure HDInsight do usługi Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak skonfigurować okres przechowywania obszaru roboczego usługi Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Wbudowany klaster usługi Azure HDInsight w usłudze Azure Monitor. Upewnij się, że używany obszar roboczy usługi Azure Log Analytics ma okres przechowywania dzienników ustawiony zgodnie z przepisami dotyczącymi zgodności organizacji.

Jak przywdzielić klaster usługi Azure HDInsight do usługi Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak skonfigurować okres przechowywania obszaru roboczego usługi Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Użyj zapytań obszaru roboczego usługi Azure Log Analytics, aby wysyłać zapytania do dzienników usługi Azure HDInsight:

Jak utworzyć zapytania niestandardowe dla klastrów usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** Użyj obszaru roboczego usługi Azure Log Analytics do monitorowania i alertów o nietypowych działaniach w dziennikach zabezpieczeń i zdarzeniach związanych z klastrem usługi Azure HDInsight.

Jak zarządzać alertami w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak ostrzegać o danych dziennika analizy dziennika:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki:** Usługa Azure HDInsight jest wyposażona w wstępnie zainstalowaną i włączoną funkcję Clamscan dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować/monitorować wszelkie dzienniki, które generuje Clamscan.

Zrozumieć Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy do rejestrowania dns.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki:** Ręcznie skonfiguruj rejestrowanie konsoli na podstawie węzła.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Obsługa rekordu lokalnego konta administracyjnego, który jest tworzony podczas inicjowania obsługi administracyjnej klastra usługi Azure HDInsight, a także innych kont, które tworzysz. Ponadto jeśli używana jest integracja usługi Azure AD, usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i dlatego są możliwe do queryable. Użyj modułu programu Azure AD PowerShell do wykonywania kwerend adhoc w celu odnajdowania kont, które są członkami grup administracyjnych.

Ponadto można użyć zaleceń dotyczących tożsamości i zarządzania dostępem usługi Azure Security Center.

Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Jak monitorować tożsamość i dostęp za pomocą usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Podczas inicjowania obsługi administracyjnej klastra platforma Azure wymaga utworzenia nowych haseł dla portalu sieci web i dostępu do bezpiecznego powłoki (SSH). Nie ma żadnych domyślnych haseł do zmiany, jednak można określić różne hasła dla dostępu SSH i portalu sieci Web.

Jak ustawić hasła podczas inicjowania obsługi administracyjnej klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki:** Integruj uwierzytelnianie dla klastra usługi Azure HDInsight z usługą Azure Active Directory. Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych.

Ponadto można użyć zaleceń dotyczących tożsamości i zarządzania dostępem usługi Azure Security Center.

Jak zintegrować uwierzytelnianie usługi Azure HDInsight z usługą Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Jak monitorować tożsamość i dostęp za pomocą usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Użyj narzędzia Azure HDInsight ID Broker, aby zalogować się do klastrów pakietu zabezpieczeń przedsiębiorstwa (ESP) przy użyciu uwierzytelniania wieloskładnikowego, bez podawania żadnych haseł. Jeśli zalogowano się już do innych usług platformy Azure, takich jak witryna Azure portal, możesz zalogować się do klastra usługi Azure HDInsight za pomocą środowiska logowania jednokrotnego.

Jak włączyć brokera identyfikatorów USŁUGI Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi tożsamości i zarządzania dostępem usługi Azure Security Center. Klastry usługi Azure HDInsight ze skonfigurowanym pakietem zabezpieczeń przedsiębiorstwa mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą używać poświadczeń domeny do uwierzytelniania za pomocą klastrów i uruchamiania zadań dużych zbiorów danych. Po uwierzytelnieniu z włączoną obsługą uwierzytelniania wieloskładnikowego użytkownicy będą musieli wyzwać drugi czynnik uwierzytelniania.

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj paws (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowany do logowania się i konfigurowania klastrów usługi Azure HDInsight i powiązanych zasobów.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Klastry usługi Azure HDInsight ze skonfigurowanym pakietem zabezpieczeń przedsiębiorstwa mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny do uwierzytelniania. Raporty zabezpieczeń usługi Azure Active Directory (AAD) można używać do generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku usługi AAD. Usługa Azure Security Center służy do monitorowania tożsamości i dostępu do aktywności.

Jak zidentyfikować użytkowników AAD oznaczonych jako ryzykowne:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować tożsamość użytkowników i dostęp do aktywności w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Klastry usługi Azure HDInsight ze skonfigurowanym pakietem zabezpieczeń przedsiębiorstwa mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny do uwierzytelniania. Użyj funkcji Nazwane lokalizacje dostępu warunkowego, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa AAD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. AAD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Klastry usługi Azure HDInsight ze skonfigurowanym pakietem zabezpieczeń przedsiębiorstwa (ESP) można połączyć z domeną, dzięki czemu użytkownicy domeny mogą używać poświadczeń domeny do uwierzytelniania za pomocą klastrów.

Jak utworzyć i skonfigurować wystąpienie AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Jak skonfigurować pakiet zabezpieczeń przedsiębiorstwa za pomocą usług domenowych Active Directory platformy Azure w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Użyj uwierzytelniania usługi Azure Active Directory (AAD) w klastrze usługi Azure HDInsight. Usługa AAD udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp. 

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Użyj dzienników logowania i inspekcji usługi Azure Active Directory (AAD), aby monitorować próby uzyskania dostępu do dezaktywowanych kont; te dzienniki mogą być zintegrowane z dowolnym narzędziem SIEM/monitoringu innych firm.

Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi AAD, wysyłając dzienniki inspekcji i logi logowania do obszaru roboczego usługi Azure Log Analytics. Konfigurowanie żądanych alertów w obszarze roboczym usługi Azure Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Klastry usługi Azure HDInsight ze skonfigurowanym pakietem zabezpieczeń przedsiębiorstwa (ESP) można połączyć z domeną, dzięki czemu użytkownicy domeny mogą używać poświadczeń domeny do uwierzytelniania za pomocą klastrów.  Funkcja wykrywania ryzyka i ochrony tożsamości usługi Azure Active Directory (AAD) umożliwia konfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić ryzykowne logowania AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla usługi Azure HDInsight.

Lista obsługiwanych usług skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów w zasobach związanych z wdrożeniami usługi Azure HDInsight, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Klastry usługi Azure HDInsight i wszystkie skojarzone konta magazynu powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone w sieciowej grupie zabezpieczeń (NSG) lub zaporze platformy Azure. Dane klastra powinny znajdować się w zabezpieczonym koncie usługi Azure Storage lub usłudze Azure Data Lake Storage (Gen1 lub Gen2).

Wybierz opcje magazynowania dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Jak zabezpieczyć usługę Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Dla klastrów usługi Azure HDInsight przechowywania lub przetwarzania poufnych informacji, oznacz klaster i powiązanych zasobów jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych za pomocą eksfiltracji, należy ograniczyć ruch sieciowy wychodzący dla klastrów usługi Azure HDInsight przy użyciu zapory azure.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight z Zaporą platformy Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki**: Szyfrowanie wszystkich poufnych informacji podczas przesyłania. Upewnij się, że klienci łączący się z klastrem usługi Azure HDInsight lub magazynami danych klastra (konta usługi Azure Storage lub usługa Azure Data Lake Storage Gen1/Gen2) mogą negocjować tls 1.2 lub więcej. Zasoby platformy Microsoft Azure będą domyślnie negocjować usługę TLS 1.2. 

Poznaj szyfrowanie usługi Azure Data Lake Storage podczas przesyłania:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Poznaj szyfrowanie konta usługi Azure Storage podczas przesyłania:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania stratom danych nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Dzięki pakietowi zabezpieczeń dla przedsiębiorstwa Azure HDInsight (ESP) można używać Apache Ranger do tworzenia i zarządzania szczegółowymi zasadami kontroli dostępu i zaciemniania danych dla danych przechowywanych w plikach, folderach, bazach danych, tabelach i wierszach/kolumnach. Administrator hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC) w celu zabezpieczenia Apache Hive, HBase, Kafka i Spark przy użyciu tych wtyczek w Apache Ranger.

Konfigurowanie zasad RBAC za pomocą apache Ranger umożliwia skojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że ludzie mają tylko uprawnienia potrzebne do wykonywania swoich obowiązków służbowych.

Konfiguracje pakietów zabezpieczeń przedsiębiorstwa z usługami domenowymi Active Directory platformy Azure w programie HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki:** Dla klastrów usługi Azure HDInsight przechowywania lub przetwarzania poufnych informacji, oznacz klaster i powiązanych zasobów jako poufne przy użyciu tagów. Funkcje identyfikacji, klasyfikacji i zapobiegania stratom nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Jeśli używasz usługi Azure SQL Database do przechowywania metadanych Apache Hive i Apache Oozie, upewnij się, że dane SQL pozostają szyfrowane przez cały czas. W przypadku kont usługi Azure Storage i magazynu usługi Data Lake (Gen1 lub Gen2) zaleca się zezwolenie firmie Microsoft na zarządzanie kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami.

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Jak utworzyć usługę Azure Data Lake Storage przy użyciu kluczy szyfrowania zarządzanych przez klienta:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Opis szyfrowania dla usługi Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Jak skonfigurować przezroczyste szyfrowanie danych dla bazy danych SQL przy użyciu kluczy zarządzanych przez klienta:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Konfigurowanie ustawień diagnostycznych dla kont usługi Azure Storage skojarzonych z klastrami usługi Azure HDInsight w celu monitorowania i rejestrowania wszystkich operacji CRUD względem danych klastra. Włącz inspekcję dla wszystkich kont magazynu lub magazynów usługi Data Lake skojarzonych z klastrem usługi Azure HDInsight.

Jak włączyć dodatkowe rejestrowanie/inspekcję konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Jak włączyć dodatkowe rejestrowanie/inspekcję usługi Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Wdrożenie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm.

Opcjonalnie, jeśli masz Rapid7, Qualys lub inną subskrypcję platformy zarządzania lukami, możesz użyć akcji skryptów, aby zainstalować agentów oceny luk w zabezpieczeniach w węzłach klastra usługi Azure HDInsight i zarządzać węzłami za pośrednictwem odpowiedniego portalu.

Jak zainstalować Rapid7 Agent ręcznie:

https://insightvm.help.rapid7.com/docs/install

Jak zainstalować Qualys Agent ręcznie:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Jak używać akcji skryptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Automatyczne aktualizacje systemu zostały włączone dla obrazów węzłów klastra, jednak należy okresowo ponownie uruchamiać węzły klastra, aby upewnić się, że aktualizacje są stosowane.

Microsoft do obsługi i aktualizacji podstawowych obrazów węzłów usługi Azure HDInsight.

Jak skonfigurować harmonogram poprawek systemu operacyjnego dla klastrów HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki:** Użyj akcji skryptów lub innych mechanizmów, aby załatać klastry usługi Azure HDInsight. Nowo utworzone klastry zawsze będą miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Jak skonfigurować harmonogram poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight opartych na systemie Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Jak używać akcji skryptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki:** Zaimplementuj rozwiązanie do zarządzania lukami w zabezpieczeniach innych firm, które z czasem może porównywać skanowanie luk w zabezpieczeniach. Jeśli masz subskrypcję Rapid7 lub Qualys, możesz użyć portalu tego dostawcy do wyświetlania i porównywania skanowania luk w zabezpieczeniach typu back-to-back.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Użyj wspólnego programu oceny ryzyka (np. wspólnego systemu oceny luk w zabezpieczeniach) lub domyślnych ocen ryzyka dostarczonych przez narzędzie do skanowania innych firm.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.), w tym klastrów usługi Azure HDInsight w ramach subskrypcji.  Upewnij się, że masz odpowiednie (odczytu) uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, podając metadane logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W stosownych przypadkach należy używać tagowania, grup zarządzania i oddzielnych subskrypcji, aby organizować i śledzić zasoby. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki:** Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki:** Wdrożenie rozwiązania innej firmy do monitorowania węzłów klastra pod kątem niezatwierdzonych aplikacji.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.), w tym klastrów usługi Azure HDInsight w ramach subskrypcji.  Usuń wszystkie niezatwierdzone zasoby platformy Azure, które można odkryć. W przypadku węzłów klastra usługi Azure HDInsight należy zaimplementować rozwiązanie innej firmy w celu usunięcia lub alertu w niezatwierdzonych programach.

Jak tworzyć zapytania za pomocą programu Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki:** W przypadku węzłów klastra usługi Azure HDInsight należy zaimplementować rozwiązanie innej firmy, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki:** W przypadku węzłów klastra usługi Azure HDInsight należy zaimplementować rozwiązanie innej firmy, aby zapobiec wykonywaniu nieautoryzowanych typów plików.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ograniczanie użytkownikom możliwości interakcji z usługą Azure Resources Manager za pomocą skryptów

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; Nie ma to zastosowania do usługi Azure HDInsight, ponieważ użytkownicy (nie-administratorzy) klastra nie potrzebują dostępu do poszczególnych węzłów do uruchamiania zadań. Administrator klastra ma dostęp administratora do wszystkich węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; benchmark jest przeznaczony dla usługi Azure Apps Service lub zasobów obliczeniowych hosting aplikacji sieci web.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.HDInsight", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej klastra HDInsight.

Jak wyświetlić dostępne aliasy zasad platformy Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki:** Obrazy systemu operacyjnego Azure HDInsight zarządzane i obsługiwane przez firmę Microsoft. Klient odpowiedzialny za implementowanie bezpiecznych konfiguracji dla systemu operacyjnego węzłów klastra. 


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla klastrów usługi Azure HDInsight i powiązanych zasobów.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki:** Obrazy systemu operacyjnego Azure HDInsight zarządzane i obsługiwane przez firmę Microsoft. Klient odpowiedzialny za implementowanie konfiguracji stanu na poziomie systemu operacyjnego.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; obrazy niestandardowe nie mają zastosowania do usługi Azure HDInsight.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.HDInsight", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki:** Zaimplementuj rozwiązanie innej firmy, aby utrzymać żądany stan dla systemów operacyjnych węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.HDInsight", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji klastra USŁUGI HDInsight.

Jak wyświetlić dostępne aliasy zasad platformy Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki:** Zaimplementuj rozwiązanie innej firmy do monitorowania stanu systemów operacyjnych węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Usługa Azure HDInsight zawiera obsługę bring your own key (BYOK) dla platformy Apache Kafka. Ta funkcja umożliwia posiadanie kluczy używanych do szyfrowania danych w spoczynku i zarządzanie nimi.

Wszystkie dyski zarządzane w usłudze Azure HDInsight są chronione za pomocą szyfrowania usługi Azure Storage Service (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli włączysz BYOK, podasz klucz szyfrowania dla usługi Azure HDInsight do używania i zarządzania nim przy użyciu usługi Azure Key Vault.

Usługa Key Vault może również używać z wdrożeniami usługi Azure HDInsight do zarządzania kluczami magazynu klastra (konta usługi Azure Storage i usługa Azure Data Lake Storage)

Jak przynieść własny klucz do apache Kafka na platformie Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Tożsamości zarządzane mogą być używane w usłudze Azure HDInsight, aby umożliwić klastrom dostęp do usług domenowych usługi Azure Active Directory, dostęp do usługi Azure Key Vault lub dostęp do plików w usłudze Azure Data Lake Storage Gen2.

Poznaj tożsamości zarządzane za pomocą usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Jeśli przy użyciu kodu związanego z wdrożeniem usługi Azure HDInsight, można zaimplementować Skaner poświadczeń do identyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki:** Usługa Azure HDInsight jest wyposażona w wstępnie zainstalowaną i włączoną funkcję Clamscan dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować/monitorować wszelkie dzienniki, które generuje Clamscan.

Poznaj aplikację Clamscan dla usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście obsługującym usługi platformy Azure, jednak nie działa na zawartości klienta.

Wstępnie przeskanuj wszystkie pliki przekazywane do dowolnych zasobów platformy Azure związanych z wdrożeniem klastra usługi Azure HDInsight, takich jak Magazyn usługi Data Lake, Magazyn obiektów blob itp. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych przypadkach.

Poznaj oprogramowanie antywirusowe firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki:** Usługa Azure HDInsight jest wyposażona w wstępnie zainstalowaną i włączoną funkcję Clamscan dla obrazów węzłów klastra. Clamscan będzie automatycznie wykonywać aktualizacje aparatu i definicji, jednak agregacja i zarządzanie dziennikami będą musiały być wykonywane ręcznie.

Poznaj aplikację Clamscan dla usługi Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Podczas korzystania z konta usługi Azure Storage dla magazynu danych klastra HDInsight wybierz odpowiednią opcję nadmiarowości (LRS, ZRS, GRS, RA-GRS).  Podczas korzystania z usługi Azure SQL Database dla magazynu danych klastra usługi Azure HDInsight należy skonfigurować replikację geograficzną active.

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak skonfigurować nadmiarowość dla baz danych SQL platformy Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Podczas korzystania z konta usługi Azure Storage dla magazynu danych klastra usługi Azure HDInsight wybierz odpowiednią opcję nadmiarowości (LRS, ZRS, GRS, RA-GRS). Jeśli używasz usługi Azure Key Vault dla dowolnej części wdrożenia usługi Azure HDInsight, upewnij się, że kopie zapasowe kluczy są archiwizowane.

Wybierz opcje magazynowania dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak wykonać kopię zapasową kluczy usługi Key Vault na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Jeśli usługa Azure Key Vault jest używana z wdrożeniem usługi Azure HDInsight, przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

Jak przynieść własny klucz do apache Kafka na platformie Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Jeśli usługa Azure Key Vault jest używana z wdrożeniem usługi Azure HDInsight, włącz opcję usuwania programowego w magazynie kluczy, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie programowe w usłudze Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki**: Upewnij się, że istnieją pisemne plany reagowania na incydenty, które określają role personelu, a także fazy obsługi incydentów/zarządzania.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność alertów, aby ułatwić ustalanie priorytetów kolejności, w jakiej uczestniczysz w każdym alertie, dzięki czemu gdy zasób zostanie naruszony, możesz uzyskać do niego od razu. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu. Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w oparciu o zarządzał infrastrukturą, usługami i aplikacjami firmy Microsoft w chmurze:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

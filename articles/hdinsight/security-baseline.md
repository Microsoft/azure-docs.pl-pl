---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi HDInsight
description: Punkt odniesienia zabezpieczeń platformy Azure dla usługi HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5174c3bf2150c4671f1034eba6e661a10c222aec
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807686"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi HDInsight

Podstawowa baza danych zabezpieczeń Azure dla usługi HDInsight zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: zabezpieczenia obwodu w usłudze Azure HDInsight są realizowane za pomocą sieci wirtualnych. Administrator przedsiębiorstwa może utworzyć klaster wewnątrz sieci wirtualnej i użyć sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego będą mogły komunikować się z klastrem usługi Azure HDInsight. Ta konfiguracja zapewnia ochronę obwodową. Wszystkie klastry wdrożone w sieci wirtualnej będą miały również prywatny punkt końcowy, który jest rozpoznawany jako prywatny adres IP w Virtual Network na potrzeby prywatnego dostępu HTTP do bram klastra.

Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

Jak wdrożyć usługę Azure HDInsight w ramach Virtual Network i zabezpieczyć grupę zabezpieczeń sieci: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight za pomocą zapory platformy Azure: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dla sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń używanej do zabezpieczania klastra usługi Azure HDInsight. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta usługi Azure Storage do inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety platformy Azure Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i konfigurowania nieprawidłowo skonfigurowanych sieci.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć usługę Azure Analiza ruchu i korzystać z niej:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; Test porównawczy jest przeznaczony dla usług Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: dla ochrony przed atakami DDoS Włącz ochronę standardową Azure DDoS w sieci wirtualnej, w której wdrożono usługę Azure HDInsight. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

Jak skonfigurować ochronę DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Opis Azure Security Center zintegrowanej analizy zagrożeń:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Włącz usługi Network Security Group (sieciowej grupy zabezpieczeń) Flog dzienniki dla sieciowej grupy zabezpieczeń dołączonego do podsieci używanej do ochrony klastra usługi Azure HDInsight. Zapisz dzienniki przepływu sieciowej grupy zabezpieczeń na koncie usługi Azure Storage w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher platformy Azure.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: wymagania mogą być spełnione w przypadku usługi Azure Security Control o identyfikatorze 1,1; Wdróż klaster usługi Azure HDInsight w sieci wirtualnej i zabezpiecz go za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

Istnieje kilka zależności dotyczących usługi Azure HDInsight, które wymagają ruchu przychodzącego. Ruch przychodzący zarządzania nie może być wysyłany przez urządzenie zapory. Adresy źródłowe dla wymaganego ruchu związanego z zarządzaniem są znane i publikowane. Utwórz reguły sieciowej grupy zabezpieczeń z tymi informacjami, aby zezwolić na ruch z tylko zaufanych lokalizacji, zabezpieczając ruch przychodzący do klastrów.

Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

Jak wdrożyć usługę HDInsight w ramach Virtual Network i zabezpieczyć ją przy użyciu sieciowej grupy zabezpieczeń: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Informacje o zależnościach i użyciu zapory usługi HDInsight: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Adresy IP zarządzania usługą HDInsight: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; Test porównawczy jest przeznaczony dla usług Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dołączonych do podsieci, w której wdrożono klaster usługi Azure HDInsight. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Informacje o tagach usług dla usługi Azure HDInsight i korzystaniu z nich:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z klastrem usługi Azure HDInsight. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. HDInsight" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci klastra usługi Azure HDInsight.

Możesz również użyć planów platformy Azure, aby uprościć duże wdrożenia platformy Azure przez spakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

Jak wyświetlić dostępne aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowej grupy zabezpieczeń (sieciowych grup zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z klastrem usługi Azure HDInsight. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wdrożeniami usługi Azure HDInsight. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak utworzyć alerty w Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla składników klastra usługi Azure HDInsight, ale można zaktualizować synchronizację czasu dla wdrożeń obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: można dołączyć klaster usługi Azure HDInsight, aby Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez klaster. Wykorzystanie niestandardowych zapytań w celu wykrywania zagrożeń i reagowania na nie w środowisku. 

Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak utworzyć niestandardowe zapytania dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie Azure monitor dla klastra usługi HDInsight, kierowanie go do log Analytics obszaru roboczego. Spowoduje to zarejestrowanie odpowiednich informacji o klastrze i metryk systemu operacyjnego dla wszystkich węzłów klastra usługi Azure HDInsight.

Jak włączyć rejestrowanie dla klastra usługi HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak wykonywać zapytania dotyczące dzienników usługi HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: dołączanie klastra usługi Azure HDInsight do Azure monitor. Upewnij się, że używany obszar roboczy Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie klastra usługi Azure HDInsight do Azure monitor. Upewnij się, że używany obszar roboczy platformy Azure Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Użyj zapytań obszaru roboczego usługi Azure log Analytics, aby wykonywać zapytania dotyczące dzienników usługi Azure HDInsight:

Jak utworzyć niestandardowe zapytania dla klastrów usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: używanie obszaru roboczego usługi Azure log Analytics do monitorowania i generowania alertów dotyczących nietypowych działań w dziennikach zabezpieczeń i zdarzeniach związanych z klastrem usługi Azure HDInsight.

Jak zarządzać alertami w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak ostrzec dane dziennika usługi log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować i monitorować wszystkie dzienniki ClamScan.

Opis ClamScan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy na potrzeby rejestrowania w systemie DNS.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: ręczne Konfigurowanie rejestrowania konsoli na poszczególnych węzłach.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: zachowywanie rekordu lokalnego konta administracyjnego, które jest tworzone podczas aprowizacji klastra klastra usługi Azure HDInsight oraz innych utworzonych kont. Ponadto jeśli jest używana integracja z usługą Azure AD, usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i dlatego queryable. Moduł Azure AD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Jak monitorować tożsamość i uzyskać dostęp za pomocą Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: podczas aprowizacji klastra platforma Azure wymaga utworzenia nowych haseł dla portalu internetowego i dostępu Secure Shell (SSH). Nie ma domyślnych haseł do zmiany, można jednak określić różne hasła dla dostępu SSH i portalu internetowego.

Jak ustawić hasła podczas aprowizacji klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: integracja uwierzytelniania dla klastra usługi Azure HDInsight z Azure Active Directory. Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

Jak zintegrować uwierzytelnianie usługi Azure HDInsight z usługą Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Jak monitorować tożsamość i uzyskać dostęp za pomocą Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj usługi Azure HDInsight ID Broker do logowania się do klastrów pakiet Enterprise Security (ESP) przy użyciu Multi-Factor Authentication, bez podawania żadnych haseł. Jeśli zalogowano się już do innych usług platformy Azure, takich jak Azure Portal, możesz zalogować się do klastra usługi Azure HDInsight przy użyciu logowania jednokrotnego (SSO).

Jak włączyć brokera identyfikatorów usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem. Klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny do uwierzytelniania w klastrach i uruchamiania zadań Big Data. Podczas uwierzytelniania przy włączonej operacji uwierzytelniania wieloskładnikowego (MFA) użytkownicy będą otrzymywać monit o podanie drugiego czynnika uwierzytelniania.

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanym do logowania się i konfigurowania klastrów usługi Azure HDInsight oraz powiązanych zasobów.

Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się przy użyciu poświadczeń domeny. Raporty zabezpieczeń Azure Active Directory (AAD) mogą być używane do generowania dzienników i alertów, gdy w środowisku usługi AAD występuje podejrzane lub niebezpieczne działanie. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

Jak zidentyfikować użytkowników usługi AAD oflagowane dla działania ryzykownego:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się przy użyciu poświadczeń domeny. Użyj dostępu warunkowego o nazwie lokalizacje, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. W usłudze AAD dane są chronione przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. W usłudze AAD są również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Klastry usługi Azure HDInsight z skonfigurowanymi pakiet Enterprise Security (ESP) mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się w klastrach za pomocą poświadczeń domeny.

Jak utworzyć i skonfigurować wystąpienie usługi AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Jak skonfigurować pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: używanie uwierzytelniania Azure Active Directory (AAD) z klastrem usługi Azure HDInsight. W usłudze AAD znajdują się dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp. 

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Użyj usługi logowania Azure Active Directory (AAD) i dzienników inspekcji, aby monitorować próby dostępu do zdezaktywowanych kont; te dzienniki można zintegrować z dowolnym narzędziem SIEM/monitorowania innych firm.

Aby usprawnić ten proces, można utworzyć ustawienia diagnostyczne dla kont użytkowników usługi AAD, wysłać dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics platformy Azure. Skonfiguruj żądane alerty w obszarze roboczym usługi Azure Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: klastry usługi Azure HDInsight z skonfigurowanymi pakiet Enterprise Security (ESP) mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą korzystać z poświadczeń domeny w celu uwierzytelniania w klastrach.  Użyj funkcji wykrywania ryzyka Azure Active Directory (AAD) i usługi Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Dodatkowo można pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

Jak wyświetlić ryzykowne logowania w usłudze AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: niedostępne; Usługa Azure HDInsight nie Skrytka klienta jeszcze obsługiwana.

Lista obsługiwanych usług Skrytka klienta: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z wdrożeniami usługi Azure HDInsight, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Klastry usługi Azure HDInsight i wszystkie powiązane konta magazynu powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) lub zapory platformy Azure. Dane klastra powinny znajdować się w ramach zabezpieczonego konta usługi Azure Storage lub Azure Data Lake Storage (Gen1 lub Gen2).

Wybierz opcje magazynu dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Jak zabezpieczyć Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku klastrów usługi Azure HDInsight przechowujących lub przetwarzających informacje poufne należy oznaczyć klaster i powiązane zasoby jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight za pomocą zapory platformy Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z klastrem usługi Azure HDInsight lub magazynami danych klastra (kontami usługi Azure Storage lub Azure Data Lake Storage Gen1/Gen2) mogą negocjować protokół TLS 1,2 lub nowszy. Zasoby Microsoft Azure domyślnie negocjują TLS 1,2. 

Informacje o szyfrowaniu Azure Data Lake Storage podczas przesyłania:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Informacje na temat szyfrowania konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: dzięki usłudze Azure HDInsight pakiet Enterprise Security (ESP) można używać platformy Apache Ranger do tworzenia i zarządzania szczegółowymi zasadami kontroli dostępu i zaciemniania danych dla danych przechowywanych w plikach, folderach, bazach danych, tabelach i wierszach/kolumnach. Administrator usługi Hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC), aby zabezpieczyć Apache Hive, HBase, Kafka i Spark przy użyciu tych wtyczek w usłudze Apache Ranger.

Skonfigurowanie zasad RBAC przy użyciu programu Apache Ranger umożliwia kojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że osoby mają tylko uprawnienia niezbędne do wykonywania swoich obowiązków służbowych.

Pakiet Enterprise Security konfiguracje z Azure Active Directory Domain Services w usłudze HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: w przypadku klastrów usługi Azure HDInsight przechowujących lub przetwarzających informacje poufne należy oznaczyć klaster i powiązane zasoby jako poufne przy użyciu tagów. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

Zrozumienie ochrony danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: w przypadku używania Azure SQL Database do przechowywania metadanych Apache Hive i Apache Oozie upewnij się, że dane SQL pozostają zaszyfrowane przez cały czas. W przypadku kont usługi Azure Storage i Data Lake Storage (Gen1 lub Gen2) zaleca się umożliwienie firmie Microsoft zarządzania kluczami szyfrowania, ale istnieje możliwość zarządzania własnymi kluczami.

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Jak utworzyć Azure Data Lake Storage przy użyciu kluczy szyfrowania zarządzanych przez klienta:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Opis szyfrowania Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Jak skonfigurować Transparent Data Encryption dla SQL Database przy użyciu kluczy zarządzanych przez klienta:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Konfigurowanie ustawień diagnostycznych dla kont usługi Azure Storage skojarzonych z klastrami usługi Azure HDInsight do monitorowania i rejestrowania wszystkich operacji CRUD na danych klastra. Włącz inspekcję dla wszystkich kont magazynu lub magazynów Data Lake skojarzonych z klastrem usługi Azure HDInsight.

Jak włączyć dodatkowe rejestrowanie/inspekcję dla konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Jak włączyć dodatkowe rejestrowanie/inspekcję dla Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm.

Opcjonalnie, jeśli istnieje Rapid7, Qualys lub jakakolwiek inna subskrypcja platformy do zarządzania lukami w zabezpieczeniach, można użyć akcji skryptu, aby zainstalować agentów oceny luk w zabezpieczeniach w węzłach klastra usługi Azure HDInsight i zarządzać węzłami za pomocą odpowiedniego portalu.

Jak zainstalować agenta Rapid7 ręcznie:

https://insightvm.help.rapid7.com/docs/install

Jak zainstalować agenta Qualys ręcznie:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Jak używać akcji skryptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: włączono automatyczne aktualizacje systemu dla obrazów węzłów klastra, jednak należy okresowo ponownie uruchomić węzły klastra, aby upewnić się, że są stosowane aktualizacje.

Firma Microsoft do obsługi i aktualizowania podstawowych obrazów węzłów usługi Azure HDInsight.

Jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: używanie akcji skryptu lub innych mechanizmów do poprawiania klastrów usługi Azure HDInsight. Nowo utworzone klastry będą zawsze miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight opartych na systemie Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Jak używać akcji skryptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm, które umożliwia porównanie luk w zabezpieczeniach w czasie. Jeśli masz subskrypcję usługi Rapid7 lub Qualys, możesz użyć portalu tego dostawcy do wyświetlania i porównywania skanów z oddziałami wstecznymi.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać wszystkie zasoby (takie jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym klastry usługi Azure HDInsight, w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Opis kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania węzłów klastra dla niezatwierdzonych aplikacji.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać wszystkie zasoby (takie jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym klastry usługi Azure HDInsight, w ramach subskrypcji.  Usuń wszystkie niezatwierdzone zasoby platformy Azure, które zostały wykryte. W przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innej firmy w celu usunięcia lub alertu dotyczącego niezatwierdzonego oprogramowania.

Jak tworzyć zapytania za pomocą usługi Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: w przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Jak skonfigurować Azure Policy i zarządzać nimi: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: w przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanych typów plików.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z usługi Azure Resources za pośrednictwem skryptów

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; Nie dotyczy to usługi Azure HDInsight, ponieważ użytkownicy (inni niż administratorzy) klastra nie potrzebują dostępu do poszczególnych węzłów w celu uruchamiania zadań. Administrator klastra ma dostęp do wszystkich węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; Test porównawczy jest przeznaczony dla usług Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci klastra usługi HDInsight.

Jak wyświetlić dostępne aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: obrazy systemu operacyjnego usługi Azure HDInsight zarządzane i utrzymywane przez firmę Microsoft. Klient odpowiedzialny za implementację bezpiecznych konfiguracji dla systemu operacyjnego węzłów klastra. 


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla klastrów usługi Azure HDInsight i powiązanych zasobów.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Opis efektów Azure Policy:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: obrazy systemu operacyjnego usługi Azure HDInsight zarządzane i utrzymywane przez firmę Microsoft. Klient odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

[Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; obrazy niestandardowe nie mają zastosowania do usługi Azure HDInsight.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu utrzymania żądanego stanu dla systemów operacyjnych węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji klastra usługi HDInsight.

[Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

[Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania stanu systemów operacyjnych węzłów klastra.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: usługa Azure HDInsight obejmuje obsługę Bring Your Own Key (BYOK) dla Apache Kafka. Ta funkcja umożliwia posiadanie kluczy używanych do szyfrowania danych przechowywanych w usłudze REST i zarządzanie nimi.

Wszystkie dyski zarządzane w usłudze Azure HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Po włączeniu BYOK należy podać klucz szyfrowania dla usługi Azure HDInsight, aby używać go i zarządzać nim przy użyciu Azure Key Vault.

Key Vault mogą być również używane z wdrożeniami usługi Azure HDInsight w celu zarządzania kluczami magazynu klastra (kontami usługi Azure Storage i Azure Data Lake Storage).

Jak uzyskać własny klucz dla Apache Kafka w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: tożsamości zarządzane mogą być używane w usłudze Azure HDInsight, aby umożliwić Klastrom dostęp do Azure Active Directory usług domenowych, Azure Key Vault dostępu lub plików dostępu w Azure Data Lake Storage Gen2.

Poznaj zarządzane tożsamości za pomocą usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Jeśli używasz dowolnego kodu związanego z wdrożeniem usługi Azure HDInsight, możesz zaimplementować skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

Jak skonfigurować skaner poświadczeń:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować i monitorować wszystkie dzienniki ClamScan.

Informacje o ClamScan usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure, ale nie jest uruchamiane w treści klienta.

Przed przeskanowaniem plików przekazywanych do wszystkich zasobów platformy Azure związanych z wdrożeniem klastra usługi Azure HDInsight, takich jak Data Lake Storage, Blob Storage itd. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych wystąpieniach.

Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra. Program ClamScan będzie automatycznie wykonywał aktualizacje aparatu i definicji, jednak agregacja i zarządzanie dziennikami należy wykonać ręcznie.

Informacje o ClamScan usługi Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych klastra HDInsight wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS).  W przypadku korzystania z Azure SQL Database dla magazynu danych klastra usługi Azure HDInsight Skonfiguruj aktywną replikację geograficzną.

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak skonfigurować nadmiarowość dla Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych klastra usługi Azure HDInsight wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS). W przypadku korzystania z Azure Key Vault dla dowolnej części wdrożenia usługi Azure HDInsight upewnij się, że utworzono kopię zapasową kluczy.

Wybierz opcje magazynu dla klastra usługi Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Jeśli Azure Key Vault jest używany z wdrożeniem usługi Azure HDInsight, przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta.

Jak uzyskać własny klucz dla Apache Kafka w usłudze Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Jeśli Azure Key Vault jest używany z wdrożeniem usługi Azure HDInsight, Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć Soft-Delete w Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami. Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

Jak skonfigurować eksport ciągły:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

Jak skonfigurować automatyzację przepływu pracy i Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Aby uzyskać więcej informacji na temat strategii firmy Microsoft i wykonywania testów z zakresu tworzenia zespołu Red-site i testowania aplikacji na żywo, należy wykonać następujące działania: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
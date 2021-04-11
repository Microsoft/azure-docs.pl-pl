---
title: Podstawa zabezpieczeń platformy Azure dla usługi HDInsight
description: Linia bazowa zabezpieczeń usługi HDInsight zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565014"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Podstawa zabezpieczeń platformy Azure dla usługi HDInsight

Ta linia bazowa zabezpieczeń stosuje wskazówki dotyczące [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) w usłudze HDInsight. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące usługi HDInsight. **Formanty** nie mają zastosowania do usługi HDInsight zostały wykluczone.

 
Aby dowiedzieć się, jak Usługa HDInsight całkowicie mapuje na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi HDInsight](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: zabezpieczenia obwodu w usłudze Azure HDInsight są realizowane za pomocą sieci wirtualnych. Administrator przedsiębiorstwa może utworzyć klaster wewnątrz sieci wirtualnej i użyć sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego będą mogły komunikować się z klastrem usługi Azure HDInsight. Ta konfiguracja zapewnia ochronę obwodową. Wszystkie klastry wdrożone w sieci wirtualnej będą miały również prywatny punkt końcowy, który jest rozpoznawany jako prywatny adres IP w Virtual Network na potrzeby prywatnego dostępu HTTP do bram klastra.

Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

- [Jak wdrożyć usługę Azure HDInsight w ramach Virtual Network i zabezpieczyć grupę zabezpieczeń sieci](hdinsight-create-virtual-network.md)

- [Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight za pomocą zapory platformy Azure](hdinsight-restrict-outbound-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dla sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń używanej do zabezpieczania klastra usługi Azure HDInsight. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i Wyślij dzienniki do konta usługi Azure Storage do inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety platformy Azure Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i konfigurowania nieprawidłowo skonfigurowanych sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć usługę Azure Analiza ruchu i korzystać z niej](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: dla ochrony przed atakami DDoS Włącz ochronę standardową Azure DDoS w sieci wirtualnej, w której wdrożono usługę Azure HDInsight. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowej grupy zabezpieczeń podłączonych do podsieci używanej do ochrony klastra usługi Azure HDInsight. Zapisz dzienniki przepływu sieciowej grupy zabezpieczeń na koncie usługi Azure Storage w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher platformy Azure.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wymagania mogą być spełnione w przypadku usługi Azure Security Control o identyfikatorze 1,1; Wdróż klaster usługi Azure HDInsight w sieci wirtualnej i zabezpiecz go za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

Istnieje kilka zależności dotyczących usługi Azure HDInsight, które wymagają ruchu przychodzącego. Ruch przychodzący zarządzania nie może być wysyłany przez urządzenie zapory. Adresy źródłowe dla wymaganego ruchu związanego z zarządzaniem są znane i publikowane. Utwórz reguły sieciowej grupy zabezpieczeń z tymi informacjami, aby zezwolić na ruch z tylko zaufanych lokalizacji, zabezpieczając ruch przychodzący do klastrów.

Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

- [Jak wdrożyć usługę HDInsight w ramach Virtual Network i zabezpieczyć grupę zabezpieczeń sieci](hdinsight-create-virtual-network.md)

- [Informacje o zależnościach i użyciu zapory usługi HDInsight](hdinsight-restrict-outbound-traffic.md)

- [Adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dołączonych do podsieci, w której wdrożono klaster usługi Azure HDInsight. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usług dla usługi Azure HDInsight](../virtual-network/network-security-groups-overview.md#service-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych związanych z klastrem usługi Azure HDInsight. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. HDInsight" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci klastra usługi Azure HDInsight.

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrolki kontroli RBAC platformy Azure i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowej grupy zabezpieczeń (sieciowych grup zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z klastrem usługi Azure HDInsight. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI), aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wdrożeniami usługi Azure HDInsight. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: można dołączyć klaster usługi Azure HDInsight, aby Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez klaster. Wykorzystanie niestandardowych zapytań w celu wykrywania zagrożeń i reagowania na nie w środowisku. 

- [Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak utworzyć niestandardowe zapytania dla klastra usługi Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie Azure monitor dla klastra usługi HDInsight, kierowanie go do log Analytics obszaru roboczego. Spowoduje to zarejestrowanie odpowiednich informacji o klastrze i metryk systemu operacyjnego dla wszystkich węzłów klastra usługi Azure HDInsight.

- [Jak włączyć rejestrowanie dla klastra usługi HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak wykonywać zapytania dotyczące dzienników usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: dołączanie klastra usługi Azure HDInsight do Azure monitor. Upewnij się, że używany obszar roboczy Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie klastra usługi Azure HDInsight do Azure monitor. Upewnij się, że używany obszar roboczy platformy Azure Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Jak dołączyć klaster usługi Azure HDInsight do Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Użyj zapytań obszaru roboczego usługi Azure log Analytics, aby wykonywać zapytania dotyczące dzienników usługi Azure HDInsight:

- [Jak utworzyć niestandardowe zapytania dla klastrów usługi Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: używanie obszaru roboczego usługi Azure log Analytics do monitorowania i generowania alertów dotyczących nietypowych działań w dziennikach zabezpieczeń i zdarzeniach związanych z klastrem usługi Azure HDInsight.

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować i monitorować wszystkie dzienniki ClamScan.

- [Zrozumienie ClamScan](./hdinsight-faq.md#security-and-certificates)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy na potrzeby rejestrowania w systemie DNS.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: ręczne Konfigurowanie rejestrowania konsoli na poszczególnych węzłach.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: zachowywanie rekordu lokalnego konta administracyjnego, które jest tworzone podczas aprowizacji klastra klastra usługi Azure HDInsight oraz innych utworzonych kont. Ponadto jeśli jest używana integracja z usługą Azure Active Directory (Azure AD), usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i dlatego queryable. Moduł Azure AD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: podczas aprowizacji klastra platforma Azure wymaga utworzenia nowych haseł dla portalu internetowego i dostępu Secure Shell (SSH). Nie ma domyślnych haseł do zmiany, można jednak określić różne hasła dla dostępu SSH i portalu internetowego.

- [Jak ustawić hasła podczas aprowizacji klastra usługi Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: integracja uwierzytelniania dla klastra usługi Azure HDInsight z usługą Azure Active Directory (Azure AD). Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak zintegrować uwierzytelnianie usługi Azure HDInsight z usługą Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Użyj usługi Azure HDInsight ID Broker do logowania się do klastrów pakiet Enterprise Security (ESP) przy użyciu uwierzytelniania wieloskładnikowego, bez podawania żadnych haseł. Jeśli zalogowano się już do innych usług platformy Azure, takich jak Azure Portal, możesz zalogować się do klastra usługi Azure HDInsight przy użyciu logowania jednokrotnego (SSO).

- [Jak włączyć usługę Azure HDInsight ID brokera](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem. Klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny do uwierzytelniania w klastrach i uruchamiania zadań Big Data. Podczas uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego, użytkownicy będą otrzymywać monit o podanie drugiego czynnika uwierzytelniania.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania klastrów usługi Azure HDInsight oraz powiązanych zasobów.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się przy użyciu poświadczeń domeny. Raporty zabezpieczeń Azure Active Directory (Azure AD) mogą być używane do generowania dzienników i alertów, gdy w środowisku usługi Azure AD występuje podejrzane lub niebezpieczne działanie. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: klastry usługi Azure HDInsight ze skonfigurowanym pakiet Enterprise Security mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się przy użyciu poświadczeń domeny. Użyj dostępu warunkowego o nazwie lokalizacje, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Klastry usługi Azure HDInsight z skonfigurowanymi pakiet Enterprise Security (ESP) mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą uwierzytelniać się w klastrach za pomocą poświadczeń domeny.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Jak skonfigurować pakiet Enterprise Security przy użyciu Azure AD Domain Services w usłudze Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: użyj uwierzytelniania Azure Active Directory (Azure AD) z klastrem usługi Azure HDInsight. Usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: użyj programu Azure Active Directory (Azure AD) logowanie i dzienniki inspekcji, aby monitorować próby dostępu do zdezaktywowanych kont; te dzienniki można zintegrować z dowolnym narzędziem SIEM/monitorowania innych firm.

Aby usprawnić ten proces, można utworzyć ustawienia diagnostyczne dla kont użytkowników usługi Azure AD, wysłać dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Azure Log Analytics. Skonfiguruj żądane alerty w obszarze roboczym usługi Azure Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: klastry usługi Azure HDInsight z skonfigurowanymi pakiet Enterprise Security (ESP) mogą być połączone z domeną, dzięki czemu użytkownicy domeny mogą korzystać z poświadczeń domeny w celu uwierzytelniania w klastrach. Użyj funkcji wykrywania ryzyka Azure Active Directory (Azure AD) i ochrony tożsamości, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Dodatkowo można pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: niedostępne; Usługa Azure HDInsight nie Skrytka klienta jeszcze obsługiwana.

- [Lista obsługiwanych usług Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów dotyczących zasobów związanych z wdrożeniami usługi Azure HDInsight, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Klastry usługi Azure HDInsight i wszystkie powiązane konta magazynu powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) lub zapory platformy Azure. Dane klastra powinny znajdować się w ramach zabezpieczonego konta usługi Azure Storage lub Azure Data Lake Storage (Gen1 lub Gen2).

- [Wybieranie opcji magazynu dla klastra usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Jak zabezpieczyć Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Zabezpieczanie kont usługi Azure Storage](../storage/blobs/security-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku klastrów usługi Azure HDInsight przechowujących lub przetwarzających informacje poufne należy oznaczyć klaster i powiązane zasoby jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla klastrów usługi Azure HDInsight przy użyciu zapory platformy Azure.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Jak ograniczyć ruch wychodzący dla klastrów usługi Azure HDInsight za pomocą zapory platformy Azure](hdinsight-restrict-outbound-traffic.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z klastrem usługi Azure HDInsight lub magazynami danych klastra (kontami usługi Azure Storage lub Azure Data Lake Storage Gen1/Gen2) mogą negocjować protokół TLS 1,2 lub nowszy. Zasoby Microsoft Azure domyślnie negocjują TLS 1,2. 

- [Informacje o szyfrowaniu Azure Data Lake Storage podczas przesyłania](../data-lake-store/data-lake-store-security-overview.md)

- [Informacje na temat szyfrowania konta usługi Azure Storage](../storage/blobs/security-recommendations.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: dzięki usłudze Azure HDInsight pakiet Enterprise Security (ESP) można używać platformy Apache Ranger do tworzenia i zarządzania szczegółowymi zasadami kontroli dostępu i zaciemniania danych dla danych przechowywanych w plikach, folderach, bazach danych, tabelach i wierszach/kolumnach. Administrator usługi Hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC), aby zabezpieczyć Apache Hive, HBase, Kafka i Spark przy użyciu tych wtyczek w usłudze Apache Ranger.

Skonfigurowanie zasad RBAC przy użyciu programu Apache Ranger umożliwia kojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że osoby mają tylko uprawnienia niezbędne do wykonywania swoich obowiązków służbowych.

- [Pakiet Enterprise Security konfiguracji z usługami domenowymi Azure Active Directory (Azure AD) w usłudze HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: w przypadku klastrów usługi Azure HDInsight przechowujących lub przetwarzających informacje poufne należy oznaczyć klaster i powiązane zasoby jako poufne przy użyciu tagów. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: w przypadku używania Azure SQL Database do przechowywania metadanych Apache Hive i Apache Oozie upewnij się, że dane SQL pozostają zaszyfrowane przez cały czas. W przypadku kont usługi Azure Storage i Data Lake Storage (Gen1 lub Gen2) zaleca się umożliwienie firmie Microsoft zarządzania kluczami szyfrowania, ale istnieje możliwość zarządzania własnymi kluczami.

- [Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Jak utworzyć Azure Data Lake Storage przy użyciu kluczy szyfrowania zarządzanych przez klienta](../data-lake-store/data-lake-store-get-started-portal.md)

- [Opis szyfrowania dla Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Jak skonfigurować Transparent Data Encryption dla SQL Database przy użyciu kluczy zarządzanych przez klienta](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Konfigurowanie ustawień diagnostycznych dla kont usługi Azure Storage skojarzonych z klastrami usługi Azure HDInsight do monitorowania i rejestrowania wszystkich operacji CRUD na danych klastra. Włącz inspekcję dla wszystkich kont magazynu lub magazynów Data Lake skojarzonych z klastrem usługi Azure HDInsight.

- [Jak włączyć dodatkowe rejestrowanie/inspekcję dla konta usługi Azure Storage](../storage/common/manage-storage-analytics-logs.md)

- [Jak włączyć dodatkowe rejestrowanie/inspekcję dla Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm.

Opcjonalnie, jeśli istnieje Rapid7, Qualys lub jakakolwiek inna subskrypcja platformy do zarządzania lukami w zabezpieczeniach, można użyć akcji skryptu, aby zainstalować agentów oceny luk w zabezpieczeniach w węzłach klastra usługi Azure HDInsight i zarządzać węzłami za pomocą odpowiedniego portalu.

- [Jak ręcznie zainstalować agenta Rapid7](https://insightvm.help.rapid7.com/docs/install)

- [Jak ręcznie zainstalować agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Jak używać akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: włączono automatyczne aktualizacje systemu dla obrazów węzłów klastra, jednak należy okresowo ponownie uruchomić węzły klastra, aby upewnić się, że są stosowane aktualizacje.

Firma Microsoft do obsługi i aktualizowania podstawowych obrazów węzłów usługi Azure HDInsight.

- [Jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi HDInsight](hdinsight-os-patching.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: używanie akcji skryptu lub innych mechanizmów do poprawiania klastrów usługi Azure HDInsight. Nowo utworzone klastry będą zawsze miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

- [Jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight opartych na systemie Linux](hdinsight-os-patching.md)

- [Jak używać akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm, które umożliwia porównanie luk w zabezpieczeniach w czasie. Jeśli masz subskrypcję usługi Rapid7 lub Qualys, możesz użyć portalu tego dostawcy do wyświetlania i porównywania skanów z oddziałami wstecznymi.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących i odnajdywanie wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym klastrów usługi Azure HDInsight w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem usługi Azure Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania węzłów klastra dla niezatwierdzonych aplikacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących i odnajdywanie wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym klastrów usługi Azure HDInsight w ramach subskrypcji.  Usuń wszystkie niezatwierdzone zasoby platformy Azure, które zostały wykryte. W przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innej firmy w celu usunięcia lub alertu dotyczącego niezatwierdzonego oprogramowania.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: w przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: w przypadku węzłów klastra usługi Azure HDInsight Zaimplementuj rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanych typów plików.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci klastra usługi HDInsight.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: obrazy systemu operacyjnego usługi Azure HDInsight zarządzane i utrzymywane przez firmę Microsoft. Klient odpowiedzialny za implementację bezpiecznych konfiguracji dla systemu operacyjnego węzłów klastra.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla klastrów usługi Azure HDInsight i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: obrazy systemu operacyjnego usługi Azure HDInsight zarządzane i utrzymywane przez firmę Microsoft. Klient odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Samouczek usługi git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos/index)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu utrzymania żądanego stanu dla systemów operacyjnych węzłów klastra.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. HDInsight", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji klastra usługi HDInsight.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania stanu systemów operacyjnych węzłów klastra.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: usługa Azure HDInsight obejmuje obsługę Bring Your Own Key (BYOK) dla Apache Kafka. Ta funkcja umożliwia posiadanie kluczy używanych do szyfrowania danych przechowywanych w usłudze REST i zarządzanie nimi.

Wszystkie dyski zarządzane w usłudze Azure HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Po włączeniu BYOK należy podać klucz szyfrowania dla usługi Azure HDInsight, aby używać go i zarządzać nim przy użyciu Azure Key Vault.

Key Vault mogą być również używane z wdrożeniami usługi Azure HDInsight w celu zarządzania kluczami magazynu klastra (kontami usługi Azure Storage i Azure Data Lake Storage).

- [Jak uzyskać własny klucz dla Apache Kafka w usłudze Azure HDInsight](./disk-encryption.md)

- [Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage](../storage/common/customer-managed-keys-configure-key-vault.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: tożsamości zarządzane mogą być używane w usłudze Azure HDInsight, aby umożliwić Klastrom dostęp do usług domenowych Azure Active Directory (Azure AD), Azure Key Vault dostępu lub dostępu do plików w Azure Data Lake Storage Gen2.

- [Zrozumienie zarządzanych tożsamości za pomocą usługi Azure HDInsight](hdinsight-managed-identities.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Jeśli używasz dowolnego kodu związanego z wdrożeniem usługi Azure HDInsight, możesz zaimplementować skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra, jednak należy zarządzać oprogramowaniem i ręcznie agregować i monitorować wszystkie dzienniki ClamScan.

- [Informacje o ClamScan usługi Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure, ale nie jest uruchamiane w treści klienta.

Przed przeskanowaniem plików przekazywanych do wszystkich zasobów platformy Azure związanych z wdrożeniem klastra usługi Azure HDInsight, takich jak Data Lake Storage, Blob Storage itd. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych wystąpieniach.

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: usługa Azure HDInsight jest dostarczana z ClamScan wstępnie zainstalowanym i włączonym dla obrazów węzłów klastra. Program ClamScan będzie automatycznie wykonywał aktualizacje aparatu i definicji, jednak agregacja i zarządzanie dziennikami należy wykonać ręcznie.

- [Informacje o ClamScan usługi Azure Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych klastra HDInsight wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS).  W przypadku korzystania z Azure SQL Database dla magazynu danych klastra usługi Azure HDInsight Skonfiguruj aktywną replikację geograficzną.

- [Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage](../storage/common/storage-redundancy.md)

- [Jak skonfigurować nadmiarowość dla Azure SQL Database](../azure-sql/database/active-geo-replication-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych klastra usługi Azure HDInsight wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS). W przypadku korzystania z Azure Key Vault dla dowolnej części wdrożenia usługi Azure HDInsight upewnij się, że utworzono kopię zapasową kluczy.

- [Wybieranie opcji magazynu dla klastra usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage](../storage/common/storage-redundancy.md)

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Jeśli Azure Key Vault jest używany z wdrożeniem usługi Azure HDInsight, przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Jak uzyskać własny klucz dla Apache Kafka w usłudze Azure HDInsight](./disk-encryption.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Jeśli Azure Key Vault jest używany z wdrożeniem usługi Azure HDInsight, Włącz nietrwałe usuwanie w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć nietrwałe usuwanie Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)

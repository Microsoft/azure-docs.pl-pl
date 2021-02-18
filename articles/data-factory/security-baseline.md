---
title: Podstawa zabezpieczeń platformy Azure dla Azure Data Factory
description: Podstawa zabezpieczeń platformy Azure dla Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ab4f92c9723dc98d0b6c32d3a49df8d4f724ebfa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585470"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Podstawa zabezpieczeń platformy Azure dla Azure Data Factory

Podstawą zabezpieczeń platformy Azure dla Azure Data Factory są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: podczas tworzenia Azure-SSIS Integration Runtime (IR) można przyłączyć ją do sieci wirtualnej. Dzięki temu Azure Data Factory mogą tworzyć pewne zasoby sieciowe, takie jak sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) i moduł równoważenia obciążenia. Istnieje również możliwość udostępnienia własnego statycznego publicznego adresu IP lub utworzenia Azure Data Factory. Na sieciowej grupy zabezpieczeń, który jest automatycznie tworzony przez Azure Data Factory, Port 3389 jest domyślnie otwarty dla całego ruchu. Zablokuj tę możliwość, aby upewnić się, że tylko Administratorzy mają dostęp.

Self-Hosted urząd skarbowy można wdrożyć na maszynie lokalnej lub na maszynie wirtualnej platformy Azure wewnątrz sieci wirtualnej. Upewnij się, że wdrożenie podsieci sieci wirtualnej ma sieciowej grupy zabezpieczeń skonfigurowany pod kątem zezwalania tylko na dostęp administracyjny. Azure-SSIS IR nie zezwolił na ruch wychodzący portu 3389 domyślnie w regule zapory systemu Windows w każdym węźle IR do ochrony. Możesz zabezpieczyć zasoby skonfigurowane przez sieć wirtualną, kojarząc sieciowej grupy zabezpieczeń z podsiecią i ustawiając rygorystyczne reguły.

W przypadku udostępnienia linku prywatnego Użyj prywatnych punktów końcowych, aby zabezpieczyć wszystkie zasoby połączone z potokiem Azure Data Factory, na przykład Azure SQL Server. Połączenie prywatne polega na tym, że ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

* [Jak utworzyć Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [Jak utworzyć i skonfigurować samoobsługowe środowisko IR](./create-self-hosted-integration-runtime.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

* [Przyłączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dla sieci wirtualnej i sieciowej grupy zabezpieczeń skojarzonej z wdrożeniem Integration Runtime.

Ponadto należy włączyć dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowej grupy zabezpieczeń chroniące wdrożenie Integration Runtime i wysyłać dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu.

Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z wdrożeniem Integration Runtime w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

* [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

* [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowej grupy zabezpieczeń chroniących wdrożenie Integration Runtime i wysyłanie dzienników do konta usługi Azure Storage na potrzeby inspekcji ruchu.

Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Jeśli chcesz przeprowadzić inspekcję ruchu wychodzącego z Azure-SSIS IR, możesz kierować ruchem zainicjowanym z Azure-SSIS IR do lokalnego urządzenia zapory za pośrednictwem platformy Azure ExpressRoutee tunelowanie lub wirtualne urządzenie sieciowe (urządzenie WUS) z portalu Azure Marketplace, które obsługuje możliwości identyfikatorów/adresów IP. Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń.

* [Przyłączanie Azure-SSIS Integration Runtime do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla usługi Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: używanie tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) lub na zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. DataFactoryManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch przychodzący dla odpowiedniej usługi lub odmówić go. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

* [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

* [Opis Azure Data Factory określonych tagów usługi](./join-azure-ssis-integration-runtime-virtual-network.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych i zasobów sieciowych skojarzonych z wystąpieniami usługi Azure Data Factory za pomocą Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. DataFactory" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Data Factory. Mogą również korzystać z wbudowanych definicji zasad związanych z siecią lub wystąpieniami usługi Azure Data Factory, takimi jak:
- Należy włączyć Standard DDoS Protection

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Przykłady Azure Policy dla sieci](../governance/policy/samples/built-in-policies.md#network) 

* [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień Azure Data Factory, aby zapewnić metadane i organizację logiczną.

Użyj dowolnych wbudowanych definicji Azure Policy związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Data Factory. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Data Factory sygnatur czasowych w dziennikach.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Data Factory. W Azure Monitor można wykonywać zapytania dotyczące obszaru roboczego Log Analytics, który jest skonfigurowany do odbierania dzienników aktywności Azure Data Factory. Użyj kont usługi Azure Storage, aby uzyskać długoterminowy/archiwizowany magazyn dzienników lub Centra zdarzeń do eksportowania danych do innych systemów.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub zdarzenia związane z zabezpieczeniami innych firm (SIEM). Możesz także zintegrować Azure Data Factory z usługą git, aby korzystać z kilku korzyści z kontroli źródła, takich jak możliwość śledzenia i inspekcji zmian oraz możliwość przywracania zmian wprowadzających błędy.

* [Jak skonfigurować ustawienia diagnostyczne](../azure-monitor/essentials/diagnostic-settings.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Kontrola źródła w Azure Data Factory](./source-control.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, usługi Azure Event Hubs lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Ustawienia diagnostyczne służą do konfigurowania dzienników diagnostycznych dla zasobów nieobliczeniowych w Azure Data Factory, takich jak metryki i dane uruchamiania potoków. Azure Data Factory przechowuje dane przebiegu potoku przez 45 dni. Aby zachować te dane przez dłuższy czas, Zapisz dzienniki diagnostyczne na koncie magazynu na potrzeby inspekcji lub inspekcji ręcznej i określ czas przechowywania w dniach. Możesz również przesłać strumieniowo dzienniki do usługi Azure Event Hubs lub wysłać dzienniki do obszaru roboczego Log Analytics w celu przeprowadzenia analizy.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

* [Informacje o dziennikach diagnostycznych Azure Data Factory](./monitor-using-azure-monitor.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz użyć Azure monitor do zbierania danych z maszyny wirtualnej. Zainstalowanie rozszerzenia maszyny wirtualnej Log Analytics umożliwia Azure Monitor zbieranie danych z maszyn wirtualnych platformy Azure. Azure Security Center może następnie zapewnić monitorowanie dzienników zdarzeń zabezpieczeń dla Virtual Machines. Dane dotyczące ilości danych generowanych przez dziennik zdarzeń zabezpieczeń nie są domyślnie przechowywane.

Jeśli organizacja ma zachować dane dziennika zdarzeń zabezpieczeń, może być przechowywana w ramach warstwy zbierania danych. w tym momencie można wykonać zapytanie w Log Analytics.

* [Jak zbierać dane z usługi Azure Virtual Machines w programie Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

* [Włączanie zbierania danych w Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Data Factory. W przypadku wybrania opcji przechowywania dzienników w obszarze roboczym Log Analytics Ustaw okres przechowywania Log Analytics obszaru roboczego zgodnie z przepisami obowiązującymi w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

* [Jak włączyć dzienniki diagnostyczne w Azure Data Factory](./monitor-using-azure-monitor.md)

* [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Data Factory i wysyłanie dzienników do log Analytics obszaru roboczego. Użyj Log Analytics, aby analizować i monitorować dzienniki pod kątem nietypowego zachowania i regularnie przeglądać wyniki. Upewnij się, że ustawienia diagnostyczne są również włączone dla wszystkich magazynów danych związanych z wdrożeniami Azure Data Factory. Aby uzyskać wskazówki dotyczące włączania ustawień diagnostycznych, zapoznaj się z podstawą zabezpieczeń każdej usługi.

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Włącz również ustawienia diagnostyczne dla maszyny wirtualnej.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Schemat Log Analytics](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Jak zbierać dane z maszyny wirtualnej platformy Azure za pomocą Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: można zgłaszać alerty dla obsługiwanych metryk w Data Factory, przechodząc do &amp; sekcji metryki alertów w programie Azure monitor.

Skonfiguruj ustawienia diagnostyczne dla Azure Data Factory i Wyślij dzienniki do Log Analytics obszaru roboczego. W obszarze roboczym Log Analytics Skonfiguruj alerty, które mają być wykonywane po wstępnym zdefiniowanym zestawie warunków. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Ponadto upewnij się, że ustawienia diagnostyczne są włączone dla usług związanych z magazynami danych. Aby uzyskać wskazówki, można odwołać się do linii bazowej zabezpieczeń każdej usługi.

* [Alerty w Azure Data Factory](./monitor-visually.md#alerts)

* [Wszystkie obsługiwane strony metryk](../azure-monitor/essentials/metrics-supported.md)

* [Jak skonfigurować alerty w obszarze roboczym Log Analytics](../azure-monitor/alerts/alerts-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz użyć programu Microsoft chroniącego przed złośliwym kodem dla platformy azure Cloud Services Virtual Machines i skonfigurować maszyny wirtualne do rejestrowania zdarzeń na koncie usługi Azure Storage. Skonfiguruj obszar roboczy Log Analytics, aby pozyskiwać zdarzenia z kont magazynu, a następnie utwórz alerty, jeśli są odpowiednie. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps".

* [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

* [Jak włączyć monitorowanie na poziomie gościa dla Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Data Factory nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz włączyć rejestrowanie inspekcji w wierszu polecenia. Azure Security Center zapewnia monitorowanie dzienników zdarzeń zabezpieczeń dla maszyn wirtualnych platformy Azure. Security Center udostępnia Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia automatycznej aprowizacji lub można zainstalować agenta ręcznie. Agent włącza zdarzenie tworzenia procesu 4688 i pole wiersza polecenia wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center.

* [Zbieranie danych w usłudze Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: w ramach Azure Data Factory upewnij się, że regularnie śledzisz i uzgodnisz dostęp użytkowników. Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.

Ponadto na poziomie dzierżawy Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych, które mają dostęp administracyjny do płaszczyzny kontroli wystąpień Azure Data Factory.

Chociaż usługa Azure AD jest zalecaną metodą administrowania dostępem użytkowników, należy pamiętać, że w przypadku uruchamiania Integration Runtime na maszynie wirtualnej platformy Azure (VM), maszyna wirtualna może również mieć konta lokalne. Zarówno konta lokalne, jak i domeny powinny być przeglądane i zarządzane, zwykle z minimalnym wpływem. Ponadto firma Microsoft zaleca, aby program Privileged Identity Manager został sprawdzony pod kątem funkcji just in Time, aby zmniejszyć dostępność uprawnień administracyjnych.

* [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](./concepts-roles-permissions.md)

* [Informacje o programie Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

* [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Informacje dotyczące kont lokalnych](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Data Factory używa Azure Active Directory (AD) w celu zapewnienia dostępu do Azure Portal oraz konsoli Azure Data Factory. Usługa Azure AD nie ma koncepcji domyślnych haseł, ale użytkownik jest odpowiedzialny za zmianę lub niezezwalanie na domyślne hasła dla wszystkich aplikacji niestandardowych lub innych firm.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych na potrzeby dostępu do płaszczyzny kontroli systemu Azure (Azure Portal) oraz konsoli Azure Data Factory. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych w usłudze Azure AD.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, konta administratorów na platformie Azure Virtual Machines można skonfigurować przy użyciu programu Azure Privileged Identity Manager (PIM). Usługa Azure Privileged Identity Manager udostępnia kilka opcji, takich jak ustawienia podniesienia uprawnień w czasie, Multi-Factor Authentication i delegowania, dzięki czemu uprawnienia są dostępne tylko dla określonych ram czasowych i wymagają od drugiej osoby zatwierdzenia.

* [Informacje o tożsamości i dostępie Azure Security Center](../security-center/security-center-identity-access.md)

* [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Informacje o programie Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](./concepts-roles-permissions.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj rejestracji aplikacji platformy Azure (nazwy głównej usługi), aby pobrać token, którego aplikacja lub funkcja może używać w celu uzyskiwania dostępu do magazynów Recovery Services i korzystania z nich.

* [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Jak zarejestrować aplikację kliencką (nazwę główną usługi) w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informacje o interfejsie API usługi Azure Recovery Services](/rest/api/recoveryservices/)

* [Informacje na temat interfejsu API REST dla Azure Data Factory](/rest/api/datafactory/)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włączanie Azure Active Directory Multi-Factor Authentication (MFA) i przestrzeganie Azure Security Center zaleceń dotyczących zarządzania tożsamościami i dostępem.

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: używanie raportów zabezpieczeń Azure Active Directory do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz również przenieść maszynę wirtualną na platformę Azure. Microsoft Azure wskaźnikiem wydajności jest skalowalne i natywne rozwiązanie do zarządzania zdarzeniami zabezpieczeń (SIEM) i o (Security Orchestration). Platforma Azure — wskaźnik przedstawia inteligentne rozwiązania w zakresie zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia.

* [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

* [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Fabryka danych może być skojarzona z zarządzaną tożsamością dla zasobów platformy Azure, które reprezentują określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do uwierzytelniania Azure SQL Database. Wydaną fabrykę mogą uzyskać dostęp do danych z lub do bazy danych lub z niej kopiować przy użyciu tej tożsamości.

Jeśli używasz Integration Runtime (IR) na maszynie wirtualnej platformy Azure, możesz użyć tożsamości zarządzanych do uwierzytelniania w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie. Kod, który jest uruchomiony na maszynie wirtualnej, może używać tożsamości zarządzanej do żądania tokenów dostępu dla usług, które obsługują uwierzytelnianie usługi Azure AD.

* [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Jakie są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

* [Kopiowanie i Przekształcanie danych w Azure SQL Database przy użyciu Azure Data Factory](./connector-azure-sql-database.md)

* [Jak skonfigurować uwierzytelnianie Azure Active Directory i zarządzać nim za pomocą Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Jeśli używasz integracji środowiska uruchomieniowego na maszynie wirtualnej platformy Azure, musisz przejrzeć lokalne grupy zabezpieczeń i użytkowników, aby upewnić się, że nie ma żadnych nieoczekiwanych kont, które mogłyby naruszyć bezpieczeństwo systemu.

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

* [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do Azure Active Directory (AD), inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitorowania. Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure (VM), na płycie maszyny wirtualnej na platformie Azure. Microsoft Azure wskaźnikiem wydajności jest skalowalne i natywne rozwiązanie do zarządzania zdarzeniami zabezpieczeń (SIEM) i o (Security Orchestration). Platforma Azure — wskaźnik przedstawia inteligentne rozwiązania w zakresie zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia.

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](../event-hubs/authorize-access-azure-active-directory.md)

* [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów Azure Data Factory, takich Azure SQL Database lub Virtual Machines platformy Azure. W przypadku pominięcia zachowania logowania do konta na płaszczyźnie kontroli (Azure Portal) Użyj funkcji Azure AD Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

* [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Enable Azure Active Directory authentication for Azure-SSIS Integration Runtime (Włączanie uwierzytelniania usługi Azure Active Directory dla środowiska Azure-SSIS Integration Runtime)](./enable-aad-authentication-azure-ssis-ir.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, usługa Azure skrytka klienta udostępnia interfejs umożliwiający klientom przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Pamiętaj, że gdy skrytka platformy Azure jest niedostępna dla Azure Data Factory samej, skrytka platformy Azure obsługuje Azure SQL Database i Virtual Machines platformy Azure.

* [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Użyj funkcji odnajdywania i klasyfikowania danych Azure SQL Database. Funkcja odnajdywania i klasyfikowania danych zapewnia zaawansowane funkcje wbudowane w Azure SQL Database na potrzeby odnajdywania, klasyfikowania i etykietowania &amp; ochrony poufnych danych w bazach danych.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak używać funkcji odnajdywania i klasyfikowania danych dla platformy Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Środowisko Integration Runtime powinno być rozdzielone przez sieć wirtualną (VNet)/Subnet i odpowiednio oznakowane.

 Do przeprowadzenia izolacji sieci można także użyć prywatnych punktów końcowych. Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Opis linku prywatnego](../private-link/private-endpoint-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku źródeł danych (takich jak Azure SQL Database) przechowywanie lub przetwarzanie poufnych informacji dla wdrożenia Azure Data Factory, Oznacz powiązane zasoby jako poufne przy użyciu tagów.

W przypadku udostępnienia linku prywatnego Użyj prywatnych punktów końcowych, aby zabezpieczyć wszystkie zasoby połączone z potokiem Azure Data Factory. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Istnieje również możliwość zmniejszenia ryzyka związanego z eksfiltracji danych przez skonfigurowanie ścisłego zestawu reguł ruchu wychodzącego w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) i skojarzenie tego sieciowej grupy zabezpieczeń z podsiecią.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

* [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Jeśli magazyn danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie transfery danych między usługami przenoszenia danych w Data Factory i magazynem danych w chmurze są realizowane za pośrednictwem protokołu HTTPS lub TLS bezpiecznego kanału. Używana wersja protokołu TLS to 1,2.

Wszystkie połączenia do Azure SQL Database i Azure Synapse Analytics wymagają szyfrowania (SSL/TLS), gdy dane są przesyłane do i z bazy danych. Podczas tworzenia potoku przy użyciu formatu JSON należy dodać właściwość szyfrowania i ustawić jej wartość na true w parametrach połączenia. W przypadku usługi Azure Storage można użyć protokołu HTTPS w parametrach połączenia.

* [Informacje o szyfrowaniu podczas przesyłania Azure Data Factory](./data-movement-security-considerations.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: jeśli używasz Azure Data Factory do kopiowania i przekształcania wystąpień Azure SQL Database, użyj funkcji odnajdywania i klasyfikowania danych Azure SQL Database. Funkcja odnajdywania i klasyfikowania danych zapewnia zaawansowane funkcje wbudowane w Azure SQL Database na potrzeby odnajdywania, klasyfikowania i etykietowania &amp; ochrony poufnych danych w bazach danych.

Funkcje odnajdywania i klasyfikowania danych nie są jeszcze dostępne dla innych usług platformy Azure.

* [Jak używać funkcji odnajdywania i klasyfikowania danych dla platformy Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli Azure Data Factory (Azure Portal).

Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.

W przypadku Data Factory źródeł danych, takich jak Azure SQL Database, zapoznaj się z linią bazową zabezpieczeń dla tej usługi, aby uzyskać więcej informacji na temat kontroli RBAC platformy Azure.

* [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](./concepts-roles-permissions.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: zalecamy włączenie mechanizmu szyfrowania danych dla wszelkich magazynów danych związanych z wdrożeniami Azure Data Factory. Aby uzyskać więcej informacji na temat szyfrowania przechowywanych danych, można odwołać się do linii bazowej zabezpieczeń dla tej usługi.

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, dyski wirtualne na Windows Virtual Machines (VM) są szyfrowane w spoczynku przy użyciu szyfrowania po stronie serwera lub Azure Disk Encryption (ADE). Azure Disk Encryption wykorzystuje funkcję BitLocker systemu Windows do szyfrowania dysków zarządzanych z kluczami zarządzanymi przez klienta w ramach maszyny wirtualnej gościa. Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.

Poświadczenia lub wartości tajne można przechowywać w Azure Key Vault i używać ich podczas wykonywania potoku w celu przekazania do działań. Możesz również przechowywać poświadczenia dla magazynów danych i obliczeń w Azure Key Vault. Azure Data Factory Pobiera poświadczenia podczas wykonywania działania, które korzysta z magazynu/obliczeń danych.

* [Informacje o szyfrowaniu w Azure Data Factory](./data-movement-security-considerations.md)

* [Szyfrowanie po stronie serwera dla usługi Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Jak używać Azure Key Vault wpisów tajnych w działaniach potoku](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Jak poświadczenia w Azure Key Vault](./store-credentials-in-key-vault.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące wprowadzania zmian do Azure Data Factory i powiązanych zasobów.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

* [Rejestrowanie usługi Azure Storage Analytics](../storage/common/storage-analytics-logging.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: jeśli używasz Azure SQL Database jako magazynu danych, Włącz zaawansowane zabezpieczenia danych dla Azure SQL Database i postępuj zgodnie z zaleceniami z Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na serwerach usługi Azure SQL.

Jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, postępuj zgodnie z zaleceniami z Azure Security Center, aby przeprowadzać oceny luk w zabezpieczeniach na maszynach wirtualnych. Skorzystaj z rozwiązań zabezpieczeń platformy Azure lub rozwiązania innych firm, aby przeprowadzać oceny luk w zabezpieczeniach dla maszyn wirtualnych.

* [Jak uruchomić oceny luk w zabezpieczeniach na Azure SQL Database](../azure-sql/database/sql-vulnerability-assessment.md)

* [Jak włączyć zaawansowane zabezpieczenia danych](../azure-sql/database/azure-defender-for-sql.md)

* [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, użyj rozwiązania Azure Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych. Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Narzędzia, takie jak System Center Updates Publisher (aktualizacje wydawcy), umożliwiają publikowanie aktualizacji niestandardowych w programie Windows Server Update Services (WSUS). Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Update Management rozwiązanie na platformie Azure](../automation/update-management/overview.md)

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](../automation/update-management/manage-updates-for-vm.md)

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz użyć rozwiązania do zarządzania poprawkami innej firmy. Możesz użyć rozwiązania Update Management platformy Azure do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych. Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Narzędzia, takie jak System Center Updates Publisher (aktualizacje wydawcy), umożliwiają publikowanie aktualizacji niestandardowych w programie Windows Server Update Services (WSUS). Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

* [Update Management rozwiązanie na platformie Azure](../automation/update-management/overview.md)

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](../automation/update-management/manage-updates-for-vm.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z rekomendacji dotyczących zarządzania lukami zalecaną przez Azure Security Center można przestawiać w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

* [Opis skanera zintegrowanej luki w zabezpieczeniach dla maszyn wirtualnych](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz użyć skanera natywnej luki w zabezpieczeniach. Skaner luk w zabezpieczeniach dołączony do Azure Security Center jest obsługiwany przez Qualys. Skaner Qualys to wiodące narzędzie do identyfikacji luk w zabezpieczeniach w czasie rzeczywistym w usłudze Azure Virtual Machines.

Gdy Security Center identyfikuje luki w zabezpieczeniach, przedstawia wyniki i powiązane informacje jako zalecenia. Powiązane informacje obejmują czynności zaradcze, powiązane CVEs, wyniki CVSS i inne. Można wyświetlić zidentyfikowane luki w zabezpieczeniach dla jednej lub większej liczby subskrypcji lub dla określonej maszyny wirtualnej.

* [Zintegrowany skaner luk w zabezpieczeniach dla maszyn wirtualnych](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

* [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Definiowanie zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Skorzystaj z spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na Virtual Machines. Usługa Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidacji obciążeń i zasobów.

Uwaga: nazwa oprogramowania, wersja, Wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać dostęp do daty instalacji i innych informacji, klient musi włączyć diagnostykę na poziomie gościa i przenieść dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

* [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

* [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Azure Automation zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów. Change Tracking można użyć do zidentyfikowania wszystkich programów zainstalowanych w programie Virtual Machines. Aby usunąć nieautoryzowane oprogramowanie, można zaimplementować własny proces lub użyć konfiguracji stanu Azure Automation.

* [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md)

* [Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking](../automation/change-tracking/overview.md)

* [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, użyj Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że wykonywane jest tylko autoryzowane oprogramowanie i że wszystkie nieautoryzowane oprogramowanie jest blokowane na maszynach wirtualnych.

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/index.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: adaptacyjne Sterowanie aplikacjami to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z platformą Azure i poza platformą Azure (Windows i Linux). Zaimplementuj rozwiązanie innych firm, jeśli nie spełni wymagania organizacji.

Należy zauważyć, że dotyczy to tylko sytuacji, gdy Integration Runtime jest uruchomiona na maszynie wirtualnej platformy Azure.

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: Jeśli używasz integracji środowiska uruchomieniowego na maszynie wirtualnej platformy Azure, w zależności od typu skryptów, możesz użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure. Możesz również wykorzystać Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

* [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: aplikacje o wysokim ryzyku wdrożone w środowisku platformy Azure mogą być izolowane przy użyciu sieci wirtualnej, podsieci, subskrypcji, grup zarządzania itp. i dostatecznie zabezpieczone za pomocą zapory platformy Azure, zapory aplikacji sieci Web (WAF) lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

* [Sieci wirtualne i maszyny wirtualne na platformie Azure](../virtual-machines/network-overview.md)

* [Co to jest usługa Azure Firewall?](../firewall/overview.md)

* [Co to jest usługa Azure Web Application Firewall?](../web-application-firewall/overview.md)

* [Sieciowe grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md)

* [Co to jest platforma Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)

* [Przewodnik po decyzjach związanych z subskrypcjami](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla Azure Data Factory z Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DataFactory", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Data Factory.

* [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Jeśli używasz integracji środowiska uruchomieniowego na maszynie wirtualnej platformy Azure, użyj zalecenia Azure Security Center [Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń na Virtual Machines], aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.

* [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)

* [Jak skorygować Azure Security Center zalecenia](../security-center/security-center-remediate-recommendations.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

* [Informacje na temat tworzenia szablonów Azure Resource Manager](../virtual-machines/windows/ps-template.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, pamiętaj, że istnieje kilka opcji utrzymywania bezpiecznej konfiguracji dla maszyn wirtualnych na potrzeby wdrożenia:
- Szablony Azure Resource Manager: są to pliki w formacie JSON używane do wdrożenia maszyny wirtualnej z Azure Portal, a szablon niestandardowy musi być obsługiwany. Firma Microsoft przeprowadza konserwację szablonów podstawowych.
- Niestandardowy wirtualny dysk twardy (VHD): w niektórych przypadkach może być wymagane posiadanie niestandardowych plików VHD, takich jak w przypadku złożonych środowisk, które nie mogą być zarządzane za pomocą innych metod. -Azure Automation konfiguracja stanu: po wdrożeniu podstawowego systemu operacyjnego może to służyć do bardziej szczegółowego kontrolowania ustawień i wymuszania przez strukturę automatyzacji.

W przypadku większości scenariuszy szablony maszyn wirtualnych firmy Microsoft połączone z konfiguracją żądanego stanu Azure Automation mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń.

* [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informacje na temat tworzenia szablonów Azure Resource Manager](../virtual-machines/windows/ps-template.md)

* [Jak przekazać niestandardowy wirtualny dysk twardy maszyny wirtualnej na platformę Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

* [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Dokumentacja Azure Repos](/azure/devops/repos/index)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz obrazów niestandardowych, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Skorzystaj z usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

Rola współautor Data Factory może służyć do tworzenia fabryk danych i zarządzania nimi, a także w ramach tych zasobów podrzędnych.

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Informacje na temat usługi Azure RBAC dla Container Registry](../container-registry/container-registry-roles.md)

* [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](./concepts-roles-permissions.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. DataFactory", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: to zalecenie można zastosować, jeśli Integration Runtime jest uruchomiona na maszynie wirtualnej platformy Azure. Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](../automation/automation-dsc-onboarding.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. DataFactory", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów platformy Azure.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: to zalecenie można zastosować, jeśli Integration Runtime jest uruchomiona na maszynie wirtualnej platformy Azure. Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

* [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](../automation/automation-dsc-onboarding.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

Możesz również przechowywać poświadczenia lub wartości tajne w Azure Key Vault i używać ich podczas wykonywania potoku, aby przejść do działań. Upewnij się, że funkcja usuwania nietrwałego jest włączona.

* [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Jak utworzyć Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

* [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

* [Używanie wpisów tajnych usługi Azure Key Vault w działaniach potoku](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Usuwanie nietrwałe w Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: podczas tworzenia fabryki danych można utworzyć zarządzaną tożsamość wraz z tworzeniem fabryczną. Zarządzana tożsamość to zarządzana aplikacja zarejestrowana do Azure Active Directory i reprezentuje tę konkretną fabrykę danych.

* [Tożsamość zarządzana dla Azure Data Factory](./data-factory-service-identity.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, możesz użyć usługi Microsoft chroniącej przed złośliwym kodem dla platformy Azure Windows Virtual Machines do ciągłego monitorowania i obrony zasobów.

* [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure App Service), ale nie jest ono uruchamiane w Twojej zawartości.

Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd.

Użyj wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu.

* [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

* [Zrozumienie wykrywania zagrożeń Azure Security Center dla usług danych](../security-center/azure-defender.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: po wdrożeniu usługa Microsoft chroniąca przed złośliwym kodem na platformie Azure automatycznie zainstaluje najnowsze aktualizacje sygnatur, platform i aparatu. Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. System operacyjny Windows może być dodatkowo chroniony przy użyciu dodatkowych zabezpieczeń w celu ograniczenia ryzyka ataków na ataki przez wirusy lub złośliwe oprogramowanie w usłudze Microsoft Defender Advanced Threat Protection, która integruje się z Azure Security Center.

* [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

* [Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Włącz Azure Backup i skonfiguruj maszynę wirtualną, a także żądaną częstotliwość i okres przechowywania dla automatycznych kopii zapasowych.

W przypadku wszystkich magazynów danych zapoznaj się z planem zabezpieczeń tej usługi, aby zapoznać się z zaleceniami dotyczącymi wykonywania zwykłych, zautomatyzowanych kopii zapasowych.

* [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

* [Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z ustawień maszyny wirtualnej](../backup/backup-azure-vms-first-look-arm.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, Włącz Azure Backup i docelowe maszyny wirtualne platformy Azure, a także żądaną częstotliwość i okres przechowywania. Utwórz kopię zapasową kluczy zarządzanych przez klienta w ramach Azure Key Vault.

W przypadku wszystkich magazynów danych zapoznaj się z planem zabezpieczeń tej usługi, aby zapoznać się z zaleceniami dotyczącymi wykonywania zwykłych, zautomatyzowanych kopii zapasowych.

* [Omówienie kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-introduction.md)

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure, upewnij się, że możliwość okresowego wykonywania przywracania danych w programie Azure Backup. W razie potrzeby przetestuj odzyskiwanie zawartości do izolowanej sieci VLAN. Okresowe testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

W przypadku wszystkich magazynów danych zapoznaj się z planem zabezpieczeń tej usługi, aby uzyskać wskazówki dotyczące sprawdzania poprawności kopii zapasowych.

* [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

* [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: jeśli używasz Integration Runtime na maszynie wirtualnej platformy Azure i wrócisz do niej za pomocą Azure Backup, maszyna wirtualna będzie szyfrowana przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup może również tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption. Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEKs), które są chronione w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania klucza Azure Key Vault (KEKs). Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

* [Usuwanie nietrwałe dla maszyn wirtualnych](../backup/backup-azure-security-feature-cloud.md)

* [Azure Key Vault — omówienie usuwania nietrwałego](../key-vault/general/soft-delete-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

* [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

* [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

* [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: 

* [Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
---
title: Podstawa zabezpieczeń platformy Azure dla Azure Databricks
description: Podstawa zabezpieczeń platformy Azure dla Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681696"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Podstawa zabezpieczeń platformy Azure dla Azure Databricks

Podstawą zabezpieczeń platformy Azure dla Azure Databricks są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: wdrażanie Azure Databricks w własnej sieci wirtualnej platformy Azure. Domyślne wdrożenie Azure Databricks to w pełni zarządzana usługa na platformie Azure: wszystkie zasoby płaszczyzny danych, w tym sieć wirtualna, do której zostaną skojarzone wszystkie klastry, zostaną wdrożone w zablokowanej grupie zasobów. W przypadku konieczności dostosowania sieci można jednak wdrożyć Azure Databricks zasoby płaszczyzny danych we własnej sieci wirtualnej (wtrysk sieci VNet), co umożliwi implementację niestandardowych konfiguracji sieciowych. Możesz zastosować własną grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) z regułami niestandardowymi do określonych ograniczeń ruchu wychodzącego.

Ponadto można skonfigurować reguły sieciowej grupy zabezpieczeń, aby określić ograniczenia ruchu wychodzącego w podsieci, w której wdrożono wystąpienie Azure Databricks. Zaporę platformy Azure można skonfigurować pod kątem obszarów roboczych z wstrzykiwanymi sieciami WIRTUALNYmi.

* [Jak wdrożyć Azure Databricks we własnym Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak zarządzać usługą sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: wdrażanie Azure Databricks w własnej sieci wirtualnej platformy Azure. Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) nie jest tworzona automatycznie. Wymagane jest utworzenie planu bazowego sieciowej grupy zabezpieczeń tylko z domyślnymi regułami platformy Azure. Podczas wdrażania obszaru roboczego są dodawane reguły wymagane przez kostki danych. Możesz również rozpocząć od pustej sieciowej grupy zabezpieczeń, a odpowiednie reguły zostaną dodane automatycznie. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływów do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

* [Jak wdrożyć Azure Databricks we własnym Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Jak włączyć i używać Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Jak włączyć Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu Azure DDoS Protection w sieciach wirtualnych platformy Azure skojarzonych z wystąpieniami Azure Databricks w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi publicznymi adresami IP.

* [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Informacje o ochronie przed zagrożeniami dla warstwy sieci platformy Azure w Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: wdrażanie Azure Databricks w własnej sieci wirtualnej platformy Azure. Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) nie jest tworzona automatycznie. Wymagane jest utworzenie planu bazowego sieciowej grupy zabezpieczeń tylko z domyślnymi regułami platformy Azure. Podczas wdrażania obszaru roboczego są dodawane reguły wymagane przez kostki danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysyłać dzienniki przepływów do obszaru roboczego Log Analytics i używać Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

* [Jak wdrożyć Azure Databricks we własnym Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Jak włączyć i używać Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Jak włączyć Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: wdrażanie wirtualnego urządzenia sieciowego z obsługą dostawcy tożsamości/adresów IP (urządzenie WUS) z portalu Azure Marketplace w celu utworzenia obszaru roboczego zintegrowanego z siecią wirtualną, w którym wszystkie klastry Azure Databricks mają pojedynczy adres IP dla ruchu wychodzącego. Pojedynczy adres IP może służyć jako dodatkowa warstwa zabezpieczeń z innymi usługami i aplikacjami platformy Azure, które umożliwiają dostęp na podstawie określonych adresów IP. Do zarządzania ruchem ruchu przychodzącego i wychodzącego można użyć zapory platformy Azure lub innych narzędzi innych firm, takich jak urządzenie WUS.

Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą ze źródła analizy zagrożeń firmy Microsoft.

* [How to Assign a Single Public IP for VNet-Injected Workspaces Using Azure Firewall (W jaki sposób przypisać pojedynczy publiczny adres IP dla obszarów roboczych wprowadzonych w sieci wirtualnej przy użyciu usługi Azure Firewall)](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Jak utworzyć urządzenie WUS](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń, które są dołączone do podsieci, z którymi jest skojarzone wystąpienie Azure Databricks. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. Tagi usług nie są obsługiwane w przypadku niewstrzykiwanych obszarów roboczych sieci wirtualnej.

* [Zrozumienie tagów usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla wystąpień Azure Databricks przy użyciu Azure Policy. Możesz użyć aliasów Azure Policy w przestrzeni nazw "Microsoft. datakosteks", aby zdefiniować niestandardowe definicje zasad. Zasady nie będą miały zastosowania do zasobów w ramach zarządzanej grupy zasobów.

Możesz również użyć planów platformy Azure, aby uprościć duże wdrożenia platformy Azure przez pakowanie najważniejszych artefaktów środowiska, takich jak szablony zarządzania zasobami platformy Azure, kontrola dostępu oparta na rolach (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Omówienie aliasów Azure Policy i struktury definicji](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Jak utworzyć Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z wystąpieniem Azure Databricks. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Databricks. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Jak utworzyć alerty w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, ale istnieje możliwość zarządzania ustawieniami synchronizacji czasu dla zasobów obliczeniowych. Ponieważ Azure Databricks jest usługą PaaS, nie masz bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks i nie można skonfigurować ustawień synchronizacji czasu.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Databricks. W Azure Monitor można wykonywać zapytania dotyczące obszaru roboczego Log Analytics, który jest skonfigurowany do odbierania danych i dzienników diagnostycznych. Użyj kont usługi Azure Storage, aby uzyskać długoterminowy/archiwizowany magazyn dzienników lub Centra zdarzeń do eksportowania danych do innych systemów. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM).

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium

* [Jak skonfigurować ustawienia diagnostyczne](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

W przypadku rejestrowania inspekcji Azure Databricks zapewnia kompleksowe dzienniki diagnostyczne dla działań wykonywanych przez Azure Databricks użytkowników, co pozwala przedsiębiorstwom monitorować szczegółowe Azure Databricks wzorców użycia.

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy))

* [Jak włączyć ustawienia diagnostyczne dla Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Azure Databricks zapewniają kompleksowe dzienniki diagnostyczne działań wykonywanych przez Azure Databricks użytkowników, co pozwala przedsiębiorstwom monitorować szczegółowe Azure Databricks wzorców użytkowania.

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium. Rejestrowanie zabezpieczeń systemu operacyjnego jest niedostępne.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Jak włączyć ustawienia diagnostyczne dla Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Databricks. W przypadku wybrania opcji przechowywania dzienników w obszarze roboczym Log Analytics Ustaw okres przechowywania Log Analytics obszaru roboczego zgodnie z przepisami obowiązującymi w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania. Działania związane z zabezpieczeniami są śledzone w dziennikach inspekcji datakostki.

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium

* [Jak włączyć ustawienia diagnostyczne w Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Databricks i wysyłanie dzienników do log Analytics obszaru roboczego. Użyj obszaru roboczego Log Analytics, aby analizować i monitorować dzienniki Azure Databricks pod kątem nietypowego zachowania i regularnie przeglądać wyniki.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium

* [Jak włączyć ustawienia diagnostyczne w Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Jak wykonywać zapytania dotyczące dzienników Azure Databricks wysyłanych do Log Analytics obszaru roboczego](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: Konfigurowanie ustawień diagnostycznych dla wystąpienia Azure Databricks i wysyłanie dzienników do log Analytics obszaru roboczego. W obszarze roboczym Log Analytics Skonfiguruj alerty, które mają być wykonywane po wstępnym zdefiniowanym zestawie warunków.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Uwaga: dzienniki diagnostyczne Azure Databricks wymagają planu Azure Databricks w warstwie Premium

* [Jak wysłać dzienniki Azure Databricks do log Analytics obszaru roboczego](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Jak skonfigurować alerty w obszarze roboczym Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Databricks nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Databricks interfejsów API Standard scim można użyć do zarządzania użytkownikami w Azure Databricks obszarze roboczym i udzielania uprawnień administracyjnych Wyznaczeni użytkownikom. Można także zarządzać nimi za pomocą interfejsu użytkownika Azure Databricks.

* [Jak używać interfejsów API Standard scim](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Jak dodawać i usuwać użytkowników w Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Databricks używa Azure Active Directory (AD) w celu zapewnienia dostępu do Azure Portal oraz konsoli administracyjnej Azure Databricks. Usługa Azure AD nie ma koncepcji domyślnych haseł, ale użytkownik jest odpowiedzialny za zmianę lub niezezwalanie na domyślne hasła dla wszystkich aplikacji niestandardowych lub innych firm.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: można użyć Azure Databricks interfejsów API Standard scim, aby dodać użytkowników z uprawnieniami administratora w Azure Databricks. Można także zarządzać nimi za pomocą interfejsu użytkownika Azure Databricks.

* [Jak używać interfejsów API Standard scim](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Jak dodawać i usuwać użytkowników w Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Azure Databricks jest automatycznie skonfigurowana do uwierzytelniania użytkowników za pomocą logowania jednokrotnego Azure Active Directory. Użytkownicy spoza organizacji muszą zakończyć proces zaproszenia i zostać dodany do dzierżawy Active Directory, zanim będą mogli zalogować się do Azure Databricks za pomocą logowania jednokrotnego. Można zaimplementować Standard scim w celu zautomatyzowania aprowizacji i nieaprowizacji użytkowników z obszarów roboczych.

* [Jak używać interfejsów API Standard scim](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Zapoznaj się z logowaniem jednokrotnym dla Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Jak zapraszać użytkowników do dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory.

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń Azure Active Directory w celu generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem. Ponadto można korzystać z dzienników diagnostycznych Azure Databricks.

* [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Azure Databricks jest automatycznie skonfigurowana do uwierzytelniania użytkowników za pomocą logowania jednokrotnego Azure Active Directory. Użytkownicy spoza organizacji muszą zakończyć proces zaproszenia i zostać dodany do dzierżawy Active Directory, zanim będą mogli zalogować się do Azure Databricks za pomocą logowania jednokrotnego.

* [Zapoznaj się z logowaniem jednokrotnym dla Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Jak zapraszać użytkowników do dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. Możesz również zaimplementować interfejsy API Standard scim i dzienniki diagnostyczne Azure Databricks, aby przejrzeć dostęp użytkowników. Możesz również użyć interfejsów API Standard scim i dzienników diagnostycznych Azure Databricks, aby przejrzeć dostęp użytkowników.

Ponadto należy regularnie przeglądać i zarządzać dostępem użytkowników w konsoli administracyjnej Azure Databricks.

* [Raportowanie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Jak zarządzać dostępem użytkowników w konsoli administracyjnej Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitorowania. Ponadto można użyć dzienników diagnostycznych Azure Databricks, aby przejrzeć aktywność dostępu użytkowników.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

* [Jak używać interfejsów API Standard scim](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ryzyka i ochrony tożsamości usługi Azure AD, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy. Ponadto można użyć dzienników diagnostycznych Azure Databricks, aby przejrzeć aktywność dostępu użytkowników.

* [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: gdy bilety pomocy technicznej są otwarte, inżynierowie usług i pomocy technicznej będą proszeni o zgodę na dostęp do odpowiednich danych klienta.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: obecnie niedostępna

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu wystąpień Azure Databricks, które przetwarzają poufne informacje.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Domyślne wdrożenie Azure Databricks to w pełni zarządzana usługa, która jest wdrażana w ramach własnej sieci wirtualnej. W przypadku konieczności dostosowania sieci można wdrożyć Azure Databricks w własnej sieci wirtualnej. Najlepszym rozwiązaniem jest utworzenie oddzielnych obszarów roboczych Azure Databricks dla różnych zespołów lub działów firmy.

* [Jak wdrożyć Azure Databricks we własnym Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Jak utworzyć Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych.

**Wskazówki**: przestrzeganie architektury ochrony eksfiltracji (datakostki) w celu ograniczenia możliwości eksfiltracji danych.

* [Ochrona danych Eksfiltracji za pomocą Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Databricks i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: obecnie niedostępna

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Firma Microsoft domyślnie NEGOCJUJE protokół TLS 1,2 podczas administrowania wystąpieniem Azure Databricks za pomocą konsoli Azure Portal lub Azure Databricks. Aplikacja sieci Web datakosteks obsługuje protokół TLS 1,3.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla Azure Databricks. Tagi Azure Databricks wystąpienia i powiązane zasoby, które mogą przetwarzać informacje poufne jako takie i wdrażać rozwiązania innych firm, jeśli są wymagane do celów zgodności.

Platforma datacegłs jest tylko do obliczeń, a wszystkie dane są przechowywane w innych usługach danych platformy Azure. W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: obecnie niedostępna

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: w Azure Databricks można używać list kontroli dostępu (ACL) do konfigurowania uprawnień dostępu do tabel danych, klastrów, pul, zadań i obiektów obszaru roboczego, takich jak notesy, eksperymenty i foldery. Wszyscy użytkownicy administracyjni mogą zarządzać listami kontroli dostępu, ponieważ mogą oni mieć uprawnienia delegowane do zarządzania listami kontroli dostępu. Za pomocą funkcji RBAC platformy Azure można ustawić uprawnienia w obszarze roboczym Azure Databricks.

Uwaga: Kontrola dostępu tabela, klaster, Pula, zadanie i obszar roboczy są dostępne tylko w planie Azure Databricks w warstwie Premium

* [Jak zarządzać kontrolą dostępu w Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Databricks i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: obszar roboczy Azure Databricks obejmuje płaszczyznę zarządzania hostowaną w Azure Databricks zarządzanej sieci wirtualnej i płaszczyzny danych wdrożonej w sieci wirtualnej zarządzanej przez klienta. Płaszczyzna kontroli przechowuje wszystkie notesy użytkowników i powiązane z nimi wyniki w bazie danych. Domyślnie wszystkie Notesy i wyniki są szyfrowane przy użyciu innego klucza szyfrowania.

System plików datakosteks (DBFS) to rozproszony system plików zainstalowany w obszarze roboczym Azure Databricks i dostępne w klastrach Azure Databricks. DBFS jest zaimplementowana jako konto magazynu w zarządzanej grupie zasobów obszaru roboczego Azure Databricks. Domyślnie konto magazynu jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Twoje dane są przechowywane w posiadanych usługach danych platformy Azure i można je zaszyfrować. Nie zaleca się używania DBFS do przechowywania danych produkcyjnych

Uwaga: te funkcje nie są dostępne dla wszystkich subskrypcji Azure Databricks. Aby zażądać dostępu, skontaktuj się z przedstawicielem swojego konta Microsoft lub datakostki.

* [Jak włączyć klucze zarządzane przez klienta dla notesów Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Jak włączyć klucze zarządzane przez klienta dla systemu plików Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla momentu wprowadzenia zmian do krytycznych Azure Databricks obszarów roboczych.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm.

Jeśli masz subskrypcję platformy do zarządzania lukami w zabezpieczeniach, możesz użyć skryptów inicjalizacji Azure Databricks, aby zainstalować agentów oceny luk w zabezpieczeniach na węzłach klastra Azure Databricks i zarządzać węzłami za pomocą odpowiedniego portalu. Należy pamiętać, że każde rozwiązanie innej firmy działa inaczej.

* [Jak ręcznie zainstalować agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Jak ręcznie zainstalować agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks Skrypty inicjalizacji](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Firma Microsoft obsługuje obrazy podstawowe węzłów klastra Azure Databricks, ale użytkownik jest odpowiedzialny za zapewnienie, że nie będą mieć zainstalowanych poprawek w węzłach klastra. Aby dodać aktualizację konserwacji do istniejącego działającego klastra, należy ponownie uruchomić klaster.

* [Informacje o aktualizacjach konserwacji środowiska uruchomieniowego dla Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Firma Microsoft obsługuje obrazy podstawowe węzłów klastra Azure Databricks, ale użytkownik jest odpowiedzialny za zapewnienie, że wszystkie aplikacje innych firm są zainstalowane.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Implementowanie rozwiązania do zarządzania lukami w zabezpieczeniach innych firm, które ma możliwość porównania skanowania luk w zabezpieczeniach w czasie. Jeśli masz subskrypcję zarządzania lukami w zabezpieczeniach, możesz użyć portalu tego dostawcy do wyświetlania i porównywania skanów z oddziałami wstecznymi. Należy pamiętać, że każde rozwiązanie innej firmy działa inaczej.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach.

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Monitorowanie Azure Security Center**: brak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że istnieją odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informacje o kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania.

**Wskazówki**: Definiowanie zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; Azure Databricks to usługa PaaS, klienci nie mają bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać wszystkie zasoby (takie jak obliczenia, magazyn, Sieć, porty i protokoły itp.), w tym wystąpienia Azure Databricks w ramach subskrypcji. Usuń wszystkie niezatwierdzone zasoby platformy Azure, które zostały wykryte. W przypadku Azure Databricks węzłów klastra Zaimplementuj rozwiązanie innej firmy w celu usunięcia lub alertu dotyczącego niezatwierdzonego oprogramowania.

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; Azure Databricks to usługa PaaS, nie masz bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; Azure Databricks to usługa PaaS, nie masz bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager za pośrednictwem skryptów</div>

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; nie ma to zastosowania do Azure Databricks, ponieważ użytkownicy (inni niż administratorzy) klastra nie potrzebują dostępu do poszczególnych węzłów w celu uruchamiania zadań. Administrator klastra domyślnie ma dostęp do wszystkich węzłów klastra.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; Test porównawczy jest przeznaczony dla usług Azure Apps lub zasobów obliczeniowych obsługujących aplikacje sieci Web.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Databricks przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datakosteks", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Databricks. Należy pamiętać, że wszelkie zastosowane zasady nie działają w grupie zasobów zarządzanej przez dane.

* [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; Azure Databricks to usługa PaaS, nie masz bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Databricks przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datakosteks", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Databricks. Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Zrozumienie efektów Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Azure Databricks obrazów systemu operacyjnego zarządzanych i obsługiwanych przez firmę Microsoft. Użytkownik jest odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji zasad platformy Azure, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

* [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz niestandardowych obrazów dla Azure Databricks węzłów klastra, wypchnij niestandardowy obraz podstawowy do rejestru Docker, takiego jak Azure Container Registry (ACR).

* [Jak dostosować kontenery za pomocą usług kontenera datakostek](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Opis Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Databricks przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datakosteks", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Databricks.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; Azure Databricks to usługa PaaS, nie masz bezpośredniego dostępu do maszyn wirtualnych w klastrze Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Databricks przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datakosteks", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Databricks.

* [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania stanu systemów operacyjnych węzłów klastra Azure Databricks.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj Azure Key Vault z zakresem tajnego Azure Databricks, aby bezpiecznie zarządzać kluczami tajnymi i korzystać z nich.

* [Jak używać Azure Key Vault z Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: obecnie niedostępne; Tożsamości zarządzane nie są obecnie dostępne dla Azure Databricks

* [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: obecnie niedostępna

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Jeśli masz subskrypcję platformy zarządzania lukami w zabezpieczeniach, możesz użyć skryptów inicjalizacji Azure Databricks, aby zainstalować agentów oceny luk w zabezpieczeniach w węzłach klastra Azure Databricks i zarządzać węzłami za pomocą odpowiedniego portalu. Należy pamiętać, że każde rozwiązanie innej firmy działa inaczej.

* [Jak ręcznie zainstalować agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Jak ręcznie zainstalować agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks Skrypty inicjalizacji](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure App Service), ale nie jest ono uruchamiane w Twojej zawartości.

Skanuj wstępnie wszystkie pliki przekazywane do Azure Databricks węzłów klastra lub powiązanych zasobów.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Jeśli masz subskrypcję platformy zarządzania lukami w zabezpieczeniach, możesz użyć skryptów inicjalizacji Azure Databricks, aby zainstalować agentów oceny luk w zabezpieczeniach w węzłach klastra Azure Databricks i zarządzać węzłami za pomocą odpowiedniego portalu. Należy pamiętać, że każde rozwiązanie innej firmy działa inaczej.

* [Jak ręcznie zainstalować agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Jak ręcznie zainstalować agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks Skrypty inicjalizacji](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: dla źródeł danych Azure Databricks upewnij się, że skonfigurowano odpowiedni poziom nadmiarowości danych dla przypadku użycia. Jeśli na przykład używasz konta usługi Azure Storage dla Azure Databricks magazynu danych, wybierz odpowiednią opcję nadmiarowości (LRS, ZRS, GRS, RA-GRS).

* [Źródła danych dla Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Regionalne odzyskiwanie po awarii dla klastrów Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta związanych z implementacją Azure Databricks w programie Azure Key Vault. Do tworzenia codziennej kopii zapasowej konfiguracji elementów datakostki można także użyć interfejsu API REST i wiersza polecenia. Do tworzenia codziennej kopii zapasowej konfiguracji elementów datakostki można także użyć interfejsu API REST/CLI.

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta związanych z implementacją Azure Databricks.

* [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że funkcja usuwania nietrwałego jest włączona w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

* [Jak włączyć usuwanie nietrwałe w Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

* [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Możesz również wykorzystać Przewodnik obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

* [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesłać strumieniowo wskaźnik do alertów.

* [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni.

**Wskazówki**: * [postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

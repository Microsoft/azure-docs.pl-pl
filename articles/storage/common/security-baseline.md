---
title: Podstawa zabezpieczeń Azure dla usługi Azure Storage
description: Podstawa zabezpieczeń Azure dla usługi Azure Storage
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f4687add8fdd55c8084a7180a6e0a3bffd9751b1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715147"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Podstawa zabezpieczeń Azure dla usługi Azure Storage

Podstawowa baza danych zabezpieczeń Azure dla usługi Azure Storage zawiera zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: Skonfiguruj zaporę konta magazynu, ograniczając dostęp do klientów z określonych zakresów publicznych adresów IP, wybierz pozycję sieci wirtualne (sieci wirtualnych) na platformie Azure lub do określonych zasobów platformy Azure. Możesz również skonfigurować prywatne punkty końcowe, aby ruch do usługi magazynu z przedsiębiorstwa został przetransmitowany wyłącznie za pośrednictwem sieci prywatnych.

Uwaga: klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

- [Jak skonfigurować zaporę usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Jak skonfigurować prywatne punkty końcowe dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: monitorowanie i rejestrowanie sieci wirtualnej, podsieci i konfiguracji karty sieciowej oraz ruch sieciowy

**Wskazówki**: usługa Azure Storage zapewnia warstwowy model zabezpieczeń. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w usłudze Azure Virtual Network (VNet). Możesz użyć Azure Security Center i przestrzegać zaleceń dotyczących ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych na platformie Azure. Ponadto Włącz dzienniki przepływu sieciowej grupy zabezpieczeń dla sieci wirtualnych/podsieci skonfigurowanych dla kont magazynu za pomocą zapory konta magazynu i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. 

Należy pamiętać, że jeśli masz prywatne punkty końcowe dołączone do konta magazynu, nie można skonfigurować zasad sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla podsieci. 

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Informacje o prywatnych punktach końcowych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla konta usługi Azure Storage. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Azure Security Center zintegrowane alerty są oparte na działaniach, dla których komunikacja sieciowa była skojarzona z adresem IP, który został pomyślnie rozwiązany, niezależnie od tego, czy adres IP jest znanym ryzykownym adresem IP (na przykład znanym cryptominer) czy adresem IP, który nie został wcześniej rozpoznany jako ryzykowny. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. 

- [Jak włączyć zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Network Watcher przechwytywanie pakietów umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu między kontem magazynu a maszyną wirtualną. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów pomaga zdiagnozować anomalie w sieci, zarówno w sposób aktywny, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach w sieci, debugowanie komunikacji klient-serwer i wiele innych. Możliwe jest zdalne wyzwalanie przechwytywania pakietów, co ułatwia nawiązanie ręcznego uruchamiania przechwycenia pakietu na odpowiedniej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. 

- [Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu portalu](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-manage-portal)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/intruzów

**Wskazówki**: Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobu w sieciach wirtualnych, które wymagają dostępu do konta magazynu, użyj Virtual Network tagów usługi dla skonfigurowanej sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. magazyn) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

Jeśli dostęp do sieci należy do zakresu określonych kont magazynu, należy użyć zasad punktu końcowego usługi Virtual Network.

- [Aby uzyskać więcej informacji na temat używania tagów usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Aby uzyskać więcej informacji na temat zasad punktu końcowego usługi sieci wirtualnej dla usługi Azure Storage](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: usługa

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z kontem usługi Azure Storage za pomocą Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. Storage" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci zasobów konta magazynu. 

Możesz również używać wbudowanych definicji zasad związanych z kontem magazynu, na przykład: konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Przykłady Azure Policy dla magazynu](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci. Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach. Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów. 

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj Azure Policy, aby rejestrować zmiany konfiguracji zasobów sieciowych. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak utworzyć alerty w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: Użyj zaakceptowanego zasobu synchronizacji czasu

**Wskazówki**: nie dotyczy; Firma Microsoft przechowuje źródła czasu dla kont usługi Azure Storage.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników za pośrednictwem Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia punktów końcowych, zasoby sieciowe i inne systemy zabezpieczeń. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania, opcjonalnie z funkcjami zabezpieczeń, takimi jak niezmienny magazyn i wymuszonymi blokadami przechowywania.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: analityka magazynu platformy Azure zawiera dzienniki dla obiektów blob, kolejek i tabel. Za pomocą Azure Portal można skonfigurować dzienniki, które są rejestrowane dla Twojego konta. 

- [Jak skonfigurować monitorowanie dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: podczas przechowywania dzienników zdarzeń zabezpieczeń na koncie usługi Azure Storage lub w obszarze roboczym log Analytics można ustawić zasady przechowywania zgodnie z wymaganiami organizacji. 

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [Zmień okres przechowywania danych w Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Aby przejrzeć dzienniki usługi Azure Storage, istnieją typowe opcje, takie jak zapytania za pomocą oferty log Analytics, a także unikatową opcję bezpośredniego wyświetlania plików dziennika. W usłudze Azure Storage dzienniki są przechowywane w obiektach Blob, do których należy uzyskać dostęp bezpośrednio w http://accountname.blob.core.windows.net/ $Logs (folder rejestrowania jest domyślnie ukryty, więc musisz przejść bezpośrednio. Nie będzie on wyświetlany w poleceniach list) 

Ponadto Włącz zaawansowaną ochronę przed zagrożeniami dla konta usługi Azure Storage. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 

- [Rejestruj i Przejrzyj dane](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Jak włączyć zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: w Azure Security Center Włącz zaawansowaną ochronę przed zagrożeniami dla konta magazynu. Włącz ustawienia diagnostyczne dla konta magazynu i Wyślij dzienniki do obszaru roboczego Log Analytics. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami. 

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Jak zarządzać alertami w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Jak ostrzec dane dziennika usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Rejestrowanie analityki magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Użyj usługi Azure Security Center i Włącz ochronę przed zagrożeniami dla usługi Azure Storage, aby wykrywać operacje przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). 

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: rozwiązanie Azure DNS Analytics (wersja zapoznawcza) w Azure monitor zbiera szczegółowe informacje o INFRASTRUKTURze DNS w zakresie zabezpieczeń, wydajności i operacji. Obecnie to nie obsługuje kont usługi Azure Storage, jednak można użyć rozwiązania do rejestrowania w systemie DNS innej firmy. 

- [Zbierz informacje o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics w wersji zapoznawczej](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; testy porównawcze są przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: przechowywanie spisu kont administracyjnych

**Wskazówki**: usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych. 

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: konta usługi Azure Storage i Azure Active Directory mają koncepcję domyślnych lub pustych haseł. Usługa Azure Storage implementuje model kontroli dostępu obsługujący funkcję kontroli dostępu opartej na rolach (RBAC) na platformie Azure oraz klucz współużytkowany i sygnatury dostępu współdzielonego (SAS). Charakterystyka klucza współużytkowanego i uwierzytelniania SAS polega na tym, że żadna tożsamość nie jest skojarzona z obiektem wywołującym i dlatego nie można wykonać autoryzacji podmiotu zabezpieczeń na podstawie uprawnień. 

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Informacje o podmiotach zabezpieczeń i kontroli dostępu dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do konta magazynu. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. 

Możesz również włączyć dostęp w trybie just-in-Time/tylko do odczytu, korzystając z ról uprzywilejowanych Azure AD Privileged Identity Management usług firmy Microsoft i Azure ARM. 

- [Informacje o tożsamości i dostępie Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Przegląd Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory rejestracji jednokrotnej zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem. 

- [Informacje logowania jednokrotnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory.

**Wskazówki**: Włącz Azure Active Directory uwierzytelnianie wieloskładnikowe i postępuj zgodnie z zaleceniami Azure Security Center dotyczące zarządzania tożsamościami i dostępem, aby pomóc w ochronie zasobów konta magazynu. 

- [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem MFA skonfigurowanym do logowania się i konfigurowania zasobów konta magazynu. 

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: wysyłanie alertów wykrywania ryzyka Azure Security Center do Azure monitor i konfigurowania niestandardowych alertów/powiadomień przy użyciu grup akcji. Włącz zaawansowaną ochronę przed zagrożeniami dla konta usługi Azure Storage, aby generować alerty dla podejrzanych działań. Ponadto można używać funkcji wykrywania ryzyka usługi Azure AD do wyświetlania alertów i raportów na temat ryzykownego zachowania użytkowników. 

- [Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Omówienie wykrywania ryzyka usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów. 

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Platforma Azure zapewnia kontrolę dostępu opartą na rolach (Azure RBAC), aby uzyskać szczegółową kontrolę nad dostępem klientów do zasobów na koncie magazynu.  Użyj poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie bezpieczeństwa zamiast korzystać z klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi BLOB Storage, Użyj poświadczeń usługi Azure AD, aby utworzyć sygnatury dostępu współdzielonego (SAS) delegowania użytkowników, jeśli jest to możliwe dla wyższego poziomu zabezpieczeń.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Jak skonfigurować dostęp do danych obiektów blob platformy Azure i kolejkować dane za pomocą usługi Azure RBAC w Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Zapoznaj się z dziennikami Azure Active Directory, aby ułatwić odnalezienie starych kont, które mogą obejmować role administracyjne konta magazynu. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do zasobów konta magazynu i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.  

Sygnatury dostępu współdzielonego (SAS) można także użyć do zapewnienia bezpiecznego delegowanego dostępu do zasobów na koncie magazynu bez naruszania zabezpieczeń danych. Można kontrolować, jakie zasoby mogą uzyskać dostęp do tych zasobów oraz jak długo SYGNATURa czasowa jest ważna, między innymi parametrami.

Sprawdź także anonimowy dostęp do odczytu do kontenerów i obiektów BLOB. Domyślnie kontener i wszystkie jego obiekty blob mogą być dostępne tylko dla użytkownika, który otrzymał odpowiednie uprawnienia. Za pomocą Azure Monitor można wysyłać alerty dotyczące dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.

Jednym z efektywnych sposobów zmniejszenia ryzyka niepodejrzanego dostępu do konta użytkownika jest ograniczenie czasu trwania dostępu udzielanego użytkownikom. Ograniczone czasowo identyfikatory URI SYGNATURy dostępu współdzielonego są jedynym skutecznym sposobem, aby automatycznie wygasać dostęp użytkownika do konta magazynu. Ponadto rotacja kluczy konta magazynu często jest sposobem zapewnienia, że nieoczekiwany dostęp za pośrednictwem kluczy konta magazynu jest ograniczony.

- [Informacje o raportowaniu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Jak wyświetlać i zmieniać dostęp na poziomie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Monitorowanie konta magazynu w witrynie Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [Zarządzanie kluczami dostępu do konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Użyj analityka magazynu do rejestrowania szczegółowych informacji na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem do usługi magazynu. Wszystkie dzienniki są przechowywane w blokowych obiektach Blob w kontenerze o nazwie $logs, który jest tworzony automatycznie po włączeniu analityka magazynu dla konta magazynu.

Tworzenie ustawień diagnostycznych dla Azure Active Directory kont użytkowników, wysyłanie dzienników inspekcji i dzienników logowania do Log Analytics obszaru roboczego. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics. Aby monitorować błędy uwierzytelniania na kontach usługi Azure Storage, można utworzyć alerty powiadamiające o osiągnięciu określonych progów dla metryk zasobów magazynu. Ponadto należy użyć Azure Monitor do alertu dotyczącego dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.

- [Rejestrowanie analityki magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony przed ryzykiem i tożsamością Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania dotyczące zasobów konta magazynu. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure. 

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft potrzebuje dostępu do danych klienta, skrytka klienta (wersja zapoznawcza dla konta magazynu) udostępnia interfejs dla klientów, który umożliwia przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Firma Microsoft nie będzie potrzebować ani nie zażądać dostępu do wpisów tajnych organizacji przechowywanych w ramach konta magazynu.

- [Opis Skrytka klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów konta magazynu, które przechowują lub przetwarzają informacje poufne. 

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie izolacji przy użyciu oddzielnych subskrypcji, grup zarządzania i kont magazynu dla poszczególnych domen zabezpieczeń, takich jak środowisko, czułość danych.  Możesz ograniczyć swoje konto magazynu, aby kontrolować poziom dostępu do kont magazynu, które są używane przez aplikacje i środowiska korporacyjne na podstawie typu i podzestawu używanych sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta magazynu. Dostęp do usługi Azure Storage można kontrolować za pośrednictwem usługi Azure RBAC. Możesz również skonfigurować prywatne punkty końcowe, aby zwiększyć bezpieczeństwo ruchu sieciowego między siecią wirtualną a usługą przechodzącą przez sieć szkieletową firmy Microsoft, eliminując ekspozycję z publicznego Internetu. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Punkty końcowe usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych.

**Wskazówki**: w przypadku zasobów konta magazynu przechowujących lub przetwarzających informacje poufne Zaznacz zasoby jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla kont usługi Azure Storage za pomocą zapory platformy Azure. 

Ponadto za pomocą zasad punktu końcowego usługi sieci wirtualnej można filtrować ruch sieciowy w sieci wirtualnej do kont usługi Azure Storage za pośrednictwem punktu końcowego usługi i zezwalać na eksfiltracji danych tylko na określone konta usługi Azure Storage.

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Zasady punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: można wymusić użycie protokołu HTTPS przez włączenie bezpiecznego transferu wymaganego dla konta magazynu. Gdy ta opcja jest włączona, połączenia korzystające z protokołu HTTP będą odrzucane. Ponadto Użyj Azure Security Center i Azure Policy, aby wymusić bezpieczny transfer dla konta magazynu.

- [Jak wymagać bezpiecznego transferu w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Zasady zabezpieczeń platformy Azure monitorowane przez Security Center](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji danych nie są jeszcze dostępne dla konta usługi Azure Storage i powiązanych zasobów. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności. 

- [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Azure Active Directory (Azure AD) autoryzują prawa dostępu do zabezpieczonych zasobów za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Usługa Azure Storage definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektu BLOB lub kolejki. 

- [Jak przypisać role platformy Azure dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Jak skonfigurować dostęp do danych obiektów blob platformy Azure i kolejkować dane za pomocą usługi Azure RBAC w Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Jak utworzyć i skonfigurować wystąpienie usługi AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu i nie można go wyłączyć. Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Dane odczytywane z usługi Azure Storage są odszyfrowywane przed zwróceniem. Szyfrowanie usługi Azure Storage umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu ani dodawania kodu do dowolnych aplikacji. 

- [Informacje o szyfrowaniu usługi Azure Storage w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące wprowadzania zmian do zasobów konta magazynu. Możesz również włączyć rejestrowanie usługi Azure Storage, aby śledzić, w jaki sposób każde żądanie skierowane do usługi Azure Storage zostało autoryzowane. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2,0 przy użyciu klucza współużytkowanego lub przy użyciu sygnatury dostępu współdzielonego (SAS). Ponadto należy użyć Azure Monitor do alertu dotyczącego dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Rejestrowanie analityki magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center, aby stale przeprowadzać inspekcję i monitorować konfigurację kont magazynu. 

- [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami Third Party Software

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach bazowych, które obsługują konta magazynu.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach.

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center. 

- [Informacje na temat Azure Security Center zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym kont magazynu) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji. 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów konta magazynu, dzięki czemu metadane są logicznie zorganizowane w taksonomię. 

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia kont magazynu i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie. 

Ponadto należy użyć zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage w celu wykrywania nieautoryzowanych zasobów platformy Azure. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania.

**Wskazówki**: należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji. 


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 

 - Niedozwolone typy zasobów 
 - Dozwolone typy zasobów 

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Może to pomóc w środowiskach opartych na wysokich zabezpieczeniach, takich jak te z kontami magazynu. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Klient może uniemożliwić tworzenie zasobów lub użycie Azure Policy zgodnie z wymaganiami firmy klienta. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 

- Niedozwolone typy zasobów 
- Dozwolone typy zasobów 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager za pośrednictwem skryptów

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak te z kontami magazynu. 

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Storage", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień konta magazynu. Możesz również użyć wbudowanych definicji Azure Policy dla konta usługi Azure Storage, takich jak: 

Inspekcja nieograniczonego dostępu sieciowego do kont magazynu  
Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu  
Konta magazynu należy migrować do nowych zasobów Azure Resource Manager  
Należy włączyć bezpieczny transfer na konta magazynu  

Użyj zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla kont magazynu. 

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: Ustanów bezpieczne konfiguracje dla systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: obsługa bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów konta magazynu. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Zrozumienie Azure Policy efektów](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: Obsługa konfiguracji bezpiecznych dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj Azure Repos, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager, skrypty konfiguracji żądanego stanu itp. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: wykorzystanie Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu dla konta magazynu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów konta usługi Azure Storage. 

- [Jak skorygować zalecenia w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-securely-manage-azure-secrets"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Można użyć kluczy zarządzanych przez firmę Microsoft do szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli używasz kluczy dostarczonych przez klienta, możesz użyć Azure Key Vault, aby bezpiecznie przechowywać klucze. 

Ponadto należy często obrócić klucze konta magazynu, aby ograniczyć wpływ utraty lub ujawnienia kluczy konta magazynu.

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Zarządzanie kluczami dostępu do konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: bezpieczne i automatycznie Zarządzaj tożsamościami

**Wskazówki**: Autoryzuj dostęp do obiektów blob i kolejek w ramach kont usługi Azure Storage z tożsamościami Azure Active Directory i zarządzanymi. Usługi Azure BLOB i queue storage obsługują uwierzytelnianie Azure Active Directory (Azure AD) z tożsamościami zarządzanymi dla zasobów platformy Azure. Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawów skalowania maszyn wirtualnych i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze. 

- [Jak udzielić dostępu do obiektów blob platformy Azure i danych z kolejki przy użyciu tożsamości zarządzanej](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: używanie ochrony przed zagrożeniami w usłudze Azure Storage w celu wykrywania przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). 

Przed przekazaniem do zasobów platformy Azure, takich jak App Service, Data Lake Storage, Blob Storage itd., można również wstępnie skanować każdą zawartość pod kątem złośliwego oprogramowania.

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: dane na koncie magazynu Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi katastrofami naturalnymi. Dane można replikować w tym samym centrum danych, w różnych centrach danych w tym samym regionie lub w regionach geograficznie rozdzielonych. 

Możesz również włączyć usługę Azure Automation, aby wykonywać regularne migawki obiektów BLOB.

- [Informacje o nadmiarowości usługi Azure Storage i umowach dotyczących poziomu usług](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Tworzenie migawki obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Przegląd Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Aby utworzyć kopię zapasową danych z usług obsługiwanych przez konto magazynu, istnieje wiele dostępnych metod obejmujących używanie AzCopy lub narzędzi innych firm. Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych o kluczowym znaczeniu dla firmy w ROBAKu (zapis jeden raz, odczyt wielu). Ten stan sprawia, że dane nie są wymazywane i nie są modyfikowane dla interwału określonego przez użytkownika.

- [Wprowadzenie do narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

Klucze zarządzane/dostarczone przez klienta można tworzyć w ramach Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Jak przywrócić Key Vault certyfikaty](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Jak przywrócić klucze Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Jak przywrócić Key Vault zarządzanych kont magazynu](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault wpisy tajne](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob, plików i danych tabeli do lub z konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

Uwaga: Jeśli chcesz skopiować dane do i z usługi Azure Table Storage, zainstaluj program AzCopy w wersji 7,3.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Aby włączyć klucze zarządzane przez klienta na koncie magazynu, należy użyć Azure Key Vault do przechowywania kluczy. Należy włączyć zarówno właściwości nietrwałego usuwania, jak i nie przeczyszczania w magazynie kluczy. Funkcja usuwania nietrwałego Key Vault umożliwia Odzyskiwanie usuniętych magazynów i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty. W przypadku tworzenia kopii zapasowych danych konta magazynu w usłudze Azure Storage Blobs należy włączyć opcję usuwania nietrwałego, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów BLOB. Kopie zapasowe należy traktować jako dane poufne i zastosować odpowiednie środki kontroli dostępu i ochrony danych w ramach tej linii bazowej. Ponadto w celu zwiększenia ochrony można przechowywać obiekty danych o kluczowym znaczeniu dla firmy w ROBAKu (napisać jeden raz, odczytać wiele).

- [Jak używać usuwania nietrwałego Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

- [Przechowywanie kluczowych dla działalności danych obiektów blob z niezmiennym magazynem](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu. 

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

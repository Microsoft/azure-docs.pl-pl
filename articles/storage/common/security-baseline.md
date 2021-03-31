---
title: Podstawa zabezpieczeń Azure dla usługi Azure Storage
description: Linia bazowa zabezpieczeń usługi Azure Storage zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 56f340a8d64346fd8934e9cfbae26079d4ee7f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576591"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Podstawa zabezpieczeń Azure dla usługi Azure Storage

Ta linia bazowa zabezpieczeń ma zastosowanie do usługi Azure Storage ze wskazówkami dotyczącymi [usługi Azure Security test w wersji 1,0](../../security/benchmarks/overview-v1.md) . Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure Storage. **Formanty** nie mają zastosowania do usługi Azure Storage zostały wykluczone.

 
Aby dowiedzieć się, jak usługa Azure Storage jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi Azure Storage](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Konfigurowanie zapory konta magazynu przez ograniczenie dostępu do klientów z określonych zakresów publicznych adresów IP, Wybieranie sieci wirtualnych lub określonych zasobów platformy Azure.  Możesz również skonfigurować prywatne punkty końcowe, aby ruch do usługi magazynu z przedsiębiorstwa został przetransmitowany wyłącznie za pośrednictwem sieci prywatnych.

Uwaga: klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

- [Jak skonfigurować zaporę usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Jak skonfigurować prywatne punkty końcowe dla usługi Azure Storage](storage-private-endpoints.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: usługa Azure Storage zapewnia warstwowy model zabezpieczeń. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w Virtual Network platformy Azure. Możesz użyć Azure Security Center i przestrzegać zaleceń dotyczących ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych na platformie Azure. Ponadto należy włączyć dzienniki przepływu sieciowych grup zabezpieczeń dla sieci wirtualnych lub podsieci skonfigurowanych dla kont magazynu za pomocą zapory konta magazynu i wysyłać dzienniki do konta magazynu na potrzeby inspekcji ruchu. 

 
Należy pamiętać, że jeśli masz prywatne punkty końcowe dołączone do konta magazynu, nie można skonfigurować reguł sieciowej grupy zabezpieczeń dla podsieci. 
 

 
- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md) 
 

 
- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Informacje o prywatnych punktach końcowych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włączanie usługi Azure Defender dla magazynu dla konta usługi Azure Storage. Usługa Azure Defender for Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania.  Azure Security Center zintegrowane alerty są oparte na działaniach, dla których komunikacja sieciowa była skojarzona z adresem IP, który został pomyślnie rozwiązany, niezależnie od tego, czy adres IP jest znanym ryzykownym adresem IP (na przykład znanym cryptominer) czy adresem IP, który nie został wcześniej rozpoznany jako ryzykowny. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md) 

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Network Watcher przechwytywanie pakietów umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu między kontem magazynu a maszyną wirtualną. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów pomaga zdiagnozować anomalie w sieci, zarówno w sposób aktywny, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach w sieci, debugowanie komunikacji klient-serwer i wiele innych. Możliwe jest zdalne wyzwalanie przechwytywania pakietów, co ułatwia nawiązanie ręcznego uruchamiania przechwycenia pakietu na odpowiedniej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. 

- [ Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu portalu](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: usługa Azure Defender dla magazynu oferuje dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center, a także są wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobu w sieciach wirtualnych, które wymagają dostępu do konta magazynu, użyj Virtual Network tagów usługi dla skonfigurowanych Virtual Network do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Zapora platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład Storage) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

Jeśli dostęp do sieci należy do zakresu określonych kont magazynu, należy użyć zasad punktu końcowego usługi Virtual Network.

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../../virtual-network/service-tags-overview.md)

- [Aby uzyskać więcej informacji na temat zasad punktu końcowego usługi sieci wirtualnej dla usługi Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z kontem usługi Azure Storage za pomocą Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. Storage" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci zasobów konta magazynu. 

Możesz również używać wbudowanych definicji zasad związanych z kontem magazynu, na przykład: konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 

- [Przykłady Azure Policy dla magazynu](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Jak utworzyć Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Tagowanie umożliwia kojarzenie wbudowanych i niestandardowych par nazwa-wartość z danym zasobem sieciowym, co ułatwia organizowanie zasobów sieciowych i powiązanie zasobów platformy Azure z powrotem z projektem sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach. 

Sieciowe grupy zabezpieczeń obsługują Tagi, ale poszczególne reguły zabezpieczeń nie są. Reguły zabezpieczeń mają pole **opisu** , które służy do przechowywania niektórych informacji zwykle umieszczonych w tagu.

- [Jak utworzyć i używać tagów zasobów](../../azure-resource-manager/management/tag-resources.md)

- [Jak filtrować ruch sieciowy przy użyciu sieciowej grupy zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj Azure Policy, aby rejestrować zmiany konfiguracji zasobów sieciowych.  Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.  

 
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Jak utworzyć alerty w Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników za pośrednictwem Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia punktów końcowych, zasoby sieciowe i inne systemy zabezpieczeń. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania, opcjonalnie z funkcjami zabezpieczeń, takimi jak niezmienny magazyn i wymuszonymi blokadami przechowywania.

 
- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Azure Storage Analytics zawiera dzienniki dla obiektów blob, kolejek i tabel. Za pomocą Azure Portal można skonfigurować dzienniki, które są rejestrowane dla Twojego konta.   

 
- [Jak skonfigurować monitorowanie dla konta usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: podczas przechowywania dzienników zdarzeń zabezpieczeń na koncie usługi Azure Storage lub w obszarze roboczym log Analytics można ustawić zasady przechowywania zgodnie z wymaganiami organizacji.  

 
- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Zmień okres przechowywania danych w Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Aby przejrzeć dzienniki usługi Azure Storage, istnieją typowe opcje, takie jak zapytania za pomocą oferty log Analytics, a także unikatową opcję bezpośredniego wyświetlania plików dziennika. W usłudze Azure Storage dzienniki są przechowywane w obiektach Blob, do których należy uzyskać dostęp bezpośrednio w lokalizacji `http://accountname.blob.core.windows.net/$logs` (folder rejestrowania jest domyślnie ukryty, więc musisz przejść bezpośrednio. Nie będzie on wyświetlany w poleceniach list) 

 
Ponadto Włącz usługę Azure Defender dla magazynu dla konta magazynu. Usługa Azure Defender for Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center, a także są wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 
 

 
- [Rejestruj i Przejrzyj dane](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Konfigurowanie usługi Azure Defender dla usługi Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: w Azure Security Center włączyć usługę Azure Defender dla konta magazynu. Włącz ustawienia diagnostyczne dla konta magazynu i Wyślij dzienniki do obszaru roboczego Log Analytics. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.  

 
- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)  

 
- [Jak zarządzać alertami w Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Jak ostrzec dane dziennika usługi log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: korzystanie z usługi Azure Security Center i Włączanie usługi Azure Defender for Storage w celu wykrywania przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji skrótów i podejrzanego dostępu z poziomu aktywnego węzła zakończenia sieci Tor (anonymizing proxy). 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: rozwiązanie Azure DNS Analytics (wersja zapoznawcza) w Azure monitor zbiera szczegółowe informacje o INFRASTRUKTURze DNS w zakresie zabezpieczeń, wydajności i operacji.  Obecnie to nie obsługuje kont usługi Azure Storage, jednak można użyć rozwiązania do rejestrowania w systemie DNS innej firmy.  

 
- [Zbierz informacje o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics w wersji zapoznawczej](../../azure-monitor/insights/dns-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: konta usługi Azure Storage i Azure Active Directory (Azure AD) mają koncepcję domyślnego lub pustego hasła. Usługa Azure Storage implementuje model kontroli dostępu obsługujący funkcję kontroli dostępu opartej na rolach (RBAC) na platformie Azure oraz klucz współużytkowany i sygnatury dostępu współdzielonego (SAS). Charakterystyka klucza współużytkowanego i uwierzytelniania SAS polega na tym, że żadna tożsamość nie jest skojarzona z obiektem wywołującym i dlatego nie można wykonać autoryzacji podmiotu zabezpieczeń na podstawie uprawnień.

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

- [Informacje o podmiotach zabezpieczeń i kontroli dostępu dla konta usługi Azure Storage](storage-introduction.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do konta magazynu. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Możesz również włączyć dostęp w trybie just-in-Time/tylko za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management role uprzywilejowane dla usług firmy Microsoft i Azure Resource Manager.

- [Informacje o tożsamości i dostępie Azure Security Center](../../security-center/security-center-identity-access.md)

- [Przegląd Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory (Azure AD) SSO zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.
 

 
- [Informacje logowania jednokrotnego w usłudze Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)
 

 
- [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu usługi Azure AD](storage-auth-aad.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem w celu ułatwienia ochrony zasobów konta magazynu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów konta magazynu.  

 
- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: wysyłanie alertów wykrywania ryzyka Azure Security Center do Azure monitor i konfigurowania niestandardowych alertów/powiadomień przy użyciu grup akcji. Włącz usługę Azure Defender dla konta magazynu, aby generować alerty dla podejrzanych działań. Ponadto należy użyć funkcji wykrywania ryzyka Azure Active Directory (Azure AD) do wyświetlania alertów i raportów na temat ryzykownego zachowania użytkowników.

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)
 

- [Omówienie wykrywania ryzyka usługi Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../../azure-monitor/alerts/action-groups.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów. 

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Platforma Azure zapewnia kontrolę dostępu opartą na rolach (Azure RBAC), aby uzyskać szczegółową kontrolę nad dostępem klientów do zasobów na koncie magazynu.   Użyj poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie bezpieczeństwa zamiast korzystać z klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi BLOB Storage, Użyj poświadczeń usługi Azure AD, aby utworzyć sygnatury dostępu współdzielonego (SAS) delegowania użytkowników, jeśli jest to możliwe dla wyższego poziomu zabezpieczeń.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania](authorization-resource-provider.md)

- [Jak skonfigurować dostęp do danych obiektów blob platformy Azure i kolejkować dane za pomocą usługi Azure RBAC w Azure Portal](storage-auth-aad-rbac-portal.md)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Zapoznaj się z dziennikami usługi Azure Active Directory (Azure AD), aby ułatwić odnajdywanie starych kont, które mogą obejmować role administracyjne kont magazynu. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do zasobów konta magazynu i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 
 

 
Sygnatury dostępu współdzielonego (SAS) można także użyć do zapewnienia bezpiecznego delegowanego dostępu do zasobów na koncie magazynu bez naruszania zabezpieczeń danych. Można kontrolować, jakie zasoby mogą uzyskać dostęp do tych zasobów oraz jak długo SYGNATURa czasowa jest ważna, między innymi parametrami.
 

 
Sprawdź także anonimowy dostęp do odczytu do kontenerów i obiektów BLOB. Domyślnie kontener i wszystkie jego obiekty blob mogą być dostępne tylko dla użytkownika, który otrzymał odpowiednie uprawnienia. Za pomocą Azure Monitor można wysyłać alerty dotyczące dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.
 

 
Jednym z efektywnych sposobów zmniejszenia ryzyka niepodejrzanego dostępu do konta użytkownika jest ograniczenie czasu trwania dostępu udzielanego użytkownikom. Ograniczone czasowo identyfikatory URI SYGNATURy dostępu współdzielonego są jedynym skutecznym sposobem, aby automatycznie wygasać dostęp użytkownika do konta magazynu. Ponadto rotacja kluczy konta magazynu często jest sposobem zapewnienia, że nieoczekiwany dostęp za pośrednictwem kluczy konta magazynu jest ograniczony.
 

 
- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/) 
 

 
- [Jak wyświetlać i zmieniać dostęp na poziomie konta usługi Azure Storage](storage-auth-aad-rbac-portal.md)
 

 
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
 

 
- [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/anonymous-read-access-configure.md)
 

 
- [Monitorowanie konta magazynu w witrynie Azure Portal](manage-storage-analytics-logs.md)
 

 
- [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Użyj analityka magazynu do rejestrowania szczegółowych informacji na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem do usługi magazynu. Wszystkie dzienniki są przechowywane w blokowych obiektach Blob w kontenerze o nazwie $logs, które są tworzone automatycznie po włączeniu analityka magazynu dla konta magazynu.
 

Tworzenie ustawień diagnostycznych dla kont użytkowników usługi Azure Active Directory (Azure AD), wysyłanie dzienników inspekcji i dzienników logowania do Log Analytics obszaru roboczego. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

Aby monitorować błędy uwierzytelniania na kontach usługi Azure Storage, można utworzyć alerty powiadamiające o osiągnięciu określonych progów dla metryk zasobów magazynu. Ponadto należy użyć Azure Monitor do alertu dotyczącego dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.

- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md)
 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Aby skonfigurować automatyczne reagowanie na wykryte podejrzane działania dotyczące zasobów konta magazynu, użyj funkcji ryzyka i ochrony tożsamości usługi Azure Active Directory (Azure AD). Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft potrzebuje dostępu do danych klienta, skrytka klienta (wersja zapoznawcza dla konta magazynu) udostępnia interfejs dla klientów, który umożliwia przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Firma Microsoft nie będzie potrzebować ani nie zażądać dostępu do wpisów tajnych organizacji przechowywanych w ramach konta magazynu.

- [Opis Skrytka klienta](../../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów konta magazynu, które przechowują lub przetwarzają informacje poufne. 

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie izolacji przy użyciu osobnych subskrypcji, grup zarządzania i kont magazynu dla poszczególnych domen zabezpieczeń, takich jak środowisko i czułość danych.  Możesz ograniczyć swoje konto magazynu, aby kontrolować poziom dostępu do kont magazynu, które są używane przez aplikacje i środowiska korporacyjne na podstawie typu i podzestawu używanych sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta magazynu. Dostęp do usługi Azure Storage można kontrolować za pośrednictwem usługi Azure RBAC (Azure RBAC).

Możesz również skonfigurować prywatne punkty końcowe, aby zwiększyć bezpieczeństwo ruchu sieciowego między siecią wirtualną a usługą przechodzącą przez sieć szkieletową firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)

- [Punkty końcowe usługi dla sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku zasobów konta magazynu przechowujących lub przetwarzających informacje poufne Zaznacz zasoby jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych za pośrednictwem usługi eksfiltracji, Ogranicz wychodzący ruch sieciowy dla kont usługi Azure Storage za pomocą zapory platformy Azure.  

Ponadto za pomocą zasad punktu końcowego usługi sieci wirtualnej można filtrować ruch sieciowy w sieci wirtualnej do kont usługi Azure Storage za pośrednictwem punktu końcowego usługi i zezwalać na eksfiltracji danych tylko na określone konta usługi Azure Storage.

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Zasady punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: można wymusić użycie protokołu HTTPS przez włączenie bezpiecznego transferu wymaganego dla konta magazynu. Gdy ta opcja jest włączona, połączenia korzystające z protokołu HTTP będą odrzucane.  Ponadto Użyj Azure Security Center i Azure Policy, aby wymusić bezpieczny transfer dla konta magazynu.

- [Jak wymagać bezpiecznego transferu w usłudze Azure Storage](storage-require-secure-transfer.md)

- [Zasady zabezpieczeń platformy Azure monitorowane przez Security Center](../../security-center/policy-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji danych nie są jeszcze dostępne dla konta usługi Azure Storage i powiązanych zasobów. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Azure Active Directory (Azure AD) autoryzują prawa dostępu do zabezpieczonych zasobów za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektu BLOB lub kolejki.

- [Jak przypisać role RBAC platformy Azure dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania](authorization-resource-provider.md)

- [Jak skonfigurować dostęp do danych obiektów blob platformy Azure i kolejkować dane za pomocą usługi Azure RBAC w Azure Portal](storage-auth-aad-rbac-portal.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu i nie można go wyłączyć. Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Dane odczytywane z usługi Azure Storage są odszyfrowywane przed zwróceniem. Szyfrowanie usługi Azure Storage umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu ani dodawania kodu do dowolnych aplikacji. 

- [Informacje o szyfrowaniu usługi Azure Storage w spoczynku](storage-service-encryption.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące wprowadzania zmian do zasobów konta magazynu.  Możesz również włączyć rejestrowanie usługi Azure Storage, aby śledzić, w jaki sposób każde żądanie skierowane do usługi Azure Storage zostało autoryzowane. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2,0 przy użyciu klucza współużytkowanego lub przy użyciu sygnatury dostępu współdzielonego (SAS).  Ponadto należy użyć Azure Monitor do alertu dotyczącego dostępu anonimowego dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego. 

 
- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md) 

 
- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center, aby stale przeprowadzać inspekcję i monitorować konfigurację kont magazynu.  

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; Firma Microsoft przeprowadza zarządzanie lukami w systemach bazowych, które obsługują konta magazynu.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center. 

- [Informacje na temat Azure Security Center zabezpieczeń](/azure/security-center/security-center-secure-score)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym kont magazynu) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów konta magazynu, dzięki czemu metadane są logicznie zorganizowane w taksonomię. 

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia kont magazynu i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie. 

Ponadto w celu wykrywania nieautoryzowanych zasobów platformy Azure Użyj usługi Azure Defender dla magazynu. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../../cost-management-billing/manage/create-subscription.md) 

- [Jak utworzyć Grupy zarządzania](../../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 

 
 - Niedozwolone typy zasobów 
 
 - Dozwolone typy zasobów 
 

 
Ponadto za pomocą grafu zasobów platformy Azure można wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je. Może to pomóc w wysokich środowiskach opartych na zabezpieczeniach, takich jak te z kontami magazynu. 
 

 
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Klient może uniemożliwić tworzenie zasobów lub użycie Azure Policy zgodnie z wymaganiami firmy klienta. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy wbudowane definicje — Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak te z kontami magazynu. 

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw **Microsoft. Storage** , aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień konta magazynu. Możesz również użyć wbudowanych definicji Azure Policy dla konta usługi Azure Storage, takich jak:

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Wdrażanie usługi Azure Defender dla magazynu
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Należy włączyć bezpieczny transfer na konta magazynu

Użyj zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla kont magazynu.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów konta magazynu. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 

- [Zrozumienie Azure Policy efektów](../../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj Azure Repos, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager, skrypty konfiguracji żądanego stanu itp. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD), jeśli jest zintegrowany z usługą Azure DevOps lub usługą Azure AD, jeśli jest zintegrowany z TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: wykorzystanie Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu dla konta magazynu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad. 

- [ Jak skonfigurować Azure Policy i zarządzać nimi](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów konta usługi Azure Storage. 

- [Jak skorygować zalecenia w Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Można użyć kluczy zarządzanych przez firmę Microsoft do szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli używasz kluczy dostarczonych przez klienta, możesz użyć Azure Key Vault, aby bezpiecznie przechowywać klucze. 

Ponadto należy często obrócić klucze konta magazynu, aby ograniczyć wpływ utraty lub ujawnienia kluczy konta magazynu.

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)

- [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Autoryzuj dostęp do obiektów blob i kolejek w ramach kont usługi Azure Storage przy użyciu usług Azure Active Directory (Azure AD) i zarządzanych tożsamości. Usługi Azure BLOB i queue storage obsługują uwierzytelnianie usługi Azure AD za pomocą tożsamości zarządzanych dla zasobów platformy Azure. 

Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji działających na platformie Azure Virtual Machines r, aplikacje funkcji, zestawy skalowania maszyn wirtualnych i inne usługi. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.

- [Jak udzielić dostępu do obiektów blob platformy Azure i danych z kolejki przy użyciu tożsamości zarządzanej](storage-auth-aad-rbac-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [ Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: usługa Azure Defender for Storage umożliwia wykrywanie przeładowania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). 
 

 
Przed przekazaniem do zasobów platformy Azure, takich jak App Service, Data Lake Storage, Blob Storage i innych spełniających wymagania organizacji, można również wstępnie skanować każdą zawartość pod kątem złośliwego oprogramowania.
 

 
- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: dane na koncie magazynu Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi katastrofami naturalnymi. Dane można replikować w tym samym centrum danych, w różnych centrach danych w tym samym regionie lub w regionach geograficznie rozdzielonych. 

Możesz również włączyć usługę Azure Automation, aby wykonywać regularne migawki obiektów BLOB.

- [Informacje o nadmiarowości usługi Azure Storage i umowach Service-Level](storage-redundancy.md)

- [Tworzenie migawki obiektu BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Przegląd Azure Automation](../../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Aby utworzyć kopię zapasową danych z obsługiwanych usług konta magazynu, istnieje wiele dostępnych metod obejmujących używanie AzCopy lub narzędzi innych firm. Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych o kluczowym znaczeniu dla firmy w ROBAKu (zapis jeden raz, odczyt wielu). Ten stan sprawia, że dane nie są wymazywane i nie są modyfikowane dla interwału określonego przez użytkownika.
 

Klucze zarządzane/dostarczone przez klienta można tworzyć w ramach Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

 
- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)  

- [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Jak przywrócić Key Vault certyfikaty](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Jak przywrócić klucze Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Jak przywrócić Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault wpisy tajne](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob, plików i danych tabeli do lub z konta magazynu](storage-use-azcopy-v10.md)

Uwaga: Jeśli chcesz skopiować dane do i z usługi Azure Table Storage, zainstaluj program AzCopy w wersji 7,3.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Aby włączyć klucze zarządzane przez klienta na koncie magazynu, należy użyć Azure Key Vault do przechowywania kluczy. Należy włączyć zarówno właściwości nietrwałego usuwania, jak i nie przeczyszczania w magazynie kluczy.  Funkcja usuwania nietrwałego Key Vault umożliwia Odzyskiwanie usuniętych magazynów i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty.  W przypadku tworzenia kopii zapasowych danych konta magazynu w usłudze Azure Storage Blobs należy włączyć opcję usuwania nietrwałego, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów BLOB.  Kopie zapasowe należy traktować jako dane poufne i zastosować odpowiednie środki kontroli dostępu i ochrony danych w ramach tej linii bazowej.  Ponadto w celu zwiększenia ochrony można przechowywać obiekty danych o kluczowym znaczeniu dla firmy w ROBAKu (napisać jeden raz, odczytać wiele).

- [Jak używać usuwania nietrwałego Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Soft delete for Azure Storage blobs](../blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

- [Przechowywanie danych obiektów blob kluczowych dla działalności za pomocą magazynu niezmiennego](../blobs/storage-blob-immutable-storage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Przewodnik obsługi zdarzeń zabezpieczeń komputera NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu. 

 
Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.
 

 
- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)
 

 
- [Organizowanie zasobów platformy Azure przy użyciu tagów](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.
    

- [ Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

---
title: Plan bazowy zabezpieczeń platformy Azure dla partii
description: Plan bazowy zabezpieczeń platformy Azure dla partii
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8bda712523faa90f32a12fe7eecce27dccf3a55c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756623"
---
# <a name="azure-security-baseline-for-batch"></a>Plan bazowy zabezpieczeń platformy Azure dla partii

Usługa Azure Security Baseline for Batch zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Wdrażanie puli partii azure w sieci wirtualnej. Aby umożliwić węzłom obliczeniowym puli bezpieczną komunikację z innymi maszynami wirtualnymi lub z siecią lokalną, można aprowizować pulę w podsieci sieci wirtualnej platformy Azure. Ponadto wdrażanie puli w sieci wirtualnej zapewnia kontrolę nad sieciową grupą zabezpieczeń (NSG) używaną do zabezpieczania interfejsów sieciowych poszczególnych węzłów, a także nad podsiecią. Skonfiguruj sieć sieciowej grupy danych, aby zezwalać na ruch tylko z zaufanych adresów IP/lokalizacji w Internecie.

Jak utworzyć pulę wsadową platformy Azure w sieci wirtualnej:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Użyj usługi Azure Security Center i korygować zalecenia dotyczące ochrony sieci związane z siecią wirtualną/ siecią grupy zabezpieczeń (NSG) skojarzoną z pulą usług Batch. Włącz dzienniki przepływu w grupie ndsg używanej do ochrony puli usług wsadowych i wysyłanie dzienników do konta usługi Azure Storage w celu inspekcji ruchu. Można również wysłać dzienniki przepływu nsg do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Traffic Analytics, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Azure Traffic Analytics to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów wyszukiwania, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określania błędnych konfiguracji sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć i korzystać z analizy ruchu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmów komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz usługę Azure DDoS (rozproszona odmowa usługi) Standardowa ochrona w sieci wirtualnej chroniąca pulę usługi Azure Batch w celu ochrony przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować ochronę przed atakami DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Włącz dzienniki przepływu w sieciowej grupie zabezpieczeń (NSG) używanej do ochrony puli usługi Azure Batch i wysyłaniu dzienników do konta usługi Azure Storage w celu inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Wdrożenie systemów wykrywania włamań/włamań opartych na sieci

**Wskazówki:** Jeśli jest to wymagane do celów zgodności, wybierz wirtualne urządzenie sieciowe z portalu Azure Marketplace, które obsługuje systemy wykrywania włamań (IDS) i funkcje systemów zapobiegania włamaniom (IPS) z możliwością inspekcji ładunku.

Jeśli wykrywanie włamań i/lub zapobieganie na podstawie inspekcji ładunku nie jest wymagane, można użyć zapory azure z analizą zagrożeń. Filtrowanie oparte na analizie zagrożeń zapory platformy Azure może ostrzegać i odmawiać ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z źródła danych microsoft Threat Intelligence.

Wdrażanie zapory platformy Azure z publicznym adresem IP w tej samej sieci wirtualnej co węzły puli wsadowej platformy Azure. Konfigurowanie reguł tłumaczenia adresów sieciowych (NAT) między zaufanymi lokalizacjami w Internecie a prywatnymi adresami IP poszczególnych węzłów puli. Na zaporze platformy Azure w obszarze Analizy zagrożeń skonfiguruj "Alert i odmów", aby blokować alerty i blokowanie ruchu do/ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z źródła danych microsoft Threat Intelligence i uwzględniane są tylko rekordy najwyższego zaufania. 

Jak utworzyć pulę wsadową platformy Azure w sieci wirtualnej:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Jak wdrożyć Zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Zarządzanie ruchem w aplikacjach internetowych

**Wskazówki:** Nie dotyczy, benchmark jest przeznaczony dla aplikacji sieci web działających w usłudze Azure App Service lub wystąpieniach IaaS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Użyj tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub zapory platformy Azure skojarzonych z pulami usługi Azure Batch. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Zrozumienie tagów usług i używanie ich:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z pulami usług Azure Batch za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.Batch" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej pul usługi Azure Batch.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Reguły konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla grup usług sieciowych (NSG) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z pulami wsadowymi platformy Azure. W przypadku poszczególnych reguł sieciowej grupy danych sieciowych należy użyć pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł umożliwiających ruch do/z sieci.

Użyj dowolnej z wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieoznakowanych zasobach.

Można użyć narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do wyszukiwania lub wykonywania akcji na podstawie zasobów na podstawie ich tagów.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć nsg:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z pulami usługi Azure Batch. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Dla usługi Azure Batch, domyślnie firma Microsoft zapewnia synchronizację czasu. Jednak jeśli masz określone wymagania dotyczące synchronizacji czasu, można zaimplementować te zmiany.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Wbudowane konto usługi Azure Batch w usłudze Azure Monitor w celu zagregowania danych zabezpieczeń generowanych przez urządzenia klastra. Korzystać z niestandardowych zapytań do wykrywania zagrożeń i reagowania na nie w środowisku.  W przypadku monitorowania na poziomie zasobów usługi Azure Batch należy użyć interfejsów API usługi Batch do monitorowania lub wykonywania zapytań o stan zasobów, w tym zadań, zadań, węzłów i pul.

Jak przywdzielić konto usługi Azure Batch do usługi Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** W przypadku monitorowania poziomu konta usługi Azure Batch należy monitorować każde konto usługi Batch przy użyciu funkcji usługi Azure Monitor. Usługa Azure Monitor zbiera metryki i opcjonalnie dzienniki diagnostyczne dla zasobów o zakresie na poziomie konta usługi Batch, takich jak pule, zadania i zadania. Zbieraj i konsumuj te dane ręcznie lub programowo, aby monitorować działania na koncie usługi Batch i diagnozować problemy.

W przypadku monitorowania poziomu zasobów usługi Azure Batch należy używać interfejsów API usługi Azure Batch do monitorowania lub wykonywania zapytań o stan zasobów, w tym zadań, zadań, węzłów i pul.

Jak skonfigurować monitorowanie i rejestrowanie na poziomie konta usługi Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Zrozumienie monitorowania na poziomie zasobów wsadowych:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Zbieranie dzienników zabezpieczeń z systemu operacyjnego

**Wskazówki:** Usługa Azure Monitor zbiera metryki i dzienniki diagnostyczne dla zasobów na koncie usługi Azure Batch. Zbieraj i korzystaj z tych danych na różne sposoby, aby monitorować konto usługi Azure Batch i diagnozować problemy. Można również skonfigurować alerty metryki, dzięki czemu otrzymywać powiadomienia, gdy metryka osiągnie określoną wartość.

W razie potrzeby możesz połączyć się z poszczególnymi węzłami puli za pośrednictwem powłoki zabezpieczonej (SSH) lub protokołu RDP (Remote Desktop Protocol), aby uzyskać dostęp do dzienników lokalnego systemu operacyjnego.

Jak zbierać dzienniki diagnostyczne z konta usługi Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Jak zdalnie połączyć się z węzłami puli usługi Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Wbudowane konto wsadowe platformy Azure w usłudze Azure Monitor. Upewnij się, że używany obszar roboczy usługi Azure Log Analytics ma okres przechowywania dzienników ustawiony zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji

Jak skonfigurować monitorowanie i rejestrowanie usługi Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Jak skonfigurować okres przechowywania obszaru roboczego usługi Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Tworzenie alertów metryk usługi Azure Batch, które wyzwalają, gdy wartość określonej metryki przekroczy dany próg.

Jak skonfigurować alerty metryki usługi Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włącz alerty dla nietypowej aktywności

**Wskazówki:** Tworzenie alertów metryk usługi Azure Batch, które wyzwalają, gdy wartość określonej metryki przekroczy dany próg.

Jak skonfigurować alerty metryki usługi Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Użyj usługi Windows Defender w poszczególnych węzłach wsadowych w przypadku systemów operacyjnych Windows lub podaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2.9: Włącz rejestrowanie zapytań DNS

**Wskazówki**: Wdrażanie rozwiązania innej firmy do rejestrowania dns

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki:** Ręcznie skonfiguruj rejestrowanie konsoli i transkrypcję programu PowerShell na podstawie dla węzła.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Obsługa rekordu lokalnego konta administracyjnego, który jest tworzony podczas inicjowania obsługi administracyjnej puli usługi Azure Batch, a także innych kont, które można utworzyć. Ponadto jeśli używana jest integracja usługi Azure Active Directory (AAD), usługa AAD ma wbudowane role, które muszą być jawnie przypisane i w związku z tym są możliwe do queryable. Moduł AAD PowerShell służy do wykonywania kwerend adhoc w celu odnajdowania kont, które są członkami grup administracyjnych.

Ponadto można użyć zaleceń dotyczących tożsamości i zarządzania dostępem usługi Azure Security Center.

Jak uzyskać rolę katalogu w ujrzeniu usługi AAD w programie PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w ujmowaniu w programie AAD za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Jak monitorować tożsamość i dostęp za pomocą usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmienianie haseł domyślnych w stosownych przypadkach

**Wskazówki:** Podczas inicjowania obsługi administracyjnej puli usługi Azure Batch, masz możliwość tworzenia kont komputera lokalnego. Nie ma żadnych domyślnych haseł do zmiany, jednak można określić różne hasła dla dostępu do zabezpieczonej powłoki (SSH) i protokołu RDP (Remote Desktop Protocol). Po skonfigurowaniu puli wsadowej platformy Azure można wygenerować losowego użytkownika dla poszczególnych węzłów w witrynie Azure portal lub za pośrednictwem interfejsu API usługi Azure Resource Manager.

Jak dodać użytkownika do określonego węzła obliczeniowego:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zapewnienie wykorzystania specjalnych rachunków administracyjnych

**Wskazówki:** Integruj uwierzytelnianie dla aplikacji wsadowych platformy Azure z usługą Azure Active Directory. Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych.

Ponadto można użyć zaleceń dotyczących tożsamości i zarządzania dostępem usługi Azure Security Center.

Jak uwierzytelnić aplikacje wsadowe za pomocą usługi Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Jak monitorować tożsamość i dostęp za pomocą usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (Logowanie jednokrotne) za pomocą usługi Azure Active Directory

**Wskazówki:** Nie dotyczy, podczas gdy usługa Azure Batch obsługuje uwierzytelnianie usługi Azure AD, logowanie jednokrotne nie jest obsługiwane.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich dostępu opartego na usłudze Azure Active Directory.

**Wskazówki:** Integruj uwierzytelnianie dla aplikacji wsadowych platformy Azure z usługą Azure Active Directory (AAD). Włącz uwierzytelnianie wieloskładnikowe usługi AAD (MFA) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

 

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z dostępem uprzywilejowanym) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj paws (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowany do logowania się i konfigurowania zasobów usługi Azure Batch.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Jeśli masz zintegrowane uwierzytelnianie dla aplikacji wsadowych platformy Azure za pomocą usługi Azure Active Directory (AAD), użyj raportów zabezpieczeń usługi Azure Active Directory do generowania dzienników i alertów, gdy w środowisku występuje podejrzane lub niebezpieczne działanie. Usługa Azure Security Center służy do monitorowania tożsamości i dostępu do aktywności.

Jak zidentyfikować użytkowników usługi Azure AD oflagowanych pod kątem ryzykownych działań:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować tożsamość użytkowników i dostęp do aktywności w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Jeśli masz zintegrowane uwierzytelnianie dla aplikacji wsadowych platformy Azure za pomocą usługi Azure Active Directory, możesz użyć lokalizacji nazwanych dostępu warunkowego, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji i integruj uwierzytelnianie dla aplikacji wsadowych platformy Azure z usługą AAD. Usługa AAD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. AAD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Jak utworzyć i skonfigurować wystąpienie AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak uwierzytelnić aplikacje partii za pomocą usługi AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory(AAD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto można użyć przeglądów dostępu tożsamości platformy Azure do efektywnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko właściwi użytkownicy mają stały dostęp.

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Tworzenie ustawień diagnostycznych dla kont użytkowników usługi Azure Active Directory, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Azure Log Analytics. Konfigurowanie żądanych alertów w obszarze roboczym usługi Azure Log Analytics.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj funkcji wykrywania ryzyka i ochrony tożsamości usługi Azure Active Directory (AAD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić ryzykowne logowania AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak ować wobec pokład Azure Wartownik:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta podczas scenariuszy pomocy technicznej<br></div>

**Wskazówki**: Niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla usługi Azure Batch.
 
Lista obsługiwanych usług skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Pule wsadowe platformy Azure powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone sieciowymi grupami zabezpieczeń (NSG). Dane usługi Azure Batch powinny znajdować się w zabezpieczonym koncie usługi Azure Storage.

Jak utworzyć pulę wsadową platformy Azure w sieci wirtualnej:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitoruj i blokuj nieautoryzowane przesyłanie poufnych informacji.

**Wskazówki:** Dla kont usługi Azure Storage skojarzonych z pulami wsadowymi platformy Azure, które zawierają poufne informacje, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania stratom nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki**: Szyfrowanie wszystkich poufnych informacji podczas przesyłania. Zasoby platformy Microsoft Azure będą domyślnie negocjować usługę TLS 1.2. Upewnij się, że klienci łączący się z pulami wsadowymi platformy Azure lub magazynami danych (konta usługi Azure Storage) mogą negocjować tls 1.2 lub więcej.

Upewnij się, że protokół HTTPS jest wymagany do uzyskania dostępu do konta magazynu zawierającego dane usługi Azure Batch.

Poznaj szyfrowanie konta usługi Azure Storage w trybie przesyłania:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Dla kont usługi Azure Storage skojarzonych z pulami wsadowymi platformy Azure, które zawierają poufne informacje, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania stratom nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Jak zabezpieczyć konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Użyj kontroli dostępu opartej na rolach usługi Azure Active Directory (AAD), aby kontrolować dostęp do płaszczyzny zarządzania zasobami platformy Azure, w tym kontami wsadowymi, pulami partii i kontami magazynu.

Poznaj usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak skonfigurować rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania stratom danych nie są jeszcze dostępne dla usługi Azure Storage ani zasobów obliczeniowych. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfruj poufne informacje w spoczynku

**Wskazówki:** W przypadku kont magazynu skojarzonych z kontem usługi Azure Batch zaleca się zezwolenie firmie Microsoft na zarządzanie kluczami szyfrowania, jednak w razie potrzeby można zarządzać własnymi kluczami.

Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące tego, kiedy zmiany mają miejsce w krytycznych zasobach platformy Azure związanych z kontami/pulami/pulami usługi Azure Batch lub skojarzonymi z nimi.

Skonfiguruj ustawienia diagnostyczne dla kont magazynu skojarzonych z pulą wsadową platformy Azure, aby monitorować i rejestrować wszystkie operacje CRUD względem danych puli.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak włączyć dodatkowe rejestrowanie/inspekcję konta usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** W przypadku węzłów puli wsadowej platformy Azure użytkownik jest odpowiedzialny za zarządzanie rozwiązaniem do zarządzania lukami w zabezpieczeniach.

Opcjonalnie, jeśli masz subskrypcję platformy Rapid7, Qualys lub inną platformę do zarządzania lukami, możesz ręcznie zainstalować agentów oceny luk w zabezpieczeniach w węzłach puli usługi Batch i zarządzać węzłami za pośrednictwem odpowiedniego portalu.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie rozwiązania do zarządzania poprawkami zautomatyzowanego systemu operacyjnego

**Wskazówki:** Firma Microsoft do obsługi i aktualizowania podstawowych obrazów węzłów puli wsadowej platformy Azure. Upewnij się, że system operacyjny węzłów puli wsadowej platformy Azure pozostaje poprawiony przez cały okres istnienia klastra, co może wymagać włączenia automatycznych aktualizacji, monitorowania węzłów lub wykonywania okresowych ponownych rozruchów.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrożenie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki:** Upewnij się, że aplikacje węzłów platformy Azure w puli wsadowej pozostają załatane przez cały okres istnienia klastra, co może wymagać włączenia automatycznych aktualizacji, monitorowania węzłów lub wykonywania okresowych ponownych rozruchów.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanowania luk w zabezpieczeniach typu back-to-back

**Wskazówki:** Jeśli masz subskrypcję platformy Rapid7, Qualys lub inną subskrypcję platformy do zarządzania lukami w zabezpieczeniach, możesz użyć portalu tego dostawcy do przeglądania i porównywania skanowania luk w zabezpieczeniach typu back-to-back.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach.

**Wskazówki:** Użyj wspólnego programu oceny ryzyka (np. wspólnego systemu oceny luk w zabezpieczeniach) lub domyślnych ocen ryzyka dostarczonych przez narzędzie do skanowania innych firm.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywać wszystkie zasoby (takie jak zasoby obliczeniowe, magazyn, sieć itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie (odczytuj) uprawnienia w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager (ARM) w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

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

Jak tworzyć i tagi użytkownika:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania.

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych


**Monitorowanie usługi Azure Security Center:** nie dotyczy

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

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** W przypadku węzłów usługi Azure Batch Pool należy zaimplementować rozwiązanie innej firmy do monitorowania węzłów klastra pod kątem niezatwierdzonych aplikacji.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki:** W przypadku węzłów usługi Azure Batch Pool należy zaimplementować rozwiązanie innej firmy do monitorowania węzłów klastra pod kątem niezatwierdzonych aplikacji.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki:** W przypadku węzłów usługi Azure Batch Pool należy zaimplementować rozwiązanie innej firmy, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.


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

**Wskazówki:** W przypadku węzłów usługi Azure Batch Pool należy zaimplementować rozwiązanie innej firmy, aby zapobiec wykonywaniu nieautoryzowanych typów plików.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Ograniczanie możliwości interakcji użytkowników z usługą Azure Resource Manager za pośrednictwem skryptów</div>

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy,

Nie ma to zastosowania do usługi Azure Batch, ponieważ użytkownicy (nie-administratorzy) pul usługi Azure Batch nie potrzebują dostępu do poszczególnych węzłów do uruchamiania zadań. Administrator klastra ma już dostęp do wszystkich węzłów.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki:** Nie dotyczy, benchmark jest przeznaczony dla aplikacji sieci web działających w usłudze Azure App Service lub wystąpieniach IaaS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Batch", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji kont i pul usługi Azure Batch.

Jak wyświetlić dostępne aliasy zasad platformy Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Ustanawianie bezpiecznych konfiguracji dla systemu operacyjnego

**Wskazówki:** Ustanawianie bezpiecznych konfiguracji dla systemu operacyjnego węzłów puli wsadowej.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Obsługa bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia zasobów platformy Azure związanych z kontem i pulami usługi Batch (takimi jak sieci wirtualne, podsieci, zapory platformy Azure, konta usługi Azure Storage itp.). Do tworzenia niestandardowych zasad można użyć aliasów zasad platformy Azure z następujących obszarów nazw:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Obsługa bezpiecznych konfiguracji dla systemów operacyjnych

**Wskazówki:** Obrazy systemu operacyjnego azure batch pool zarządzane i obsługiwane przez firmę Microsoft. Użytkownik jest odpowiedzialny za implementowanie konfiguracji stanu na poziomie systemu operacyjnego.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure dla kont usługi Azure Batch, pul lub powiązanych zasobów, użyj aplikacji Repo platformy Azure, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki:** Jeśli używasz obrazów niestandardowych dla pul usługi Azure Batch, użyj kontroli dostępu opartej na rolach (RBAC), aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do obrazów.

Poznaj rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Jak skonfigurować rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj wbudowanych definicji zasad platformy Azure, aby ostrzegać, przeprowadzać inspekcje i wymuszać konfiguracje zasobów związanych z usługą Azure Batch.  Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Batch", aby utworzyć niestandardowe zasady dla kont i pul usługi Azure Batch. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki:** Zaimplementuj rozwiązanie innej firmy, aby zachować żądany stan dla systemów operacyjnych węzłów usługi Azure Batch Pool.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.Batch", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji wystąpienia usługi Azure Batch. Można również użyć wszelkich wbudowanych zasad utworzonych specjalnie dla usługi Azure Batch lub zasobów używanych przez usługę Azure Batch, takich jak:

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń

-Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej

- Dzienniki diagnostyczne na kontach usługi Batch powinny być włączone

Jak wyświetlić dostępne aliasy zasad platformy Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki:** Zaimplementuj rozwiązanie innej firmy do monitorowania stanu systemów operacyjnych węzłów usługi Azure Batch Pool.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="711-securely-manage-azure-secrets"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Usługa Azure Key Vault może być używana z wdrożeniami usługi Azure Batch do zarządzania kluczami do przechowywania puli na kontach usługi Azure Storage.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć usługę Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Niedostępne, tożsamość usługi zarządzanej nie jest obsługiwana przez usługę Azure Batch


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki:** Użyj usługi Windows Defender na poszczególnych węzłach puli usługi Azure Batch w przypadku systemów operacyjnych Windows lub podaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure Batch), jednak nie jest uruchamiana w zawartości klienta.

Wstępnie skanuj wszystkie pliki przekazywane do nieobliczalnych zasobów platformy Azure, takich jak usługa App Service, magazyn usługi Data Lake, magazyn obiektów Blob itp. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych przypadkach.

Poznaj oprogramowanie antywirusowe firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki:** Użyj usługi Windows Defender w poszczególnych węzłach puli usługi Azure Batch w przypadku systemów operacyjnych Windows i upewnij się, że automatyczna aktualizacja jest włączona. Podaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz Linuksa.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnij regularne automatyczne zapasy zapasów

**Wskazówki:** Podczas korzystania z konta usługi Azure Storage dla magazynu danych usługi Azure Batch Pool wybierz odpowiednią opcję nadmiarowości (LRS, ZRS, GRS, RA-GRS). 

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Podczas korzystania z konta usługi Azure Storage dla magazynu danych usługi Azure Batch Pool wybierz odpowiednią opcję nadmiarowości (LRS, ZRS, GRS, RA-GRS).  Jeśli używasz usługi Azure Key Vault dla dowolnej części wdrożenia usługi Azure Batch, upewnij się, że klucze są archiwizowane.

Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak wykonać kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Jeśli zarządzasz własnymi kluczami dla kont usługi Azure Storage lub innymi zasobami powiązanymi z implementacją usługi Azure Batch, okresowo testuj przywracanie kopii zapasowych kluczy.

Jak wykonać kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Jak przywrócić klucz zarządzany przez klienta za pomocą programu PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Jeśli usługa Azure Key Vault jest używana do przechowywania kluczy związanych z kontami usługi Azure Batch Pool Storage, włącz opcję usuwania programowego w usłudze Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie nietrwałe w usłudze Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Tworzenie przewodnika po reagowaniu na incydenty

**Wskazówki**: Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują role personelu, a także fazy obsługi incydentów/zarządzania.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania zdarzeń i priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność alertów, aby ułatwić ustalanie priorytetów kolejności, w jakiej uczestniczysz w każdym alertie, dzięki czemu gdy zasób zostanie naruszony, możesz uzyskać do niego od razu. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Podaj dane kontaktowe incydentu bezpieczeństwa i konfiguruj powiadomienia o alertach &nbsp;dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Azure Sentinel.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i upewnij się, że w ciągu 60 dni należy skorygować wszystkie krytyczne wyniki dotyczące zabezpieczeń.

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w oparciu o zarządzał infrastrukturą, usługami i aplikacjami firmy Microsoft w chmurze: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Synapse Analytics
description: Punkt Synapse Analytics zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c2fad393479f0ffce81a272919b8d12024001991
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565461"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Synapse Analytics

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Synapse Analytics. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Synapse Analytics. **Kontrolki** nie dotyczy Synapse Analytics zostały wykluczone.

 
Aby zobaczyć, Synapse Analytics mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny [Synapse Analytics mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Zabezpieczanie serwera Azure SQL do sieci wirtualnej za pośrednictwem Private Link. Azure Private Link umożliwia dostęp do usług PaaS platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się przez sieć szkieletową firmy Microsoft.

Alternatywnie podczas nawiązywania połączenia z pulą Synapse SQL zawęzij zakres połączenia wychodzącego do bazy danych SQL przy użyciu sieciowej grupy zabezpieczeń. Wyłącz cały ruch usługi platformy Azure do bazy danych SQL za pośrednictwem publicznego punktu końcowego, ustawiając opcję Zezwalaj usługom platformy Azure na wył. Upewnij się, że w regułach zapory nie są dozwolone żadne publiczne adresy IP.

- [Opis Azure Private Link](../private-link/private-link-overview.md)

- [Opis Private Link dla Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [How to create an NSG with a security configuration (Jak utworzyć sieciową grupy zabezpieczeń z konfiguracją zabezpieczeń)](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** podczas nawiązywania połączenia z dedykowaną pulą SQL i włączeniu dzienników przepływu sieciowej grupy zabezpieczeń dzienniki są wysyłane na konto usługi Azure Storage w celu inspekcji ruchu.

Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej sieciowej organizacji zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Używanie zaawansowanej ochrony przed zagrożeniami (ATP) na Azure Synapse SQL. Usługa ATP wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich i może wyzwalać różne alerty, takie jak "Potencjalna iniekcja SQL" i "Dostęp z nietypowej lokalizacji". Usługa ATP jest częścią oferty Advanced Data Security (ADS) i można do niej uzyskiwać dostęp za pośrednictwem centralnego portalu SQL ADS.

Włącz DDoS Protection Standardowa w sieciach wirtualnych skojarzonych z programem Azure Synapse SQL w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

- [Understand ATP for Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć Advanced Data Security dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Omówienie ads](../azure-sql/database/azure-defender-for-sql.md)

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Understand Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Podczas nawiązywania połączenia z dedykowaną pulą SQL i włączeniu dzienników przepływu sieciowej grupy zabezpieczeń wysyłaj dzienniki na konto usługi Azure Storage w celu inspekcji ruchu. Dzienniki przepływu można również wysyłać do obszaru roboczego usługi Log Analytics lub przesyłać strumieniowo do Event Hubs.  Jeśli jest to wymagane do badania anomalii działań, włącz Network Watcher przechwytywanie pakietów.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Używanie zaawansowanej ochrony przed zagrożeniami (ATP) na Azure Synapse SQL. Usługa ATP wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich i może wyzwalać różne alerty, takie jak "Potencjalna iniekcja SQL" i "Dostęp z nietypowej lokalizacji". Usługa ATP jest częścią oferty Advanced Data Security (ADS) i jest dostępna do zarządzania za pośrednictwem centralnego portalu SQL ADS. AtP integruje również alerty z Azure Security Center.

- [Understand ATP for Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** użyj tagów usługi dla sieci wirtualnej do zdefiniowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

W przypadku korzystania z punktu końcowego usługi dla dedykowanej puli SQL wymagany jest ruch wychodzący do bazy danych programu Azure SQL Publiczne adresy IP: sieciowe grupy zabezpieczeń muszą być otwarte dla adresów IP Azure SQL Database, aby umożliwić łączność. Można to zrobić przy użyciu tagów usługi sieciowej Azure SQL Database.

- [Understand service tags with service endpoints for Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Opis tagów usług i korzystanie z tych tagów](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla zasobów związanych z dedykowaną pulą SQL przy użyciu Azure Policy. Za pomocą przestrzeni nazw "Microsoft.Sql" można zdefiniować niestandardowe definicje zasad lub użyć dowolnej wbudowanej definicji zasad przeznaczonej do Azure SQL ochrony sieci bazy danych/serwera. Przykładem odpowiednich wbudowanych zasad zabezpieczeń sieci dla serwera Azure SQL Database może być: "SQL Server powinien używać punktu końcowego usługi dla sieci wirtualnej".

Użyj Azure Blueprints, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resource Management, kontrola dostępu oparta na rolach (RBAC) platformy Azure i zasady, w pojedynczej definicji strategii. Łatwe stosowanie strategii do nowych subskrypcji i środowisk oraz dostosowywanie kontroli i zarządzania za pomocą zarządzania wersjami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć usługę Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej organizacji sieciowej należy użyć pola "Opis", aby określić potrzebę biznesową i/lub czas trwania (itp.) dla reguł, które zezwalają na ruch do/z sieci.

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomić o istniejących nieotagowanych zasobach.

Możesz użyć interfejsu Azure PowerShell wiersza polecenia platformy Azure, aby szukać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z dedykowaną pulą SQL. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Zasady inspekcji można zdefiniować dla określonej bazy danych lub jako domyślne zasady serwera na platformie Azure (które hostuje Azure Synapse). Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

Jeśli inspekcja serwera jest włączona, zawsze ma zastosowanie do bazy danych. Baza danych będzie podlegać inspekcji, niezależnie od ustawień inspekcji bazy danych.

Po włączeniu inspekcji można zapisywać je w dzienniku inspekcji na koncie usługi Azure Storage, w obszarze roboczym usługi Log Analytics lub w Event Hubs.

Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Jak skonfigurować inspekcję dla zasobów Azure SQL zasobów](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Włącz inspekcję na poziomie serwera Azure SQL dedykowanej puli SQL i wybierz lokalizację magazynu dla dzienników inspekcji (Azure Storage, Log Analytics lub Event Hubs).

Inspekcja może być włączona zarówno na poziomie bazy danych, jak i serwera i jest sugerowana jako włączona tylko na poziomie serwera, chyba że wymagane jest skonfigurowanie oddzielnego ujścia danych lub przechowywania dla określonej bazy danych.

- [Jak włączyć inspekcję dla Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Jak włączyć inspekcję dla serwera](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Różnice w zasadach inspekcji na poziomie serwera i na poziomie bazy danych](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W przypadku przechowywania dzienników związanych z dedykowaną pulą SQL na koncie magazynu, w obszarze roboczym usługi Log Analytics lub w centrach zdarzeń należy ustawić okres przechowywania dzienników zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji.

- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Jak ustawić parametry przechowywania dzienników w obszarze roboczym usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Przechwytywanie zdarzeń przesyłania strumieniowego w Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników. Użyj usługi Advanced Threat Protection Azure SQL Database w połączeniu z Azure Security Center, aby ostrzegania o nietypowych działaniach związanych z bazą danych SQL. Możesz też skonfigurować alerty na podstawie wartości metryk lub wpisów dziennika aktywności platformy Azure związanych z bazą danych SQL.

Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy.

- [Opis zaawansowanej ochrony przed zagrożeniami i alertów dla Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć Advanced Data Security dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Jak skonfigurować alerty niestandardowe dla Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** używaj usługi Advanced Threat Protection (ATP) w Azure SQL Database w połączeniu z Azure Security Center do monitorowania i ostrzegania o anomalii. Usługa ATP jest częścią oferty Advanced Data Security (ADS) i jest dostępna do zarządzania za pośrednictwem centralnego programu SQL ADS w portalu. Ads obejmuje funkcje wykrywania i klasyfikowania poufnych danych, wykrywania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania anomalii, które mogą wskazywać na zagrożenie dla bazy danych.

Alternatywnie możesz włączyć i na pokładzie danych, aby Azure Sentinel.

- [Opis zaawansowanej ochrony przed zagrożeniami i alertów dla Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć Advanced Data Security dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** użytkownicy są uwierzytelniani przy użyciu usługi Azure Active Directory (Azure AD) lub uwierzytelniania SQL.

Podczas pierwszego wdrażania Azure SQL należy określić identyfikator logowania administratora i skojarzone hasło dla tego identyfikatora logowania. To konto administracyjne jest nazywane administratorem serwera. Konta administratorów bazy danych można zidentyfikować, otwierając Azure Portal i przechodząc do karty właściwości serwera lub wystąpienia zarządzanego. Możesz również skonfigurować konto administratora usługi Azure AD z pełnymi uprawnieniami administracyjnymi. Jest to wymagane, jeśli chcesz włączyć uwierzytelnianie usługi Azure AD.

W przypadku operacji zarządzania użyj wbudowanych ról platformy Azure, które muszą być jawnie przypisane. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Uwierzytelnianie na SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Tworzenie kont dla użytkowników niebędących użytkownikami administracyjnymi](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Używanie konta usługi Azure AD do uwierzytelniania](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak zarządzać istniejącymi identyfikatorami logowania i kontami administratora w Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Active Directory (Azure AD) nie ma koncepcji haseł domyślnych. Podczas aprowizowania dedykowanej puli SQL zaleca się zintegrowanie uwierzytelniania z usługą Azure AD. W przypadku tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i żąda, aby usługa korzystała z informacji o poświadczeniach przechowywanych w usłudze Azure AD.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Opis uwierzytelniania w Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych, które logują się za pośrednictwem Azure Active Directory (Azure AD).

Aby zidentyfikować konta administratorów bazy danych, otwórz Azure Portal, a następnie przejdź do karty Właściwości serwera lub wystąpienia zarządzanego.

- [Opis Azure Security Center tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Jak zarządzać istniejącymi nazwami logowania i kontami administratorów w Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Użyj Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** Użyj rejestracji aplikacji platformy Azure (jednostki usługi), aby pobrać token, który może służyć do interakcji z magazynem danych na płaszczyźnie sterowania (Azure Portal) za pośrednictwem wywołań interfejsu API.

- [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Jak zarejestrować aplikację klienta (jednostkę usługi) w usłudze Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse interfejsu API REST sql](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na Azure Active Directory danych

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z Azure Security Center dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

- [Opis uwierzytelniania wieloskładnikowego w Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** używanie stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7. Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** raporty Azure Active Directory (Azure AD) dotyczące generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku.

Użyj zaawansowanej ochrony przed zagrożeniami dla programu Azure SQL Database w połączeniu z usługą Azure Security Center, aby wykrywać nietypowe i potencjalnie szkodliwe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

SQL Server inspekcji umożliwia tworzenie inspekcji serwerów, które mogą zawierać specyfikacje inspekcji serwera dla zdarzeń na poziomie serwera i specyfikacje inspekcji bazy danych dla zdarzeń na poziomie bazy danych. Zdarzenia inspekcji można zapisywać w dziennikach zdarzeń lub w plikach inspekcji.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować aktywność użytkowników w zakresie tożsamości i dostępu w Azure Security Center](../security-center/security-center-identity-access.md)

- [Zapoznaj się z zaawansowaną ochroną przed zagrożeniami i potencjalnymi alertami](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Informacje o logowaniach i kontach użytkowników w Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Opis SQL Server inspekcji](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego o nazwanych lokalizacjach, aby zezwolić na dostęp do portalu i usługi Azure Resource Management tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Tworzenie administratora usługi Azure Active Directory (Azure AD) dla serwera Azure SQL Database w dedykowanej puli SQL.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktywnego konta. Ponadto przeglądy dostępu usługi Azure AD są skuteczne do zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczany w roli niestandardowej bazy danych z określonymi uprawnieniami odpowiednimi dla tej grupy użytkowników.

- [Jak używać przeglądów dostępu](../active-directory/governance/access-reviews-overview.md)

- [Informacje o logowaniach i kontach użytkowników w Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Konfigurowanie uwierzytelniania usługi Azure Active Directory (Azure AD) za pomocą usługi Azure SQL i włączanie ustawień diagnostycznych dla kont użytkowników usługi Azure AD, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Log Analytics. Konfigurowanie żądanych alertów w u użytkownikach usługi Log Analytics.

Podczas korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczany w niestandardowej roli bazy danych z określonymi uprawnieniami odpowiednimi dla tej grupy użytkowników.

- [Jak używać przeglądów dostępu](../active-directory/governance/access-reviews-overview.md)

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Informacje o logowaniach i kontach użytkowników w Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania do konta

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) Identity Protection i funkcji wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto należy wdowy i pozysć dane do Azure Sentinel do dalszego badania.

Podczas korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczany w niestandardowej roli bazy danych z określonymi uprawnieniami odpowiednimi dla tej grupy użytkowników.

- [Jak wyświetlić logowania o ryzyku związane z usługą Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak Azure Sentinel](../sentinel/connect-data-sources.md)

- [Informacje o logowaniach i kontach użytkowników w Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych związanych z usługą Azure SQL Database w dedykowanej puli SQL, usługa Azure Skrytka klienta udostępnia interfejs do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych.

- [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Klasyfikacja &amp; odnajdywania danych jest wbudowana w Azure Synapse SQL. Zapewnia zaawansowane możliwości w zakresie odnajdywania, klasyfikowania, etykietowania i raportowania danych poufnych w bazach danych.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Informacje o klasyfikacji &amp; odnajdywania danych](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Zaim implementuj oddzielne subskrypcje i/lub grupy zarządzania na rzecz testowania, testowania i tworzenia aplikacji. Zasoby powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń lub Azure Firewall. Zasoby przechowujące lub przetwarzające poufne dane powinny być izolowane. Użyj Private Link; Wdrażanie serwera Azure SQL w sieci wirtualnej i nawiązywanie bezpiecznego połączenia przy użyciu Private Link.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Jak skonfigurować Private Link dla Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** W przypadku Azure SQL Database w dedykowanej puli SQL przechowującej lub przetwarzające informacje poufne oznacz bazę danych i powiązane zasoby jako poufne przy użyciu tagów. Skonfiguruj Private Link w połączeniu z tagami usługi sieciowej grupy zabezpieczeń w wystąpieniach Azure SQL Database, aby zapobiec eksfiltracji poufnych informacji.

Ponadto usługi Advanced Threat Protection dla Azure SQL Database, Azure SQL Managed Instance i Azure Synapse wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Jak skonfigurować sieciowe Private Link sieciowe, aby zapobiec eksfiltracji danych na Azure SQL Database wystąpieniach](../azure-sql/database/private-endpoint-overview.md)

- [Opis zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** użyj funkcji klasyfikacji Azure Synapse SQL Data &amp; Discovery. Klasyfikacja odnajdywania danych zapewnia zaawansowane możliwości wbudowane Azure SQL Database odnajdywania, klasyfikowania i etykietowania ochrony poufnych danych &amp; &amp; w bazach danych.

Klasyfikacja odnajdywania danych jest częścią oferty Advanced Data Security, która jest ujednoliconym pakietem zaawansowanych &amp; funkcji zabezpieczeń SQL. Dostęp do &amp; klasyfikacji odnajdywania danych i zarządzanie nimi można uzyskać za pośrednictwem centralnego portalu programu SQL ADS.

Ponadto można skonfigurować zasady dynamicznego maskowania danych (DDM) w Azure Portal. Aparat zaleceń DDM flaguje niektóre pola z bazy danych jako potencjalnie poufne pola, które mogą być dobrymi kandydatami do maskowania.

- [Jak używać odnajdywania i klasyfikacji danych na Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Opis dynamicznego maskowania danych dla Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6. Kontrola dostępu RBAC platformy Azure umożliwia kontrolowanie dostępu do zasobów 

**Wskazówki:** Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zarządzać dostępem do Azure SQL baz danych w dedykowanej puli SQL.

Autoryzacja jest kontrolowana przez członkostwo w roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień.

- [Jak zintegrować Azure SQL Server z usługą Azure Active Directory (Azure AD) w celu uwierzytelniania](../azure-sql/database/authentication-aad-overview.md)

- [Jak kontrolować dostęp w programie Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Opis autoryzacji i uwierzytelniania w Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Szyfrowanie Transparent Data Encryption (TDE) ułatwia ochronę Azure Synapse SQL przed zagrożeniem złośliwych działań w trybie offline przez szyfrowanie danych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Na platformie Azure ustawieniem domyślnym dla opcji TDE jest to, że certyfikat DEK jest chroniony przez wbudowany certyfikat serwera. Alternatywnie można użyć funkcji TDE zarządzanej przez klienta, nazywanej również obsługą funkcji Bring Your Own Key (BYOK) dla funkcji TDE. W tym scenariuszu funkcji ochrony TDE, która szyfruje klucz szyfrowania kluczy, jest zarządzany przez klienta klucz asymetryczny, który jest przechowywany w magazynie kluczy należącym do klienta i zarządzanym przez klienta Azure Key Vault (zewnętrznym systemie zarządzania kluczami opartym na chmurze platformy Azure) i nigdy nie opuszcza magazynu kluczy.

- [Informacje o przezroczystym szyfrowaniu danych zarządzanym przez usługę](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Informacje o przezroczystym szyfrowaniu danych zarządzanym przez klienta](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Jak włączyć TDE przy użyciu własnego klucza](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące ich

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają miejsce w wystąpieniach produkcyjnych pul Synapse SQL i innych krytycznych lub powiązanych zasobów.

Ponadto możesz skonfigurować alerty dla baz danych w puli usługi SQL Synapse przy użyciu Azure Portal. Alerty mogą wysyłać wiadomości e-mail lub wywołać element web hook, gdy metryka (na przykład rozmiar bazy danych lub użycie procesora CPU) osiągnie wartość progową.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Jak tworzyć alerty dla Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Włącz obsługę Advanced Data Security i postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na Azure SQL Baz danych.

- [Jak uruchamiać oceny luk w zabezpieczeniach w Azure SQL danych](../azure-sql/database/sql-vulnerability-assessment.md)

- [Jak włączyć Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Porównanie skanów luk w zabezpieczeniach typu "wstecz"

**Wskazówki:** Ocena luk w zabezpieczeniach to usługa skanowania wbudowana w Azure Synapse SQL. Usługa wykorzystuje bazę wiedzy reguł, która flaguje luki w zabezpieczeniach. Wyróżnia odchylenia od najlepszych rozwiązań, takich jak błędy konfiguracji, nadmierne uprawnienia i niechronione dane poufne.  Dostęp do oceny luk w zabezpieczeniach można uzyskać za pośrednictwem centralnego portalu usługi SQL Advanced Data Security (ADS).

- [Eksportowanie skanów oceny luk w zabezpieczeniach i zarządzanie nimi w portalu usługi SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj domyślnych klasyfikacji ryzyka (Secure Score) dostarczonych przez Azure Security Center.

Klasyfikacja &amp; odnajdywania danych jest wbudowana w Azure Synapse SQL. Zapewnia zaawansowane możliwości w zakresie odnajdywania, klasyfikowania, etykietowania i raportowania danych poufnych w bazach danych.

- [Opis Azure Security Center bezpieczeństwa](../security-center/secure-score-security-controls.md)

- [Informacje o klasyfikacji &amp; odnajdywania danych](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów związanych z dedykowaną pulą SQL w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Azure Resource Graph za pośrednictwem usługi Azure Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, które dają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne, do organizowania i śledzenia zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Zdefiniuj listę zatwierdzonych zasobów platformy Azure związanych z dedykowaną pulą SQL.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby określić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager przez skonfigurowanie "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Sql", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji zasobów związanych z dedykowaną pulą SQL. Możesz również korzystać z wbudowanych definicji zasad dla baz danych/serwera platformy Azure, takich jak:

- Wdrażanie wykrywania zagrożeń na serwerach SQL
- SQL Server używać punktu końcowego usługi dla sieci wirtualnej

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy, użyj Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać jego kodem.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Nie dotyczy; Azure Synapse SQL nie ma konfigurowalnych ustawień zabezpieczeń.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Skorzystaj z Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla wszystkich zasobów związanych z dedykowaną pulą SQL.

- [Jak skorygować rekomendacje w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Transparent Data Encryption (TDE) z kluczami zarządzanymi przez klienta w programie Azure Key Vault umożliwia szyfrowanie automatycznie generowanego klucza szyfrowania bazy danych (DEK) przy użyciu zarządzanego przez klienta klucza asymetrycznego o nazwie Funkcja ochrony TDE. Jest to również ogólnie określane jako obsługa Bring Your Own Key (BYOK) dla Transparent Data Encryption. W scenariuszu BYOK funkcji ochrony TDE są przechowywane w należących do klienta i zarządzanych Azure Key Vault. Ponadto upewnij się, że na Azure Key Vault włączono usuwanie Azure Key Vault.

- [Jak włączyć funkcję TDE z kluczem zarządzanym przez klienta z Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Jak włączyć usuwanie nie soft w Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** korzystanie z tożsamości zarządzanych w celu zapewnienia usługom platformy Azure automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Synapse SQL); Jednak nie jest on uruchamiany w przypadku zawartości klienta.

Wstępnie przeskanuj zawartość przesyłaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure SQL Server itp. Firma Microsoft nie może uzyskać dostępu do twoich danych w tych wystąpieniach.

- [Opis Microsoft Antimalware dla Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Migawki dedykowanej puli SQL są tworzone automatycznie przez cały dzień, tworząc punkty przywracania dostępne przez siedem dni. Nie można zmienić tego okresu przechowywania. Dedykowana pula SQL obsługuje osiem godzin celu punktu odzyskiwania (RPO, Recovery Point Objective). Magazyn danych w regionie podstawowym można przywrócić z dowolnej migawki wykonanej w ciągu ostatnich siedmiu dni. Należy pamiętać, że w razie potrzeby można również ręcznie wyzwolić migawki.

- [Tworzenie kopii zapasowej i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Migawki magazynu danych są tworzone automatycznie przez cały dzień, tworząc punkty przywracania dostępne przez siedem dni. Tego okresu przechowywania nie można zmienić. Dedykowana pula SQL obsługuje osiem godzin celu punktu odzyskiwania (RPO). Magazyn danych w regionie podstawowym można przywrócić z dowolnej migawki wykonanej w ciągu ostatnich siedmiu dni. Należy pamiętać, że w razie potrzeby można również ręcznie wyzwolić migawki.

Jeśli używasz klucza zarządzanego przez klienta do szyfrowania klucza szyfrowania bazy danych, upewnij się, że kopię zapasową klucza jest dawana.

- [Tworzenie kopii zapasowej i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

- [Jak utworzyć kopię zapasową Azure Key Vault kluczy](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** okresowo testuj punkty przywracania, aby upewnić się, że migawki są prawidłowe. Aby przywrócić istniejącą dedykowaną pulę SQL z punktu przywracania, możesz użyć Azure Portal programu PowerShell. Przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Jak przywrócić Azure Key Vault kluczy](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Tworzenie kopii zapasowej i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

- [Jak przywrócić istniejącą dedykowaną pulę SQL](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** W usłudze Azure SQL Database można skonfigurować pojedynczą bazę danych lub bazę danych w puli z zasadami długoterminowego przechowywania kopii zapasowych (LTR), aby automatycznie przechowywać kopie zapasowe bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywać w sposób przezroczysty przy użyciu 256-bitowego szyfrowania AES, jednego z najmocniejszych dostępnych szyfrów blokowych i są zgodne ze standardem FIPS 140-2.

Domyślnie dane na koncie magazynu są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Do szyfrowania danych możesz polegać na kluczach zarządzanych przez firmę Microsoft lub zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zarządzasz własnymi kluczami za pomocą Key Vault, upewnij się, że jest włączone usuwanie nie soft-delete.

- [Zarządzanie Azure SQL Database długoterminowego przechowywania kopii zapasowych](../azure-sql/database/long-term-backup-retention-configure.md)

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

- [Jak włączyć usuwanie nie soft in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują role personelu, a także fazy obsługi zdarzeń/zarządzania nimi.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność alertom, aby ułatwić ustalanie priorytetów kolejności, w jakiej są one związane z każdym alertem, tak aby w przypadku naruszenia bezpieczeństwa zasobu można było uzyskać do niego dostęp od razu. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub metryki użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Możesz zapoznać się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że do Twoich danych dostęp uzyskał nieupoważniony lub niedozwolony podmiot.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** Postępuj zgodnie z zasadami firmy Microsoft w zakresie zaangażowania, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Więcej informacji na temat strategii firmy Microsoft i wykonywania czerwonego zespołu oraz testów penetracyjnych na żywo dotyczących infrastruktury, usług i aplikacji w chmurze zarządzanej przez firmę Microsoft można znaleźć tutaj](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

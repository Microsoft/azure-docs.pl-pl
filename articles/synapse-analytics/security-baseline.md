---
title: Podstawowa usługa Azure Security Synapse Analytics
description: Linia bazowa zabezpieczeń Synapse Analytics zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586935"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Podstawowa usługa Azure Security Synapse Analytics

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) do Synapse Analytics. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące analizy Synapse. Nie zostały wykluczone **kontrolki** , które mają zastosowanie do analizy Synapse.

 
Aby dowiedzieć się, jak usługa Synapse Analytics została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [plikiem mapowania pełnego zabezpieczenia Synapse analizy](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: zabezpiecz swoje SQL Server platformy Azure w sieci wirtualnej za pośrednictwem prywatnego linku. Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą porusza się w sieci szkieletowej firmy Microsoft.

Alternatywnie, w przypadku nawiązywania połączenia z pulą SQL Synapse, należy zawęzić zakres połączeń wychodzących do bazy danych SQL przy użyciu sieciowej grupy zabezpieczeń. Wyłącz cały ruch usługi platformy Azure do bazy danych SQL za pośrednictwem publicznego punktu końcowego przez ustawienie opcji Zezwalaj na wyłączanie usług platformy Azure. Upewnij się, że żadne publiczne adresy IP nie są dozwolone w regułach zapory.

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Opis prywatnego linku dla usługi Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: podczas nawiązywania połączenia z dedykowaną pulą SQL i włączeniu dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dzienniki są wysyłane do konta usługi Azure Storage na potrzeby inspekcji ruchu.

Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: używanie zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Synapse SQL. ATP wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić różne alerty, takie jak "potencjalna iniekcja SQL" i "dostęp z nietypowej lokalizacji". ATP jest częścią oferty Advanced Data Security (ADS) i jest dostępna i zarządzana za pośrednictwem centralnego portalu SQL ADS.

Włącz Standard DDoS Protection w sieciach wirtualnych skojarzonych z usługą Azure Synapse SQL w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Opis usługi Azure Synapse SQL w usłudze ATP](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć zaawansowane zabezpieczenia danych dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Omówienie usługi AD](../azure-sql/database/azure-defender-for-sql.md)

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: w przypadku nawiązywania połączenia z dedykowaną pulą SQL i włączeniu dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) należy wysłać dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Dzienniki przepływów można również wysłać do obszaru roboczego Log Analytics lub przesłać strumieniowo do Event Hubs.  Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: używanie zaawansowanej ochrony przed zagrożeniami (ATP) dla usługi Azure Synapse SQL. ATP wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić różne alerty, takie jak "potencjalna iniekcja SQL" i "dostęp z nietypowej lokalizacji". ATP jest częścią oferty Advanced Data Security (ADS) i jest dostępna i zarządzana za pośrednictwem centralnego portalu SQL ADS. ATP integruje także alerty z Azure Security Center.

- [Opis usługi Azure Synapse SQL w usłudze ATP](../azure-sql/database/threat-detection-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

W przypadku korzystania z punktu końcowego usługi dla dedykowanej puli SQL wymagane są wychodzące do publicznych adresów IP w usłudze Azure SQL Database: należy otworzyć grupy zabezpieczeń sieci (sieciowych grup zabezpieczeń) w Azure SQL Database celu umożliwienia łączności z adresami IP. Można to zrobić za pomocą tagów usługi sieciowej grupy zabezpieczeń dla Azure SQL Database.

- [Opis tagów usługi z punktami końcowymi usługi dla Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla zasobów związanych z dedykowaną pulą SQL z Azure Policy. Możesz użyć przestrzeni nazw "Microsoft. SQL", aby zdefiniować niestandardowe definicje zasad lub użyć dowolnych wbudowanych definicji zasad przeznaczonych dla ochrony sieci usługi Azure SQL Database/serwera. Przykładem odpowiednich wbudowanych zasad zabezpieczeń sieci dla serwera Azure SQL Database powinna być: "SQL Server powinna używać punktu końcowego usługi sieci wirtualnej".

Korzystając z planów platformy Azure, można uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony zarządzania zasobami platformy Azure, kontrola dostępu oparta na rolach (RBAC) i zasady, w ramach jednej definicji planu. Łatwo stosuj plan do nowych subskrypcji i środowisk, a następnie dostosuj kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z dedykowaną pulą SQL. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: zasady inspekcji można zdefiniować dla konkretnej bazy danych lub jako domyślne zasady serwera na platformie Azure (która hostuje usługę Azure Synapse). Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

Jeśli Inspekcja serwera jest włączona, zawsze ma zastosowanie do bazy danych programu. Baza danych będzie poddawana inspekcji, niezależnie od ustawień inspekcji bazy danych.

Po włączeniu inspekcji można zapisać je w dzienniku inspekcji na koncie usługi Azure Storage, w obszarze roboczym Log Analytics lub Event Hubs.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak skonfigurować inspekcję zasobów usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włącz inspekcję na poziomie programu Azure SQL Server dla dedykowanej puli SQL i wybierz lokalizację magazynu dla dzienników inspekcji (Azure storage, Log Analytics lub Event Hubs).

Inspekcję można włączyć zarówno na poziomie bazy danych, jak i na serwerze, a następnie można ją włączyć tylko na poziomie serwera, chyba że wymagane jest skonfigurowanie osobnego ujścia danych lub przechowywanie dla określonej bazy danych.

- [Jak włączyć inspekcję dla Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Jak włączyć inspekcję serwera](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Różnice w zasadach inspekcji na poziomie serwera i na poziomie bazy danych](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: podczas przechowywania dzienników związanych z dedykowaną pulą SQL na koncie magazynu, w obszarze roboczym log Analytics lub w centrach zdarzeń Ustaw okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Jak ustawić parametry przechowywania dzienników w obszarze roboczym Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Przechwytywanie zdarzeń przesyłania strumieniowego w Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowych zachowań i regularne przeglądy wyników. Użyj zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database w połączeniu z Azure Security Center, aby otrzymywać alerty dotyczące nietypowej aktywności związanej z usługą SQL Database. Alternatywnie możesz skonfigurować alerty na podstawie wartości metryk lub wpisów dziennika aktywności platformy Azure związanych z bazą danych SQL.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Poznaj zaawansowaną ochronę przed zagrożeniami i alerty dla Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć zaawansowane zabezpieczenia danych dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Jak skonfigurować alerty niestandardowe dla Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure SQL Database w połączeniu z Azure Security Center do monitorowania nietypowej aktywności. ATP jest częścią oferty Advanced Data Security (ADS) i jest dostępna i zarządzana za pośrednictwem centralnych reklam programu SQL w portalu. Usługa ADS zawiera funkcje do odnajdywania i klasyfikowania poufnych danych, umieszczania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Poznaj zaawansowaną ochronę przed zagrożeniami i alerty dla Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak włączyć zaawansowane zabezpieczenia danych dla Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: użytkownicy są uwierzytelniani za pomocą usługi Azure Active Directory (Azure AD) lub uwierzytelniania SQL.

Podczas pierwszego wdrażania usługi Azure SQL należy określić nazwę logowania administratora i skojarzone hasło dla tej nazwy logowania. To konto administracyjne nosi nazwę administrator serwera. Można zidentyfikować konta administratorów dla bazy danych, otwierając Azure Portal i przechodząc do karty właściwości serwera lub wystąpienia zarządzanego. Możesz również skonfigurować konto administratora usługi Azure AD z pełnymi uprawnieniami administracyjnymi, co jest wymagane, jeśli chcesz włączyć uwierzytelnianie w usłudze Azure AD.

W przypadku operacji zarządzania Użyj wbudowanych ról platformy Azure, które muszą być jawnie przypisane. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Uwierzytelnianie dla SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Tworzenie kont dla użytkowników niebędących administratorami](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Korzystanie z konta usługi Azure AD na potrzeby uwierzytelniania](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak zarządzać istniejącymi nazwami logowania i kontami administratora w usłudze Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Po zainicjowaniu obsługi dedykowanej puli SQL zaleca się wybranie integracji uwierzytelniania z usługą Azure AD. Przy użyciu tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i żąda, aby usługa korzystała z informacji o poświadczeniach przechowywanych w usłudze Azure AD.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim przy użyciu usługi Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Informacje o uwierzytelnianiu w usłudze Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych, które logują się za pośrednictwem Azure Active Directory (Azure AD).

Aby zidentyfikować konta administratorów dla bazy danych, Otwórz Azure Portal i przejdź do karty właściwości serwera lub wystąpienia zarządzanego.

- [Informacje o tożsamości i dostępie Azure Security Center](../security-center/security-center-identity-access.md)

- [Jak zarządzać istniejącymi nazwami logowania i kontami administratora w usłudze Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Użyj rejestracji aplikacji platformy Azure (nazwy głównej usługi), aby pobrać token, który może służyć do współpracy z magazynem danych na płaszczyźnie kontroli (Azure Portal) za pośrednictwem wywołań interfejsu API.

- [Jak wywołać interfejsy API REST platformy Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Jak zarejestrować aplikację kliencką (nazwę główną usługi) za pomocą Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informacje o interfejsie API REST usługi Azure Synapse SQL](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

- [Opis uwierzytelniania wieloskładnikowego w usłudze Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) na potrzeby generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Użyj zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database w połączeniu z Azure Security Center, aby wykrywać nietypowe działania i generować alerty o nietypowych i potencjalnie szkodliwych próbach dostępu do baz danych lub ich wykorzystywania.

Inspekcja SQL Server umożliwia tworzenie inspekcji serwera, które mogą zawierać specyfikacje inspekcji serwera dla zdarzeń na poziomie serwera i specyfikacje inspekcji bazy danych dla zdarzeń na poziomie bazy danych. Zdarzenia inspekcji można zapisywać w dziennikach zdarzeń lub w celu inspekcji plików.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

- [Przejrzyj zaawansowaną ochronę przed zagrożeniami i potencjalne alerty](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Informacje o identyfikatorach logowania i kontach użytkowników w usłudze Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Omówienie inspekcji SQL Server](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić portalowi i dostępowi do usługi Azure Resource Management tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Utwórz administratora usługi Azure Active Directory (Azure AD) dla serwera Azure SQL Database w dedykowanej puli SQL.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim przy użyciu usługi Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych programu. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczony w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

- [Jak korzystać z przeglądów dostępu](../active-directory/governance/access-reviews-overview.md)

- [Informacje o identyfikatorach logowania i kontach użytkowników w usłudze Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: konfigurowanie uwierzytelniania Azure Active Directory (Azure AD) za pomocą usługi Azure SQL i Włączanie ustawień diagnostycznych dla kont użytkowników usługi Azure AD, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego log Analytics. Skonfiguruj żądane alerty w Log Analytics.

W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych programu. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczony w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

- [Jak korzystać z przeglądów dostępu](../active-directory/governance/access-reviews-overview.md)

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Informacje o identyfikatorach logowania i kontach użytkowników w usłudze Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Ponadto dane na płycie oraz pozyskiwanie danych na platformie Azure — wskaźnik do dalszej analizy.

W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych programu. Upewnij się, że co najmniej jeden użytkownik bazy danych jest umieszczony w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

- [Jak wyświetlić informacje o ryzyku usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/connect-data-sources.md)

- [Informacje o identyfikatorach logowania i kontach użytkowników w usłudze Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych związanych z Azure SQL Database w dedykowanej puli SQL, usługa Azure skrytka klienta udostępnia interfejs umożliwiający przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych.

- [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Klasyfikacja odnajdywania danych &amp; jest wbudowana w usługę Azure Synapse SQL. Zapewnia zaawansowane możliwości w zakresie odnajdywania, klasyfikowania, etykietowania i raportowania danych poufnych w bazach danych.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Zrozumienie klasyfikacji odnajdywania danych &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone w ramach sieciowej grupy zabezpieczeń lub zapory platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być izolowane. Użyj linku prywatnego; Wdróż SQL Server platformy Azure w sieci wirtualnej i łącz się bezpiecznie przy użyciu linku prywatnego.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

- [Jak skonfigurować link prywatny dla Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: dla dowolnych Azure SQL Database w dedykowanej puli SQL przechowujących lub przetwarzających informacje poufne należy oznaczyć bazę danych i powiązane zasoby jako poufne przy użyciu tagów. Skonfiguruj prywatne łącze w połączeniu z tagami usługi Network Security Group (sieciowej grupy zabezpieczeń) w wystąpieniach Azure SQL Database, aby zapobiec eksfiltracji informacji poufnych.

Ponadto Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database, wystąpienie zarządzane SQL Azure i usługa Azure Synapse wykrywają anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Jak skonfigurować link prywatny i sieciowych grup zabezpieczeń, aby zapobiec eksfiltracji danych w wystąpieniach Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

- [Zapoznaj się z zaawansowaną ochroną przed zagrożeniami Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: korzystanie z funkcji klasyfikacji usługi Azure Synapse SQL Data Discovery &amp; . Klasyfikacja odnajdywania danych &amp; zapewnia zaawansowane funkcje wbudowane w Azure SQL Database do odnajdywania, klasyfikowania i etykietowania &amp; ochrony poufnych danych w bazach danych.

Klasyfikacja odnajdywania danych &amp; jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Klasyfikację odnajdywania danych &amp; można uzyskać i zarządzać nią za pośrednictwem centralnego portalu SQL ads.

Ponadto można skonfigurować zasady dynamicznego maskowania danych (DDM) w Azure Portal. Aparat zaleceń DDM oflagowuje niektóre pola z bazy danych jako potencjalnie poufne pola, które mogą być dobrymi kandydatami do maskowania.

- [Jak używać funkcji odnajdywania i klasyfikowania danych dla platformy Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Opis dynamicznego maskowania danych dla usługi Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: Użyj kontroli dostępu RBAC na platformie Azure w celu kontrolowania dostępu do zasobów 

**Wskazówki**: Używanie kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure w celu zarządzania dostępem do baz danych Azure SQL Database w dedykowanej puli SQL.

Autoryzacja jest kontrolowana za pomocą członkostw roli bazy danych konta użytkownika i uprawnień na poziomie obiektów. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień.

- [Jak zintegrować usługę Azure SQL Server z usługą Azure Active Directory (Azure AD) na potrzeby uwierzytelniania](../azure-sql/database/authentication-aad-overview.md)

- [Jak kontrolować dostęp w usłudze Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Informacje o autoryzacji i uwierzytelnianiu w usłudze Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: przezroczyste szyfrowanie danych (TDE) pomaga chronić usługę Azure Synapse SQL przed zagrożeniem złośliwego działania w trybie offline przez szyfrowanie danych przechowywanych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Na platformie Azure domyślne ustawienie dla TDE polega na tym, że klucz szyfrowania danych jest chroniony przez wbudowany certyfikat serwera. Alternatywnie można korzystać z TDE zarządzanych przez klienta, nazywanego również Bring Your Own Key (BYOK) dla TDE. W tym scenariuszu funkcja ochrony TDE, która szyfruje plik szyfrowania danych, jest kluczem asymetrycznym zarządzanym przez klienta, który jest przechowywany w Azure Key Vault należącym do klienta i zarządzanym (zewnętrznym systemie zarządzania kluczami opartymi na chmurze platformy Azure) i nigdy nie opuszcza magazynu kluczy.

- [Informacje o przezroczystym szyfrowaniu danych zarządzanym przez usługę](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Informacje o przezroczystym szyfrowaniu danych zarządzanych przez klienta](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Jak włączyć TDE przy użyciu własnego klucza](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych puli SQL Synapse i innych krytycznych lub powiązanych zasobów.

Ponadto można skonfigurować alerty dla baz danych w puli usługi SQL Synapse przy użyciu Azure Portal. Alerty mogą wysyłać wiadomość e-mail lub wywoływać element webhook, gdy pewna Metryka (na przykład rozmiar bazy danych lub użycie procesora) osiągnie wartość progową.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Tworzenie alertów dotyczących usługi Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Włącz zaawansowane zabezpieczenia danych i postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach w bazach danych Azure SQL Database.

- [Jak uruchamiać oceny luk w zabezpieczeniach w bazach danych Azure SQL](../azure-sql/database/sql-vulnerability-assessment.md)

- [Jak włączyć zaawansowane zabezpieczenia danych](../azure-sql/database/azure-defender-for-sql.md)

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Ocena luk w zabezpieczeniach to usługa skanowania wbudowana w usługę Azure Synapse SQL. Usługa korzysta z bazy wiedzy o regułach, które flagą luki w zabezpieczeniach. Wyróżniają one odchylenia od najlepszych rozwiązań, takich jak niezmienione konfiguracje, nadmierne uprawnienia i niechronione dane poufne.  Dostęp do oceny luk w zabezpieczeniach można uzyskać i zarządzać nimi za pośrednictwem centralnego portalu SQL Advanced Data Security (AD).

- [Zarządzanie i eksportowanie skanów oceny luk w zabezpieczeniach w portalu SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

Klasyfikacja odnajdywania danych &amp; jest wbudowana w usługę Azure Synapse SQL. Zapewnia zaawansowane możliwości w zakresie odnajdywania, klasyfikowania, etykietowania i raportowania danych poufnych w bazach danych.

- [Informacje na temat Azure Security Center zabezpieczeń](../security-center/secure-score-security-controls.md)

- [Zrozumienie klasyfikacji odnajdywania danych &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów związanych z dedykowaną pulą SQL w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

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

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure związanych z dedykowaną pulą SQL.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. SQL", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji zasobów związanych z dedykowaną pulą SQL. Możesz również używać wbudowanych definicji zasad dla baz danych/serwera platformy Azure, takich jak:

- Wdrażanie wykrywania zagrożeń na serwerach SQL
- SQL Server powinien używać punktu końcowego usługi sieci wirtualnej

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: nie dotyczy; Usługa Azure Synapse SQL nie ma konfigurowalnych ustawień zabezpieczeń.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: wykorzystanie Azure Security Center do wykonywania skanowania bazowego dla wszystkich zasobów związanych z dedykowaną pulą SQL.

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: transparent Data Encryption (TDE) z kluczami zarządzanymi przez klienta w programie Azure Key Vault umożliwiają szyfrowanie automatycznie generowanego klucza szyfrowania bazy danych (z kluczem asymetrycznym) przy użyciu klucza asymetrycznego zarządzanego przez klienta. Jest to również ogólnie określane jako Bring Your Own Key (BYOK) obsługa Transparent Data Encryption. W scenariuszu BYOK funkcja ochrony TDE jest przechowywana w Azure Key Vault należącym do klienta i zarządzana. Ponadto upewnij się, że w Azure Key Vault jest włączona funkcja usuwania nietrwałego.

- [Jak włączyć TDE z kluczem zarządzanym przez klienta z Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Jak włączyć usuwanie nietrwałe w Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Synapse SQL); nie jest on jednak uruchamiany w treści klienta.

Wstępnie Skanuj zawartość przekazywaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, SQL Server platformy Azure itd. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: migawki dedykowanej puli SQL są automatycznie podejmowane przez cały dzień tworzenia punktów przywracania dostępnych przez siedem dni. Nie można zmienić tego okresu przechowywania. Dedykowana Pula SQL obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych można przywrócić w regionie podstawowym z jednej z migawek wykonanych w ciągu ostatnich siedmiu dni. Należy zauważyć, że w razie potrzeby można także ręcznie wyzwolić migawki.

- [Tworzenie kopii zapasowych i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: migawki magazynu danych są automatycznie podejmowane przez cały dzień tworzenia punktów przywracania dostępnych przez siedem dni. Nie można zmienić tego okresu przechowywania. Dedykowana Pula SQL obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych można przywrócić w regionie podstawowym z jednej z migawek wykonanych w ciągu ostatnich siedmiu dni. Należy zauważyć, że w razie potrzeby można także ręcznie wyzwolić migawki.

Jeśli używasz klucza zarządzanego przez klienta do szyfrowania klucza szyfrowania bazy danych, upewnij się, że utworzono kopię zapasową klucza.

- [Tworzenie kopii zapasowych i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

- [Jak utworzyć kopię zapasową kluczy Azure Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowo Przetestuj punkty przywracania, aby upewnić się, że migawki są prawidłowe. Aby przywrócić istniejącą dedykowaną pulę SQL z punktu przywracania, można użyć Azure Portal lub programu PowerShell. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Tworzenie kopii zapasowych i przywracanie w dedykowanej puli SQL](sql-data-warehouse/backup-and-restore.md)

- [Jak przywrócić istniejącą dedykowaną pulę SQL](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: w Azure SQL Database można skonfigurować jedną lub bazę danych w puli z długoterminowymi zasadami przechowywania kopii zapasowych (LTR), aby automatycznie zachować kopie zapasowe bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.

Domyślnie dane na koncie magazynu są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania danych. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zarządzasz własnymi kluczami przy użyciu Key Vault, upewnij się, że jest włączona funkcja usuwania nietrwałego.

- [Zarządzanie długoterminowym przechowywaniem kopii zapasowych Azure SQL Database](../azure-sql/database/long-term-backup-retention-configure.md)

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

- [Jak włączyć usuwanie nietrwałe w Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub metryki używanej do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które doprowadziło do alertu.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Możesz zapoznać się z publikacją NIST: Przewodnik po testach, szkoleniu i wykonywaniu programów dla planów i możliwości IT.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

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

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w ramach infrastruktury, usług i aplikacji chmurowych firmy Microsoft, zobacz](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

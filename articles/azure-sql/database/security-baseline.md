---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure SQL Database
description: Punkt Azure SQL Database zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29db5b82d73bf96465581ccd6a663455464bbeb9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599574"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure SQL Database

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../../security/benchmarks/overview-v1.md) do Azure SQL Database. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure SQL Database. **Wykluczono** mechanizmy kontroli Azure SQL Database, za które odpowiada firma Microsoft. Aby zobaczyć, Azure SQL Database mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Monitor [mapowania punktów odniesienia zabezpieczeń.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** możesz włączyć usługę Azure Private Link, aby zezwolić na dostęp do usług PaaS platformy Azure (na przykład SQL Database) i usług partnerów hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. 

Aby zezwolić na ruch Azure SQL Database, użyj tagów usługi SQL, aby zezwolić na ruch wychodzący przez sieciowe grupy zabezpieczeń.

Reguły sieci wirtualnej Azure SQL Database akceptować tylko komunikację, która jest wysyłana z wybranych podsieci w sieci wirtualnej.

- [Jak skonfigurować Private Link dla Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [Jak używać reguł i punktów końcowych usługi dla sieci wirtualnej dla serwerów baz danych](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** użyj Azure Security Center i koryguj zalecenia dotyczące ochrony sieci dla podsieci, w Azure SQL Database serwer jest wdrożony. 

W przypadku usługi Azure Virtual Machines (VM), która będzie nawiązywać połączenie z wystąpieniem programu Azure SQL Database Server, włącz dzienniki przepływu sieciowej grupy zabezpieczeń dla sieciowych grup zabezpieczeń chroniących te maszyny wirtualne i wysyłaj dzienniki na konto usługi Azure Storage w celu inspekcji ruchu. 

Możesz również wysyłać dzienniki przepływu sieciowych grup zabezpieczeń do obszaru roboczego usługi Log Analytics i używać Analiza ruchu, aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania punktów aktywności, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Jak włączyć i używać Analiza ruchu](../../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz DDoS Protection Standardowa w sieciach wirtualnych skojarzonych z wystąpieniami usługi SQL Server w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

- [Jak skonfigurować ochronę przed DDoS](/azure/virtual-network/manage-ddos-protection)

- [Understand Azure Security Center Integrated Threat Intelligence](/azure/security-center/security-center-alerts-data-services)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** W przypadku usługi Azure Virtual Machines (maszyn wirtualnych), które będą nawiązywać połączenie z wystąpieniem usługi Azure SQL Database, włącz dzienniki przepływu sieciowej grupy zabezpieczeń dla sieciowych grup zabezpieczeń chroniących te maszyny wirtualne i wyślij dzienniki na konto usługi Azure Storage w celu inspekcji ruchu. Jeśli jest to wymagane do badania anomalii działań, włącz Network Watcher przechwytywania pakietów.

- [How to Enable NSG Flow Logs](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć Network Watcher](../../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamaniom (IDS/IPS)

**Wskazówki:** włączanie zaawansowanej ochrony przed zagrożeniami (ATP) dla Azure SQL Database.  Użytkownicy otrzymują alerty o podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach i atakach przez wstrzyknięcie kodu SQL, a także anomalie w zakresie dostępu do bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami integruje również alerty z Azure Security Center. 

- [Opis i używanie usługi Advanced Threat Protection dla Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** użyj tagów usługi dla sieci wirtualnej do zdefiniowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

W przypadku korzystania z punktów końcowych usługi dla usługi Azure SQL Database jest wymagany ruch wychodzący do publicznych adresów IP usługi Azure SQL Database: sieciowe grupy zabezpieczeń muszą być otwarte dla adresów IP usługi Azure SQL Database, aby umożliwić łączność. Można to zrobić przy użyciu tagów usługi sieciowej Azure SQL Database.

- [Opis tagów usługi z punktami końcowymi usługi dla Azure SQL Database](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [Opis tagów usługi i korzystanie z tych tagów](../../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie konfiguracji zabezpieczeń sieci dla wystąpień Azure SQL Database za pomocą Azure Policy. Możesz użyć przestrzeni nazw "Microsoft.Sql", aby zdefiniować niestandardowe definicje zasad, lub użyć dowolnej wbudowanej definicji zasad przeznaczonej do Azure SQL Database ochrony sieci serwera. Przykładem odpowiednich wbudowanych zasad zabezpieczeń sieci dla serwera Azure SQL Database może być: "SQL Server powinien używać punktu końcowego usługi dla sieci wirtualnej".

 

Użyj Azure Blueprints, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony usługi Azure Resource Management, kontrola dostępu oparta na rolach (RBAC) platformy Azure i zasady, w pojedynczej definicji strategii. Łatwe stosowanie strategii do nowych subskrypcji i środowisk oraz dostosowywanie kontroli i zarządzania za pomocą zarządzania wersjami.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak utworzyć usługę Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej organizacji sieciowej należy użyć pola "Opis", aby określić potrzebę biznesową i/lub czas trwania (itp.) dla wszystkich reguł, które zezwalają na ruch do/z sieci.

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone z tagami, i powiadamiać o istniejących nieotagowanych zasobach.

Możesz użyć interfejsu Azure PowerShell wiersza polecenia platformy Azure, aby szukać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych Azure SQL Database wystąpieniami serwera. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak tworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Włącz inspekcję dla Azure SQL Database, aby śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji na koncie usługi Azure Storage, w obszarze roboczym usługi Log Analytics lub Event Hubs.

Ponadto można przesyłać strumieniowo dane telemetryczne diagnostyki Azure SQL do rozwiązania w chmurze Azure SQL Analytics, które monitoruje wydajność usług Azure SQL Databases i Azure SQL Managed Instances na dużą skalę i w wielu subskrypcjach. Może ona ułatwić zbieranie i wizualizowanie Azure SQL Database wydajności oraz ma wbudowaną analizę na potrzeby rozwiązywania problemów z wydajnością.

- [Jak skonfigurować inspekcję dla Azure SQL Database](/azure/sql-database/sql-database-auditing)

- [Jak zbierać dzienniki i metryki platformy za pomocą Azure Monitor](/azure/sql-database/sql-database-metrics-diag-logging)

- [Jak przesyłać strumieniowo diagnostykę do Azure SQL Analytics](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Włącz inspekcję wystąpienia serwera Azure SQL Database i wybierz lokalizację magazynu dla dzienników inspekcji (Azure Storage, Log Analytics lub Event Hub).

- [Jak włączyć inspekcję dla Azure SQL Server](/azure/sql-database/sql-database-auditing)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Podczas przechowywania dzienników Azure SQL Database w obszarze roboczym usługi Log Analytics ustaw okres przechowywania dzienników zgodnie z przepisami dotyczącymi zgodności w organizacji.

- [Jak ustawić parametry przechowywania dzienników](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników. Użyj Azure Security Center Advanced Threat Protection firmy Azure Security Center alerty dotyczące nietypowych działań związanych Azure SQL Database wystąpieniem. Możesz też skonfigurować alerty na podstawie wartości metryk lub wpisów dziennika aktywności platformy Azure związanych Azure SQL Database wystąpieniami.

- [Opis usługi Advanced Threat Protection i alertów dla Azure SQL Server](/azure/sql-database/sql-database-threat-detection-overview)

- [Jak skonfigurować alerty niestandardowe dla Azure SQL Database](alerts-insights-configure-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** użyj Azure Security Center Advanced Threat Protection dla baz danych Azure SQL do monitorowania i alertów dotyczących anomalii. Włącz Azure Defender sql dla baz danych SQL. Azure Defender sql obejmuje funkcje odnajdywania i klasyfikowania poufnych danych, wykrywania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania anomalii działań, które mogą wskazywać na zagrożenie dla bazy danych.

- [Opis zaawansowanej ochrony przed zagrożeniami i alertów dla Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

- [Jak włączyć obsługę Azure Defender SQL dla Azure SQL Database](azure-defender-for-sql.md)

- [Jak zarządzać alertami w Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Active Directory (Azure AD) nie ma koncepcji haseł domyślnych. Podczas aprowizowania Azure SQL Database zalecane jest zintegrowanie uwierzytelniania z usługą Azure AD.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL](/azure/azure-sql/database/authentication-aad-configure)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie zasad i procedur dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center zarządzania tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

- [Opis Azure Security Center tożsamości i dostępu](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włączanie Azure Active Directory wieloskładnikowego (Azure AD) i postępuj zgodnie z Azure Security Center tożsamości i zarządzania dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** używanie stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** raporty Azure Active Directory (Azure AD) dotyczące generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku.

Użyj zaawansowanej ochrony przed zagrożeniami Azure SQL Database, aby wykrywać nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorować aktywność użytkowników w zakresie tożsamości i dostępu w Azure Security Center](../../security-center/security-center-identity-access.md)

- [Zapoznaj się z zaawansowaną ochroną przed zagrożeniami i potencjalnymi alertami](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego o nazwanych lokalizacjach, aby zezwolić na dostęp do portalu i usługi Azure Resource Management tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** tworzenie Azure Active Directory (Azure AD) dla wystąpień Azure SQL Database serwera.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL](authentication-aad-configure.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory-domain-services/tutorial-create-instance.md)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto za pomocą przeglądów dostępu tożsamości platformy Azure można wydajnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

- [Jak używać przeglądów dostępu do tożsamości platformy Azure](../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Konfigurowanie uwierzytelniania Azure Active Directory (Azure AD) za pomocą usługi Azure SQL i tworzenie ustawień diagnostycznych dla kont użytkowników usługi Azure AD, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Log Analytics. Skonfiguruj żądane alerty w obszarze roboczym usługi Log Analytics.

- [Jak skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nim za pomocą Azure SQL](authentication-aad-configure.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozysć dane do usługi Azure Sentinel do dalszej badania.

- [Jak wyświetlić logowania o ryzyku związane z usługą Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, usługa Azure Skrytka klienta udostępnia klientom interfejs do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klientów.

- [Opis Skrytka klienta](../../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie oddzielnych subskrypcji i/lub grup zarządzania na rzecz tworzenia, testowania i produkcji. Zasoby powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio otagowane i zabezpieczone w sieciowej sieciowej Azure Firewall. Zasoby przechowujące lub przetwarzające poufne dane powinny być izolowane. Użyj Private Link; Wd Azure SQL Server wewnątrz sieci wirtualnej i połącz się prywatnie przy użyciu prywatnych punktów końcowych.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak skonfigurować Private Link dla Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** w przypadku baz danych Azure SQL Database przechowywania lub przetwarzania poufnych informacji oznacz bazę danych i powiązane zasoby jako poufne przy użyciu tagów. Skonfiguruj Private Link w połączeniu z tagami usługi sieciowej grupy zabezpieczeń w wystąpieniach Azure SQL Database, aby zapobiec eksfiltracji poufnych informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Jak skonfigurować sieciowe Private Link sieciowe, aby zapobiec eksfiltracji danych w wystąpieniach Azure SQL Database sieciowych](/azure/sql-database/sql-database-private-endpoint-overview)

- [Informacje na temat ochrony danych klientów na platformie Azure](../../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Użyj funkcji odnajdywania Azure SQL Database klasyfikacji danych. Odnajdywanie i klasyfikacja danych zapewnia zaawansowane możliwości wbudowane Azure SQL Database odnajdywania, klasyfikowania i etykietowania chroniącego poufne dane &amp; w bazach danych.

- [Jak używać odnajdywania i klasyfikacji danych na Azure SQL Server](/azure/sql-database/sql-database-data-discovery-and-classification)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach

**Wskazówki:** używanie Azure Active Directory (Azure AD) do uwierzytelniania i kontrolowania dostępu do Azure SQL Database wystąpień.

- [Jak zintegrować serwer Azure SQL Server z usługą Azure AD w celu uwierzytelniania](/azure/sql-database/sql-database-aad-authentication)

- [Jak kontrolować dostęp na Azure SQL Server](/azure/sql-database/sql-database-control-access)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Transparent Data Encryption (TDE) pomaga chronić usługi Azure SQL Database, Azure SQL Managed Instance i Azure Data Warehouse przed zagrożeniami złośliwych działań w trybie offline przez szyfrowanie danych magazynowany. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Domyślnie funkcja TDE jest włączona dla wszystkich nowo wdrożonych baz danych w SQL Database i SQL Managed Instance. Kluczem szyfrowania TDE może zarządzać firma Microsoft lub klient.

- [Jak zarządzać przezroczystym szyfrowaniem danych i używać własnych kluczy szyfrowania](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają miejsce w wystąpieniach produkcyjnych Azure SQL Database i innych krytycznych lub powiązanych zasobach.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Włącz obsługę Azure Defender SQL for Azure SQL Database i postępuj zgodnie z zaleceniami firmy Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach na serwerach Azure SQL Server.

- [Jak uruchamiać oceny luk w zabezpieczeniach na Azure SQL Database](/azure/sql-database/sql-vulnerability-assessment)

- [Jak włączyć obsługę Azure Defender SQL](azure-defender-for-sql.md)

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Odpowiedzialność**: Klient

**Azure Security Center zabezpieczeń:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4. Porównanie skanów luk w zabezpieczeniach typu "wstecz"

**Wskazówki:** włącz okresowe skanowania cykliczne dla Azure SQL Database wystąpień; Spowoduje to skonfigurowanie oceny luk w zabezpieczeniach w celu automatycznego uruchamiania skanowania bazy danych raz w tygodniu. Podsumowanie wyników skanowania zostanie wysłane na podany adres e-mail. Porównaj wyniki, aby sprawdzić, czy luki w zabezpieczeniach zostały skorygowane.

- [Jak wyeksportować raport oceny luk w zabezpieczeniach w programie Azure Security Center](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj domyślnych klasyfikacji ryzyka (Secure Score) dostarczonych przez Azure Security Center.

- [Opis Azure Security Center bezpieczeństwa](/azure/security-center/security-center-secure-score)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph, aby odpytywać i odnajdywać wszystkie zasoby (w tym wystąpienia Azure SQL Server) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure można Resource Graph za pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, które dają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Do organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w terminowy sposób.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby określić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj Azure Policy lub Azure Security Center dla serwerów/baz danych Azure SQL, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy, użyj Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać jego kodem.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.SQL", aby utworzyć zasady niestandardowe służące do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Wykorzystaj Azure Security Center do przeprowadzania skanowania linii bazowej dla serwerów Azure SQL i baz danych.

- [Jak korygować zalecenia w Azure Security Center](/azure/security-center/security-center-sql-service-recommendations)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Użyj Azure Key Vault do przechowywania kluczy szyfrowania dla Azure SQL Database Transparent Data Encryption (TDE).

- [Jak chronić poufne dane przechowywane na Azure SQL Server i przechowywać klucze szyfrowania w Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Korzystanie z tożsamości zarządzanych w celu zapewnienia usługom platformy Azure automatycznie zarządzanej tożsamości w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w Azure Key Vault, bez żadnych poświadczeń w kodzie.

- [Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Jak skonfigurować tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service), ale nie działa w przypadku zawartości klienta.

Wstępnie przeskanuj zawartość przesyłaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure SQL Server itp. Firma Microsoft nie może uzyskać dostępu do twoich danych w tych wystąpieniach.

- [Opis Microsoft Antimalware dla Azure Cloud Services i Virtual Machines](../../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Aby chronić firmę przed utratą danych, program Azure SQL Database automatycznie co tydzień tworzy pełne kopie zapasowe bazy danych, różnicowe kopie zapasowe bazy danych co 12 godzin, a kopie zapasowe dziennika transakcji co 5–10 minut. Kopie zapasowe są przechowywane w magazynie RA-GRS przez co najmniej 7 dni dla wszystkich warstw usług. Wszystkie warstwy usług z wyjątkiem pomoc techniczna Basic przechowywania kopii zapasowych na czas przywracania do punktu w czasie, maksymalnie do 35 dni.

Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla kopii zapasowych tygodniowych, miesięcznych i/lub rocznie. Zużycie magazynu zależy od wybranej częstotliwości tworzenia kopii zapasowych i okresów przechowywania.

- [Understand backups and business continuity with Azure SQL Server](/azure/sql-database/sql-database-business-continuity)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** usługa Azure SQL Database automatycznie tworzy kopie zapasowe bazy danych przechowywane w okresie od 7 do 35 dni i używa magazynu geograficznie nadmiarowego dostępnego do odczytu platformy Azure (RA-GRS) w celu zapewnienia ich zachowywania nawet wtedy, gdy centrum danych jest niedostępne. Te kopie zapasowe są tworzone automatycznie. W razie potrzeby włącz długoterminowe geograficznie nadmiarowe kopie zapasowe dla Azure SQL Database.

Jeśli używasz kluczy zarządzanych przez klienta do Transparent Data Encryption, upewnij się, że kopię zapasową kluczy jest dawana.

- [Opis kopii zapasowych na Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Zapewnianie możliwości okresowego przywracania danych zawartości w ramach Azure Backup. W razie potrzeby przetestuj przywracanie zawartości do izolowanej sieci VLAN. Przetestuj przywracanie kopii zapasowej kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Jak odzyskać Azure SQL Database kopii zapasowych przy użyciu przywracania do punktu w czasie](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Włącz usuwanie nie soft na Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [How to enable soft delete in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują role personelu, a także fazy obsługi zdarzeń/zarządzania nimi.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność alertom, aby ułatwić ustalanie priorytetów kolejności, w jakiej są one związane z każdym alertem, tak aby w przypadku naruszenia zabezpieczeń zasobu można było uzyskać do niego dostęp od razu. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Możesz zapoznać się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1. Przeprowadzanie regularnych testów penetracyjnych zasobów platformy Azure i zapewnianie korygowania wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze. 

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

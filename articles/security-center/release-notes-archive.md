---
title: Archiwum nowości w programie Azure Security Center
description: Opis nowości i zmian w tym Azure Security Center sześć miesięcy temu i wcześniej.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/04/2021
ms.author: memildin
ms.openlocfilehash: 9d376a374d1934f55b6a6fb15f1642c81b30b2fc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718669"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiwum nowości w programie Azure Security Center?

Podstawowa [strona Co nowego w programie Azure Security Center?](release-notes.md) zawiera aktualizacje z ostatnich sześciu miesięcy, natomiast ta strona zawiera starsze elementy.

Ta strona zawiera informacje dotyczące:

- Nowe funkcje
- Poprawki błędów
- Funkcje uznane za przestarzałe


## <a name="october-2020"></a>Październik 2020 r.

Aktualizacje w październiku obejmują:
- [Ocena luk w zabezpieczeniach dla maszyn wirtualnych w środowisku lokalnym i w wielu chmurach (wersja zapoznawcza)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall zalecenie zostało dodane (wersja zapoznawcza)](#azure-firewall-recommendation-added-preview)
- [W rekomendacji usług Kubernetes Services należy zdefiniować autoryzowane zakresy adresów IP, aby szybko rozwiązać ten problem](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Pulpit nawigacyjny zgodności z przepisami zawiera teraz opcję usuwania standardów](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Usunięto tabelę Microsoft.Security/securityStatuses z Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Ocena luk w zabezpieczeniach dla maszyn wirtualnych w środowisku lokalnym i w wielu chmurach (wersja zapoznawcza)

[Azure Defender dla serwerów](defender-for-servers-introduction.md)zintegrowany skaner do oceny luk w zabezpieczeniach (obsługiwany przez firmę Qualys) teraz skanuje Azure Arc serwerów.

Po włączeniu funkcji Azure Arc na maszynach spoza platformy Azure usługa Security Center zaoferuje wdrożenie na nich zintegrowanego skanera luk w zabezpieczeniach — ręcznie i na dużą skalę.

Dzięki tej aktualizacji możesz uwolnić możliwości usługi **Azure Defender** serwerów w celu skonsolidowania programu zarządzania lukami w zabezpieczeniach dla wszystkich zasobów platformy Azure i spoza platformy Azure.

Główne możliwości:

- Monitorowanie stanu aprowizowania skanera va (oceny luk w zabezpieczeniach) na Azure Arc wirtualnych
- Aprowizowanie zintegrowanego agenta va na niechronionych maszynach z systemami Windows i Linux Azure Arc (ręcznie i na dużą skalę)
- Odbieranie i analizowanie wykrytych luk w zabezpieczeniach z wdrożonych agentów (ręcznie i na dużą skalę)
- Ujednolicone środowisko dla maszyn wirtualnych platformy Azure i Azure Arc wirtualnych

[Dowiedz się więcej o wdrażaniu zintegrowanego skanera luk w zabezpieczeniach na maszynach hybrydowych.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Dowiedz się więcej Azure Arc serwerach z obsługą usługi](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall zalecenie zostało dodane (wersja zapoznawcza)

Dodano nowe zalecenie w celu ochrony wszystkich sieci wirtualnych za pomocą Azure Firewall.

Zalecenie: **Sieci wirtualne powinny być** chronione przez Azure Firewall, aby ograniczyć dostęp do sieci wirtualnych i zapobiec potencjalnym zagrożeniom przy użyciu Azure Firewall.

Dowiedz się więcej o [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>W rekomendacji usług Kubernetes Services należy zdefiniować autoryzowane zakresy adresów IP, aby szybko je rozwiązać

Rekomendacja **Autoryzowane zakresy adresów IP powinna być zdefiniowana w usługach Kubernetes Services** teraz ma opcję szybkiej poprawki.

Aby uzyskać więcej informacji na temat tego zalecenia i wszystkich innych zaleceń dotyczących Security Center, zobacz Zalecenia dotyczące [zabezpieczeń — przewodnik informacyjny.](recommendations-reference.md)

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Autoryzowane zakresy adresów IP powinny być zdefiniowane w rekomendacji usług Kubernetes Services z opcją szybkiej poprawki":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Pulpit nawigacyjny zgodności z przepisami zawiera teraz opcję usuwania standardów

Security Center pulpit nawigacyjny zgodności z przepisami zapewnia wgląd w twój wskaźnik zgodności w oparciu o sposób spełniania określonych wymagań i kontroli zgodności.

Pulpit nawigacyjny zawiera domyślny zestaw standardów prawnych. Jeśli którykolwiek z podanych standardów nie jest istotny dla Twojej organizacji, teraz jest to prosty proces usuwania ich z interfejsu użytkownika dla subskrypcji. Standardy można usuwać tylko na *poziomie* subskrypcji; nie jest zakresem grupy zarządzania.

Aby dowiedzieć się [więcej, zobacz Usuwanie standardu z pulpitu nawigacyjnego.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Usunięto tabelę Microsoft.Security/securityStatuses z Azure Resource Graph (ARG)

Azure Resource Graph to usługa platformy Azure, która została zaprojektowana w celu zapewnienia efektywnej eksploracji zasobów z możliwością wykonywania zapytań na dużą skalę w danym zestawie subskrypcji, dzięki czemu można efektywnie zarządzać środowiskiem. 

Na Azure Security Center można używać usług ARG i [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) do wykonywania zapytań dotyczących szerokiego zakresu danych o stanie zabezpieczeń. Na przykład:

- Korzystanie ze spisu zasobów (ARG)
- Udokumentowano przykładowe zapytanie ARG dotyczące sposobu identyfikowania kont bez włączonego [uwierzytelniania wieloskładnikowego (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

W ramach funkcji ARG istnieją tabele danych, których można używać w zapytaniach.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer i dostępne tabele":::

> [!TIP]
> W dokumentacji ARG wymieniono wszystkie dostępne tabele w tabeli [Azure Resource Graph dokumentacji typów zasobów.](../governance/resource-graph/reference/supported-tables-resources.md)

Z tej aktualizacji tabela **Microsoft.Security/securityStatuses** została usunięta. Interfejs API securityStatuses jest nadal dostępny.

Z zastępowania danych może korzystać tabela Microsoft.Security/Assessments.

Główna różnica między pakietami Microsoft.Security/securityStatuses i Microsoft.Security/Assessments polega na tym, że podczas gdy pierwsza pokazuje agregację ocen, sekundy mają jeden rekord dla każdej z nich.

Na przykład element Microsoft.Security/securityStatuses zwróci wynik z tablicą dwóch ocen zasad:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Natomiast microsoft.Security/Assessments będzie przechowywać rekord dla każdej takiej oceny zasad w następujący sposób:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Przykład konwertowania istniejącego zapytania ARG przy użyciu securityStatuses do korzystania z tabeli assessments:**

Zapytanie, które odwołuje się do securitystatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Zapytanie zastępcze dla tabeli Assessments:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Więcej informacji znajduje się pod następującymi linkami:
- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Wrzesień 2020

Aktualizacje we wrześniu obejmują:
- [Security Center nowy wygląd!](#security-center-gets-a-new-look)
- [Azure Defender wydano](#azure-defender-released)
- [Azure Defender dla Key Vault jest ogólnie dostępna](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender ochrony magazynu dla plików i ADLS Gen2 jest ogólnie dostępna](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Narzędzia spisu zasobów są teraz ogólnie dostępne](#asset-inventory-tools-are-now-generally-available)
- [Wyłączanie wyszukiwania określonych luk w zabezpieczeniach w przypadku skanowania rejestrów kontenerów i maszyn wirtualnych](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Wykluczanie zasobu z rekomendacji](#exempt-a-resource-from-a-recommendation)
- [Łączniki usług AWS i GCP Security Center zapewniać środowisko wielochmurowe](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pakiet rekomendacji ochrony obciążeń kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Wyniki oceny luk w zabezpieczeniach są teraz dostępne w ramach eksportu ciągłego](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Zapobieganie błędom konfiguracji zabezpieczeń przez wymuszanie zaleceń podczas tworzenia nowych zasobów](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Ulepszono zalecenia dotyczące sieciowych grup zabezpieczeń](#network-security-group-recommendations-improved)
- [Zalecenie dotyczące przestarzałej wersji zapoznawczej usługi AKS "Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Ulepszone powiadomienia e-Azure Security Center e-mail](#email-notifications-from-azure-security-center-improved)
- [Ocena bezpieczeństwa nie obejmuje zaleceń dotyczących wersji zapoznawczej](#secure-score-doesnt-include-preview-recommendations)
- [Zalecenia obejmują teraz wskaźnik ważności i interwał świeżości](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center nowy wygląd!

Wydaliśmy odświeżony interfejs użytkownika dla Security Center w portalu. Nowe strony zawierają nową stronę przeglądu i pulpity nawigacyjne dla wskaźników bezpieczeństwa, spisu zasobów i Azure Defender.

Przeprojektowana strona przeglądu zawiera teraz kafelek do uzyskiwania dostępu do wskaźników bezpieczeństwa, spisu zasobów i Azure Defender nawigacyjnych. Zawiera również kafelek łączący się z pulpitem nawigacyjnym zgodności z przepisami.

Dowiedz się więcej na [temat strony przeglądu](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender wydano

**Azure Defender** to platforma ochrony obciążeń w chmurze (CWPP) zintegrowana w ramach usługi Security Center na potrzeby zaawansowanej, inteligentnej ochrony obciążeń platformy Azure i obciążeń hybrydowych. Zastępuje ona Security Center warstwy cenowej Standardowa firmy. 

Po włączeniu usługi Azure Defender  z obszaru Cen i ustawień programu Azure Security Center wszystkie następujące plany usługi Defender są włączone jednocześnie i zapewniają kompleksową ochronę warstw obliczeniowych, danych i usług środowiska:

- [Usługa Azure Defender dla serwerów](defender-for-servers-introduction.md)
- [Usługa Azure Defender dla usługi App Service](defender-for-app-service-introduction.md)
- [Usługa Azure Defender dla usługi Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Usługa Azure Defender dla usługi Key Vault](defender-for-key-vault-introduction.md)
- [Usługa Azure Defender dla platformy Kubernetes](defender-for-kubernetes-introduction.md)
- [Usługa Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)

Każdy z tych planów został szczegółowo wyjaśniony w Security Center dokumentacji.

Dzięki dedykowanemu pulpitowi nawigacyjnemu Azure Defender alerty zabezpieczeń i zaawansowaną ochronę przed zagrożeniami dla maszyn wirtualnych, baz danych SQL, kontenerów, aplikacji internetowych, sieci i innych.

[Dowiedz się więcej o Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender dla Key Vault jest ogólnie dostępna

Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. 

**Azure Defender for Key Vault** zapewnia natywną dla platformy Azure zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Z drugiej Azure Defender Key Vault chroni wiele zasobów zależnych od kont Key Vault kont.

Opcjonalny plan jest teraz dostępny w ósmej ósemce. Ta funkcja była w wersji zapoznawczej jako "zaawansowana ochrona przed zagrożeniami Azure Key Vault".

Ponadto strony Key Vault w poszczególnych Azure Portal zawierają teraz dedykowaną stronę Zabezpieczeń, na **Security Center** zalecenia i alerty. 

Dowiedz się więcej [w Azure Defender for Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender ochrona magazynu dla plików i ADLS Gen2 jest ogólnie dostępna 

**Azure Defender dla usługi Storage** wykrywa potencjalnie szkodliwe działania na kontach usługi Azure Storage. Dane mogą być chronione bez względu na to, czy są przechowywane jako kontenery obiektów blob, udziały plików czy magazyny typu data lakes.

Obsługa [Azure Files](../storage/files/storage-files-introduction.md) [i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) jest teraz ogólnie dostępna.

Od 1 października 2020 r. rozpoczniemy naliowanie opłat za ochronę zasobów w tych usługach.

Dowiedz się więcej na [Azure Defender magazynu](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Narzędzia spisu zasobów są teraz ogólnie dostępne

Strona spisu zasobów Azure Security Center zawiera jedną stronę do wyświetlania stanu zabezpieczeń zasobów, z które nawiązliśmy połączenie Security Center.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie udostępnia zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Jeśli jakikolwiek zasób ma zaległe rekomendacje, pojawi się w spisie.

Dowiedz się więcej z [tematu Explore and manage your resources with asset inventory (Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów).](asset-inventory.md)



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Wyłączanie wyszukiwania określonych luk w zabezpieczeniach w przypadku skanowania rejestrów kontenerów i maszyn wirtualnych

Azure Defender skanery luk w zabezpieczeniach do skanowania obrazów w Azure Container Registry i maszynach wirtualnych.

Jeśli organizacja musi zignorować wyszukiwanie, a nie je skorygować, możesz je opcjonalnie wyłączyć. Wyłączone wyniki nie wpływają na ocenę bezpieczeństwa ani nie generują niepożądanego szumu.

Gdy wyszukiwanie spełnia kryteria zdefiniowane w zasadach wyłączania, nie będzie wyświetlane na liście wyników.

Ta opcja jest dostępna na stronach szczegółów zaleceń dla:

- **Luki w zabezpieczeniach Azure Container Registry obrazów powinny zostać skorygowane**
- **Należy skorygować luki w zabezpieczeniach maszyn wirtualnych**

Dowiedz się więcej [w tematach Wyłączanie określonych wyników dla obrazów kontenerów](defender-for-container-registries-usage.md#disable-specific-findings-preview) i Wyłączanie określonych wyników [dla maszyn wirtualnych.](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)


### <a name="exempt-a-resource-from-a-recommendation"></a>Wykluczanie zasobu z rekomendacji

Czasami zasób będzie wymieniony jako w złej kondycji w odniesieniu do określonej rekomendacji (i w związku z tym obniży poziom bezpieczeństwa), nawet jeśli uważasz, że tak nie powinno być. Być może został on skorygowany przez proces, który nie jest śledzony przez Security Center. Być może Twoja organizacja zdecydowała się zaakceptować ryzyko dla tego konkretnego zasobu. 

W takich przypadkach można utworzyć regułę wykluczania i upewnić się, że w przyszłości zasób nie będzie wymieniony wśród zasobów w złej kondycji. Te reguły mogą zawierać udokumentowane uzasadnienie, jak opisano poniżej.

Dowiedz się więcej z tematu Exempt a resource from recommendations and secure score (Wyklucz [zasób z rekomendacji i oceny bezpieczeństwa).](exempt-resource.md)


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Łączniki usług AWS i GCP Security Center środowisko wielu chmur

W przypadku obciążeń w chmurze często obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą robić to samo.

Azure Security Center chroni teraz obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Dołączanie kont usług AWS i GCP do usługi Security Center integruje usługi AWS Security Hub, GCP Security Command i Azure Security Center. 

Aby dowiedzieć się więcej, zobacz Connect [your AWS accounts to Azure Security Center](quickstart-onboard-aws.md) and Connect your GCP accounts to Azure Security Center (Łączenie kont usługi AWS z usługą Azure Security Center i Łączenie kont [GCP z Azure Security Center.](quickstart-onboard-gcp.md)


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pakiet zaleceń dla ochrony obciążeń kubernetes

Aby mieć pewność, że obciążenia Kubernetes są domyślnie bezpieczne, Security Center dodaje rekomendacje dotyczące wzmacniania zabezpieczeń na poziomie rozwiązania Kubernetes, w tym opcje wymuszania za pomocą kontroli narzutu kubernetes.

Po zainstalowaniu dodatku usługi Azure Policy dla platformy Kubernetes w klastrze usługi AKS każde żądanie do serwera interfejsu API Kubernetes będzie monitorowane przy użyciu wstępnie zdefiniowanego zestawu najlepszych rozwiązań, zanim zostanie utrwalone w klastrze. Następnie można skonfigurować usługę w celu wymuszania najlepszych rozwiązań i nakłoniania ich do przyszłych obciążeń.

Można na przykład na przykład nakaźć, aby nie można było tworzyć kontenerów uprzywilejowanych, a wszystkie przyszłe żądania w tym celu będą blokowane.

Dowiedz się więcej z [tematu Workload protection best-practices using Kubernetes admission control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)(Najlepsze rozwiązania dotyczące ochrony obciążeń przy użyciu kontroli na danych kubernetes).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Wyniki oceny luk w zabezpieczeniach są teraz dostępne w ramach eksportu ciągłego

Używaj eksportu ciągłego, aby przesyłać strumieniowo alerty i zalecenia w czasie rzeczywistym do Azure Event Hubs, obszarów roboczych usługi Log Analytics lub Azure Monitor. Następnie możesz zintegrować te dane z rozwiązaniami SIEM (takimi jak Azure Sentinel, Power BI, Azure Data Explorer i inne.

Security Center zintegrowane narzędzia do oceny luk w zabezpieczeniach zwracają wyniki dotyczące zasobów jako zalecenia z akcjami w ramach zalecenia "nadrzędnego", takiego jak "Należy skorygować luki w zabezpieczeniach maszyn wirtualnych". 

Wyniki dotyczące zabezpieczeń są teraz dostępne do eksportu za pośrednictwem eksportu ciągłego po wybraniu zaleceń i włączeniu **opcji uwzględnij wyniki zabezpieczeń.**

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Uwzględnianie przełącznika wyników zabezpieczeń w konfiguracji eksportu ciągłego" :::

Powiązane strony:

- [Security Center zintegrowane rozwiązanie do oceny luk w zabezpieczeniach dla maszyn wirtualnych platformy Azure](deploy-vulnerability-assessment-vm.md)
- [Security Center zintegrowane rozwiązanie do oceny luk w zabezpieczeniach dla obrazów Azure Container Registry luk w zabezpieczeniach](defender-for-container-registries-usage.md)
- [Eksport ciągły](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Zapobieganie błędom konfiguracji zabezpieczeń przez wymuszanie zaleceń podczas tworzenia nowych zasobów

Błędy konfiguracji zabezpieczeń są główną przyczyną zdarzeń zabezpieczeń. Security Center ma teraz możliwość zapobiegania  błędom konfiguracji nowych zasobów w odniesieniu do określonych zaleceń. 

Ta funkcja może pomóc w zabezpieczonych obciążeniach i ustabilizowaniu oceny bezpieczeństwa.

Wymuszanie bezpiecznej konfiguracji na podstawie określonego zalecenia jest oferowane w dwóch trybach:

- Przy użyciu **efektu Odmów** Azure Policy można zatrzymać tworzenia zasobów w złej kondycji

- Korzystając z opcji Wymuś, możesz skorzystać z efektu **DeployIfNotExist** zasad platformy Azure i automatycznie skorygować niezgodne zasoby podczas tworzenia 
 
Ta opcja jest dostępna dla wybranych zaleceń dotyczących zabezpieczeń i znajduje się w górnej części strony szczegółów zasobu.

Aby dowiedzieć się [więcej, zobacz Zapobieganie błędom konfiguracji za pomocą zaleceń wymuś/odmów.](prevent-misconfigurations.md)

###  <a name="network-security-group-recommendations-improved"></a>Ulepszono zalecenia dotyczące sieciowych grup zabezpieczeń

Następujące zalecenia dotyczące zabezpieczeń związane z sieciowymi grupami zabezpieczeń zostały ulepszone w celu zmniejszenia liczby wystąpień wyników fałszywie dodatnich.

- Wszystkie porty sieciowe powinny być ograniczone w sieciowej sieci skojarzonej z maszyną wirtualną
- Porty zarządzania powinny być zamknięte na maszynach wirtualnych
- Maszyny wirtualne z Internetem powinny być chronione za pomocą sieciowych grup zabezpieczeń
- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Zalecenie dotyczące przestarzałej wersji zapoznawczej usługi AKS "Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes"

Zalecenie w wersji zapoznawczej "Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes Services" jest przestarzałe, zgodnie z opisem w [Azure Kubernetes Service](../aks/use-pod-security-policies.md) dokumentacji.

Funkcja zasad zabezpieczeń zasobników (wersja zapoznawcza) została ustawiona na potrzeby cofania pracy i nie będzie już dostępna po 15 października 2020 r. na rzecz Azure Policy aKS.

Po wycofaniu zasad zabezpieczeń zasobników (wersja zapoznawcza) należy wyłączyć tę funkcję we wszystkich istniejących klastrach przy użyciu przestarzałej funkcji w celu przeprowadzenia przyszłych uaktualnień klastra i pozostania w ramach pomocy technicznej platformy Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Ulepszone powiadomienia e-Azure Security Center e-mail

Ulepszono następujące obszary wiadomości e-mail dotyczące alertów zabezpieczeń: 

- Dodano możliwość wysyłania powiadomień e-mail dotyczących alertów dla wszystkich poziomów ważności
- Dodano możliwość powiadamiania użytkowników z różnymi rolami platformy Azure w ramach subskrypcji
- Domyślnie proaktywnie powiadamiamy właścicieli subskrypcji o alertach o wysokiej ważności (które mają wysokie prawdopodobieństwo wystąpienia rzeczywistych naruszeń)
- Usunęliśmy pole numeru telefonu ze strony konfiguracji powiadomień e-mail

Aby dowiedzieć się [więcej, zobacz Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń.](security-center-provide-security-contact-details.md)


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Ocena bezpieczeństwa nie obejmuje zaleceń dotyczących wersji zapoznawczej 

Security Center stale ocenia zasoby, subskrypcje i organizację pod zakresie problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w jeden wynik, aby można było na pierwszy rzut oka stwierdzić bieżącą sytuację w zakresie zabezpieczeń: im wyższa ocena, tym niższy zidentyfikowany poziom ryzyka.

W przypadku nowych zagrożeń nowe porady dotyczące zabezpieczeń są udostępniane w Security Center dzięki nowym rekomendacjom. Aby uniknąć niespodzianek zmian wskaźnika bezpieczeństwa i zapewnić okres prolongaty, w którym  można eksplorować nowe zalecenia, zanim wpłyną one na wyniki, rekomendacje oflagowane jako Wersja zapoznawcza nie są już uwzględniane w obliczeniach wskaźnika bezpieczeństwa. Nadal należy je korygować wszędzie tam, gdzie to możliwe, tak aby po zakończeniu okresu zapoznawczego współtworzyły twój wynik.

Ponadto zalecenia **dotyczące wersji** zapoznawczej nie renderuje zasobu "W złej kondycji".

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Zalecenie z flagą wersji zapoznawczej":::

[Dowiedz się więcej na temat oceny bezpieczeństwa.](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Zalecenia obejmują teraz wskaźnik ważności i interwał świeżości

Strona szczegółów zaleceń zawiera teraz wskaźnik interwału świeżości (jeśli jest to istotne) i jasny obraz ważności zalecenia.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Strona rekomendacji przedstawiająca świeżość i ważność":::


## <a name="august-2020"></a>Sierpień 2020 r.

Aktualizacje w sierpniu obejmują:

- [Spis zasobów — nowy, zaawansowany widok stanu zabezpieczeń zasobów](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Dodano obsługę Azure Active Directory domyślnych zabezpieczeń (w przypadku uwierzytelniania wieloskładnikowego)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Dodano zalecenie dotyczące jednostki usługi](#service-principals-recommendation-added)
- [Ocena luk w zabezpieczeniach maszyn wirtualnych — skonsolidowane zalecenia i zasady](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nowe zasady zabezpieczeń usługi AKS dodane do ASC_default — do użytku tylko przez klientów korzystających z prywatnej wersji zapoznawczej](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Spis zasobów — nowy, zaawansowany widok stanu zabezpieczeń zasobów

Security Center zasobów (obecnie w wersji zapoznawczej) umożliwia wyświetlenie stanu zabezpieczeń zasobów połączonych z Security Center.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie udostępnia zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach. Jeśli jakikolwiek zasób ma zaległe rekomendacje, pojawi się w spisie.

Możesz użyć widoku i jego filtrów, aby eksplorować dane dotyczące bezpieczeństwa i podjąć dalsze działania na podstawie wyników.

Dowiedz się więcej o [spisie zasobów.](asset-inventory.md)


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Dodano obsługę Azure Active Directory domyślnych zabezpieczeń (w przypadku uwierzytelniania wieloskładnikowego)

Security Center dodano pełną obsługę domyślnych ustawień [zabezpieczeń](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), czyli bezpłatnych zabezpieczeń tożsamości firmy Microsoft.

Ustawienia domyślne zabezpieczeń zapewniają wstępnie skonfigurowane ustawienia zabezpieczeń tożsamości, aby chronić organizację przed powszechnymi atakami związanymi z tożsamościami. Wartości domyślne zabezpieczeń już łącznie chronią ponad 5 milionów dzierżaw; 50 000 dzierżaw jest również chronionych przez Security Center.

Security Center udostępnia teraz zalecenie dotyczące zabezpieczeń za każdym razem, gdy identyfikuje subskrypcję platformy Azure bez włączonych wartości domyślnych zabezpieczeń. Do tej pory Security Center włączenie uwierzytelniania wieloskładnikowego przy użyciu dostępu warunkowego, który jest częścią licencji Azure Active Directory (AD) Premium. W przypadku klientów korzystających z bezpłatnej usługi Azure AD zalecamy teraz włączenie domyślnych ustawień zabezpieczeń. 

Naszym celem jest zachęcić większą liczbę klientów do zabezpieczenia środowisk chmury przy użyciu usługi MFA i ograniczenia jednego z najwyższych czynników ryzyka, które ma również największy wpływ na wskaźnik [bezpieczeństwa.](secure-score-security-controls.md)

Dowiedz się więcej o [ustawieniach domyślnych zabezpieczeń.](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)


### <a name="service-principals-recommendation-added"></a>Dodano zalecenie dotyczące jednostki usługi

Dodano nowe zalecenie, aby zalecić, Security Center klientów korzystających z certyfikatów zarządzania do zarządzania ich subskrypcjami przełączają się na jednostki usługi.

Zalecenie:  Jednostki usługi powinny być używane do ochrony subskrypcji, a nie certyfikatów zarządzania, zaleca użycie jednostki usługi lub Azure Resource Manager w celu bezpieczniejszego zarządzania subskrypcjami. 

Dowiedz się więcej na [temat obiektów aplikacji i jednostki usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych — skonsolidowane zalecenia i zasady

Security Center sprawdza maszyny wirtualne, aby wykryć, czy są uruchomione rozwiązanie do oceny luk w zabezpieczeniach. Jeśli nie zostanie znalezione żadne rozwiązanie do oceny luk w zabezpieczeniach, Security Center zalecenia w celu uproszczenia wdrożenia.

Po znalezieniu luk w zabezpieczeniach Security Center rekomendację podsumowującą wyniki, które należy zbadać i skorygować w razie potrzeby.

Aby zapewnić spójne środowisko dla wszystkich użytkowników, niezależnie od typu skanera, z których korzysta, zunifikowaliśmy cztery zalecenia w następujące dwa:

|Ujednolicone zalecenie|Zmień opis|
|----|:----|
|**Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach**|Zastępuje następujące dwa zalecenia:<br> Włączanie wbudowanego rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwanych przez rozwiązanie Qualys (obecnie przestarzałe) (dołączone do warstwy Standardowa)<br> Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych (obecnie przestarzałe) (w warstwach Standardowa i Bezpłatna)|
|**Należy skorygować luki w zabezpieczeniach maszyn wirtualnych**|Zastępuje następujące dwa zalecenia:<br>Korygowanie luk w zabezpieczeniach znalezionych na maszynach wirtualnych (obsługiwanych przez firmę Qualys) (obecnie przestarzałe)<br>Luki w zabezpieczeniach powinny być korygowane przez rozwiązanie do oceny luk w zabezpieczeniach (obecnie przestarzałe)|
|||

Teraz użyjesz tego samego zalecenia, aby wdrożyć rozszerzenie do oceny luk w zabezpieczeniach firmy Security Center lub licencjonowane prywatnie rozwiązanie ("BYOL") od partnera, takiego jak Qualys lub Rapid7.

Ponadto gdy luki w zabezpieczeniach zostaną znalezione i zgłoszone Security Center, pojedyncze zalecenie powiadomi Cię o wynikach niezależnie od rozwiązania do oceny luk w zabezpieczeniach, które je zidentyfikuje.

#### <a name="updating-dependencies"></a>Aktualizowanie zależności

Jeśli masz skrypty, zapytania lub automatyzacje odwołujące się do poprzednich zaleceń lub kluczy/nazw zasad, użyj poniższych tabel, aby zaktualizować odwołania:

##### <a name="before-august-2020"></a>Do sierpnia 2020 r.

| Zalecenie|Zakres|
|----|:----|
|**Włączanie wbudowanego rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwanych przez firmę Qualys)**<br>Klucz: 550e890b-e652-4d22-8274-60b3bdb24c63|Wbudowane|
|**Korygowanie luk w zabezpieczeniach znalezionych na maszynach wirtualnych (obsługiwanych przez firmę Qualys)**<br>Klucz: 1195afff-c881-495e-9bc5-14862111ae03f|Wbudowane|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych**<br>Klucz: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Luki w zabezpieczeniach powinny zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Klucz: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Zasady|Zakres|
|----|:----|
|**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Wbudowane|
|**Luki w zabezpieczeniach powinny zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Identyfikator zasad: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>Od sierpnia 2020 r.

|Zalecenie|Zakres|
|----|:----|
|**Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach**<br>Klucz: ffff0522-1e88-47fc-8382-2a80ba848f5d|Wbudowane + BYOL|
|**Należy skorygować luki w zabezpieczeniach maszyn wirtualnych**<br>Klucz: 1195afff-c881-495e-9bc5-14862111ae03f|Wbudowane + BYOL|
|||

|Zasady|Zakres|
|----|:----|
|[**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Wbudowane + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nowe zasady zabezpieczeń usługi AKS dodane do ASC_default — do użytku tylko przez klientów korzystających z prywatnej wersji zapoznawczej

Aby mieć pewność, że obciążenia kubernetes są domyślnie bezpieczne, Security Center dodaje zasady na poziomie rozwiązania Kubernetes i zalecenia dotyczące wzmacniania zabezpieczeń, w tym opcje wymuszania za pomocą kontroli danych na potrzeby rozwiązania Kubernetes.

Wcześniesza faza tego projektu obejmuje prywatną zapoznawczą i dodanie nowych (domyślnie wyłączonych) zasad do ASC_default projektu.

Możesz bezpiecznie zignorować te zasady i nie będzie to miało wpływu na Twoje środowisko. Jeśli chcesz je włączyć, zarejestruj się w wersji zapoznawczej na stronie i https://aka.ms/SecurityPrP wybierz jedną z następujących opcji:

1. **Pojedyncza wersja zapoznawcza** — aby dołączyć tylko do tej prywatnej wersji zapoznawczej. Jawnie wspomnisz o "ciągłym skanowaniu ASC" jako wersji zapoznawczej, do których chcesz dołączyć.
1. **Bieżący program** — do dodania do tej i przyszłych prywatnych wersji zapoznawczych. Konieczne będzie ukończenie umowy dotyczącej profilu i ochrony prywatności.


## <a name="july-2020"></a>Lipiec 2020 r.

Aktualizacje w lipcu obejmują:
- [Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrona przed zagrożeniami dla usługi Azure Storage rozszerzona o Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Ulepszenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżona dokumentacja](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Funkcje adaptacyjnego sterowania aplikacją zostały zaktualizowane o nowe rekomendacje i obsługę symboli wieloznacznych w zasadach ścieżki](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sześć zasad zaawansowanych zabezpieczeń danych SQL jest przestarzałych](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace

Podczas wdrażania rozwiązania do oceny luk w zabezpieczeniach Security Center wcześniej przeprowadzono sprawdzanie poprawności przed wdrożeniem. Sprawdzeniem było potwierdzenie, że jest to docelowa maszyna wirtualna w witrynie Marketplace. 

Po tej aktualizacji kontrola została usunięta i można teraz wdrożyć narzędzia do oceny luk w zabezpieczeniach na "niestandardowych" maszynach z systemami Windows i Linux. Obrazy niestandardowe to te, które zostały zmodyfikowane z ustawień domyślnych witryny Marketplace.

Chociaż można teraz wdrożyć zintegrowane rozszerzenie do oceny luk w zabezpieczeniach (obsługiwane przez rozwiązanie Qualys) na [](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) wielu maszynach, pomoc techniczna jest dostępna tylko w przypadku korzystania z systemu operacyjnego wymienionego w tece Wdrażanie zintegrowanego skanera luk w zabezpieczeniach na maszynach wirtualnych w warstwie Standardowa

Dowiedz się więcej o [zintegrowanym skanerze luk w zabezpieczeniach dla maszyn wirtualnych (wymaga Azure Defender).](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)

Dowiedz się więcej na temat korzystania z własnego, licencjonowanego prywatnie rozwiązania do oceny luk w zabezpieczeniach firmy Qualys lub Rapid7, korzystając z tematu [Deploying a partner vulnerability scanning solution](deploy-vulnerability-assessment-vm.md)(Wdrażanie rozwiązania do skanowania luk w zabezpieczeniach partnera).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Storage rozszerzona o Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)

Ochrona przed zagrożeniami dla usługi Azure Storage wykrywa potencjalnie szkodliwe działania na kontach usługi Azure Storage. Security Center wyświetla alerty w przypadku wykrycia próby uzyskania dostępu do kont magazynu lub wykorzystania ich. 

Dane mogą być chronione bez względu na to, czy są przechowywane jako kontenery obiektów blob, udziały plików czy magazyny typu data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami

Dodano osiem nowych zaleceń w celu zapewnienia prostego sposobu włączania funkcji ochrony przed zagrożeniami usługi Azure Security Center dla następujących typów zasobów: maszyny wirtualne, plany App Service, serwery Azure SQL Database, serwery SQL na maszynach, konta usługi Azure Storage, klastry Azure Kubernetes Service, rejestry Azure Container Registry i magazyny Azure Key Vault.

Nowe rekomendacje to:

- **Zaawansowane zabezpieczenia danych powinny być włączone na Azure SQL Database serwerach**
- **Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure App Service planach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Container Registry rejestrach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Key Vault magazynach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Kubernetes Service klastrach**
- **Na kontach usługi Azure Storage należy włączyć zaawansowaną ochronę przed zagrożeniami**
- **Na maszynach wirtualnych należy włączyć zaawansowaną ochronę przed zagrożeniami**

Te nowe rekomendacje należą do **kontrolki zabezpieczeń Włącz Azure Defender** zabezpieczeń.

Rekomendacje obejmują również możliwość szybkiej poprawki. 

> [!IMPORTANT]
> Korygowanie dowolnych z tych zaleceń spowoduje naliowanie opłat za ochronę odpowiednich zasobów. Te opłaty zaczną się natychmiast, jeśli masz powiązane zasoby w bieżącej subskrypcji. Lub w przyszłości, jeśli je dodasz w późniejszym terminie.
> 
> Jeśli na przykład nie masz żadnych klastrów Azure Kubernetes Service w ramach subskrypcji i włączysz ochronę przed zagrożeniami, nie będą naliczane żadne opłaty. Jeśli w przyszłości dodasz klaster w tej samej subskrypcji, będzie on automatycznie chroniony i opłaty za niego zaczną się w tym czasie.

Dowiedz się więcej na temat każdego z nich na [stronie z informacjami o zaleceniach dotyczących zabezpieczeń.](recommendations-reference.md)

Dowiedz się więcej o [ochronie przed zagrożeniami w Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Ulepszenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżona dokumentacja

W ramach ciągłych inwestycji w domenę zabezpieczeń kontenerów z przyjemnością udostępniamy znaczącą poprawę wydajności podczas dynamicznych skanów obrazów kontenerów przechowywanych w Security Center firmy Azure Container Registry. Skanowanie trwa zwykle około dwóch minut. W niektórych przypadkach może to potrwać do 15 minut.

Aby zwiększyć przejrzystość i wskazówki dotyczące Azure Security Center zabezpieczeń kontenerów firmy Azure Security Center, odświeżyliśmy również strony dokumentacji dotyczącej zabezpieczeń kontenerów. 

Dowiedz się Security Center o zabezpieczeniach kontenerów w następujących artykułach:

- [Omówienie Security Center zabezpieczeń kontenera firmy](container-security.md)
- [Szczegóły integracji z programem Azure Container Registry](defender-for-container-registries-introduction.md)
- [Szczegóły integracji z programem Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Jak skanować rejestry i wzmacnianie zabezpieczeń hostów platformy Docker](container-security.md)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami Azure Kubernetes Service klastrach](alerts-reference.md#alerts-akscluster)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami dla Azure Kubernetes Service hostów](alerts-reference.md#alerts-containerhost)
- [Zalecenia dotyczące zabezpieczeń kontenerów](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Funkcje adaptacyjnego sterowania aplikacją zaktualizowane o nowe zalecenia i obsługę symboli wieloznacznych w zasadach ścieżki

Funkcja adaptacyjnego sterowania aplikacją otrzymała dwie istotne aktualizacje:

* Nowe zalecenie identyfikuje potencjalnie uzasadnione zachowanie, które nie było wcześniej dozwolone. Nowe **zalecenie,** Reguły listy zezwalania w zasadach adaptacyjnego sterowania aplikacją, powinno zostać zaktualizowane. Zostanie wyświetlony monit o dodanie nowych reguł do istniejących zasad w celu zmniejszenia liczby fałszywych alarmów w alertach naruszenia adaptacyjnych kontrolek aplikacji.

* Reguły ścieżek obsługują teraz symbole wieloznaczne. W tej aktualizacji można skonfigurować reguły dozwolonych ścieżek przy użyciu symboli wieloznacznych. Istnieją dwa obsługiwane scenariusze:

    * Użycie symbolu wieloznacznego na końcu ścieżki, aby zezwolić na wszystkie pliki wykonywalne w tym folderze i podfolderach

    * Użycie symbolu wieloznacznego w środku ścieżki w celu włączenia znanej nazwy pliku wykonywalnego ze zmianą nazwy folderu (np. osobiste foldery użytkowników ze znanym plikiem wykonywalnym, automatycznie generowane nazwy folderów itp.).


[Dowiedz się więcej o adaptacyjnych kontrolkach aplikacji.](security-center-adaptive-application.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sześć zasad zaawansowanych zabezpieczeń danych SQL jest przestarzałych

Sześć zasad związanych z zaawansowanym zabezpieczeniami danych dla maszyn SQL jest przestarzałych:

- W zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL należy ustawić typy zaawansowanej ochrony przed zagrożeniami na wartość "Wszystkie"
- W ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server dla zaawansowanych typów ochrony przed zagrożeniami należy ustawić wartość "Wszystkie"
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail do odbierania alertów zabezpieczeń
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych programu SQL Server

Dowiedz się więcej [o wbudowanych zasadach](./policy-reference.md).



## <a name="june-2020"></a>Czerwiec 2020 r.

Aktualizacje w czerwcu obejmują:

- [Interfejs API oceny bezpieczeństwa (wersja zapoznawcza)](#secure-score-api-preview)
- [Zaawansowane zabezpieczenia danych dla maszyn SQL (platforma Azure, inne chmury i lokalne) (wersja zapoznawcza)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Dwa nowe zalecenia dotyczące wdrażania agenta usługi Log Analytics na maszynach Azure Arc (wersja zapoznawcza)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nowe zasady tworzenia konfiguracji ciągłego eksportu i automatyzacji przepływu pracy na dużą skalę](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nowe zalecenie dotyczące używania sieciowych urządzeń sieciowych do ochrony maszyn wirtualnych, które nie mają połączenia z Internetem](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nowe zasady umożliwiające ochronę przed zagrożeniami i zaawansowane zabezpieczenia danych](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Interfejs API oceny bezpieczeństwa (wersja zapoznawcza)

Teraz możesz uzyskać dostęp do swojego wyniku za pośrednictwem interfejsu [API oceny bezpieczeństwa](/rest/api/securitycenter/securescores/) (obecnie w wersji zapoznawczej). Metody interfejsu API zapewniają elastyczność wykonywania zapytań o dane i tworzenia własnego mechanizmu raportowania bezpiecznych wyników w czasie. Na przykład możesz użyć interfejsu API **secure scores,** aby uzyskać ocenę dla określonej subskrypcji. Ponadto możesz użyć interfejsu API **bezpiecznych kontrolek oceny,** aby wyświetlić listę kontroli zabezpieczeń i bieżącego wyniku subskrypcji.

Przykłady narzędzi zewnętrznych, które są możliwe za pomocą interfejsu API oceny bezpieczeństwa, można znaleźć w obszarze bezpiecznej oceny [w naszej społeczności usługi GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Dowiedz się więcej na [temat oceny bezpieczeństwa i kontroli zabezpieczeń w Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Zaawansowane zabezpieczenia danych dla maszyn SQL (platforma Azure, inne chmury i lokalne) (wersja zapoznawcza)

Azure Security Center zaawansowane zabezpieczenia danych dla maszyn SQL teraz chronią serwery SQL Server hostowane na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych. Zwiększa to ochronę serwerów SQL natywnych dla platformy Azure w celu pełnej obsługi środowisk hybrydowych.

Zaawansowane zabezpieczenia danych zapewniają ocenę luk w zabezpieczeniach i zaawansowaną ochronę przed zagrożeniami dla maszyn SQL niezależnie od tego, gdzie się znajdują.

Konfiguracja obejmuje dwa kroki:

1. Wdrożenie agenta usługi Log Analytics na SQL Server hosta w celu zapewnienia połączenia z kontem platformy Azure.

1. Włączenie opcjonalnego pakietu na Security Center cennika i ustawień.

Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych dla maszyn SQL.](defender-for-sql-usage.md)



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dwa nowe zalecenia dotyczące wdrażania agenta usługi Log Analytics na Azure Arc (wersja zapoznawcza)

Dodano dwa nowe zalecenia, które ułatwiają wdrażanie agenta usługi [Log Analytics](../azure-monitor/agents/log-analytics-agent.md) na maszynach Azure Arc i zapewnianie, że są one chronione przez Azure Security Center:

- **Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych z systemem Windows Azure Arc (wersja zapoznawcza)**
- **Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych z systemem Linux Azure Arc (wersja zapoznawcza)**

Te nowe zalecenia będą wyświetlane w tych samych czterech mechanizmach kontroli zabezpieczeń co istniejące (powiązane) **zalecenie.** Na maszynach powinien być zainstalowany agent monitorowania: korygowanie konfiguracji zabezpieczeń, stosowanie adaptacyjnej kontroli aplikacji, stosowanie aktualizacji systemu i włączanie ochrony punktu końcowego.

Zalecenia obejmują również funkcję Szybkiej poprawki, która pomaga przyspieszyć proces wdrażania. 

Dowiedz się więcej o tych dwóch nowych zaleceniach w tabeli [zaleceń dotyczących obliczeń i](recommendations-reference.md#recs-compute) aplikacji.

Dowiedz się więcej o tym, Azure Security Center korzysta z agenta, zobacz What is the Log Analytics agent? (Co [to jest agent usługi Log Analytics?).](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

Dowiedz się więcej [o rozszerzeniach dla Azure Arc maszyn wirtualnych.](../azure-arc/servers/manage-vm-extensions.md)


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nowe zasady do tworzenia konfiguracji ciągłego eksportu i automatyzacji przepływu pracy na dużą skalę

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas badania i ograniczania zdarzeń zabezpieczeń.

Aby wdrożyć konfiguracje automatyzacji w całej organizacji, użyj tych wbudowanych zasad [](continuous-export.md) platformy Azure "DeployIfdNotExist", aby utworzyć i skonfigurować procedury ciągłego eksportu i automatyzacji [przepływu](workflow-automation.md) pracy:

Zasady można znaleźć w zasadach platformy Azure:


|Cel  |Zasady  |Identyfikator zasad  |
|---------|---------|---------|
|Eksport ciągły do centrum zdarzeń|[Wdróż eksportowanie alertów i zaleceń usługi Azure Security Center do centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Ciągły eksport do obszaru roboczego usługi Log Analytics|[Wdróż eksport do obszaru roboczego usługi Log Analytics w celu uzyskania alertów i zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Wprowadzenie do szablonów [automatyzacji przepływu pracy.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Dowiedz się więcej o używaniu dwóch zasad eksportu z tematu [Configure workflow automation at scale using the supplied policies](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) (Konfigurowanie automatyzacji przepływu pracy na dużą skalę przy użyciu dostarczonych zasad) i Set up a continuous export (Konfigurowanie eksportu [ciągłego).](continuous-export.md#set-up-a-continuous-export)


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nowe zalecenie dotyczące używania sieciowych urządzeń sieciowych do ochrony maszyn wirtualnych, które nie mają połączenia z Internetem

Kontrola zabezpieczeń "implementowanie najlepszych rozwiązań w zakresie zabezpieczeń" zawiera teraz następujące nowe zalecenie:

- **Maszyny wirtualne, które nie mają połączenia z Internetem, powinny być chronione za pomocą sieciowych grup zabezpieczeń**

Istniejące zalecenie: maszyny wirtualne z Dostępem do Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń **i** nie rozróżniały maszyn wirtualnych z Dostępem do Internetu i maszyn wirtualnych, które nie mają połączenia z Internetem. W obu przypadkach wygenerowano zalecenie o wysokiej ważności, jeśli maszyna wirtualna nie została przypisana do sieciowej grupy zabezpieczeń. To nowe zalecenie oddziela komputery nieznajdące się z Internetu w celu zmniejszenia liczby wyników fałszywie dodatnich i uniknięcia niepotrzebnych alertów o wysokiej ważności.

Dowiedz się więcej w [tabeli zaleceń dotyczących](recommendations-reference.md#recs-networking) sieci.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nowe zasady włączania ochrony przed zagrożeniami i zaawansowane zabezpieczenia danych

Nowe zasady poniżej zostały dodane do domyślnej inicjatywy ASC i mają pomóc w włączeniu ochrony przed zagrożeniami lub zaawansowanych zabezpieczeń danych dla odpowiednich typów zasobów.

Zasady można znaleźć w zasadach platformy Azure:


| Zasady                                                                                                                                                                                                                                                                | Identyfikator zasad                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Zaawansowane zabezpieczenia danych powinny być włączone na Azure SQL Database serwerach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Na kontach usługi Azure Storage należy włączyć zaawansowaną ochronę przed zagrożeniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Key Vault magazynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure App Service planach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Container Registry rejestrach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona Azure Kubernetes Service klastrach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Zaawansowaną ochronę przed zagrożeniami należy włączyć na Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Dowiedz się więcej o [ochronie przed zagrożeniami w Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Maj 2020 r.

Aktualizacje w programie mogą obejmować:
- [Reguły pomijania alertów (wersja zapoznawcza)](#alert-suppression-rules-preview)
- [Ocena luk w zabezpieczeniach maszyny wirtualnej jest teraz ogólnie dostępna](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Zmiany dostępu just in time (JIT) do maszyny wirtualnej](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Zalecenia niestandardowe zostały przeniesione do oddzielnej kontrolki zabezpieczeń](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Dodano przełącznik w celu wyświetlania rekomendacji w kontrolkach lub jako listy płaskiej](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Rozszerzona kontrola zabezpieczeń "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń"](#expanded-security-control-implement-security-best-practices)
- [Zasady niestandardowe z niestandardowymi metadanymi są teraz ogólnie dostępne](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Możliwości analizy zrzutu awaryjnego podczas migracji do wykrywania ataków bez plików](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Reguły pomijania alertów (wersja zapoznawcza)

Ta nowa funkcja (obecnie w wersji zapoznawczej) pomaga ograniczyć liczbę alertów. Użyj reguł, aby automatycznie ukrywać alerty, które są nieszkodliwe lub związane z normalnymi działaniami w organizacji. Dzięki temu można skoncentrować się na najbardziej istotnych zagrożeniach. 

Alerty zgodne z włączonymi regułami pomijania będą nadal generowane, ale ich stan zostanie odrzucony. Stan jest wyświetlony w 1000 Azure Portal jednak dostęp do alertów Security Center zabezpieczeń.

Reguły pomijania definiują kryteria, dla których alerty powinny być automatycznie odrzucane. Zazwyczaj używa się reguły pomijania, aby:

- pomijanie alertów zidentyfikowanych jako wyniki fałszywie dodatnie

- pomijanie alertów wyzwalanych zbyt często, aby były przydatne

Dowiedz się więcej [na temat pomijania alertów Azure Security Center ochrony przed zagrożeniami.](alerts-suppression-rules.md)


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Ocena luk w zabezpieczeniach maszyny wirtualnej jest teraz ogólnie dostępna

Security Center standardowa obejmuje teraz zintegrowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. To rozszerzenie jest obsługiwane przez firmę Qualys, ale raportuje swoje wyniki bezpośrednio do Security Center. Nie potrzebujesz licencji firmy Qualys, a nawet konta qualys — wszystko jest obsługiwane bezproblemowo w Security Center.

Nowe rozwiązanie może stale skanować maszyny wirtualne w celu znalezienia luk w zabezpieczeniach i prezentowania wyników Security Center. 

Aby wdrożyć rozwiązanie, użyj nowego zalecenia dotyczącego zabezpieczeń:

"Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwanych przez firmę Qualys)"

Dowiedz się więcej Security Center zintegrowanej oceny luk [w zabezpieczeniach dla maszyn wirtualnych.](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Zmiany dostępu just in time (JIT) do maszyny wirtualnej

Security Center opcjonalną funkcję ochrony portów zarządzania maszyn wirtualnych. Zapewnia to ochronę przed najpowszechnszą formą ataków siłowych.

Ta aktualizacja wprowadza następujące zmiany w tej funkcji:

- Nazwa zalecenia, które zaleca włączenie funkcji JIT na maszynie wirtualnej, została zmieniona. Wcześniej"Just-in-time network access control should be applied on virtual machines" (Kontrola dostępu just in time do sieci powinna być stosowana na maszynach wirtualnych) jest teraz: "Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time".

- Zalecenie jest wyzwalane tylko wtedy, gdy istnieją otwarte porty zarządzania.

Dowiedz się więcej o [funkcji dostępu JIT.](security-center-just-in-time.md)


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Zalecenia niestandardowe zostały przeniesione do oddzielnej kontrolki zabezpieczeń

Jedną z kontroli zabezpieczeń wprowadzona z rozszerzonym wynikiem bezpieczeństwa była "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Wszelkie rekomendacje niestandardowe utworzone dla Twoich subskrypcji zostały automatycznie umieszczone w tej kontrolce. 

Aby ułatwić znajdowanie rekomendacji niestandardowych, przenieśliśmy je do dedykowanej kontrolki zabezpieczeń "Zalecenia niestandardowe". Ta kontrola nie ma wpływu na ocenę bezpieczeństwa.

Dowiedz się więcej o mechanizmach kontroli zabezpieczeń w [rozszerzonym zabezpieczonych ocenach (wersja zapoznawcza) Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Dodano przełącznik w celu wyświetlania rekomendacji w kontrolkach lub jako listy płaskiej

Mechanizmy kontroli zabezpieczeń to logiczne grupy powiązanych zaleceń dotyczących zabezpieczeń. Odzwierciedlają one narażone na ataki ataki. Kontrolka to zestaw zaleceń dotyczących zabezpieczeń z instrukcjami, które ułatwiają zaimplementowanie tych zaleceń.

Aby od razu sprawdzić, jak dobrze organizacja zabezpiecza poszczególne ataki, przejrzyj wyniki dla każdej kontroli zabezpieczeń.

Domyślnie zalecenia są wyświetlane w kontrolkach zabezpieczeń. Dzięki tej aktualizacji można również wyświetlać je jako listę. Aby wyświetlić prostą listę posortowaną według stanu kondycji zasobów, których dotyczy problem, użyj nowego przełącznika "Grupuj według kontrolek". Przełącznik znajduje się nad listą w portalu.

Mechanizmy kontroli zabezpieczeń — i ten przełącznik — są częścią nowego środowisku oceny bezpieczeństwa. Pamiętaj, aby wysłać nam swoją opinię z poziomu portalu.

Dowiedz się więcej o mechanizmach kontroli zabezpieczeń w [rozszerzonym zabezpieczonych ocenach (wersja zapoznawcza) Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Przełącznik Grupuj według kontrolek dla rekomendacji":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Rozszerzona kontrola zabezpieczeń "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń" 

Jedną z kontroli zabezpieczeń wprowadzonych z ulepszonym wynikiem bezpieczeństwa jest "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Gdy zalecenie znajduje się w tej kontrolce, nie ma to wpływu na ocenę bezpieczeństwa. 

Dzięki tej aktualizacji trzy rekomendacje zostały przeniesione z kontrolek, w których pierwotnie zostały umieszczone, i do tej kontroli najlepszych rozwiązań. Zrobiliśmy ten krok, ponieważ ustaliliśmy, że ryzyko związane z tymi trzema zaleceniami jest niższe, niż początkowo myśleliśmy.

Ponadto wprowadzono i dodano dwa nowe zalecenia do tej kontrolki.

Trzy przeniesione zalecenia to:

- **Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji** (pierwotnie w kontrolce "Włącz uwierzytelniania wieloskładnikowego")
- **Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji** (pierwotnie w kontrolce "Zarządzanie dostępem i uprawnieniami")
- **Dla subskrypcji należy** wyznaczyć maksymalnie 3 właścicieli (pierwotnie w kontrolce "Zarządzanie dostępem i uprawnieniami")

Dwa nowe zalecenia dodane do kontrolki to:

- **Rozszerzenie konfiguracji gościa powinno** być zainstalowane na maszynach wirtualnych z systemem Windows (wersja zapoznawcza) — użycie programu [Azure Policy Guest Configuration](../governance/policy/concepts/guest-configuration.md) zapewnia wgląd wewnątrz maszyn wirtualnych w ustawienia serwera i aplikacji (tylko system Windows).

- Windows Defender exploit Guard należy włączyć na maszynach **(wersja zapoznawcza)** — Windows Defender Exploit Guard korzysta z agenta Azure Policy konfiguracji gościa. Funkcja Exploit Guard ma cztery składniki, które są przeznaczone do blokowania urządzeń przed szeroką gamą wektorów ataków i zachowań blokowych często używanych w atakach złośliwego oprogramowania, umożliwiając jednocześnie przedsiębiorstwom zrównoważenie ryzyka bezpieczeństwa i wymagań dotyczących produktywności (tylko system Windows).

Dowiedz się więcej o Windows Defender Exploit Guard z [tematu Create and deploy an Exploit Guard policy](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)(Tworzenie i wdrażanie zasad exploit guard).

Dowiedz się więcej o mechanizmach kontroli zabezpieczeń w [rozszerzonym zabezpieczonych ocenach (wersja zapoznawcza).](secure-score-security-controls.md)



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Zasady niestandardowe z niestandardowymi metadanymi są teraz ogólnie dostępne

Zasady niestandardowe są teraz częścią środowiska Security Center, wskaźnik bezpieczeństwa i pulpit nawigacyjny standardów zgodności z przepisami. Ta funkcja jest teraz ogólnie dostępna i umożliwia rozszerzenie zakresu oceny zabezpieczeń w organizacji w Security Center. 

Utwórz niestandardową inicjatywę w usłudze Azure Policy, dodaj do niego zasady i dodaj je do Azure Security Center i wizualizować je jako rekomendacje.

Dodaliśmy również opcję edytowania metadanych rekomendacji niestandardowych. Opcje metadanych obejmują ważność, kroki korygowania, informacje o zagrożeniach i inne.  

Dowiedz się więcej o [ulepszaniu rekomendacji niestandardowych przy użyciu szczegółowych informacji.](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Możliwości analizy zrzutu awaryjnego podczas migracji do wykrywania ataków bez plików 

Integrujemy możliwości wykrywania zrzutów awaryjnego systemu Windows (CDA) z wykrywaniem ataków [bez plików.](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers) Analiza wykrywania ataków bez plików zapewnia ulepszone wersje następujących alertów zabezpieczeń dla maszyn z systemem Windows: Wykryto iniekcja kodu, Wykryto zaszyfrowany moduł systemu Windows, Wykryto kod powłoki i Wykryto podejrzany segment kodu.

Niektóre korzyści wynikające z tego przejścia:

- **Proaktywne i terminowe wykrywanie złośliwego** oprogramowania — podejście CDA dotyczyło oczekiwania na awarię, a następnie uruchomienia analizy w celu znalezienia złośliwych artefaktów. Korzystanie z wykrywania ataków bez plików zapewnia proaktywną identyfikację zagrożeń w pamięci, gdy są one uruchomione. 

- **Wzbogacone alerty** — alerty zabezpieczeń z wykrywania ataków bez plików obejmują wzbogacenia, które nie są dostępne z usługi CDA, takie jak informacje o aktywnych połączeniach sieciowych. 

- **Agregacja alertów** — gdy cda wykryła wiele wzorców ataku w ramach jednego zrzutu awaryjnego, wyzwoliła wiele alertów zabezpieczeń. Wykrywanie ataków bez plików łączy wszystkie zidentyfikowane wzorce ataku z tego samego procesu w jeden alert, eliminując konieczność korelowania wielu alertów.

- **Mniejsze wymagania w obszarze roboczym usługi Log Analytics** — zrzuty awaryjne zawierające potencjalnie poufne dane nie będą już przekazywane do obszaru roboczego usługi Log Analytics.






## <a name="april-2020"></a>Kwiecień 2020 r.

Aktualizacje w kwietniu obejmują:
- [Dynamiczne pakiety zgodności są teraz ogólnie dostępne](#dynamic-compliance-packages-are-now-generally-available)
- [Zalecenia dotyczące tożsamości są teraz zawarte Azure Security Center warstwie Bezpłatna](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamiczne pakiety zgodności są teraz ogólnie dostępne

Pulpit Azure Security Center zgodności z przepisami zawiera teraz dynamiczne pakiety zgodności **(teraz** ogólnie dostępne) do śledzenia dodatkowych standardów branżowych i prawnych.

Pakiety zgodności dynamicznej można dodać do subskrypcji lub grupy zarządzania na stronie Security Center zabezpieczeń. Po doliczaniu standardu lub testu porównawczego standard jest wyświetlany na pulpicie nawigacyjnym zgodności z przepisami ze wszystkimi skojarzonymi danymi zgodności mapowanych jako oceny. Do pobrania będzie dostępny raport podsumowujący dla wszystkich standardów, które zostały do nich doone.

Teraz możesz dodać standardy, takie jak:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **Uk Official i UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nowy)** (pełniejsza reprezentacja usługi Azure CIS 1.1.0)

Ponadto niedawno dodaliśmy test porównawczy zabezpieczeń platformy [Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)— specyficzne dla firmy Microsoft wytyczne dotyczące najlepszych rozwiązań w zakresie zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. Dodatkowe standardy będą obsługiwane na pulpicie nawigacyjnym, gdy staną się dostępne.  
 
Dowiedz się więcej o [dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami.](update-regulatory-compliance-packages.md)


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Zalecenia dotyczące tożsamości są teraz zawarte Azure Security Center warstwie Bezpłatna

Zalecenia dotyczące zabezpieczeń tożsamości i dostępu w Azure Security Center warstwie Bezpłatna są teraz ogólnie dostępne. Jest to część pracy nad tym, aby funkcje zarządzania poziomem zabezpieczeń w chmurze (CSPM) nie podlegały zmianom. Do tej pory te zalecenia były dostępne tylko w warstwie cenowej Standardowa.

Przykłady zaleceń dotyczących tożsamości i dostępu:

- "Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w twojej subskrypcji".
- "Dla Subskrypcji należy wyznaczyć maksymalnie trzech właścicieli".
- "Przestarzałe konta powinny zostać usunięte z subskrypcji".

Jeśli masz subskrypcje w warstwie cenowej Bezpłatna, ta zmiana wpłynie na ich bezpieczeństwo, ponieważ nigdy nie zostały one ocenione pod zakresie zabezpieczeń tożsamości i dostępu.

Dowiedz się więcej o [zaleceniach dotyczących tożsamości i dostępu.](recommendations-reference.md#recs-identityandaccess)

Dowiedz się więcej na [temat zarządzania wymuszaniem uwierzytelniania wieloskładnikowego (MFA) w subskrypcjach.](security-center-identity-access.md)



## <a name="march-2020"></a>Marzec 2020 r.

Aktualizacje w marcu obejmują:

- [Automatyzacja przepływu pracy jest teraz ogólnie dostępna](#workflow-automation-is-now-generally-available)
- [Integracja Azure Security Center z Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Ochrona Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Ulepszone środowisko just in time](#improved-just-in-time-experience)
- [Dwa zalecenia dotyczące zabezpieczeń aplikacji internetowych są przestarzałe](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Automatyzacja przepływu pracy jest teraz ogólnie dostępna

Funkcja automatyzacji przepływu pracy Azure Security Center jest teraz ogólnie dostępna. Za jego pomocą możesz automatycznie wyzwalać Logic Apps alertów zabezpieczeń i zaleceń. Ponadto wyzwalacze ręczne są dostępne dla alertów i wszystkich zaleceń, które mają dostępną opcję szybkiej poprawki.

Każdy program zabezpieczeń zawiera wiele przepływów pracy dla reagowania na zdarzenia. Te procesy mogą obejmować powiadamianie odpowiednich uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych kroków korygowania. Eksperci ds. zabezpieczeń zalecają zautomatyzowanie jak najwięcej kroków tych procedur. Automatyzacja zmniejsza narzut i może zwiększyć bezpieczeństwo, zapewniając szybkie, spójne i spójne kroki procesu zgodnie ze wstępnie zdefiniowanymi wymaganiami.

Aby uzyskać więcej informacji o możliwościach automatycznego i ręcznego uruchamiania Security Center przepływów pracy, zobacz [Automatyzacja przepływów pracy.](workflow-automation.md)

Dowiedz się więcej o [tworzeniu Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integracja Azure Security Center z Windows Admin Center

Teraz można przenieść lokalne serwery z systemem Windows z serwera Windows Admin Center bezpośrednio do Azure Security Center. Security Center następnie stanie się pojedynczym okienkiem, aby wyświetlić informacje o zabezpieczeniach dla wszystkich zasobów usługi Windows Admin Center, w tym serwerów lokalnych, maszyn wirtualnych i dodatkowych obciążeń PaaS.

Po przeniesieniu serwera z Windows Admin Center do Azure Security Center można:

- Wyświetlanie alertów zabezpieczeń i zaleceń w Security Center rozszerzenia Windows Admin Center.
- Wyświetl poziom zabezpieczeń i pobierz dodatkowe szczegółowe informacje o serwerach zarządzanych Windows Admin Center w Security Center w Azure Portal (lub za pośrednictwem interfejsu API).

Dowiedz się więcej [o tym, jak zintegrować Azure Security Center z Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrona Azure Kubernetes Service

Azure Security Center rozszerza funkcje zabezpieczeń kontenerów w celu ochrony Azure Kubernetes Service (AKS).

Popularna platforma typu open source Kubernetes została tak powszechnie przyjęta, że jest teraz standardem branżowym aranżacji kontenerów. Pomimo tej szerokiej implementacji nadal nie ma wiedzy na temat sposobu zabezpieczania środowiska Kubernetes. Ochrona powierzchni atakowych konteneryzowanych aplikacji wymaga specjalistycznej wiedzy, aby zapewnić, że infrastruktura jest bezpiecznie skonfigurowana i stale monitorowana pod kątem potencjalnych zagrożeń.

Ochrona Security Center obejmuje:

- **Odnajdywanie i widoczność** — ciągłe odnajdywanie zarządzanych wystąpień usługi AKS w ramach subskrypcji zarejestrowanych w Security Center.
- **Zalecenia dotyczące** zabezpieczeń — zalecenia z akcjami, które ułatwiają przestrzeganie najlepszych rozwiązań w zakresie zabezpieczeń dla aks. Te zalecenia są uwzględniane w ocenie bezpieczeństwa, aby upewnić się, że są one przeglądane jako część bezpieczeństwa organizacji. Przykładowe zalecenie związane z usługą AKS to "Kontrola dostępu oparta na rolach powinna być używana w celu ograniczenia dostępu do klastra usługi Kubernetes".
- **Ochrona przed** zagrożeniami — dzięki ciągłej analizie wdrożenia usługi AKS usługa Security Center alerty o zagrożeniach i złośliwych działaniach wykrytych na poziomie hosta i klastra usługi AKS.

Dowiedz się więcej o [integracji usług Azure Kubernetes Services z Security Center](defender-for-kubernetes-introduction.md).

Dowiedz się więcej o [funkcjach zabezpieczeń kontenerów w Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Ulepszone środowisko just in time

Funkcje, działanie i interfejs użytkownika dla Azure Security Center just in time firmy Azure Security Center, które zabezpieczą porty zarządzania, zostały udoskonalone w następujący sposób: 

- **Pole uzasadnienie** — w przypadku żądania dostępu do maszyny wirtualnej za pośrednictwem strony just in time usługi Azure Portal dostępne jest nowe pole opcjonalne, które umożliwia wprowadzenie uzasadnienia żądania. Informacje wprowadzone w tym polu można śledzić w dzienniku aktywności. 
- **Automatyczne czyszczenie nadmiarowych reguł just in time (JIT)** — przy każdej aktualizacji zasad JIT automatycznie uruchamiane jest narzędzie do oczyszczania w celu sprawdzenia poprawności całego zestawu reguł. Narzędzie szuka niezgodności między regułami w zasadach i regułami w sieciowej grupy zabezpieczeń. Jeśli narzędzie oczyszczania wykryje niezgodność, określa przyczynę i, gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są już potrzebne. Czyszczenia nigdy nie usuwa utworzonych reguł. 

Dowiedz się więcej o [funkcji dostępu JIT.](security-center-just-in-time.md)


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dwa zalecenia dotyczące zabezpieczeń aplikacji internetowych są przestarzałe

Dwa zalecenia dotyczące zabezpieczeń związane z aplikacjami internetowymi są przestarzałe: 

- Reguły dotyczące aplikacji internetowych w sieciowych sieciach usługi IaaS powinny być wzmocnione.
    (Powiązane zasady: reguły sieciowych grupy zabezpieczeń dla aplikacji internetowych w UAAS powinny być wzmocnione)

- Dostęp do App Services powinien być ograniczony.
    (Powiązane zasady: dostęp do App Services powinien być ograniczony [wersja zapoznawcza])

Te zalecenia nie będą już wyświetlane Security Center liście zaleceń. Powiązane zasady nie będą już uwzględniane w inicjatywie o nazwie "Security Center Domyślne".

Dowiedz się więcej o [zaleceniach dotyczących zabezpieczeń.](recommendations-reference.md)




## <a name="february-2020"></a>Luty 2020 r.

### <a name="fileless-attack-detection-for-linux-preview"></a>Wykrywanie ataków bez plików dla systemu Linux (wersja zapoznawcza)

W związku z tym, że osoby atakujące coraz bardziej wykorzystują niewidzialniejsze metody w celu uniknięcia wykrywania, Azure Security Center rozszerza wykrywanie ataków bez plików dla systemu Linux, oprócz systemu Windows. Ataki bez plików wykorzystują luki w zabezpieczeniach oprogramowania, iniekują złośliwe ładunki do niegroźnych procesów systemowych i ukrywają się w pamięci. Te techniki:

- minimalizowanie lub eliminowanie śladów złośliwego oprogramowania na dysku
- znacznie zmniejsza prawdopodobieństwo wykrycia przez rozwiązania do skanowania pod kątem złośliwego oprogramowania oparte na dyskach

Aby znić to zagrożenie, Azure Security Center wykrywanie ataków bez plików dla systemu Windows w październiku 2018 r., a także rozszerzono wykrywanie ataków bez plików w systemie Linux. 



## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="enhanced-secure-score-preview"></a>Rozszerzony wynik bezpieczeństwa (wersja zapoznawcza)

Rozszerzona wersja funkcji bezpiecznego oceny Azure Security Center jest teraz dostępna w wersji zapoznawczej. W tej wersji wiele zaleceń jest grupowanych w kontrolki zabezpieczeń, które lepiej odzwierciedlają narażone na ataki powierzchnie (na przykład ograniczają dostęp do portów zarządzania).

Zapoznaj się ze zmianami bezpiecznego wyniku w fazie wersji zapoznawczej i określ inne działania naprawcze, które pomogą Ci jeszcze bardziej zabezpieczyć środowisko.

Dowiedz się więcej o [rozszerzonym zabezpieczonych ocenach (wersja zapoznawcza).](secure-score-security-controls.md)



## <a name="november-2019"></a>Listopad 2019 r.

Aktualizacje w listopadzie obejmują:
 - [Ochrona przed zagrożeniami dla Azure Key Vault w Ameryka Północna regionach (wersja zapoznawcza)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Usługa Threat Protection dla usługi Azure Storage obejmuje kontrolę reputacji złośliwego oprogramowania](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatyzacja przepływu pracy za Logic Apps (wersja zapoznawcza)](#workflow-automation-with-logic-apps-preview)
 - [Szybka poprawka dla zasobów zbiorczych, które są ogólnie dostępne](#quick-fix-for-bulk-resources-generally-available)
 - [Skanuj obrazy kontenerów pod poszukiwaniu luk w zabezpieczeniach (wersja zapoznawcza)](#scan-container-images-for-vulnerabilities-preview)
 - [Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)](#additional-regulatory-compliance-standards-preview)
 - [Ochrona przed zagrożeniami dla Azure Kubernetes Service (wersja zapoznawcza)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Ocena luk w zabezpieczeniach maszyny wirtualnej (wersja zapoznawcza)](#virtual-machine-vulnerability-assessment-preview)
 - [Zaawansowane zabezpieczenia danych dla serwerów SQL w usłudze Azure Virtual Machines (wersja zapoznawcza)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Obsługa zasad niestandardowych (wersja zapoznawcza)](#support-for-custom-policies-preview)
 - [Rozszerzanie Azure Security Center o platformę dla społeczności i partnerów](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Zaawansowane integracje z eksportowaniem zaleceń i alertów (wersja zapoznawcza)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Dołączanie serwerów wstępnych w celu Security Center z Windows Admin Center (wersja zapoznawcza)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Ochrona przed zagrożeniami dla Azure Key Vault w Ameryka Północna regionach (wersja zapoznawcza)

Azure Key Vault to podstawowa usługa do ochrony danych i poprawiania wydajności aplikacji w chmurze, oferująca możliwość centralnego zarządzania kluczami, wpisami tajnymi, kluczami kryptograficznymi i zasadami w chmurze. Ponieważ Azure Key Vault dane poufne i krytyczne dla działania firmy, wymagają maksymalnego bezpieczeństwa magazynów kluczy i przechowywanych w nich danych.

Azure Security Center ochrony przed zagrożeniami dla usługi Azure Key Vault zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do magazynów kluczy lub wykorzystania ich. Ta nowa warstwa ochrony umożliwia klientom dostęp do zagrożeń związanych z magazynami kluczy bez konieczności eksperta ds. zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń. Ta funkcja jest dostępna w publicznej wersji zapoznawczej w Ameryka Północna Regionach.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Usługa Threat Protection dla usługi Azure Storage obejmuje kontrolę reputacji złośliwego oprogramowania

Ochrona przed zagrożeniami dla usługi Azure Storage oferuje nowe funkcje wykrywania obsługiwane przez usługę Microsoft Threat Intelligence w celu wykrywania przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji skrótów i podejrzanego dostępu z aktywnego węzła wyjściowego Tor (serwera proxy anonimizacji). Teraz można wyświetlać wykryte złośliwe oprogramowanie na kontach magazynu przy użyciu Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatyzacja przepływu pracy za Logic Apps (wersja zapoznawcza)

Organizacje z centralnie zarządzanymi zabezpieczeniami i procesów IT/operations implementują wewnętrzne procesy przepływu pracy w celu kierowania wymaganymi działaniami w organizacji, gdy w ich środowiskach zostaną wykryte rozbieżności. W wielu przypadkach te przepływy pracy są powtarzalnymi procesami, a automatyzacja może znacznie usprawnić procesy w organizacji.

Obecnie wprowadzamy nową funkcję w u usługach Security Center, która umożliwia klientom tworzenie konfiguracji automatyzacji korzystających z usługi Azure Logic Apps i tworzenie zasad, które będą automatycznie wyzwalać je na podstawie określonych wyników usługi ASC, takich jak zalecenia lub alerty. Aplikację logiki platformy Azure można skonfigurować tak, aby wykonać dowolną akcję niestandardową obsługiwaną przez ogromną społeczność łączników aplikacji logiki lub użyć jednego z szablonów dostarczonych przez usługę Security Center, takiego jak wysłanie wiadomości e-mail lub otwarcie biletu usługi &trade; ServiceNow.

Aby uzyskać więcej informacji na temat funkcji automatycznego i ręcznego uruchamiania Security Center przepływów pracy, zobacz [Automatyzacja przepływów pracy.](workflow-automation.md)

Aby dowiedzieć się więcej o tworzeniu Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Szybka poprawka dla zasobów zbiorczych, które są ogólnie dostępne

W przypadku wielu zadań, które użytkownik otrzymał w ramach bezpiecznego wyniku, możliwość skutecznego rozwiązywania problemów w dużej floty może stać się trudna.

Aby uprościć korygowanie błędnych konfiguracji zabezpieczeń i szybko skorygować zalecenia dotyczące zbiorczych zasobów oraz poprawić ocenę bezpieczeństwa, należy Szybka poprawka korygowania.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która skonfiguruje ustawienie w Twoim imieniu.

Szybka poprawka jest obecnie ogólnie dostępna dla klientów w ramach strony Security Center rekomendacji.

Zobacz, które zalecenia mają włączoną szybką poprawkę w [przewodniku informacyjnym po zaleceniach dotyczących zabezpieczeń.](recommendations-reference.md)


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skanuj obrazy kontenerów pod poszukiwaniu luk w zabezpieczeniach (wersja zapoznawcza)

Azure Security Center teraz skanować obrazy kontenerów w Azure Container Registry w poszukiwaniu luk w zabezpieczeniach.

Skanowanie obrazów działa przez analizę pliku obrazu kontenera, a następnie sprawdzenie, czy istnieją znane luki w zabezpieczeniach (obsługiwane przez firmę Qualys).

Samo skanowanie jest automatycznie wyzwalane podczas wypychania nowych obrazów kontenerów do Azure Container Registry. Znalezione luki w zabezpieczeniach będą Security Center zaleceniami i uwzględnione w bezpiecznym wyniku platformy Azure wraz z informacjami na temat sposobu ich poprawiania w celu zmniejszenia dozwolonej przez nie powierzchni ataków.


### <a name="additional-regulatory-compliance-standards-preview"></a>Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)

Pulpit nawigacyjny Zgodność z przepisami zapewnia szczegółowe informacje na temat stanu zgodności na podstawie Security Center kontroli. Na pulpicie nawigacyjnym pokazano, w jaki sposób środowisko jest zgodne z kontrolami i wymaganiami określonymi w określonych standardach prawnych i branżowych testach porównawczych, a także zawiera normatywne zalecenia dotyczące sposobu spełniania tych wymagań.

Pulpit nawigacyjny zgodności z przepisami obsługuje do tej pory cztery wbudowane standardy: Azure CIS 1.1.0, PCI-DSS, ISO 27001 i SOC-TSP. Ogłaszamy teraz publiczną wersję zapoznawczą dodatkowych obsługiwanych standardów: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM i UK Official wraz z uk NHS. Udostępniamy również zaktualizowaną wersję usługi Azure CIS 1.1.0 obejmującą więcej kontrolek ze standardu i zwiększenie rozszerzalności.

[Dowiedz się więcej o dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami.](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Ochrona przed zagrożeniami dla Azure Kubernetes Service (wersja zapoznawcza)

Rozwiązanie Kubernetes szybko staje się nowym standardem wdrażania oprogramowania w chmurze i zarządzania nimi. Niewiele osób ma duże doświadczenie w pracy z rozwiązaniami Kubernetes, a wiele koncentruje się tylko na ogólnej inżynierii i administrowaniu oraz pomija aspekt zabezpieczeń. Aby zapewnić bezpieczeństwo środowiska Kubernetes, należy upewnić się, że nie pozostawiono otwartych drzwi powierzchni ataków ukierunkowanego na kontener, które są dostępne dla osób atakujących. Security Center rozszerza obsługę w przestrzeni kontenerów na jedną z najszybciej rozwijających się usług na platformie Azure — Azure Kubernetes Service (AKS).

Nowe możliwości w tej publicznej wersji zapoznawczej obejmują:

- **Widoczność & odnajdywania** — ciągłe odnajdywanie zarządzanych wystąpień usługi AKS Security Center zarejestrowanych subskrypcji usługi.
- **Rekomendacje dotyczące bezpiecznego** wyniku — elementy z akcjami, które ułatwiają klientom przestrzeganie najlepszych rozwiązań w zakresie zabezpieczeń dla usługi AKS i zwiększenie poziomu bezpieczeństwa. Zalecenia obejmują takie elementy jak "Kontrola dostępu oparta na rolach powinna być używana w celu ograniczenia dostępu do klastra usługi Kubernetes Service".
- **Wykrywanie zagrożeń** — analiza oparta na hoście i klastrze, na przykład "Wykryto uprzywilejowany kontener".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Ocena luk w zabezpieczeniach maszyny wirtualnej (wersja zapoznawcza)

Aplikacje zainstalowane na maszynach wirtualnych często mogą mieć luki w zabezpieczeniach, które mogą prowadzić do naruszenia zabezpieczeń maszyny wirtualnej. Ogłaszamy, że warstwa Standardowa Security Center zawiera wbudowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. Ocena luk w zabezpieczeniach, która jest napędzana przez firmę Qualys w publicznej wersji zapoznawczej, umożliwi ciągłe skanowanie wszystkich zainstalowanych aplikacji na maszynie wirtualnej w celu znalezienia narażonych aplikacji i przedstawienia wyników w środowisku portalu Security Center. Security Center wszystkie operacje wdrażania, aby użytkownik nie wymagał dodatkowej pracy. W przyszłości planujemy udostępnić opcje oceny luk w zabezpieczeniach, aby obsługiwać unikatowe potrzeby biznesowe naszych klientów.

[Dowiedz się więcej o ocenach luk w zabezpieczeniach dla usługi Azure Virtual Machines.](deploy-vulnerability-assessment-vm.md)


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w usłudze Azure Virtual Machines (wersja zapoznawcza)

Azure Security Center ochrony przed zagrożeniami i oceny luk w zabezpieczeniach dla baz danych SQL działających na maszynach wirtualnych IaaS jest teraz w wersji zapoznawczej.

[Ocena luk w zabezpieczeniach](../azure-sql/database/sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia ona wgląd w poziom zabezpieczeń w ramach oceny bezpieczeństwa platformy Azure i obejmuje kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bezpieczeństwa bazy danych.

[Zaawansowana ochrona przed](../azure-sql/database/threat-detection-overview.md) zagrożeniami wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub wykorzystania go. Stale monitoruje bazę danych pod celu wykrycia podejrzanych działań i udostępnia alerty zabezpieczeń zorientowane na działania w przypadku anomalii wzorców dostępu do bazy danych. Te alerty zawierają szczegółowe informacje o podejrzanych działaniach i zalecane akcje w celu zbadania i ograniczenia zagrożenia.


### <a name="support-for-custom-policies-preview"></a>Obsługa zasad niestandardowych (wersja zapoznawcza)

Azure Security Center obsługuje teraz zasady niestandardowe (w wersji zapoznawczej).

Nasi klienci chcieli rozszerzyć zakres bieżących ocen zabezpieczeń na Security Center o własne oceny zabezpieczeń oparte na zasadach, które tworzą w Azure Policy. Dzięki obsługi zasad niestandardowych jest to teraz możliwe.

Te nowe zasady będą częścią środowiska rekomendacji Security Center, wskaźnik bezpieczeństwa i pulpit nawigacyjny standardów zgodności z przepisami. Dzięki użyciu obsługi zasad niestandardowych możesz teraz utworzyć niestandardową inicjatywę w programie Azure Policy, a następnie dodać ją jako zasady w Security Center i zwizualizować jako zalecenie.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Rozszerzanie Azure Security Center o platformę dla społeczności i partnerów

Użyj Security Center, aby otrzymywać rekomendacje nie tylko od firmy Microsoft, ale również od istniejących rozwiązań od partnerów, takich jak Check Point, Tenable i CyberArk, z o wiele większą integracją.  Security Center prosty przepływ dołączania może połączyć istniejące rozwiązania z usługą Security Center, umożliwiając wyświetlanie rekomendacji dotyczących bezpieczeństwa w jednym miejscu, uruchamianie ujednoliconych raportów i wykorzystanie wszystkich możliwości rozwiązania Security Center względem zaleceń wbudowanych i partnerskich. Możesz również wyeksportować Security Center do produktów partnerskich.

[Dowiedz się więcej o programie Microsoft Intelligent Security Association.](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Zaawansowane integracje z eksportowaniem rekomendacji i alertów (wersja zapoznawcza)

Aby włączyć scenariusze na poziomie przedsiębiorstwa na Security Center, można teraz korzystać z alertów i zaleceń usługi Security Center w dodatkowych miejscach z wyjątkiem Azure Portal lub interfejsu API. Można je wyeksportować bezpośrednio do centrum zdarzeń i obszarów roboczych usługi Log Analytics. Oto kilka przepływów pracy, które można utworzyć wokół tych nowych możliwości:

- Eksportowanie do obszaru roboczego usługi Log Analytics umożliwia tworzenie niestandardowych pulpitów nawigacyjnych przy użyciu Power BI.
- Dzięki eksportowaniu do centrum zdarzeń będzie można eksportować alerty i zalecenia usługi Security Center do rozwiązań SIEM innych firm, do rozwiązania innej firmy w czasie rzeczywistym lub Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Dołączanie serwerów on-prem w celu Security Center z Windows Admin Center (wersja zapoznawcza)

Windows Admin Center to portal zarządzania dla serwerów z systemem Windows, które nie zostały wdrożone na platformie Azure i oferuje kilka funkcji zarządzania platformą Azure, takich jak tworzenie kopii zapasowych i aktualizacje systemu. Niedawno dodaliśmy możliwość dołączania tych serwerów spoza platformy Azure, które mają być chronione przez usługę ASC bezpośrednio z Windows Admin Center platformy Azure.

Dzięki temu nowemu doświadczeniu użytkownicy będą dołączać serwer WAC w celu Azure Security Center i umożliwiać wyświetlanie alertów zabezpieczeń i zaleceń bezpośrednio w Windows Admin Center aplikacji.


## <a name="september-2019"></a>Wrzesień 2019 r.

Aktualizacje we wrześniu obejmują:

 - [Zarządzanie regułami za pomocą ulepszeń funkcji adaptacyjnego sterowania aplikacją](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrolowanie zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Zarządzanie regułami za pomocą ulepszeń funkcji adaptacyjnego sterowania aplikacją

Ulepszono środowisko zarządzania regułami maszyn wirtualnych przy użyciu funkcji adaptacyjnego sterowania aplikacją. Azure Security Center funkcje adaptacyjnego sterowania aplikacjami pomagają kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych. Oprócz ogólnej poprawy zarządzania regułami nowa korzyść umożliwia kontrolowanie typów plików, które będą chronione po dodaniu nowej reguły.

[Dowiedz się więcej o adaptacyjnych kontrolkach aplikacji.](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrolowanie zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy

Azure Security Center usuwania luk w zabezpieczeniach kontenerów można teraz włączyć lub wyłączyć za pośrednictwem Azure Policy.

Aby wyświetlić włączone zasady zabezpieczeń, z Security Center otwórz stronę Zasady zabezpieczeń.


## <a name="august-2019"></a>Sierpień 2019 r.

Aktualizacje w sierpniu obejmują:

 - [Dostęp just in time (JIT) do maszyny wirtualnej dla Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Korygowanie jednym kliknięciem w celu zwiększenia bezpieczeństwa (wersja zapoznawcza)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Zarządzanie wieloma dzierżawami](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Dostęp just in time (JIT) do maszyny wirtualnej dla Azure Firewall 

Dostęp just in time (JIT) do maszyny wirtualnej Azure Firewall jest teraz ogólnie dostępny. Użyj go do zabezpieczenia Azure Firewall chronionych środowisk oprócz środowisk chronionych przez sieciowej chmurze.

Dostęp JIT do maszyny wirtualnej zmniejsza narażenie na sieciowe ataki wolumetryczne, zapewniając kontrolowany dostęp do maszyn wirtualnych tylko w razie potrzeby przy użyciu sieciowej Azure Firewall sieciowej.

Po włączeniu dostępu JIT dla maszyn wirtualnych należy utworzyć zasady, które określają porty, które mają być chronione, czas pozostawania otwartych portów oraz zatwierdzone adresy IP, z których można uzyskiwać dostęp do tych portów. Te zasady pomagają zachować kontrolę nad tym, co użytkownicy mogą zrobić, gdy zażądają dostępu.

Żądania są rejestrowane w dzienniku aktywności platformy Azure, co umożliwia łatwe monitorowanie i inspekcję dostępu. Strona just in time ułatwia również szybkie identyfikowanie istniejących maszyn wirtualnych z włączoną obsługą funkcji JIT i maszyn wirtualnych, na których zaleca się dostęp JIT.

[Dowiedz się więcej o Azure Firewall](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Korygowanie jednym kliknięciem w celu zwiększenia bezpieczeństwa (wersja zapoznawcza)

Ocena bezpieczeństwa to narzędzie, które pomaga ocenić poziom zabezpieczeń obciążenia. Przegląda rekomendacje dotyczące zabezpieczeń i określa ich priorytety, aby wiedzieć, które zalecenia należy wykonać w pierwszej kolejności. Ułatwia to znalezienie najpoważniejszych luk w zabezpieczeniach w celu ustalania priorytetów badania.

Aby uprościć korygowanie nieporozumień w zakresie zabezpieczeń i ułatwić szybkie poprawianie bezpieczeństwa oceny, dodaliśmy nową funkcję, która umożliwia korygowanie rekomendacji dotyczących zbiorczej ilości zasobów za pomocą jednego kliknięcia.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która skonfiguruje ustawienie w Twoim imieniu.

Zobacz, które zalecenia mają włączoną szybką poprawkę w [przewodniku informacyjnym po zaleceniach dotyczących zabezpieczeń.](recommendations-reference.md)


### <a name="cross-tenant-management"></a>Zarządzanie wieloma dzierżawami

Security Center obsługuje teraz scenariusze zarządzania między dzierżawami w ramach Azure Lighthouse. Dzięki temu można uzyskać wgląd w poziom zabezpieczeń wielu dzierżawców w wielu dzierżawach i zarządzać nimi w Security Center. 

[Dowiedz się więcej na temat obsługi zarządzania między dzierżawami.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="updates-to-network-recommendations"></a>Aktualizacje zaleceń dotyczących sieci

Azure Security Center (ASC) uruchomiła nowe rekomendacje dotyczące sieci i ulepszyła niektóre istniejące. Teraz użycie Security Center zapewnia jeszcze lepszą ochronę sieci dla zasobów. 

[Dowiedz się więcej o zaleceniach dotyczących sieci.](recommendations-reference.md#recs-networking)


## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="adaptive-network-hardening---generally-available"></a>Adaptacyjne wzmacnianie zabezpieczeń sieci — ogólnie dostępne

Jednym z największych ataków dla obciążeń działających w chmurze publicznej są połączenia do i z publicznego Internetu. Naszym klientom trudno jest dowiedzieć się, które reguły sieciowej grupy zabezpieczeń powinny być dostępne, aby upewnić się, że obciążenia platformy Azure są dostępne tylko dla wymaganych zakresów źródeł. Dzięki tej funkcji Security Center wzorce ruchu sieciowego i łączności obciążeń platformy Azure oraz udostępnia zalecenia dotyczące reguł sieciowej grupy zarządzania dla maszyn wirtualnych skierowanych do Internetu. Pomaga to naszemu klientowi lepiej skonfigurować zasady dostępu do sieci i ograniczyć narażenie na ataki. 

[Dowiedz się więcej o adaptacyjnym wzmacnianiu zabezpieczeń sieci.](security-center-adaptive-network-hardening.md)
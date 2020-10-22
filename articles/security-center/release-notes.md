---
title: Informacje o wersji Azure Security Center
description: Opis nowości i zmian w programie Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: ed4bd97dfe64a85785cf7805da2cf7f942baecd4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367549"
---
# <a name="whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Security Center jest aktywnym programowaniem i od bieżąco otrzymuje ulepszenia. Ta strona zawiera informacje o nowych funkcjach, poprawkach błędów i przestarzałych funkcjach, aby zachować aktualność.

Ta strona jest często aktualizowana, więc często należy ją ponownie odwiedzać. 

> [!TIP]
> Jeśli szukasz elementów starszych niż sześć miesięcy, znajdziesz je w [archiwum, co nowego w programie Azure Security Center](release-notes-archive.md).


## <a name="october-2020"></a>Październik 2020 r.

- [Ocena luk w zabezpieczeniach dla maszyn lokalnych i wielochmurowych (wersja zapoznawcza)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Dodane zalecenie dotyczące zapory platformy Azure (wersja zapoznawcza)](#azure-firewall-recommendation-added-preview)
- [Autoryzowane zakresy adresów IP powinny być zdefiniowane w zaleceniu usług Kubernetes Services zaktualizowanym z opcją szybkie rozwiązanie.](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Tabela Microsoft. Security/securityStatuses została usunięta z grafu zasobów platformy Azure (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Ocena luk w zabezpieczeniach dla maszyn lokalnych i wielochmurowych (wersja zapoznawcza)

Program [Azure Defender dla serwerów](defender-for-servers-introduction.md)"zintegrowany skaner do oceny luk w zabezpieczeniach (obsługiwany przez Qualys) skanuje teraz serwery z włączonym rozwiązaniem Azure Arc.

Gdy usługa Azure Arc została włączona na maszynach spoza platformy Azure, Security Center będzie oferować ręczne wdrażanie skanera zintegrowanej luki w zabezpieczeniach.

Dzięki tej aktualizacji można wyłączyć **usługę Azure Defender dla serwerów** , aby skonsolidować program do zarządzania lukami w zabezpieczeniach dla wszystkich zasobów platformy Azure i spoza platformy Azure.

Główne możliwości:

- Monitorowanie stanu aprowizacji skanera (ocena luk w zabezpieczeniach) na maszynach usługi Azure Arc
- Inicjowanie obsługi zintegrowanego agenta VA do niechronionych maszyn z systemem Windows i Linux na platformie Azure (ręcznie i w skali)
- Otrzymywanie i analizowanie wykrytych luk w zabezpieczeniach wdrożonych agentów (ręcznie i w skali)
- Ujednolicone środowisko dla maszyn wirtualnych platformy Azure i usługi Azure Arc

[Dowiedz się więcej o wdrażaniu zintegrowanego skanera luk w zabezpieczeniach na maszynach hybrydowych](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Dowiedz się więcej o serwerach z obsługą usługi Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Dodane zalecenie dotyczące zapory platformy Azure (wersja zapoznawcza)

Dodano nowe zalecenie umożliwiające ochronę wszystkich sieci wirtualnych za pomocą zapory platformy Azure.

Zalecenie, **sieci wirtualne powinny być chronione przez zaporę platformy Azure** , zaleca się ograniczyć dostęp do sieci wirtualnych i uniemożliwić potencjalne zagrożenia za pomocą zapory platformy Azure.

Dowiedz się więcej o [zaporze platformy Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Autoryzowane zakresy adresów IP powinny być zdefiniowane w zaleceniu usług Kubernetes Services zaktualizowanym z opcją szybkie rozwiązanie.

**Zatwierdzone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes** teraz mają opcję Szybkie rozwiązanie.

Aby uzyskać więcej informacji na temat tego zalecenia i wszystkich innych Security Center zaleceń, zobacz [zalecenia dotyczące zabezpieczeń — Podręcznik referencyjny](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie":::


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabela Microsoft. Security/securityStatuses została usunięta z grafu zasobów platformy Azure (ARG)

Azure Resource Graph to usługa platformy Azure, która została zaprojektowana w celu zapewnienia wydajnej eksploracji zasobów dzięki możliwości wykonywania zapytań na dużą skalę w ramach danego zestawu subskrypcji, dzięki czemu możesz efektywnie zarządzać środowiskiem. 

Aby uzyskać Azure Security Center, można użyć ARG i [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) do wykonywania zapytań o szeroki zakres danych stan zabezpieczeń. Na przykład:

- Wykorzystanie spisu zasobów (ARG)
- Zarejestrowano przykładowe zapytanie ARG dotyczące [identyfikowania kont bez włączonej usługi uwierzytelniania wieloskładnikowego (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

W ciągu ARG istnieją tabele danych, których można używać w zapytaniach.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie"
}
```
W związku z tym firma Microsoft. Security/Assessments będzie przechowywać rekord dla każdej takiej oceny zasad w następujący sposób:

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

**Przykład konwersji istniejącego zapytania ARG przy użyciu securityStatuses, aby teraz korzystać z tabeli ocen:**

Zapytanie odwołujące się do SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Zastępowanie zapytania dla tabeli ocen:

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

Dowiedz się więcej na następujące linki:
- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Wrzesień 2020

Aktualizacje we wrześniu obejmują:
- [Security Center Pobiera nowy wygląd!](#security-center-gets-a-new-look)
- [Wydano usługę Azure Defender](#azure-defender-released)
- [Usługa Azure Defender dla Key Vault jest ogólnie dostępna](#azure-defender-for-key-vault-is-generally-available)
- [Usługa Azure Defender do ochrony magazynu dla plików i ADLS Gen2 jest ogólnie dostępna](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Narzędzia spisu zasobów są teraz ogólnie dostępne](#asset-inventory-tools-are-now-generally-available)
- [Wyłączenie konkretnej luki w zabezpieczeniach w poszukiwaniu skanów rejestrów kontenerów i maszyn wirtualnych](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Wykluczanie zasobu z rekomendacji](#exempt-a-resource-from-a-recommendation)
- [Łączniki AWS i GCP w Security Center zapewniają środowisko z obsługą chmury](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pakiet rekomendacji ochrony obciążeń Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Wyniki oceny luk w zabezpieczeniach są teraz dostępne w przypadku eksportu ciągłego](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Zapobiegaj błędom konfiguracji zabezpieczeń, wymuszając zalecenia podczas tworzenia nowych zasobów](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Ulepszone zalecenia dotyczące sieciowej grupy zabezpieczeń](#network-security-group-recommendations-improved)
- [Przestarzałe zalecenie dotyczące wersji zapoznawczej AKS "pod kątem zasad zabezpieczeń należy zdefiniować w usługach Kubernetes Services"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Ulepszona wiadomość e-mail z powiadomieniem Azure Security Center](#email-notifications-from-azure-security-center-improved)
- [Wynik zabezpieczony nie obejmuje zaleceń dotyczących wersji zapoznawczej](#secure-score-doesnt-include-preview-recommendations)
- [Zalecenia zawierają teraz wskaźnik ważności i interwał Aktualności](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center Pobiera nowy wygląd!

Opublikowano odświeżony interfejs użytkownika dla stron portalu Security Center. Nowe strony zawierają nową stronę przeglądu, a także pulpity nawigacyjne do zabezpieczania oceny, spisu zasobów i usługi Azure Defender.

Przeprojektowana Strona przeglądu zawiera teraz kafelek umożliwiające dostęp do usług pulpitu nawigacyjnego bezpiecznego oceny, spisu zasobów i usługi Azure Defender. Zawiera również kafelek łączący pulpit nawigacyjny zgodności z przepisami.

Dowiedz się więcej o [stronie Przegląd](overview-page.md).


### <a name="azure-defender-released"></a>Wydano usługę Azure Defender

**Usługa Azure Defender** to platforma ochrony obciążeń w chmurze (CWPP) zintegrowana w ramach Security Center na potrzeby zaawansowanej, inteligentnej, chronionej platformy Azure i obciążeń hybrydowych. Zastępuje Security Center opcję warstwy cenowej standardowa. 

Po włączeniu usługi Azure Defender w obszarze **cennika i ustawienia** Azure Security Center następujące plany usługi Defender są dostępne jednocześnie i zapewniają kompleksowe zabezpieczenia dla warstw obliczeniowych, danych i usług w środowisku:

- [Usługa Azure Defender dla serwerów](defender-for-servers-introduction.md)
- [Usługa Azure Defender dla usługi App Service](defender-for-app-service-introduction.md)
- [Usługa Azure Defender dla usługi Storage](defender-for-storage-introduction.md)
- [Usługa Azure Defender dla bazy danych SQL](defender-for-sql-introduction.md)
- [Usługa Azure Defender dla usługi Key Vault](defender-for-key-vault-introduction.md)
- [Usługa Azure Defender dla platformy Kubernetes](defender-for-kubernetes-introduction.md)
- [Usługa Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)

Każdy z tych planów został opisany osobno w dokumentacji Security Center.

Dzięki dedykowanemu pulpitowi nawigacyjnemu usługa Azure Defender udostępnia alerty zabezpieczeń i zaawansowaną ochronę przed zagrożeniami dla maszyn wirtualnych, baz danych SQL, kontenerów, aplikacji sieci Web, sieci i innych.

[Dowiedz się więcej o usłudze Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Usługa Azure Defender dla Key Vault jest ogólnie dostępna

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. 

**Usługa Azure Defender dla Key Vault** zapewnia platformę Azure — natywną, zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Dzięki rozszerzeniu usługa Azure Defender dla Key Vault chroni wiele zasobów zależnych od kont Key Vault.

Plan opcjonalny jest teraz rozpowszechny. Ta funkcja była dostępna w wersji zapoznawczej jako "Zaawansowana ochrona przed zagrożeniami dla Azure Key Vault".

Ponadto strony Key Vault w Azure Portal zawierają teraz dedykowaną stronę **zabezpieczeń** dla **Security Center** zaleceń i alertów.

Dowiedz się więcej w [usłudze Azure Defender dla Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Usługa Azure Defender do ochrony magazynu dla plików i ADLS Gen2 jest ogólnie dostępna 

**Usługa Azure Defender for Storage** wykrywa potencjalnie szkodliwe działanie na kontach usługi Azure Storage. Dane można chronić, niezależnie od tego, czy są przechowywane jako kontenery obiektów blob, udziały plików czy jeziora danych.

Obsługa [Azure Files](../storage/files/storage-files-introduction.md) i [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) jest teraz ogólnie dostępna.

Od 1 października 2020 rozpocznie się naliczanie opłat za ochronę zasobów w tych usługach.

Dowiedz się więcej w [usłudze Azure Defender dla magazynu](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Narzędzia spisu zasobów są teraz ogólnie dostępne

Strona spisu zasobów Azure Security Center udostępnia pojedynczą stronę do wyświetlania stan zabezpieczeń zasobów, z którymi nawiązano połączenie Security Center.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Jeśli którykolwiek z zasobów ma zaległe zalecenia, zostaną one wyświetlone w spisie.

Dowiedz się więcej w temacie [Eksplorowanie zasobów i zarządzanie nimi przy użyciu spisu zasobów i narzędzi do zarządzania](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Wyłączenie konkretnej luki w zabezpieczeniach w poszukiwaniu skanów rejestrów kontenerów i maszyn wirtualnych

Usługa Azure Defender zawiera skanery luk w zabezpieczeniach do skanowania obrazów w Azure Container Registry i maszynach wirtualnych.

Jeśli istnieje organizacja, która ma być ignorowana, zamiast skorygować ją, możesz ją wyłączyć. Wyłączone wyniki nie wpływają na swój Bezpieczny wynik ani nie generują niechcianych szumów.

Gdy wyszukiwanie jest zgodne z kryteriami zdefiniowanymi w regułach wyłączania, nie będą wyświetlane na liście wyników.

Ta opcja jest dostępna na stronie Szczegóły zaleceń dla:

- **Luki w zabezpieczeniach Azure Container Registry obrazów należy skorygować**
- **Luki w zabezpieczeniach maszyn wirtualnych należy skorygować**

Dowiedz się więcej w temacie [wyłączanie określonych wyników dla obrazów kontenerów](defender-for-container-registries-usage.md#disable-specific-findings-preview) i [wyłączanie określonych wyników dla maszyn wirtualnych](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Wykluczanie zasobu z rekomendacji

Czasami zasób będzie wyświetlany jako w złej kondycji w odniesieniu do konkretnego zalecenia (i w związku z tym obniżenie bezpiecznego wyniku), nawet jeśli nie będzie to możliwe. Być może został skorygowany przez proces, który nie jest śledzony przez Security Center. Lub być może Twoja organizacja zdecydowała się zaakceptować ryzyko związane z tym konkretnym zasobem. 

W takich przypadkach można utworzyć regułę wykluczania i upewnić się, że zasób nie znajduje się na liście zasobów w złej kondycji w przyszłości. Te reguły mogą zawierać udokumentowane uzasadnienia, jak opisano poniżej.

Dowiedz się więcej na temat [wykluczania zasobu z rekomendacji i bezpiecznego oceny](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Łączniki AWS i GCP w Security Center zapewniają środowisko z obsługą chmury

W przypadku obciążeń w chmurze zwykle obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą być takie same.

Azure Security Center teraz chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Dołączanie kont AWS i GCP do Security Center integruje się z usługą AWS Security Hub, GCP Security Command i Azure Security Center. 

Więcej informacji znajduje się w temacie [Łączenie kont AWS w celu Azure Security Center](quickstart-onboard-aws.md) i [łączenia kont GCP z Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pakiet rekomendacji ochrony obciążeń Kubernetes

Aby zapewnić, że obciążenia Kubernetes są zabezpieczone domyślnie, Security Center dodaje zalecenia dotyczące ograniczania poziomu Kubernetes, w tym opcje wymuszania z Kubernetes Admission Control.

Po zainstalowaniu dodatku Azure Policy dla Kubernetes w klastrze AKS, każde żądanie do serwera interfejsu API Kubernetes zostanie monitorowane względem wstępnie zdefiniowanego zestawu najlepszych rozwiązań przed utrwaleniem w klastrze. Następnie można skonfigurować w celu wymuszenia najlepszych rozwiązań i ich upoważnienia do przyszłych obciążeń.

Na przykład można przystąpić do tego, że kontenery uprzywilejowane nie powinny być tworzone, a wszystkie przyszłe żądania, które należy wykonać, zostaną zablokowane.

Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Wyniki oceny luk w zabezpieczeniach są teraz dostępne w przypadku eksportu ciągłego

Używaj eksportu ciągłego do przesyłania strumieniowego alertów i zaleceń w czasie rzeczywistym do usługi Azure Event Hubs, Log Analytics obszarów roboczych lub Azure Monitor. Z tego miejsca możesz zintegrować te dane z rozwiązań Siem (np. na platformie Azure, Power BI, na platformie Azure Eksplorator danych i nie tylko.

Narzędzia do oceny luk w zabezpieczeniach Security Center zwracają informacje o zasobach jako zalecenia z możliwością działania w ramach zalecenia "nadrzędny", takie jak "luki w zabezpieczeniach w maszynach wirtualnych, należy skorygować". 

W przypadku wybrania opcji zalecenia dotyczące zabezpieczeń można eksportować za pomocą eksportu ciągłego, a także włączyć opcję **Uwzględnij wnioski dotyczące zabezpieczeń** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie" :::

Powiązane strony:

- [Security Center rozwiązanie do oceny luk w zabezpieczeniach dla usługi Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Security Center rozwiązanie do oceny luk w zabezpieczeniach dla Azure Container Registry obrazów](defender-for-container-registries-usage.md)
- [Eksport ciągły](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Zapobiegaj błędom konfiguracji zabezpieczeń, wymuszając zalecenia podczas tworzenia nowych zasobów

Błędy konfiguracji zabezpieczeń są główną przyczyną zdarzeń zabezpieczeń. Security Center teraz ma możliwość *zapobiegania* błędom konfiguracji nowych zasobów w odniesieniu do określonych zaleceń. 

Ta funkcja może pomóc w zabezpieczeniu obciążeń i stabilizacji bezpiecznego wyniku.

Wymuszanie bezpiecznej konfiguracji w oparciu o określone zalecenie jest oferowane w dwóch trybach:

- Przy użyciu efektu **odmowy** Azure Policy można zatrzymać tworzenie zasobów w złej kondycji

- Przy użyciu opcji **Wymuszaj** można korzystać z **DeployIfNotExist** i automatycznego korygowania niezgodnych zasobów podczas tworzenia
 
Jest to dostępne dla wybranych zaleceń dotyczących zabezpieczeń i można je znaleźć w górnej części strony Szczegóły zasobu.

Dowiedz się więcej w temacie [zapobieganie błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Ulepszone zalecenia dotyczące sieciowej grupy zabezpieczeń

Udoskonalono następujące zalecenia dotyczące zabezpieczeń dotyczące sieciowych grup zabezpieczeń, aby zmniejszyć liczbę wystąpień fałszywie dodatnich.

- Wszystkie porty sieciowe powinny być ograniczone do sieciowej grupy zabezpieczeń skojarzonych z maszyną wirtualną
- Porty zarządzania powinny być zamknięte na maszynach wirtualnych
- Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń
- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Przestarzałe zalecenie dotyczące wersji zapoznawczej AKS "pod kątem zasad zabezpieczeń należy zdefiniować w usługach Kubernetes Services"

Zalecenia dotyczące wersji zapoznawczej muszą być zdefiniowane w usługach Kubernetes Services są przestarzałe zgodnie z opisem w dokumentacji [usługi Azure Kubernetes](../aks/use-pod-security-policies.md) .

Funkcja zasad zabezpieczeń (wersja zapoznawcza) jest ustawiana jako przestarzała i nie będzie już dostępna po 15 października Azure Policy 2020 AKS.

Gdy zasady zabezpieczeń (wersja zapoznawcza) są przestarzałe, należy wyłączyć tę funkcję w przypadku wszystkich istniejących klastrów przy użyciu przestarzałej funkcji w celu przeprowadzania przyszłych uaktualnień klastra i pozostawania w ramach pomocy technicznej systemu Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Ulepszona wiadomość e-mail z powiadomieniem Azure Security Center

Ulepszono następujące obszary wiadomości e-mail dotyczące alertów zabezpieczeń: 

- Dodano możliwość wysyłania powiadomień e-mail o alertach dotyczących wszystkich poziomów ważności
- Dodano możliwość powiadamiania użytkowników z różnymi rolami RBAC w ramach subskrypcji
- Aktywnie Powiadamiamy właścicieli subskrypcji domyślnie o alertach o wysokiej ważności (które mają wysokie prawdopodobieństwo naruszenia oryginalnego oprogramowania)
- Usunięto pole numeru telefonu ze strony konfiguracji powiadomień e-mail

Dowiedz się więcej w temacie [Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Wynik zabezpieczony nie obejmuje zaleceń dotyczących wersji zapoznawczej 

Security Center stale ocenia zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka.

Po odnalezieniu nowych zagrożeń nowe porady dotyczące zabezpieczeń są udostępniane w Security Center przez nowe zalecenia. Aby zapobiec niezależnym zmianom i poznawać okres prolongaty, w którym można poznać nowe zalecenia, zanim wpłyną one na Twoje wyniki, zalecenia oflagowane jako **Podgląd** nie są już uwzględniane w obliczeniach bezpiecznego wyniku. Powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, więc po zakończeniu okresu korzystania z wersji zapoznawczej będą one wchodzić w skład Twojego wyniku.

Ponadto zalecenia dotyczące **wersji zapoznawczej** nie renderują zasobu w złej kondycji.

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie":::

[Dowiedz się więcej na temat bezpiecznego oceny](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Zalecenia zawierają teraz wskaźnik ważności i interwał Aktualności

Strona szczegóły dotyczące rekomendacji zawiera teraz wskaźnik czasu świeżości (jeśli dotyczy) oraz jasne wyświetlanie ważności zalecenia.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie":::



## <a name="august-2020"></a>Sierpień 2020 r.

Aktualizacje w sierpniu obejmują:

- [Spis zasobów — zaawansowany nowy widok stan zabezpieczeń zasobów](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Dodano obsługę domyślnych ustawień zabezpieczeń Azure Active Directory (w przypadku uwierzytelniania wieloskładnikowego)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Dodano zalecenie dotyczące jednostek usługi](#service-principals-recommendation-added)
- [Ocena luk w zabezpieczeniach maszyn wirtualnych — zaleceń i zasad skonsolidowanych](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nowe zasady zabezpieczeń AKS dodane do inicjatywy ASC_default — wyłącznie do użytku przez klientów prywatnej wersji zapoznawczej](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Spis zasobów — zaawansowany nowy widok stan zabezpieczeń zasobów

Security Center spisu zasobów (obecnie w wersji zapoznawczej) umożliwia wyświetlenie stan zabezpieczeń zasobów, z którymi nawiązano połączenie Security Center.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach. Jeśli którykolwiek z zasobów ma zaległe zalecenia, zostaną one wyświetlone w spisie.

Widok i jego Filtry umożliwiają Eksplorowanie danych stan zabezpieczeń i podejmowanie dalszych działań w oparciu o Twoje wyniki.

Dowiedz się więcej na temat [spisu zasobów](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Dodano obsługę domyślnych ustawień zabezpieczeń Azure Active Directory (w przypadku uwierzytelniania wieloskładnikowego)

Security Center dodaliśmy pełną obsługę [zabezpieczeń domyślnych](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), bezpłatna ochrona zabezpieczeń firmy Microsoft.

Wartości domyślne zabezpieczeń zapewniają wstępnie skonfigurowane ustawienia zabezpieczeń tożsamości, aby chronić organizację przed typowymi atakami związanymi z tożsamościami. Zabezpieczenia domyślne chronią już więcej niż 5 000 000 dzierżawców; dzierżawy 50 000 są również chronione przez Security Center.

Security Center teraz zawiera zalecenie dotyczące zabezpieczeń, za każdym razem, gdy zidentyfikuje subskrypcję platformy Azure bez włączonej wartości domyślnej zabezpieczeń. Do tej pory Security Center zalecane włączenie uwierzytelniania wieloskładnikowego przy użyciu dostępu warunkowego, który jest częścią licencji Premium Azure Active Directory (AD). W przypadku klientów korzystających bezpłatnie z usługi Azure AD zalecamy włączenie ustawień domyślnych zabezpieczeń. 

Naszym celem jest zachęcanie większej liczby klientów do zabezpieczania środowisk w chmurze za pomocą usługi MFA i łagodzenie jednego z największych zagrożeń, które są również najbardziej wpływem na [bezpieczny wynik](secure-score-security-controls.md).

Dowiedz się więcej o [domyślnych ustawieniach zabezpieczeń](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Dodano zalecenie dotyczące jednostek usługi

Dodano nowe zalecenie, aby zalecić Security Center klientom używającym certyfikatów zarządzania do zarządzania swoimi subskrypcjami przełączać się do podmiotów usługi.

Zaleca się, **Aby Ochrona Twoich subskrypcji zamiast certyfikatów zarządzania była chroniona** przy użyciu jednostek usługi lub Azure Resource Manager do bezpieczniejszego zarządzania subskrypcjami. 

Więcej informacji o [obiektach głównych aplikacji i usług znajduje się w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych — zaleceń i zasad skonsolidowanych

Security Center przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione rozwiązanie do oceny luk w zabezpieczeniach. Jeśli nie zostanie odnalezione rozwiązanie do oceny luk w zabezpieczeniach, Security Center zapewnia zalecenie upraszczające wdrożenie.

Po znalezieniu luk w zabezpieczeniach Security Center zawiera zalecenia podsumowujące wyniki, które należy zbadać i skorygować w razie potrzeby.

Aby zapewnić spójne środowisko dla wszystkich użytkowników, niezależnie od używanego typu skanera, zostały ujednolicone cztery zalecenia w następujących dwóch:

|Ujednolicone zalecenie|Zmień opis|
|----|:----|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych**|Zastępuje dwa następujące zalecenia:<br> **•** Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys (obecnie przestarzałe) (dołączone do warstwy Standardowa)<br> **•** Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych (obecnie przestarzałe) (warstwy Standardowa i bezpłatna)|
|**Luki w zabezpieczeniach maszyn wirtualnych należy skorygować**|Zastępuje dwa następujące zalecenia:<br>**•** Koryguj luki w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys) (obecnie przestarzałe)<br>**•** Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach (obecnie przestarzałe)|
|||

Teraz użyjesz tego samego zalecenia do wdrożenia rozszerzenia oceny luk w zabezpieczeniach Security Center lub rozwiązania z licencją prywatną ("BYOL") od partnera, takiego jak Qualys lub Rapid7.

Ponadto po znalezieniu luk w zabezpieczeniach i zaraportowaniu ich do Security Center, jedno zalecenie będzie powiadamiać o wynikach niezależnie od rozwiązania do oceny luk w zabezpieczeniach, które je określiło.

#### <a name="updating-dependencies"></a>Aktualizowanie zależności

Jeśli masz skrypty, zapytania lub automatyzacje odwołujące się do poprzednich zaleceń lub kluczy zasad/nazw, Użyj poniższych tabel, aby zaktualizować odwołania:

##### <a name="before-august-2020"></a>Przed 2020 sierpnia

|Zalecenie|Zakres|
|----|:----|
|**Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)**<br>Klucz: 550e890b-e652-4d22-8274-60b3bdb24c63|Wbudowane|
|**Koryguj luki w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)**<br>Klucz: 1195afff-c881-495e-9bc5-1486211ae03f|Wbudowane|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych**<br>Klucz: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Klucz: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zasady|Zakres|
|----|:----|
|**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Wbudowane|
|**Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Identyfikator zasad: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od sierpnia 2020

|Zalecenie|Zakres|
|----|:----|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych**<br>Klucz: ffff0522-1e88-47fc-8382-2a80ba848f5d|Wbudowane + BYOL|
|**Luki w zabezpieczeniach maszyn wirtualnych należy skorygować**<br>Klucz: 1195afff-c881-495e-9bc5-1486211ae03f|Wbudowane + BYOL|
||||

|Zasady|Zakres|
|----|:----|
|[**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Wbudowane + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nowe zasady zabezpieczeń AKS dodane do inicjatywy ASC_default — wyłącznie do użytku przez klientów prywatnej wersji zapoznawczej

Aby zapewnić, że obciążenia Kubernetes są zabezpieczone domyślnie, Security Center dodaje zasady na poziomie Kubernetes i zalecenia dotyczące ograniczania funkcjonalności, w tym opcje wymuszania z Kubernetes Admission Control.

Wczesna Faza tego projektu obejmuje prywatną wersję zapoznawczą oraz dodanie nowych (domyślnie wyłączonych) zasad do inicjatywy ASC_default.

Można bezpiecznie zignorować te zasady i nie będzie to miało wpływu na Twoje środowisko. Jeśli chcesz je włączyć, Utwórz konto w wersji zapoznawczej, https://aka.ms/SecurityPrP a następnie wybierz jedną z następujących opcji:

1. **Pojedynczej wersji zapoznawczej** — aby dołączyć tylko do tej prywatnej wersji zapoznawczej. Jawnie wskaż "ciągłe skanowanie ASC" jako wersję zapoznawczą, do której chcesz dołączyć.
1. **Program** będący w toku — zostanie dodany do tego i przyszłych wersji zapoznawczych. Konieczne będzie ukończenie profilu i umowy dotyczącej ochrony prywatności.


## <a name="july-2020"></a>Lipiec 2020 r.

Aktualizacje w lipcu obejmują:
- [Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrona przed zagrożeniami dla usługi Azure Storage rozwinięta w celu uwzględnienia Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Udoskonalenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżone dokumenty](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptacyjne kontrolki aplikacji zostały zaktualizowane z nowym zaleceniem i obsługą symboli wieloznacznych w regułach ścieżki](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sześć zasad usługi SQL Advanced Data Security zostało wycofane](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace

Podczas wdrażania rozwiązania do oceny luk w zabezpieczeniach Security Center poprzednio wykonać sprawdzenie poprawności przed wdrożeniem. Sprawdzenie, czy chcesz potwierdzić jednostkę SKU docelowej maszyny wirtualnej. 

W ramach tej aktualizacji sprawdzanie zostało usunięte i teraz można wdrożyć narzędzia do oceny luk w zabezpieczeniach na komputerach z systemem Windows i Linux. Niestandardowe obrazy są modyfikowane przy użyciu ustawień domyślnych portalu Marketplace.

Chociaż teraz można wdrożyć zintegrowane rozwiązanie do oceny luk w zabezpieczeniach (obsługiwane przez Qualys) na wielu maszynach, pomoc techniczna jest dostępna tylko w przypadku korzystania z systemu operacyjnego wymienionego w temacie [wdrażanie skanera zintegrowanej luki w zabezpieczeniach w warstwie Standardowa](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

Dowiedz się więcej o [zintegrowanym skanerze luk w zabezpieczeniach dla maszyn wirtualnych (wymaga usługi Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Dowiedz się więcej o korzystaniu z rozwiązania do oceny luk w zabezpieczeniach w ramach usługi Qualys lub Rapid7 w [ramach wdrażania rozwiązania partnerskiego do skanowania](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Storage rozwinięta w celu uwzględnienia Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)

Ochrona przed zagrożeniami dla usługi Azure Storage wykrywa potencjalnie szkodliwe działanie na kontach usługi Azure Storage. Security Center wyświetla alerty w przypadku wykrycia prób uzyskania dostępu do kont magazynu lub korzystania z nich. 

Dane można chronić, niezależnie od tego, czy są przechowywane jako kontenery obiektów blob, udziały plików czy jeziora danych.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami

Dodano osiem nowych zaleceń w celu zapewnienia prostego sposobu włączania funkcji ochrony przed zagrożeniami Azure Security Center dla następujących typów zasobów: maszyny wirtualne, plany App Service, serwery Azure SQL Database, serwery SQL na maszynach, konta usługi Azure Storage, klastry usługi Azure Kubernetes, rejestry Azure Container Registry i magazyny Azure Key Vault.

Nowe zalecenia są następujące:

- **Zaawansowane zabezpieczenia danych powinny być włączone na serwerach Azure SQL Database**
- **Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w planach Azure App Service**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w rejestrach Azure Container Registry**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w magazynach Azure Key Vault**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w klastrach usługi Azure Kubernetes**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona na kontach usługi Azure Storage**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona na maszynach wirtualnych**

Te nowe rekomendacje należą do kontroli zabezpieczeń **włączenia zaawansowanej ochrony przed zagrożeniami** .

Zalecenia obejmują również możliwość szybkiej naprawy. 

> [!IMPORTANT]
> Korygowaniem dowolne z tych zaleceń spowoduje naliczenie opłat za ochronę odpowiednich zasobów. Opłaty zostaną naliczone natychmiast, jeśli masz powiązane zasoby w bieżącej subskrypcji. Lub w przyszłości, jeśli dodasz je w późniejszym czasie.
> 
> Jeśli na przykład nie masz żadnych klastrów usługi Azure Kubernetes w subskrypcji i włączysz ochronę przed zagrożeniami, nie zostaną naliczone żadne opłaty. Jeśli w przyszłości dodasz klaster w ramach tej samej subskrypcji, zostanie on automatycznie objęty ochroną, a opłaty będą naliczane w tym czasie.

Więcej informacji na ten temat znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).

Dowiedz się więcej o [ochronie przed zagrożeniami w programie Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Udoskonalenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżone dokumenty

W ramach ciągłych inwestycji w domenie zabezpieczeń kontenerów mamy przyjemność podzielić znaczącą poprawę wydajności w odniesieniu do dynamicznych skanów obrazów kontenerów przechowywanych w Azure Container Registry Security Center. Skany są zwykle wykonywane zazwyczaj w ciągu około dwóch minut. W niektórych przypadkach może to potrwać do 15 minut.

Aby zwiększyć przejrzystość i wskazówki dotyczące możliwości zabezpieczeń kontenerów Azure Security Center, odświeżamy również strony dokumentacji zabezpieczeń kontenerów. 

Dowiedz się więcej o zabezpieczeniach kontenerów Security Center w następujących artykułach:

- [Przegląd funkcji zabezpieczeń kontenera Security Center](container-security.md)
- [Szczegóły integracji z Azure Container Registry](defender-for-container-registries-introduction.md)
- [Szczegóły integracji z usługą Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Instrukcje dotyczące skanowania rejestrów i ograniczania funkcjonalności hostów platformy Docker](container-security.md)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami dla klastrów usługi Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami dla hostów usługi Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Zalecenia dotyczące zabezpieczeń dla kontenerów](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptacyjne kontrolki aplikacji zostały zaktualizowane z nowym zaleceniem i obsługą symboli wieloznacznych w regułach ścieżki

Funkcja adaptacyjnych kontrolek aplikacji otrzymała dwie istotne aktualizacje:

* Nowe zalecenie identyfikuje potencjalnie wiarygodne zachowanie, które wcześniej nie było dozwolone. Nowe zalecenie, **dozwolonych reguły w ramach adaptacyjnych zasad kontroli aplikacji należy zaktualizować**, monituje o dodanie nowych reguł do istniejących zasad w celu zmniejszenia liczby fałszywych dodatnich w ramach adaptacyjnych kontroli aplikacji.

* Reguły ścieżek obsługują teraz symbole wieloznaczne. W ramach tej aktualizacji można skonfigurować dozwolone reguły ścieżek przy użyciu symboli wieloznacznych. Istnieją dwa obsługiwane scenariusze:

    * Używanie symbolu wieloznacznego na końcu ścieżki, aby zezwolić na wszystkie pliki wykonywalne w tym folderze i podfolderach

    * Użycie symbolu wieloznacznego w środku ścieżki w celu włączenia znanej nazwy pliku wykonywalnego ze zmianą nazwy folderu (np. osobiste foldery użytkowników z znanym plikiem wykonywalnym, automatycznie wygenerowanymi nazwami folderów itp.).


[Dowiedz się więcej na temat adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sześć zasad usługi SQL Advanced Data Security zostało wycofane

Sześć zasad związanych z zaawansowanymi zabezpieczeniami danych dla maszyn SQL jest przestarzałych:

- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL
- Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server

Dowiedz się więcej o [zasadach wbudowanych](./policy-reference.md).





## <a name="june-2020"></a>Czerwiec 2020 r.

Aktualizacje w czerwcu obejmują:
- [Interfejs API usługi Secure Score (wersja zapoznawcza)](#secure-score-api-preview)
- [Zaawansowane zabezpieczenia danych dla maszyn SQL (Azure, innych chmur i Premium) (wersja zapoznawcza)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Dwa nowe zalecenia dotyczące wdrażania agenta Log Analytics na maszynach usługi Azure ARC (wersja zapoznawcza)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nowe zasady tworzenia eksportu ciągłego i konfiguracji automatyzacji przepływu pracy na dużą skalę](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nowe zalecenie dotyczące używania sieciowych grup zabezpieczeń do ochrony maszyn wirtualnych niepołączonych z Internetem](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nowe zasady umożliwiające ochronę przed zagrożeniami i zaawansowane zabezpieczenia danych](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Interfejs API usługi Secure Score (wersja zapoznawcza)

Teraz możesz uzyskać dostęp do oceny za pośrednictwem [interfejsu API protokołu Secure Score](/rest/api/securitycenter/securescores/) (obecnie w wersji zapoznawczej). Metody interfejsu API zapewniają elastyczność umożliwiającą wykonywanie zapytań dotyczących danych i Tworzenie własnego mechanizmu raportowania z bezpiecznymi wynikami w czasie. Na przykład możesz użyć interfejsu API **Secure Scores** , aby uzyskać ocenę dla określonej subskrypcji. Ponadto można użyć interfejsu API **kontroli** zabezpieczeń, aby wyświetlić listę kontrolek bezpieczeństwa i bieżący wynik subskrypcji.

Aby zapoznać się z przykładowymi narzędziami zewnętrznymi z bezpiecznym interfejsem API oceny, zapoznaj [się z obszarem "bezpieczeństwo" w naszej społeczności usługi GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Dowiedz się więcej o [bezpiecznych kontrolach oceny i zabezpieczeń w Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Zaawansowane zabezpieczenia danych dla maszyn SQL (Azure, innych chmur i Premium) (wersja zapoznawcza)

Usługa Advanced Data Security Azure Security Center dla maszyn SQL chroni teraz serwery SQL hostowane na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych. Rozszerza to ochronę natywnych serwerów SQL platformy Azure, aby w pełni obsługiwać środowiska hybrydowe.

Zaawansowane zabezpieczenia danych zapewniają ocenę luk w zabezpieczeniach i zaawansowaną ochronę przed zagrożeniami dla maszyn SQL, wszędzie tam, gdzie się znajdują.

Konfiguracja obejmuje dwie czynności:

1. Wdrożenie agenta Log Analytics na komputerze-hoście SQL Server w celu udostępnienia połączenia z kontem platformy Azure.

1. Włączanie opcjonalnego pakietu na stronie cennika i ustawienia Security Center.

Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych dla maszyn SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dwa nowe zalecenia dotyczące wdrażania agenta Log Analytics na maszynach usługi Azure ARC (wersja zapoznawcza)

Dodano dwa nowe zalecenia ułatwiające wdrożenie [agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md) na maszynach usługi Azure Arc i zapewnienie ich ochrony przez Azure Security Center:

- **Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)**
- **Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)**

Te nowe zalecenia pojawią się w tych samych czterech kontrolach zabezpieczeń, co istniejące (powiązane) zalecenie, **Agent monitorowania powinien być zainstalowany na maszynach**: skoryguj konfiguracje zabezpieczeń, Zastosuj adaptacyjną kontrolę aplikacji, Zastosuj aktualizacje systemu i Włącz program Endpoint Protection.

Zalecenia obejmują również funkcję szybkiej naprawy, która ułatwia przyspieszenie procesu wdrożenia. 

Dowiedz się więcej na temat tych dwóch nowych zaleceń w tabeli [zaleceń dotyczących obliczeń i aplikacji](recommendations-reference.md#recs-computeapp) .

Dowiedz się więcej o tym, jak Azure Security Center używa agenta w [ramach agentów log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

Dowiedz się więcej [na temat rozszerzeń dla maszyn usługi Azure Arc](../azure-arc/servers/manage-vm-extensions.md#enable-extensions-from-the-portal).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nowe zasady tworzenia eksportu ciągłego i konfiguracji automatyzacji przepływu pracy na dużą skalę

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas potrzebny do zbadania i ograniczenia zdarzeń związanych z bezpieczeństwem.

Aby wdrożyć konfiguracje automatyzacji w organizacji, Użyj wbudowanych zasad platformy Azure "DeployIfdNotExist", aby utworzyć i skonfigurować procedury automatyzacji [ciągłego eksportowania](continuous-export.md) i [przepływu pracy](workflow-automation.md) :

Zasady można znaleźć w usłudze Azure Policy:


|Cel  |Zasady  |Identyfikator zasad  |
|---------|---------|---------|
|Eksport ciągły do centrum zdarzeń|[Wdróż eksportowanie alertów i zaleceń usługi Azure Security Center do centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Eksport ciągły do obszaru roboczego Log Analytics|[Wdróż eksport do obszaru roboczego usługi Log Analytics w celu uzyskania alertów i zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Wprowadzenie do [szablonów automatyzacji przepływu pracy](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Dowiedz się więcej o korzystaniu z dwóch zasad eksportu w celu [ciągłego eksportowania Azure Security Center alertów i zaleceń za pośrednictwem zasad](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nowe zalecenie dotyczące używania sieciowych grup zabezpieczeń do ochrony maszyn wirtualnych niepołączonych z Internetem

Kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń" obejmuje teraz następujące nowe zalecenia:

- **Maszyny wirtualne, które nie są dostępne w Internecie, powinny być chronione przy użyciu sieciowych grup zabezpieczeń**

Istniejące zalecenie, dostępne **z Internetu maszyny wirtualne powinny być chronione za pomocą sieciowych grup zabezpieczeń**, nie rozróżniać się między maszynami wirtualnymi internetowymi i niepołączonymi z Internetem. Dla obu tych celów zostało wygenerowane zalecenie o wysokiej ważności, jeśli maszyna wirtualna nie została przypisana do sieciowej grupy zabezpieczeń. To nowe zalecenie oddziela maszyny niepołączone z Internetem, aby zmniejszyć liczbę fałszywych ostrzeżeń i uniknąć niepotrzebnych alertów o wysokiej ważności.

Dowiedz się więcej z tabeli [zalecenia dotyczące sieci](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nowe zasady umożliwiające ochronę przed zagrożeniami i zaawansowane zabezpieczenia danych

Nowe zasady poniżej zostały dodane do inicjatywy default ASC i zostały zaprojektowane z myślą o włączeniu ochrony przed zagrożeniami lub zaawansowanej ochrony danych dla odpowiednich typów zasobów.

Zasady można znaleźć w usłudze Azure Policy:


| Zasady                                                                                                                                                                                                                                                                | Identyfikator zasad                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Zaawansowane zabezpieczenia danych powinny być włączone na serwerach Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona na kontach usługi Azure Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w magazynach Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w planach Azure App Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w rejestrach Azure Container Registry](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w klastrach usługi Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona na Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Dowiedz się więcej o [ochronie przed zagrożeniami w programie Azure Security Center](azure-defender.md).





## <a name="may-2020"></a>Maj 2020 r.

Aktualizacje w programie mogą obejmować:
- [Reguły pomijania alertów (wersja zapoznawcza)](#alert-suppression-rules-preview)
- [Ocena luk w zabezpieczeniach maszyn wirtualnych jest teraz ogólnie dostępna](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Zmiany dostępu do maszyny wirtualnej just-in-Time (JIT)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Zalecenia niestandardowe zostały przeniesione do oddzielnej kontroli zabezpieczeń](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Przełącznik dodany do wyświetlania zaleceń w kontrolkach lub jako płaska lista](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Rozszerzona kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń"](#expanded-security-control-implement-security-best-practices)
- [Zasady niestandardowe z metadanymi niestandardowymi są teraz ogólnie dostępne](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Funkcje analizy zrzutów awaryjnych migrowane do wykrywania ataków bezplikowych](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Reguły pomijania alertów (wersja zapoznawcza)

Ta nowa funkcja (obecnie dostępna w wersji zapoznawczej) umożliwia zmniejszenie zmęczenia alertów. Użyj reguł, aby automatycznie ukrywać alerty, które są znane jako niewielkiej ilości lub związane z normalnymi działaniami w organizacji. Pozwala to skupić się na najbardziej odpowiednich zagrożeniach. 

Alerty zgodne z włączonymi regułami pomijania będą nadal generowane, ale ich stan zostanie ustawiony na odrzucony. Stan można sprawdzić w Azure Portal lub uzyskać dostęp do Security Center alertów zabezpieczeń.

Reguły pomijania definiują kryteria, dla których alerty powinny być automatycznie odrzucane. Zazwyczaj można użyć reguły pomijania, aby:

- Pomijaj alerty, które zostały zidentyfikowane jako fałszywe pozytywne

- Pomijanie alertów, które są wyzwalane zbyt często, aby być przydatne

Dowiedz się więcej [na temat pomijania alertów z ochrony przed zagrożeniami w usłudze Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych jest teraz ogólnie dostępna

Warstwa standardowa Security Center obejmuje teraz zintegrowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. To rozszerzenie jest obsługiwane przez Qualys, ale raportuje jego wyniki bezpośrednio z powrotem do Security Center. Nie potrzebujesz licencji Qualys, a nawet konta Qualys — wszystko, co jest obsługiwane bezproblemowo w Security Center.

Nowe rozwiązanie może stale skanować maszyny wirtualne, aby znaleźć luki w zabezpieczeniach i przedstawić wyniki w Security Center. 

Aby wdrożyć rozwiązanie, należy zastosować nowe zalecenie dotyczące zabezpieczeń:

"Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)"

Dowiedz się więcej [na temat oceny zintegrowanej luki w zabezpieczeniach Security Center dla maszyn wirtualnych](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Zmiany dostępu do maszyny wirtualnej just-in-Time (JIT)

Security Center zawiera opcjonalną funkcję ochrony portów zarządzania maszynami wirtualnymi. Zapewnia to obronę przed najbardziej powszechnymi atakami na ataki.

Ta aktualizacja wprowadza następujące zmiany w tej funkcji:

- Zalecenie, które zalecić włączenie JIT na maszynie wirtualnej, zostało zmienione. Wcześniej na maszynach wirtualnych należy zastosować kontrolę dostępu do sieci just-in-Time, która jest teraz: "porty zarządzania maszyn wirtualnych należy chronić za pomocą kontroli dostępu just in Time do sieci".

- Zalecenie jest wyzwalane tylko wtedy, gdy istnieją otwarte porty zarządzania.

Dowiedz się więcej o [funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Zalecenia niestandardowe zostały przeniesione do oddzielnej kontroli zabezpieczeń

Jedna kontrola zabezpieczeń wprowadzona z ulepszonym zabezpieczonym wynikiem to "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Wszelkie niestandardowe zalecenia utworzone dla subskrypcji zostały automatycznie umieszczone w tym formancie. 

Aby ułatwić znalezienie niestandardowych zaleceń, przeniesiono je do dedykowanej kontroli zabezpieczeń, "zalecenia niestandardowe". Ten formant nie ma wpływu na Twój bezpieczny wynik.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Przełącznik dodany do wyświetlania zaleceń w kontrolkach lub jako płaska lista

Funkcje kontroli zabezpieczeń są logicznymi grupami powiązanych zaleceń dotyczących zabezpieczeń. Odzwierciedlają one podatne na ataki. Kontrolka jest zestawem zaleceń dotyczących zabezpieczeń, z instrukcjami, które ułatwiają zaimplementowanie tych zaleceń.

Aby od razu sprawdzić, jak dobrze organizacja zabezpiecza poszczególne osoby atakujące, zapoznaj się z wynikami każdej kontroli zabezpieczeń.

Domyślnie Twoje zalecenia są wyświetlane w obszarze zabezpieczenia. W ramach tej aktualizacji można także wyświetlić je jako listę. Aby wyświetlić je jako prostą listę posortowaną według stanu kondycji odpowiednich zasobów, Użyj nowego przełącznika "Grupuj według kontrolek". Przełącznik znajduje się nad listą w portalu.

Formanty zabezpieczeń — i ten przełącznik — są częścią nowego bezpiecznego wyniku. Pamiętaj, aby wysłać nam swoją opinię z portalu.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Rozszerzona kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń" 

Jedna kontrola zabezpieczeń wprowadzona w ramach udoskonalonego bezpiecznego wyniku to "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Gdy zalecenie jest w tej kontrolce, nie ma to wpływu na bezpieczny wynik. 

W przypadku tej aktualizacji trzy zalecenia zostały przeniesione z kontrolek, w których zostały one pierwotnie umieszczone, i do tej kontroli najlepszych rozwiązań. Ten krok został osiągnięty, ponieważ wykryto, że ryzyko tych trzech zaleceń jest mniejsze niż początkowo zostało przemyślane.

Ponadto wprowadzono dwa nowe rekomendacje i dodano je do tej kontrolki.

Trzy zaleceń, które przeniesiono:

- Usługę **MFA należy włączyć na kontach z uprawnieniami do odczytu w ramach subskrypcji** (wcześniej w formancie "Włącz usługę MFA").
- **Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji** (wcześniej w kontrolce "Zarządzanie dostępem i uprawnieniami").
- Dla subskrypcji (początkowo w kontrolce "Zarządzaj dostępem i uprawnieniami") **należy wyznaczyć maksymalnie 3 właścicieli** .

Dwa nowe rekomendacje dodane do kontrolki są następujące:

- **Rozszerzenie konfiguracji gościa powinno być zainstalowane na maszynach wirtualnych z systemem Windows (wersja zapoznawcza)** — używanie [Azure Policy konfiguracji gościa](../governance/policy/concepts/guest-configuration.md) zapewnia widoczność w obrębie maszyn wirtualnych do ustawień serwera i aplikacji (tylko system Windows).

- **Funkcja Windows Defender Exploit Guard powinna być włączona na maszynach (wersja zapoznawcza)** — usługa Windows Defender Exploit guard wykorzystuje Azure Policy agenta konfiguracji gościa. Funkcja Exploit Guard ma cztery składniki przeznaczone do blokowania urządzeń przed szeroką gamą wektorów ataków i blokowania zachowań często używanych w atakach złośliwego oprogramowania, a jednocześnie umożliwia przedsiębiorstwom zrównoważenie zagrożeń bezpieczeństwa i wymagań dotyczących produktywności (tylko system Windows).

Dowiedz się więcej o funkcji Windows Defender Exploit Guard w temacie [Tworzenie i wdrażanie zasad funkcji Exploit Guard](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Zasady niestandardowe z metadanymi niestandardowymi są teraz ogólnie dostępne

Zasady niestandardowe są teraz częścią środowiska zaleceń Security Center, bezpieczeństwa i oceny zgodności z przepisami. Ta funkcja jest teraz ogólnie dostępna i umożliwia zwiększenie zakresu oceny zabezpieczeń w organizacji w Security Center. 

Utwórz niestandardową inicjatywę w usłudze Azure Policy, Dodaj do niej zasady i Dołącz ją do Azure Security Center i Wizualizuj ją jako zalecenia.

Teraz dodano również opcję edytowania niestandardowych metadanych rekomendacji. Opcje metadanych obejmują ważność, kroki korygowania, informacje o zagrożeniach i nie tylko.  

Dowiedz się więcej o [ulepszaniu niestandardowych zaleceń ze szczegółowymi informacjami](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Funkcje analizy zrzutów awaryjnych migrowane do wykrywania ataków bezplikowych 

Firma Microsoft integruje możliwości wykrywania zrzutów awaryjnych systemu Windows () w [wykrywanie ataków bezplikowych](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Analiza wykrywania ataków bezplikowych zapewnia ulepszone wersje następujących alertów zabezpieczeń dla maszyn z systemem Windows: odnaleziono iniekcję kodu, wykryto zamaskowanego modułu systemu Windows, wykryty wykryto kod powłoki oraz podejrzany segment kodu.

Niektóre zalety tego przejścia:

- **Proaktywne i czasowe wykrywanie złośliwego oprogramowania** — podejście, które może oczekiwać na wystąpienie awarii, a następnie uruchomienie analizy, aby znaleźć złośliwe artefakty. Korzystanie z funkcji wykrywania ataków bez plików umożliwia aktywne zidentyfikowanie zagrożeń w pamięci, gdy są one uruchomione. 

- **Ulepszone alerty** — alerty zabezpieczeń związane z wykrywaniem ataków bez plików obejmują wzbogacania, które nie są dostępne, na przykład informacje o aktywnych połączeniach sieciowych. 

- **Agregacja alertów** — gdy serwer sieci wykryje wiele wzorców ataków w ramach pojedynczego zrzutu awaryjnego, wyzwoliło wiele alertów zabezpieczeń. Wykrywanie ataków bez plików łączy wszystkie zidentyfikowane wzorce ataku z tego samego procesu w jeden alert, usuwając konieczność skorelowania wielu alertów.

- **Obniżone wymagania w obszarze roboczym log Analytics** — Zrzuty awaryjne zawierające potencjalnie poufne dane nie będą już przekazywane do log Analytics obszaru roboczego.
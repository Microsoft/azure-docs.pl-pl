---
title: Informacje o wersji dla Azure Security Center
description: Opis nowości i zmian w programie Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c47543fa1401a8da533a9af3ebe4554e405622d3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835541"
---
# <a name="whats-new-in-azure-security-center"></a>Co nowego w programie Azure Security Center?

Security Center jest w trakcie aktywnego opracowywania i na bieżąco otrzymuje ulepszenia. Aby być na bieżąco z najnowszymi zmianami, ta strona zawiera informacje o nowych funkcjach, poprawkach błędów i przestarzałych funkcjach.

Ta strona jest często aktualizowana, dlatego należy ją często odwiedzać. 

Aby dowiedzieć się więcej *o planowanych* zmianach, które wkrótce Security Center, zobacz Ważne nadchodzące zmiany w [Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Jeśli szukasz elementów starszych niż sześć miesięcy, znajdziesz je w archiwum Co nowego w [programie Azure Security Center](release-notes-archive.md).

## <a name="april-2021"></a>Kwiecień 2021 r.

Aktualizacje w kwietniu obejmują:
- [Ostatnio ściągane obrazy rejestru kontenerów są teraz ponownie skanowane co tydzień (ogólna dostępność)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [Używanie Azure Defender dla Kubernetes do ochrony hybrydowych i wielochmurowych wdrożeń platformy Kubernetes (wersja zapoznawcza)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [Zalecenia dotyczące włączania usługi Azure Defender DNS i Resource Manager (wersja zapoznawcza)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [Dodano trzy standardy zgodności z przepisami: Azure CIS 1.3.0, CMMC Level 3 i New Zelandia ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Cztery nowe rekomendacje związane z konfiguracją gościa (wersja zapoznawcza)](#four-new-recommendations-related-to-guest-configuration-preview)
- [Zalecenia dotyczące cmk zostały przeniesione do kontroli zabezpieczeń najlepszych rozwiązań](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender są przestarzałe](#11-azure-defender-alerts-deprecated)
- [Dwa zalecenia z kontroli zabezpieczeń "Zastosuj aktualizacje systemu" zostały wycofane](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Azure Defender sql na kafelku komputera usunięty z pulpitu Azure Defender nawigacyjnego](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>Ostatnio ściągane obrazy rejestru kontenerów są teraz ponownie skanowane co tydzień (ogólna dostępność)

Azure Defender rejestrów kontenerów zawiera wbudowany skaner luk w zabezpieczeniach. Ten skaner natychmiast skanuje dowolny obraz wypchnięcie do rejestru i dowolny obraz ściągnięty w ciągu ostatnich 30 dni.

Nowe luki w zabezpieczeniach są codziennie odkrywane. Dzięki tej aktualizacji obrazy kontenerów, które zostały ściągnięte z rejestrów w ciągu ostatnich 30 dni, będą ponownie **skanowane** co tydzień. Dzięki temu na obrazach zostaną zidentyfikowane nowo wykryte luki w zabezpieczeniach.

Za skanowanie jest naliczana opłata za każdy obraz, więc za te ponowne skanowania nie są naliczane dodatkowe opłaty.

Dowiedz się więcej na temat tego skanera w te Azure Defender do skanowania obrazów pod poszukiwaniu luk w zabezpieczeniach za pomocą funkcji Azure Defender [rejestrów kontenerów.](defender-for-container-registries-usage.md)


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Używanie Azure Defender dla Kubernetes do ochrony hybrydowych i wielochmurowych wdrożeń platformy Kubernetes (wersja zapoznawcza)

Azure Defender dla Kubernetes rozszerza możliwości ochrony przed zagrożeniami, aby chronić klastry niezależnie od tego, gdzie są wdrażane. Zostało to włączone dzięki integracji z [Azure Arc kubernetes](../azure-arc/kubernetes/overview.md) i jego [nowymi możliwościami rozszerzeń.](../azure-arc/kubernetes/extensions.md) 

Po włączeniu usługi Azure Arc w klastrach spoza platformy Azure Kubernetes nowa rekomendacja od firmy Azure Security Center oferuje wdrożenie rozszerzenia Azure Defender za pomocą zaledwie kilku kliknięć.

Użyj zalecenia (**Azure Arc kubernetes** z włączonym programem powinny mieć zainstalowane rozszerzenie usługi Azure Defender ) i rozszerzenie do ochrony klastrów Kubernetes wdrożonych u innych dostawców chmury, chociaż nie w ich zarządzanych usługach Kubernetes.

Ta integracja między Azure Security Center, Azure Defender i Azure Arc kubernetes zapewnia:

- Łatwe aprowizowanie rozszerzenia Azure Defender w niechronionych Azure Arc klastrach Kubernetes (ręcznie i na dużą skalę)
- Monitorowanie rozszerzenia Azure Defender aprowizowania z portalu Azure Arc Portal
- Zalecenia dotyczące zabezpieczeń Security Center są zgłaszane na nowej stronie Zabezpieczenia w witrynie Azure Arc Portal
- Zidentyfikowane zagrożenia bezpieczeństwa Azure Defender są zgłaszane na nowej stronie Zabezpieczenia w Azure Arc Portal
- Azure Arc kubernetes są zintegrowane z platformą Azure Security Center platformą i doświadczeniem

Dowiedz się więcej [Azure Defender dla Kubernetes Use Azure Defender dla Kubernetes with your on-premises and multi-cloud Kubernetes clusters](defender-for-kubernetes-azure-arc.md)(Używanie usługi Azure Defender dla Kubernetes z lokalnymi i wielochmurowym klastrami Kubernetes).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center wdrażania rozszerzenia usługi Azure Defender dla Azure Arc kubernetes z włączoną obsługą usługi Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>Zalecenia dotyczące włączania usługi Azure Defender DNS i Resource Manager (wersja zapoznawcza)

Dodano dwa nowe zalecenia, aby uprościć proces włączania Azure Defender [dla](defender-for-resource-manager-introduction.md) Resource Manager i [Azure Defender dns:](defender-for-dns-introduction.md)

- **Azure Defender dla Resource Manager powinna** być włączona — program Defender dla usługi Resource Manager automatycznie monitoruje operacje zarządzania zasobami w organizacji. Azure Defender wykrywa zagrożenia i ostrzega o podejrzanych działaniach.
- **Azure Defender usługi DNS** powinna być włączona — usługa Defender dla systemu DNS zapewnia dodatkową warstwę ochrony zasobów w chmurze przez ciągłe monitorowanie wszystkich zapytań DNS z zasobów platformy Azure. Azure Defender alerty o podejrzanych działaniach w warstwie DNS.

Włączenie Azure Defender planów powoduje naliczanie opłat. Dowiedz się więcej o cenach dla regionu Security Center stronie cennika: https://aka.ms/pricing-security-center .

> [!TIP]
> Rekomendacje dotyczące wersji zapoznawczej nie renderują zasobu w złej kondycji i nie są uwzględniane w obliczeniach wskaźnika bezpieczeństwa. Koryguj je wszędzie tam, gdzie to możliwe, tak aby po zakończeniu okresu zapoznawczego przyczyniły się do oceny. Dowiedz się więcej o tym, jak reagować na te zalecenia, zobacz [Korygowanie zaleceń](security-center-remediate-recommendations.md)w Azure Security Center .


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Dodano trzy standardy zgodności z przepisami: Azure CIS 1.3.0, CMMC Level 3 i New Zelandia ISM Restricted

Dodaliśmy trzy standardy do użycia z Azure Security Center. Za pomocą pulpitu nawigacyjnego zgodności z przepisami można teraz śledzić zgodność za pomocą:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC Level 3](../governance/policy/samples/cmmc-l3.md)
- [Nowa Zelandia ISM z ograniczeniami](../governance/policy/samples/new-zealand-ism.md)

Możesz przypisać je do swoich subskrypcji zgodnie z opisem w tece Dostosowywanie zestawu standardów na pulpicie [nawigacyjnym zgodności z przepisami.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Dodano trzy standardy do Azure Security Center pulpitu nawigacyjnego zgodności z przepisami." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Dowiedz się więcej w:
- [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md)
- [Samouczek: poprawa zgodności z przepisami](security-center-compliance-dashboard.md)
- [Często zadawane pytania — pulpit nawigacyjny zgodności z przepisami](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Cztery nowe zalecenia dotyczące konfiguracji gościa (wersja zapoznawcza)

Rozszerzenie Konfiguracja [gościa platformy](../governance/policy/concepts/guest-configuration.md) Azure zgłasza Security Center, aby zapewnić, że ustawienia maszyn wirtualnych w gościu są wzmocnione. Rozszerzenie nie jest wymagane dla serwerów z usługą Arc, ponieważ jest zawarte w agencie połączonej maszyny z usługą Arc. Rozszerzenie wymaga tożsamości zarządzanej przez system na maszynie.

Dodaliśmy cztery nowe rekomendacje, aby Security Center jak najlepiej z tego rozszerzenia.

- Dwa zalecenia zawierają monit o zainstalowanie rozszerzenia i jego wymaganej tożsamości zarządzanej przez system:
    - **Rozszerzenie konfiguracji gościa powinno być zainstalowane na maszynach**
    - **Rozszerzenie konfiguracji gościa maszyn wirtualnych powinno zostać wdrożone z tożsamością zarządzaną przypisaną przez system**

- Po zainstalowaniu i uruchomieniu rozszerzenia rozpocznie się inspekcja maszyn. Zostanie wyświetlony monit o wzmacnianie ustawień, takich jak konfiguracja ustawień systemu operacyjnego i środowiska. Te dwa zalecenia będą monitować o wzmacnianie zabezpieczeń maszyn z systemami Windows i Linux zgodnie z opisem:
    - **Windows Defender na maszynach należy włączyć funkcję Exploit Guard**
    - **Uwierzytelnianie na maszynach z systemem Linux powinno wymagać kluczy SSH**

Dowiedz się więcej [w te Azure Policy konfiguracji gościa programu](../governance/policy/concepts/guest-configuration.md).

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>Zalecenia dotyczące cmk zostały przeniesione do kontroli zabezpieczeń najlepszych rozwiązań

Każdy program zabezpieczeń w organizacji obejmuje wymagania dotyczące szyfrowania danych. Domyślnie dane klientów platformy Azure są szyfrowane w spoczynku za pomocą kluczy zarządzanych przez usługę. Jednak klucze zarządzane przez klienta (CMK) są często wymagane do spełnienia standardów zgodności z przepisami. Klucz CMK umożliwia szyfrowanie danych przy użyciu [klucza Azure Key Vault](../key-vault/general/overview.md) utworzonego i należącego do Ciebie. Zapewnia to pełną kontrolę i odpowiedzialność za cykl życia klucza, w tym rotację i zarządzanie.

Azure Security Center zabezpieczeń są logicznymi grupami powiązanych zaleceń dotyczących zabezpieczeń i odzwierciedlają narażone na ataki. Każda kontrolka ma maksymalną liczbę punktów, które można dodać do oceny bezpieczeństwa w przypadku skorygowania wszystkich zaleceń wymienionych w kontrolce dla wszystkich zasobów. Kontrolka **zabezpieczeń Implementuj najlepsze** rozwiązania w zakresie zabezpieczeń nie ma żadnych punktów. Dlatego rekomendacje w tej kontrolce nie wpływają na ocenę bezpieczeństwa.

Zalecenia wymienione poniżej są przenoszone do kontroli zabezpieczeń **Implementowanie najlepszych** rozwiązań w zakresie zabezpieczeń, aby lepiej odzwierciedlały ich opcjonalny charakter. Dzięki temu można zagwarantować, że te zalecenia będą mieć najwłasniejszy sposób kontroli, aby osiągnąć cel.

- Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku
- Azure Machine Learning powinny być szyfrowane przy użyciu klucza zarządzanego przez klienta
- Cognitive Services powinny włączyć szyfrowanie danych przy użyciu klucza zarządzanego przez klienta
- Rejestry kontenerów powinny być szyfrowane przy użyciu klucza zarządzanego przez klienta (CMK)
- Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku
- Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku
- Konta magazynu powinny używać klucza zarządzanego przez klienta (CMK) do szyfrowania

Dowiedz się, które zalecenia znajdują się w poszczególnych mechanizmach kontroli [zabezpieczeń, i zapoznaj się z ich zaleceniami.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender są przestarzałe

Alerty Azure Defender są przestarzałe.

- Nowe alerty zastąpią te dwa alerty i zapewniają lepsze pokrycie:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | WERSJA ZAPOZNAWCZA — wykryto uruchomienie funkcji "Get-AzureDomainInfo" zestawu narzędzi MicroBurst |
    | ARM_MicroBurstRunbook    | WERSJA ZAPOZNAWCZA — wykryto uruchomienie funkcji "Get-AzurePasswords" zestawu narzędzi MicroBurst  |
    |                          |                                                                          |

- Te dziewięć alertów odnosi się do Azure Active Directory Identity Protection (IPC), który został już przestarzały:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Nieznane właściwości logowania |
    | AnonymousLogin      | Anonimowy adres IP          |
    | InfectedDeviceLogin | Adres IP połączony ze złośliwym oprogramowaniem     |
    | ImpossibleTravel    | Nietypowa podróż               |
    | MaliciousIP         | Złośliwy adres IP          |
    | LeakedCredentials   | Ujawnione poświadczenia            |
    | PasswordSnięciay       | Rozsyłanie haseł                |
    | LeakedCredentials   | Analiza zagrożeń w usłudze Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Te dziewięć alertów IPC nigdy nie Security Center alertów. Są one częścią łącznika usługi Azure Active Directory (AAD) Identity Protection (IPC), który wysyłał je do Security Center. W ciągu ostatnich dwóch lat jedynymi klientami, którzy widzili te alerty, są organizacje, które skonfigurowała eksport (z łącznika do usługi ASC) w 2019 r. lub wcześniej. IPC usługi AAD nadal pokazywał je we własnych systemach alertów i nadal były dostępne w Azure Sentinel. Jedyną zmianą jest to, że nie są już wyświetlane w Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Dwa zalecenia z kontroli zabezpieczeń "Zastosuj aktualizacje systemu" zostały wycofane 

Następujące dwa zalecenia zostały wycofane, a zmiany mogą spowodować niewielki wpływ na ocenę bezpieczeństwa:

- **Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny**
- **Na maszynach powinien być zainstalowany agent monitorowania.** To zalecenie dotyczy tylko maszyn lokalnych, a część jego logiki zostanie przeniesiona do innego zalecenia. Problemy z kondycją agenta usługi Log Analytics powinny zostać rozwiązane **na maszynach**

Zalecamy sprawdzenie konfiguracji ciągłego eksportu i automatyzacji przepływu pracy, aby sprawdzić, czy te zalecenia są w nich zawarte. Ponadto wszelkie pulpity nawigacyjne lub inne narzędzia do monitorowania, które mogą z nich korzystać, powinny być odpowiednio aktualizowane.

Dowiedz się więcej o tych zaleceniach na [stronie z informacjami o zaleceniach dotyczących zabezpieczeń.](recommendations-reference.md)

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender sql na kafelku komputera usunięty z pulpitu Azure Defender nawigacyjnego

Obszar Azure Defender pulpitu nawigacyjnego zawiera kafelki dla odpowiednich planów Azure Defender dla danego środowiska. Ze względu na problem z raportowaniem liczby chronionych i niechronionych zasobów zdecydowaliśmy się na tymczasowe usunięcie stanu pokrycia zasobów dla usługi Azure Defender dla programu **SQL** na maszynach, dopóki problem nie zostanie rozwiązany.


## <a name="march-2021"></a>Marzec 2021 r.

Aktualizacje w marcu obejmują:

- [Azure Firewall zintegrowane z Security Center](#azure-firewall-management-integrated-into-security-center)
- [Ocena luk w zabezpieczeniach SQL obejmuje teraz środowisko "Wyłącz regułę" (wersja zapoznawcza)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor skoroszyty zintegrowane z Security Center i trzema udostępnionym szablonami](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Pulpit nawigacyjny zgodności z przepisami zawiera teraz raporty inspekcji platformy Azure (wersja zapoznawcza)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Dane rekomendacji można wyświetlać w Azure Resource Graph za pomocą polecenia "Eksploruj w ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Aktualizacje zasad wdrażania automatyzacji przepływu pracy](#updates-to-the-policies-for-deploying-workflow-automation)
- [Dwa starsze zalecenia nie zapisują już danych bezpośrednio w dzienniku aktywności platformy Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Ulepszenia strony zaleceń](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall zintegrowane z Security Center

Po otwarciu Azure Security Center pierwszą wyświetlaną stroną jest strona przeglądu. 

Ten interaktywny pulpit nawigacyjny zapewnia ujednolicony widok zabezpieczeń obciążeń chmury hybrydowej. Ponadto przedstawiono w nim alerty zabezpieczeń, informacje dotyczące pokrycia i nie tylko.

W ramach ułatwiania wyświetlania stanu zabezpieczeń z centralnej obsługi zintegrowaliśmy interfejs Azure Firewall Manager tym pulpitem nawigacyjnym. Teraz możesz sprawdzić stan pokrycia zapory we wszystkich sieciach i centralnie zarządzać Azure Firewall od Security Center.

Dowiedz się więcej o tym [pulpicie nawigacyjnym Azure Security Center stronie przeglądu.](overview-page.md)

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center nawigacyjny z omówieniem pulpitu nawigacyjnego z kafelkiem Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Ocena luk w zabezpieczeniach SQL obejmuje teraz środowisko "Wyłącz regułę" (wersja zapoznawcza)

Security Center zawiera wbudowany skaner luk w zabezpieczeniach, który pomaga wykrywać, śledzić i korygować potencjalne luki w zabezpieczeniach bazy danych. Wyniki skanowania oceny zawierają przegląd stanu zabezpieczeń maszyn SQL i szczegółowe informacje o wszelkich ustaleniach dotyczących zabezpieczeń.

Jeśli organizacja musi zignorować wyszukiwanie, a nie je skorygować, możesz je opcjonalnie wyłączyć. Wyłączone wyniki nie wpływają na ocenę bezpieczeństwa ani nie generują niepożądanego szumu.

Dowiedz się więcej [z tematu Disable specific findings (Wyłączanie określonych wyników).](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor workbooks integrated into Security Center and three templates provided (Security Center skoroszyty zintegrowane z Security Center udostępnionym szablonem)

W ramach konferencji Ignite Spring 2021 ogłosiliśmy zintegrowane środowisko Azure Monitor Workbooks w Security Center.

Możesz skorzystać z nowej integracji, aby rozpocząć korzystanie z szablonów Security Center galerii. Za pomocą szablonów skoroszytów można uzyskać dostęp do dynamicznych i wizualnych raportów oraz tworzyć je w celu śledzenia bezpieczeństwa organizacji. Ponadto możesz tworzyć nowe skoroszyty na podstawie danych usługi Security Center lub innych obsługiwanych typów danych oraz szybko wdrażać skoroszyty społeczności Security Center społeczności usługi GitHub firmy Security Center.

Dostępne są trzy raporty szablonów:

- **Bezpieczny wynik w czasie** — śledzenie ocen subskrypcji i zmian zaleceń dotyczących zasobów
- **Aktualizacje systemu** — wyświetlanie brakujących aktualizacji systemu według zasobów, systemu operacyjnego, ważności i innych
- **Wyniki oceny luk w zabezpieczeniach** — wyświetl wyniki skanowania pod poszukiwaniu luk w zabezpieczeniach zasobów platformy Azure

Dowiedz się więcej na temat korzystania z tych raportów lub tworzenia własnych tworzyć rozbudowane, interaktywne [raporty Security Center danych.](custom-dashboards-azure-workbooks.md)

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Raport Ocena bezpieczeństwa w czasie":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Pulpit nawigacyjny zgodności z przepisami zawiera teraz raporty inspekcji platformy Azure (wersja zapoznawcza)

Z paska narzędzi pulpitu nawigacyjnego zgodności z przepisami można teraz pobierać raporty dotyczące certyfikacji platformy Azure i usługi Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Pasek narzędzi pulpitu nawigacyjnego zgodności z przepisami":::

Możesz wybrać kartę dla odpowiednich typów raportów (PCI, SOC, ISO i innych) i użyć filtrów, aby znaleźć określone raporty, których potrzebujesz.

Dowiedz się więcej na [temat zarządzania standardami na pulpicie nawigacyjnym zgodności z przepisami.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrowanie listy dostępnych raportów inspekcji platformy Azure":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Dane rekomendacji można wyświetlać w Azure Resource Graph za pomocą polecenia "Eksploruj w ARG"

Strony szczegółów rekomendacji zawierają teraz przycisk paska narzędzi "Eksploruj w ARG". Użyj tego przycisku, aby otworzyć Azure Resource Graph i eksplorować, eksportować i udostępniać dane zalecenia.

Azure Resource Graph (ARG) zapewnia natychmiastowy dostęp do informacji o zasobach w środowiskach chmury dzięki niezawodnym możliwościom filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób programowego wykonywania zapytań dotyczących informacji w subskrypcjach platformy Azure lub z poziomu Azure Portal.

Dowiedz się więcej [o Azure Resource Graph (ARG).](../governance/resource-graph/index.yml)

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Eksplorowanie danych rekomendacji w Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Aktualizacje zasad wdrażania automatyzacji przepływu pracy

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas badania i ograniczania zdarzeń zabezpieczeń.

Zapewniamy trzy Azure Policy "DeployIfNotExist", które tworzą i konfigurują procedury automatyzacji przepływu pracy, aby można było wdrażać automatyzacje w całej organizacji:

|Cel  |Zasady  |Identyfikator zasad  |
|---------|---------|---------|
|Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatyzacja przepływu pracy w przypadku zmian zgodności z przepisami|[Wdrażanie automatyzacji przepływu pracy w Azure Security Center zgodności z przepisami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Istnieją dwie aktualizacje funkcji tych zasad:

- Po przypisaniu pozostaną one włączone przez wymuszanie.
- Teraz możesz dostosować te zasady i zaktualizować dowolny z parametrów nawet po ich wdrożeniu. Jeśli na przykład użytkownik chce dodać inny klucz oceny lub edytować istniejący klucz oceny, może to zrobić.

Wprowadzenie do szablonów [automatyzacji przepływu pracy.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Dowiedz się więcej o tym, jak [automatyzować odpowiedzi Security Center wyzwalaczy.](workflow-automation.md)


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Dwa starsze zalecenia nie zapisują już danych bezpośrednio w dzienniku aktywności platformy Azure 

Security Center przekazuje dane dla prawie wszystkich zaleceń dotyczących zabezpieczeń do usługi Azure Advisor, która z kolei zapisuje je w dzienniku aktywności [platformy Azure.](../azure-monitor/essentials/activity-log.md)

W przypadku dwóch zaleceń dane są jednocześnie zapisywane bezpośrednio w dzienniku aktywności platformy Azure. Dzięki tej zmianie Security Center zapisywanie danych dla tych starszych zaleceń dotyczących zabezpieczeń bezpośrednio w dzienniku aktywności. Zamiast tego eksportujemy dane do Azure Advisor jak w przypadku wszystkich innych zaleceń.

Dwa starsze zalecenia to:
- Problemy z kondycją programu Endpoint Protection powinny zostać rozwiązane na maszynach
- Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach

Jeśli masz dostęp do informacji dotyczących tych dwóch zaleceń w kategorii "Zalecenie typu TaskDiscovery" dziennika aktywności, ta informacja nie jest już dostępna.


### <a name="recommendations-page-enhancements"></a>Ulepszenia strony zaleceń 

Wydaliśmy ulepszoną wersję listy zaleceń, aby przedstawić więcej informacji na pierwszy rzut oka.

Teraz na stronie zobaczysz:

1. Maksymalna ocena i bieżący wynik dla każdej kontroli zabezpieczeń.
1. Ikony zastępujące tagi, takie jak **Szybka poprawka** i **Podgląd**.
1. Nowa kolumna przedstawiająca [inicjatywę Zasady powiązaną](security-policy-concept.md) z każdym zaleceniem — widoczną, gdy ustawienie "Grupuj według kontrolek" jest wyłączone.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Ulepszenia strony Azure Security Center zalecenia firmy — marzec 2021 r." lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Ulepszenia Azure Security Center &quot;płaskiej&quot; listy zaleceń firmy — marzec 2021 r." lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Aby dowiedzieć się [więcej, zobacz Zalecenia dotyczące zabezpieczeń Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Luty 2021 r.

Aktualizacje w lutym obejmują:

- [Nowa strona alertów zabezpieczeń w wersji Azure Portal ogólnie dostępnej](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Zalecenia dotyczące ochrony obciążeń kubernetes wydane w wersji ogólnie dostępnej](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Integracja usługi Microsoft Defender for Endpoint z Azure Defender obsługuje teraz system Windows Server 2019 i Windows 10 Virtual Desktop (WVD) (w wersji zapoznawczej)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Bezpośredni link do zasad ze strony szczegółów zalecenia](#direct-link-to-policy-from-recommendation-details-page)
- [Zalecenie dotyczące klasyfikacji danych SQL nie ma już wpływu na ocenę bezpieczeństwa](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Automatyzacje przepływu pracy mogą być wyzwalane przez zmiany w ocenach zgodności z przepisami (w wersji zapoznawczej)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Ulepszenia strony spisu zasobów](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nowa strona alertów zabezpieczeń w wersji Azure Portal ogólnie dostępnej

Azure Security Center alertów zabezpieczeń została przeprojektowana w celu zapewnienia:

- **Ulepszone środowisko triage dla** alertów — ułatwianie redukowania alertów i skoncentrowanie się na najbardziej odpowiednich zagrożeniach. Lista zawiera dostosowywalne filtry i opcje grupowania.
- **Więcej informacji na liście alertów —** takich jak MITRE ATT&taktyki ACK.
- **Przycisk tworzenia przykładowych alertów** — aby ocenić Azure Defender i przetestować alerty. (w przypadku integracji rozwiązania SIEM, powiadomień e-mail i automatyzacji przepływu pracy) można tworzyć przykładowe alerty ze wszystkich Azure Defender biznesowych.
- **Dopasowanie do Azure Sentinel** zdarzeń firmy — w przypadku klientów, którzy korzystają z obu produktów, przełączanie się między nimi jest teraz prostsze i można łatwo nauczyć się od siebie nawzajem.
- **Lepsza wydajność** w przypadku dużych list alertów.
- **Nawigacja za** pomocą klawiatury na liście alertów.
- **Alerty z Azure Resource Graph** — alerty można Azure Resource Graph, czyli interfejsie API przypominacym Kusto, dla wszystkich zasobów. Jest to również przydatne w przypadku tworzenia własnych pulpitów nawigacyjnych alertów. [Dowiedz się więcej o Azure Resource Graph](../governance/resource-graph/index.yml).
- **Tworzenie funkcji przykładowych alertów** — aby utworzyć przykładowe alerty na podstawie nowego typu alertów, zobacz [Generowanie przykładowych alertów Azure Defender alertów.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center alertów zabezpieczeń":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Zalecenia dotyczące ochrony obciążeń na platformie Kubernetes zostały wydane w wersji ogólnie dostępnej

Z przyjemnością ogłaszamy ogólną dostępność zestawu zaleceń dotyczących ochrony obciążeń kubernetes.

Aby mieć pewność, że obciążenia kubernetes są domyślnie bezpieczne, Security Center dodał rekomendacje dotyczące wzmacniania zabezpieczeń na poziomie rozwiązania Kubernetes, w tym opcje wymuszania za pomocą kontroli danych na potrzeby rozwiązania Kubernetes.

Po zainstalowaniu dodatku usługi Azure Policy dla platformy Kubernetes w klastrze usługi Azure Kubernetes Service (AKS) każde żądanie do serwera interfejsu API Kubernetes będzie monitorowane pod kątem wstępnie zdefiniowanego zestawu najlepszych rozwiązań — wyświetlanego jako 13 zaleceń dotyczących zabezpieczeń — przed utrwalenia w klastrze. Następnie można skonfigurować usługę w taki sposób, aby wymuszać najlepsze rozwiązania i na przykład na przykład w przypadku przyszłych obciążeń.

Można na przykład na przykład nakłonić, aby nie można było tworzyć kontenerów uprzywilejowanych, a wszystkie przyszłe żądania w tym celu będą blokowane.

Dowiedz się więcej z [tematu Workload protection best-practices using Kubernetes admission control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)(Najlepsze rozwiązania dotyczące ochrony obciążeń przy użyciu kontroli danych na potrzeby rozwiązania Kubernetes).

> [!NOTE]
> Rekomendacje były w wersji zapoznawczej, ale nie wyrenderowały zasobu klastra usługi AKS w złej kondycji i nie zostały uwzględnione w obliczeniach wskaźnika bezpieczeństwa. z tym ogłoszeniem gałędy zostaną one uwzględnione w obliczeniu oceny. Jeśli nie zostały one jeszcze skorygowane, może to mieć niewielki wpływ na ocenę bezpieczeństwa. Koryguj je wszędzie tam, gdzie to możliwe, zgodnie z opisem [w tece Korygowanie zaleceń w Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Usługa Microsoft Defender for Endpoint integration with Azure Defender now supports Windows Server 2019 and Windows 10 Virtual Desktop (WVD) (in preview) (Usługa Microsoft Defender for Endpoint integration with Azure Defender now supports Windows Server 2019 and Windows 10 Virtual Desktop (WVD) (in preview) (w wersji zapoznawczej)

Usługa Microsoft Defender dla punktu końcowego to całościowe rozwiązanie zabezpieczeń punktu końcowego dostarczane przez chmurę. Zapewnia oparte na ryzyku zarządzanie lukami w zabezpieczeniach i ocenę ich, a także wykrywanie punktów końcowych i reagowanie na nie (EDR). Aby uzyskać pełną listę korzyści wynikających z używania usługi Defender for Endpoint razem z programem Azure Security Center, zobacz Protect [your endpoints with Security Center's integrated EDR solution: Microsoft Defender for Endpoint](security-center-wdatp.md)(Ochrona punktów końcowych za pomocą zintegrowanego rozwiązania EDR firmy Security Center: Usługa Microsoft Defender dla punktu końcowego).

Po włączeniu usługi Azure Defender dla serwerów w systemie Windows Server licencja usługi Defender dla punktu końcowego jest dołączona do planu. Jeśli włączono już usługę Azure Defender dla serwerów i masz serwery z systemem Windows 2019 w subskrypcji, po tej aktualizacji automatycznie otrzymają one usługę Defender dla punktu końcowego. Nie jest wymagana żadna akcja ręczna. 

Obsługa została rozszerzona o system Windows Server 2019 [i Windows Virtual Desktop (WVD).](../virtual-desktop/overview.md)

> [!NOTE]
> Jeśli włączasz usługę Defender dla punktu końcowego na maszynie z systemem Windows Server 2019, upewnij się, że spełnia ona wymagania wstępne opisane w temacie Włączanie integracji usługi [Microsoft Defender dla punktu końcowego.](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Bezpośredni link do zasad ze strony szczegółów zalecenia

Podczas przeglądania szczegółów zalecenia często pomocne jest sprawdzenie podstawowych zasad. Dla każdego zalecenia obsługiwanego przez zasady na stronie szczegółów zalecenia znajduje się nowy link:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link do Azure Policy dla określonych zasad wspierających zalecenie":::

Użyj tego linku, aby wyświetlić definicję zasad i przejrzeć logikę oceny. 

Jeśli przeglądasz listę zaleceń w naszym przewodniku po zaleceniach dotyczących [zabezpieczeń,](recommendations-reference.md)zobaczysz również linki do stron definicji zasad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Uzyskiwanie dostępu do Azure Policy dla określonych zasad bezpośrednio ze strony z zaleceniami Azure Security Center strony referencyjnej" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Zalecenie dotyczące klasyfikacji danych SQL nie ma już wpływu na ocenę bezpieczeństwa
Rekomendacja **Poufne dane w bazach danych SQL nie** powinny już mieć wpływu na ocenę bezpieczeństwa. Jest to jedyne zalecenie w kontrolce zabezpieczeń Zastosuj **klasyfikację** danych, dlatego kontrola ma teraz wartość 0 dla bezpiecznego wyniku.

Aby uzyskać pełną listę wszystkich kontrolek zabezpieczeń w programie Security Center, wraz z ich wynikami i listą zaleceń w każdej z nich, zobacz Mechanizmy kontroli zabezpieczeń i [ich zalecenia.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Automatyzacje przepływu pracy mogą być wyzwalane przez zmiany w ocenach zgodności z przepisami (w wersji zapoznawczej)
Dodaliśmy trzeci typ danych do opcji wyzwalacza dla automatyzacji przepływu pracy: zmiany w ocenach zgodności z przepisami.

Dowiedz się, jak używać narzędzi automatyzacji przepływu pracy, [zobacz Automatyzowanie odpowiedzi Security Center wyzwalaczy.](workflow-automation.md)

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Używanie zmian do ocen zgodności z przepisami w celu wyzwolenia automatyzacji przepływu pracy" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Ulepszenia strony spisu zasobów
Security Center spisu zasobów została ulepszona w następujący sposób:

- Podsumowania w górnej części strony zawierają teraz niezarejestrowane subskrypcje, pokazujące liczbę subskrypcji bez Security Center włączone.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Liczba niezarejestrowanych subskrypcji w podsumowaniach w górnej części strony spisu zasobów":::

- Filtry zostały rozwinięte i ulepszone, aby uwzględnić następujące funkcje:
    - **Liczniki** — każdy filtr przedstawia liczbę zasobów spełniających kryteria każdej kategorii

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Liczniki w filtrach na stronie spisu zasobów Azure Security Center":::

    - **Filtr Zawiera wyjątki** (opcjonalnie) — zawężenie wyników do zasobów, które mają/nie zostały wyzmień. Ten filtr nie jest wyświetlany domyślnie, ale jest dostępny za pośrednictwem **przycisku Dodaj** filtr.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Dodanie filtru &quot;contains exemption&quot; (zawiera Azure Security Center) na stronie spisu zasobów firmy":::

Dowiedz się więcej o tym, jak [eksplorować zasoby i zarządzać nimi za pomocą spisu zasobów.](asset-inventory.md)

## <a name="january-2021"></a>Styczeń 2021 r.

Aktualizacje w styczniu obejmują:

- [Test porównawczy zabezpieczeń platformy Azure jest teraz domyślną inicjatywą zasad dla Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Ocena luk w zabezpieczeniach dla maszyn wirtualnych w środowisku lokalnym i w wielu chmurach została wydana w wersji ogólnie dostępnej](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Ocena bezpieczeństwa dla grup zarządzania jest teraz dostępna w wersji zapoznawczej](#secure-score-for-management-groups-is-now-available-in-preview)
- [Interfejs API bezpiecznego oceny został wydany w wersji ogólnie dostępnej](#secure-score-api-is-released-for-general-availability-ga)
- [Dangling DNS protections added to Azure Defender for App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Łączniki wielochmurowe są ogólnie dostępne](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Wyklucz całe zalecenia z bezpiecznego wyniku dla subskrypcji i grup zarządzania](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Użytkownicy mogą teraz żądać wglądu w całej dzierżawie od administratora globalnego](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Dodano 35 zaleceń w wersji zapoznawczej w celu zwiększenia pokrycia testu porównawczego zabezpieczeń platformy Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Eksportowanie csv filtrowanych list zaleceń](#csv-export-of-filtered-list-of-recommendations)
- [Zasoby "Nie dotyczy" są teraz zgłaszane jako "Zgodne" w Azure Policy oceny](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Eksportowanie cotygodniowych migawek bezpiecznego wyniku i danych zgodności z przepisami przy użyciu eksportu ciągłego (wersja zapoznawcza)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Test porównawczy zabezpieczeń platformy Azure jest teraz domyślną inicjatywą zasad dla Azure Security Center

Test porównawczy zabezpieczeń platformy Azure to zestaw wytycznych dotyczących najlepszych rozwiązań dotyczących zabezpieczeń i zgodności dla platformy Microsoft, który został przez firmę Microsoft określony w oparciu o typowe struktury zgodności. Ten szeroko stosowany test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze.

W ostatnich miesiącach Security Center wbudowanych zaleceń dotyczących zabezpieczeń znacznie wzrosła, aby rozszerzyć zakres tego testu porównawczego.

Od tej wersji test porównawczy stanowi podstawę rekomendacji Security Center i jest w pełni zintegrowany jako domyślna inicjatywa zasad. 

Wszystkie usługi platformy Azure mają stronę punktów odniesienia zabezpieczeń w dokumentacji. Na przykład [jest to Security Center linii bazowej .](security-baseline.md) Te punkty odniesienia są zbudowane na podstawie testu porównawczego zabezpieczeń platformy Azure.

Jeśli używasz pulpitu nawigacyjnego Security Center zgodności z przepisami, zobaczysz dwa wystąpienia testu porównawczego w okresie przejściowym:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center zgodności z przepisami przedstawiający test porównawczy zabezpieczeń platformy Azure":::

Nie ma to wpływu na istniejące zalecenia i w miarę rozwoju testu porównawczego zmiany będą automatycznie odzwierciedlane w Security Center. 

Aby dowiedzieć się więcej, zobacz następujące strony:

- [Dowiedz się więcej na temat testu porównawczego zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Ocena luk w zabezpieczeniach dla maszyn wirtualnych w środowisku lokalnym i w wielu chmurach została wydana w wersji ogólnie dostępnej

W październiku ogłosiliśmy podgląd skanowania serwerów z obsługą Azure Arc za pomocą usługi [Azure Defender](defender-for-servers-introduction.md)dla skanera zintegrowanego oceny luk w zabezpieczeniach serwerów (obsługiwanego przez firmę Qualys).

Jest on teraz ogólnie dostępny.

Po włączeniu funkcji Azure Arc na maszynach spoza platformy Azure usługa Security Center zaoferuje wdrożenie na nich zintegrowanego skanera luk w zabezpieczeniach — ręcznie i na dużą skalę.

Dzięki tej aktualizacji możesz uwolnić możliwości usługi **Azure Defender** serwerów w celu skonsolidowania programu zarządzania lukami w zabezpieczeniach we wszystkich twoich zasobów platformy Azure i poza platformą Azure.

Główne możliwości:

- Monitorowanie stanu aprowizowania skanera oceny luk w zabezpieczeniach na Azure Arc luk w zabezpieczeniach
- Aprowizowanie zintegrowanego agenta va dla niechronionych maszyn wirtualnych z systemami Windows i Linux Azure Arc (ręcznie i na dużą skalę)
- Odbieranie i analizowanie wykrytych luk w zabezpieczeniach z wdrożonych agentów (ręcznie i na dużą skalę)
- Ujednolicone środowisko dla maszyn wirtualnych platformy Azure i maszyn Azure Arc wirtualnych

[Dowiedz się więcej o wdrażaniu zintegrowanego skanera luk w zabezpieczeniach na maszynach hybrydowych.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Dowiedz się więcej Azure Arc serwerach z obsługą usługi](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Ocena bezpieczeństwa dla grup zarządzania jest teraz dostępna w wersji zapoznawczej

Strona oceny bezpieczeństwa zawiera teraz zagregowane wyniki bezpieczeństwa dla grup zarządzania oprócz poziomu subskrypcji. Teraz możesz wyświetlić listę grup zarządzania w organizacji i wynik dla każdej grupy zarządzania.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Wyświetlanie wyników bezpieczeństwa dla grup zarządzania.":::

Dowiedz się więcej o [zabezpieczonych ocenach i mechanizmach kontroli zabezpieczeń w Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Interfejs API bezpiecznego oceny został wydany w wersji ogólnie dostępnej

Teraz możesz uzyskać dostęp do swojego wyniku za pośrednictwem interfejsu [API bezpiecznego wyniku](/rest/api/securitycenter/securescores/). Metody interfejsu API zapewniają elastyczność wykonywania zapytań o dane i tworzenia własnego mechanizmu raportowania bezpiecznych wyników w czasie. Na przykład:

- uzyskiwanie **oceny dla** określonej subskrypcji za pomocą interfejsu API wyników bezpieczeństwa
- Użyj **interfejsu API bezpiecznych kontrolek oceny,** aby wyświetlić listę kontroli zabezpieczeń i bieżącego wyniku subskrypcji

Dowiedz się więcej o narzędziach zewnętrznych, które są możliwe za pomocą interfejsu API bezpiecznego wyniku w obszarze bezpiecznego wyniku [naszej społeczności usługi GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Dowiedz się więcej o [zabezpieczonych ocenach i mechanizmach kontroli zabezpieczeń w Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Dangling DNS protections added to Azure Defender for App Service

Przejęcie poddomeny jest powszechnym zagrożeniem o wysokiej ważności dla organizacji. Przejęcie poddomeny może nastąpić, gdy masz rekord DNS, który wskazuje na coprowizowana witrynę internetową. Takie rekordy DNS są również nazywane wpisami "dangling DNS". Rekordy CNAME są szczególnie narażone na to zagrożenie. 

Przejęcie poddomeny umożliwia podmiotom złośliwym przekierowywanie ruchu przeznaczonego dla domeny organizacji do witryny wykonującej złośliwe działania.

Azure Defender dla App Service wykrywa teraz nieaktualne wpisy DNS po zlikwidowaniu App Service sieci Web. Jest to moment, w którym wpis DNS wskaże nieistniejący zasób, a witryna internetowa jest narażona na przejęcie poddomeny. Te zabezpieczenia są dostępne niezależnie od tego, czy domeny są zarządzane za pomocą usługi Azure DNS, czy też zewnętrznego rejestratora domen i mają zastosowanie zarówno do App Service w systemie Windows, jak i App Service dla systemu Linux.

Więcej informacji:

- [App Service tabeli referencyjnej alertów](alerts-reference.md#alerts-azureappserv) — zawiera dwa nowe alerty Azure Defender, które są wyzwalane po wykryciu nieaktualnego wpisu DNS
- [Zapobieganie danglingowi wpisów DNS](../security/fundamentals/subdomain-takeover.md) i unikanie przejęcia poddomeny — informacje na temat zagrożenia związanego z przejęciem poddomeny i aspektem systemu DNS
- [Wprowadzenie do Azure Defender dla App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Łączniki wielochmurowe są ogólnie dostępne

W przypadku obciążeń w chmurze często obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą to samo zrobić.

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Połączenie kont usług AWS lub GCP integruje ich natywne narzędzia zabezpieczeń, takie jak AWS Security Hub i GCP Security Command Center, z Azure Security Center.

Ta funkcja oznacza, że Security Center widoczność i ochronę we wszystkich najważniejszych środowiskach chmury. Niektóre korzyści wynikające z tej integracji:

- Automatyczne aprowizowanie agentów — Security Center używa Azure Arc do wdrażania agenta usługi Log Analytics w wystąpieniach usług AWS
- Zarządzanie zasadami
- Zarządzanie lukami w zabezpieczeniach
- Wykrywanie i reagowanie osadzonego punktu końcowego (EDR)
- Wykrywanie błędów konfiguracji zabezpieczeń
- Pojedynczy widok przedstawiający zalecenia dotyczące zabezpieczeń od wszystkich dostawców usług w chmurze
- Uwzględnianie wszystkich zasobów w obliczeniach wskaźnika Security Center bezpiecznej oceny
- Oceny zgodności z przepisami zasobów usług AWS i GCP

Z Security Center wybierz pozycję Łączniki z wieloma chmurami. Zobaczysz opcje tworzenia nowych łączników: 

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Przycisk Dodawania konta platformy AWS Security Center stronie łączników w wielu chmurach":::

Dowiedz się więcej w:
- [Łączenie kont usług AWS z usługą Azure Security Center](quickstart-onboard-aws.md)
- [Łączenie kont GCP z Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Zwalnianie całych zaleceń z oceny bezpieczeństwa dla subskrypcji i grup zarządzania

Rozszerzamy możliwość wywłaszania, aby uwzględnić całe rekomendacje. Udostępnianie dodatkowych opcji dostrajania zaleceń dotyczących zabezpieczeń, które Security Center dla subskrypcji, grupy zarządzania lub zasobów.

Czasami zasób będzie wymieniony jako w złej kondycji, gdy wiadomo, że problem został rozwiązany przez narzędzie innej firmy, które Security Center nie zostało wykryte. Lub zalecenie będzie wyświetlane w zakresie, w którym uważasz, że nie należy. Zalecenie może być nieodpowiednie dla określonej subskrypcji. Być może Twoja organizacja zdecydowała się zaakceptować ryzyko związane z określonym zasobem lub zaleceniem.

Dzięki tej funkcji w wersji zapoznawczej możesz teraz utworzyć wyjątek dla zalecenia, aby:

- **Wyklucz zasób,** aby upewnić się, że w przyszłości nie będzie on wymieniony z zasobami w złej kondycji, i nie będzie mieć wpływu na ocenę bezpieczeństwa. Zasób zostanie wymieniony jako nie dotyczy, a przyczyna zostanie pokazana jako "wykluczona" z określonym uzasadnieniem.

- **Wyklucz subskrypcję** lub grupę zarządzania, aby upewnić się, że zalecenie nie ma wpływu na ocenę bezpieczeństwa i nie będzie wyświetlane dla subskrypcji lub grupy zarządzania w przyszłości. Jest to związane z istniejącymi zasobami i dowolnymi zasobami, które utworzysz w przyszłości. Zalecenie zostanie oznaczone określonym uzasadnieniem wybranym dla wybranego zakresu.

Dowiedz się więcej [z tematu Exempting resources and recommendations from your secure score (Wykluczanie zasobów i zaleceń z bezpiecznego wyniku).](exempt-resource.md)



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Użytkownicy mogą teraz żądać wglądu w całej dzierżawie od administratora globalnego

Jeśli użytkownik nie ma uprawnień do Security Center danych, teraz zobaczy link do żądania uprawnień od administratora globalnego organizacji. Żądanie zawiera rolę, która ma być potrzebna, oraz uzasadnienie, dlaczego jest to konieczne.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Baner z prośbą o uprawnienia dla całej dzierżawy.":::

Aby dowiedzieć się [więcej, zobacz Request tenant-wide permissions when yours are insufficient (Żądanie uprawnień dla całej dzierżawy, gdy twoje uprawnienia są niewystarczające).](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Dodano 35 zaleceń w wersji zapoznawczej w celu zwiększenia pokrycia testu porównawczego zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń](https://docs.microsoft.com/security/benchmark/azure/introduction) platformy Azure to domyślna inicjatywa zasad w Azure Security Center. 

Aby zwiększyć zasięg tego testu porównawczego, dodano następujące 35 zaleceń w wersji zapoznawczej do Security Center.

> [!TIP]
> Rekomendacje dotyczące wersji zapoznawczej nie renderują zasobu w złej kondycji i nie są uwzględniane w obliczeniach wskaźnika bezpieczeństwa. Koryguj je wszędzie tam, gdzie to możliwe, tak aby po zakończeniu okresu zapoznawczego przyczyniły się do oceny. Dowiedz się więcej o tym, jak reagować na te zalecenia, zobacz [Korygowanie zaleceń](security-center-remediate-recommendations.md)w Azure Security Center .

| Kontrola zabezpieczeń                     | Nowe zalecenia                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włączanie szyfrowania danych spoczynku            | — Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku<br>— Azure Machine Learning powinny być szyfrowane przy użyciu klucza zarządzanego przez klienta (CMK)<br>— Ochrona danych przy własnych kluczach powinna być włączona dla serwerów MySQL<br>— Ochrona danych przy własnych kluczach powinna być włączona dla serwerów PostgreSQL<br>— Cognitive Services powinny włączyć szyfrowanie danych przy użyciu klucza zarządzanego przez klienta<br>— Rejestry kontenerów powinny być szyfrowane przy użyciu klucza zarządzanego przez klienta (CMK)<br>— Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku<br>— Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku<br>— Konta magazynu powinny używać klucza zarządzanego przez klienta (CMK) do szyfrowania                                                                                                                                                              |
| Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń    | — Subskrypcje powinny mieć kontaktowy adres e-mail w przypadku problemów z zabezpieczeniami<br> — W twojej subskrypcji należy włączyć automatyczną aprowizowanie agenta usługi Log Analytics<br> — Powiadomienia e-mail dotyczące alertów o wysokiej ważności powinny być włączone<br> — Należy włączyć powiadomienie e-mail do właściciela subskrypcji dotyczące alertów o wysokiej ważności<br> — Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczaniem<br> — Magazyny kluczy powinny mieć włączoną funkcję usuwania nie soft |
| Zarządzanie dostępem i uprawnieniami        | — Aplikacje funkcji powinny mieć włączoną funkcję "Certyfikaty klienta (przychodzące certyfikaty klienta)" |
| Ochrona aplikacji przed atakami DDoS | — Web Application Firewall (WAF) powinna być włączona dla Application Gateway<br> — Web Application Firewall (WAF) powinna być włączona dla Azure Front Door Service usługi |
| Ograniczanie nieautoryzowanego dostępu do sieci | — Zapora powinna być włączona na Key Vault<br> — Prywatny punkt końcowy powinien być skonfigurowany dla Key Vault<br> — App Configuration używać linku prywatnego<br> — Azure Cache for Redis znajdować się w sieci wirtualnej<br> — Azure Event Grid powinny używać linku prywatnego<br> — Azure Event Grid tematy powinny używać linku prywatnego<br> — Azure Machine Learning obszary robocze powinny używać linku prywatnego<br> — Azure SignalR Service używać linku prywatnego<br> — Azure Spring Cloud należy używać wstrzykiwania sieci<br> - Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp sieciowy<br> — Rejestry kontenerów powinny używać linku prywatnego<br> — Dla serwerów MariaDB należy wyłączyć dostęp do sieci publicznej<br> — Dostęp do sieci publicznej powinien być wyłączony dla serwerów MySQL<br> — Dostęp do sieci publicznej powinien być wyłączony dla serwerów PostgreSQL<br> — Konto magazynu powinno korzystać z połączenia z łączem prywatnym<br> - Konta magazynu powinny ograniczać dostęp sieciowy przy użyciu reguł sieci wirtualnej<br> — Szablony Image Builder maszyn wirtualnych powinny używać linku prywatnego|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Powiązane linki:

- [Dowiedz się więcej na temat testu porównawczego zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Eksportowanie pliku CSV z przefiltrowanych list zaleceń 

W listopadzie 2020 r. dodaliśmy filtry do strony zaleceń[(lista rekomendacji zawiera teraz filtry](#recommendations-list-now-includes-filters)). W grudniu rozszerzyliśmy te filtry (strona Zalecenia zawiera nowe filtry dotyczące[środowiska, ważności i dostępnych odpowiedzi).](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses) 

W tym anonsie zmieniamy zachowanie przycisku Pobierz na **CSV,** tak aby eksport CSV zawierał tylko zalecenia aktualnie wyświetlane na filtrowanych listach. 

Na przykład na poniższej ilustracji widać, że lista została odfiltrowana do dwóch zaleceń. Wygenerowany plik CSV zawiera szczegóły stanu dla każdego zasobu objętego tymi dwoma zaleceniami.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Eksportowanie odfiltrowanych zaleceń do pliku CSV":::

Aby dowiedzieć się [więcej, zobacz Zalecenia dotyczące zabezpieczeń Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Zasoby "Nie dotyczy" są teraz zgłaszane jako "Zgodne" w Azure Policy oceny

Wcześniej zasoby, które zostały ocenione pod  celu rekomendacji i które nie mają zastosowania, pojawiały się w Azure Policy jako "Niezgodne". Żadna akcja użytkownika nie może zmienić stanu na "Zgodne". Dzięki tej zmianie są one zgłaszane jako "Zgodne" w celu zwiększenia przejrzystości.

Jedyny wpływ będzie widoczny w przypadku Azure Policy, w których liczba zgodnych zasobów zwiększy się. Nie będzie to miało wpływu na ocenę bezpieczeństwa w Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Eksportowanie cotygodniowych migawek bezpiecznego wyniku i danych zgodności z przepisami przy użyciu eksportu ciągłego (wersja zapoznawcza)

Dodaliśmy nową funkcję w [](continuous-export.md) wersji zapoznawczej do narzędzi eksportu ciągłego w celu eksportowania cotygodniowych migawek bezpiecznego wyniku i danych zgodności z przepisami.

Podczas definiowania eksportu ciągłego ustaw częstotliwość eksportowania:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Wybieranie częstotliwości eksportu ciągłego":::

- **Przesyłanie** strumieniowe — oceny będą wysyłane w czasie rzeczywistym, gdy kondycja zasobu zostanie zaktualizowana (jeśli aktualizacje nie zostaną wysłane, żadne dane nie będą wysyłane).
- **Migawki** — migawka bieżącego stanu wszystkich ocen zgodności z przepisami będzie wysyłana co tydzień (jest to funkcja w wersji zapoznawczej dla cotygodniowych migawek bezpiecznych ocen i danych zgodności z przepisami).

Aby dowiedzieć się więcej o pełnych możliwościach tej funkcji, zobacz [Ciągłe eksportowanie Security Center danych.](continuous-export.md)

## <a name="december-2020"></a>Grudzień 2020 r.

Aktualizacje w grudniu obejmują:

- [Azure Defender dla serwerów SQL na maszynach jest ogólnie dostępna](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender obsługi języka SQL dla Azure Synapse Analytics dedykowanej puli SQL jest ogólnie dostępna](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Administratorzy globalni mogą teraz udzielać sobie uprawnień na poziomie dzierżawy](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dwa nowe Azure Defender usługi: Azure Defender dns i Azure Defender dla Resource Manager (w wersji zapoznawczej)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nowa strona alertów zabezpieczeń w Azure Portal (wersja zapoznawcza)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Ponownie Security Center środowisko Azure SQL Database & SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Zaktualizowano narzędzia i filtry spisu zasobów](#asset-inventory-tools-and-filters-updated)
- [Zalecenie dotyczące aplikacji internetowych żądających certyfikatów SSL, które nie są już częścią bezpiecznego wyniku](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Strona Rekomendacje zawiera nowe filtry dotyczące środowiska, ważności i dostępnych odpowiedzi](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Eksport ciągły pobiera nowe typy danych i ulepszone zasady deployifnotexist](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender dla serwerów SQL na maszynach jest ogólnie dostępna

Azure Security Center oferuje dwa plany Azure Defender programu SQL Server:

- **Azure Defender dla Azure SQL baz danych** — chroni serwery SQL Server natywne dla platformy Azure 
- **Azure Defender dla serwerów SQL na** maszynach — rozszerza te same zabezpieczenia na serwery SQL w środowiskach hybrydowych, wielochmurowych i lokalnych

Dzięki temu **anonsowi Azure Defender SQL** chroni teraz bazy danych i ich dane niezależnie od tego, gdzie się znajdują.

Azure Defender sql obejmuje możliwości oceny luk w zabezpieczeniach. Narzędzie do oceny luk w zabezpieczeniach obejmuje następujące funkcje zaawansowane:

- **Konfiguracja linii** bazowej (nowość!) umożliwiająca inteligentne uściślinie wyników skanowania pod względem luk w zabezpieczeniach do tych, które mogą reprezentować rzeczywiste problemy z zabezpieczeniami. Po ujednaniu bazowego stanu zabezpieczeń narzędzie do oceny luk w zabezpieczeniach zgłasza tylko odchylenia od tego stanu. Wyniki zgodne z punktem odniesienia są traktowane jako przekazywanie kolejnych skanowań. Dzięki temu Ty i Twoi analitycy skoncentrujecie twoją uwagę na tym, gdzie ma to znaczenie.
- **Szczegółowe informacje dotyczące testów** porównawczych, które *ułatwiają* zrozumienie odnalezionych wyników i ich związku z zasobami.
- **Skrypty korygowania** w celu ograniczenia zidentyfikowanych zagrożeń.

Dowiedz się więcej o [Azure Defender dla języka SQL.](defender-for-sql-introduction.md)


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender obsługi języka SQL dla Azure Synapse Analytics dedykowana pula SQL jest ogólnie dostępna

Azure Synapse Analytics (dawniej SQL DW) to usługa analizy, która łączy magazynowanie danych przedsiębiorstwa i analizę danych big data. Dedykowane pule SQL to funkcje magazynowania danych przedsiębiorstwa Azure Synapse. Dowiedz się więcej [w temacie Co Azure Synapse Analytics (dawniej SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender sql chroni dedykowane pule SQL za pomocą:

- **Zaawansowana ochrona przed zagrożeniami** w celu wykrywania zagrożeń i ataków 
- **Możliwości oceny luk w zabezpieczeniach do** identyfikowania i korygowania problemów z błędami konfiguracji zabezpieczeń

Azure Defender do pakietu baz danych Azure Synapse Analytics SQL jest automatycznie dodawana do pakietu baz danych Azure SQL sql w Azure Security Center. Na stronie obszaru roboczego usługi Syn Azure Portal apse w Azure Defender znajdziesz nową kartę "Azure Defender for SQL".

Dowiedz się więcej o [Azure Defender dla języka SQL.](defender-for-sql-introduction.md)


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Administratorzy globalni mogą teraz udzielać sobie uprawnień na poziomie dzierżawy

Użytkownik z rolą Azure Active Directory administratora  globalnego może mieć obowiązki dotyczące całej dzierżawy, ale nie ma uprawnień platformy Azure do wyświetlania tych informacji w całej organizacji w Azure Security Center. 

Aby przypisać sobie uprawnienia na poziomie dzierżawy, postępuj zgodnie z instrukcjami w tece Udzielanie sobie uprawnień dla [całej dzierżawy.](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dwa nowe Azure Defender usługi: Azure Defender dns i Azure Defender dla Resource Manager (w wersji zapoznawczej)

Dodaliśmy dwie nowe możliwości ochrony przed zagrożeniami natywne dla chmury dla środowiska platformy Azure.

Te nowe zabezpieczenia znacznie zwiększają odporność na ataki ze strony osób atakujących i znacznie zwiększają liczbę zasobów platformy Azure chronionych przez Azure Defender.

- **Azure Defender dla Resource Manager** — automatycznie monitoruje wszystkie operacje zarządzania zasobami wykonywane w organizacji. Aby uzyskać więcej informacji, zobacz:
    - [Wprowadzenie do Azure Defender dla Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reagowanie na alerty usługi Azure Defender dla usługi Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista alertów dostarczonych przez Azure Defender dla Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender dns — stale** monitoruje wszystkie zapytania DNS z zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz:
    - [Wprowadzenie do Azure Defender dns](defender-for-dns-introduction.md)
    - [Reagowanie na alerty usługi Azure Defender dla usługi DNS](defender-for-dns-usage.md)
    - [Lista alertów dostarczonych przez Azure Defender dla systemu DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nowa strona alertów zabezpieczeń w Azure Portal (wersja zapoznawcza)

Azure Security Center alertów zabezpieczeń została przeprojektowana w celu zapewnienia:

- **Ulepszona triage experience for alerts** (Ulepszone środowisko do grupowania alertów) — łatwiejsza pomoc w ograniczaniu alertów i skoncentrowaniu się na najbardziej odpowiednich zagrożeniach. Lista zawiera dostosowywalne filtry i opcje grupowania
- **Więcej informacji na liście alertów** — na przykład MITRE ATT&taktyki ACK
- **Przycisk** tworzenia przykładowych alertów — aby ocenić możliwości rozwiązania Azure Defender i przetestować konfigurację alertów (w przypadku integracji rozwiązania SIEM, powiadomień e-mail i automatyzacji przepływu pracy), możesz utworzyć przykładowe alerty ze wszystkich planów Azure Defender biznesowych
- **Dopasowanie do Azure Sentinel** zdarzeń firmy — w przypadku klientów, którzy korzystają z obu produktów, przełączanie się między nimi jest teraz bardziej proste i można łatwo nauczyć się jednego od drugiego
- **Lepsza wydajność w** przypadku dużych list alertów
- **Nawigacja za pomocą** klawiatury na liście alertów
- **Alerty z Azure Resource Graph** — możesz odpytować alerty w Azure Resource Graph, interfejsie API przypominacym Kusto, dla wszystkich zasobów. Jest to również przydatne w przypadku tworzenia własnych pulpitów nawigacyjnych alertów. [Dowiedz się więcej o Azure Resource Graph](../governance/resource-graph/index.yml).

Aby uzyskać dostęp do nowego środowisko, użyj linku "Wypróbuj teraz" na banerze w górnej części strony alertów zabezpieczeń.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Baner z linkiem do nowego środowisko alertów w wersji zapoznawczej":::

Aby utworzyć przykładowe alerty na podstawie nowego środowisko alertów, zobacz [Generowanie przykładowych alertów Azure Defender alertów.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Ponownie Security Center w Azure SQL Database & SQL Managed Instance 

Środowisko Security Center SQL zapewnia dostęp do następujących funkcji Security Center i Azure Defender SQL:

- **Zalecenia dotyczące** zabezpieczeń — Security Center okresowo analizuje stan zabezpieczeń wszystkich połączonych zasobów platformy Azure, aby zidentyfikować potencjalne błędy konfiguracji zabezpieczeń. Następnie udostępnia zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach i poprawiania bezpieczeństwa organizacji.
- **Alerty zabezpieczeń** — usługa wykrywania, która stale Azure SQL pod Azure SQL zagrożeń, takich jak iniekcja SQL, ataki siłowe i nadużycie uprawnień. Ta usługa wyzwala szczegółowe i zorientowane na działania alerty zabezpieczeń w usłudze Security Center i udostępnia opcje kontynuowania badań za pomocą rozwiązania Azure Sentinel, rozwiązania SIEM firmy Microsoft natywnego dla platformy Azure.
- **Wyniki —** usługa do oceny luk w zabezpieczeniach, która stale monitoruje konfiguracje Azure SQL i pomaga w korygowaniu luk w zabezpieczeniach. Skanowania oceny zawierają przegląd Azure SQL zabezpieczeń wraz ze szczegółowymi ustaleniami bezpieczeństwa.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Center zabezpieczeń języka SQL są dostępne z poziomu Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Zaktualizowano narzędzia i filtry spisu zasobów

Strona spisu w Azure Security Center została odświeżona z następującymi zmianami:

- **Przewodniki i opinie** dodane do paska narzędzi. Spowoduje to otwarcie okienka z linkami do powiązanych informacji i narzędzi. 
- **Filtr Subskrypcje** został dodany do domyślnych filtrów dostępnych dla zasobów.
- **Otwórz link zapytania,** aby otworzyć bieżące opcje filtru jako Azure Resource Graph zapytania (wcześniej nazywanego "Wyświetl w eksploratorze grafu zasobów").
- **Opcje operatora** dla każdego filtru. Teraz możesz wybrać spośród większej liczby operatorów logicznych innych niż "=". Możesz na przykład znaleźć wszystkie zasoby z aktywnymi rekomendacjami, których tytuły zawierają ciąg "encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Kontrolki opcji operatora w filtrach spisu zasobów":::

Aby dowiedzieć się więcej na temat [spisu, zobacz Explore and manage your resources with asset inventory (Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów).](asset-inventory.md)


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Zalecenie dotyczące aplikacji internetowych żądających certyfikatów SSL, które nie są już częścią bezpiecznego wyniku

Zalecenie "Aplikacje internetowe powinny żądać certyfikatu SSL dla wszystkich żądań przychodzących" zostało przeniesione z kontroli zabezpieczeń Zarządzanie dostępem i uprawnieniami **(o** wartości maksymalnie 4 punktów) do folderu **Implementowanie** najlepszych rozwiązań w zakresie zabezpieczeń (co nie jest warte żadnych punktów). 

Zapewnienie, że aplikacja internetowa żąda certyfikatu, z pewnością sprawia, że jest on bezpieczniejszy. Jednak w przypadku publicznych aplikacji internetowych nie ma znaczenia. Jeśli uzyskujesz dostęp do lokacji za pośrednictwem protokołu HTTP, a nie HTTPS, nie otrzymasz certyfikatu klienta. Dlatego jeśli aplikacja wymaga certyfikatów klienta, nie należy zezwalać na żądania do aplikacji za pośrednictwem protokołu HTTP. Aby dowiedzieć się [więcej, zobacz Konfigurowanie wzajemnego uwierzytelniania protokołu TLS dla Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Dzięki tej zmianie zalecenie jest teraz zalecanym najlepszym rozwiązaniem, które nie ma wpływu na Ocenę. 

Dowiedz się, które zalecenia znajdują się w poszczególnych mechanizmach kontroli [zabezpieczeń, i zapoznaj się z ich zaleceniami.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Strona Rekomendacje zawiera nowe filtry dotyczące środowiska, ważności i dostępnych odpowiedzi

Azure Security Center monitoruje wszystkie połączone zasoby i generuje zalecenia dotyczące zabezpieczeń. Skorzystaj z tych zaleceń, aby zwiększyć swój czas działania chmury hybrydowej i śledzić zgodność z zasadami i standardami odpowiednimi dla Twojej organizacji, branży i kraju.

Ponieważ Security Center nadal rozszerza swoją zasięg i funkcje, lista zaleceń dotyczących zabezpieczeń rośnie z każdym miesiącem. Aby zwiększyć pokrycie testu porównawczego zabezpieczeń platformy Azure, zobacz [29 zaleceń](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)dotyczących wersji zapoznawczej.

Wraz z rosnącą listą należy odfiltrować rekomendacje, aby znaleźć te, które cię interesują. W listopadzie dodaliśmy filtry do strony zaleceń (zobacz Lista zaleceń [zawiera teraz filtry](#recommendations-list-now-includes-filters)).

Filtry dodane w tym miesiącu oferują opcje uściślić listę zaleceń zgodnie z:

- **Środowisko** — wyświetlanie zaleceń dotyczących usług AWS, GCP lub zasobów platformy Azure (lub dowolnej kombinacji)
- **Ważność** — wyświetl zalecenia zgodnie z klasyfikacją ważności ustawioną przez Security Center
- **Akcje odpowiedzi** — wyświetl zalecenia zgodnie z dostępnością opcji Security Center odpowiedzi: Szybka poprawka, Odmów i Wymusz

    > [!TIP]
    > Filtr akcji odpowiedzi zastępuje filtr **Szybka poprawka dostępna (Tak/Nie).** 
    > 
    > Dowiedz się więcej o każdej z tych opcji odpowiedzi:
    > - [Korygowanie szybkich poprawek](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Zapobieganie błędom konfiguracji za pomocą wymuszania/odmawiania — zalecenia](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Zalecenia pogrupowane według kontroli zabezpieczeń" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Eksport ciągły pobiera nowe typy danych i ulepszone zasady deployifnotexist

Azure Security Center narzędzia eksportu ciągłego umożliwiają eksportowanie Security Center i alertów firmy Security Center do użycia z innymi narzędziami do monitorowania w środowisku.

Eksport ciągły pozwala w pełni dostosować to, co zostanie wyeksportowane i gdzie zostanie. Aby uzyskać szczegółowe informacje, zobacz [Ciągłe eksportowanie Security Center danych.](continuous-export.md)

Te narzędzia zostały udoskonalone i rozwinięte w następujący sposób:

- **Rozszerzone zasady eksportu ciągłego deployifnotexist**. Zasady są teraz:

    - **Sprawdź, czy konfiguracja jest włączona.** Jeśli tak nie jest, zasady będą wyświetlane jako niezgodne i będą tworzyć zgodny zasób. Dowiedz się więcej na temat dostarczonych szablonów Azure Policy na karcie "Wdrażanie na dużą skalę za pomocą Azure Policy" w tece Konfigurowanie [eksportu ciągłego.](continuous-export.md#set-up-a-continuous-export)

    - **Obsługa eksportowania ustaleń zabezpieczeń.** W przypadku Azure Policy szablonów można skonfigurować eksport ciągły w taki sposób, aby uwzględniał wyniki. Jest to istotne w przypadku eksportowania zaleceń, które mają zalecenia "podrzędne", takich jak wyniki skanerów oceny luk w zabezpieczeniach lub konkretne aktualizacje systemu dla zalecenia "Nadrzędne" "Aktualizacje systemu powinny być instalowane na maszynach".
    
    - **Obsługa eksportowania danych bezpiecznego wyniku.**

- **Dodane dane oceny zgodności z przepisami (w wersji zapoznawczej).** Teraz możesz stale eksportować aktualizacje do ocen zgodności z przepisami, w tym dla dowolnych inicjatyw niestandardowych, do obszaru roboczego usługi Log Analytics lub centrum zdarzeń. Ta funkcja jest niedostępna w chmurach krajowych/suwerennych.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Opcje do uwzględnienia informacji o ocenie zgodnej z przepisami z danymi eksportu ciągłego.":::


## <a name="november-2020"></a>Listopad 2020 r.

Aktualizacje w listopadzie obejmują:

- [Dodano 29 zaleceń w wersji zapoznawczej w celu zwiększenia pokrycia testu porównawczego zabezpieczeń platformy Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Dodano NIST SP 800 171 R2 Security Center pulpitu nawigacyjnego zgodności z przepisami firmy Security Center firmy](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Lista zaleceń zawiera teraz filtry](#recommendations-list-now-includes-filters)
- [Ulepszona i rozszerzona funkcja automatycznego aprowizowania](#auto-provisioning-experience-improved-and-expanded)
- [Ocena bezpieczeństwa jest teraz dostępna w eksportie ciągłym (wersja zapoznawcza)](#secure-score-is-now-available-in-continuous-export-preview)
- [Zalecenie "Aktualizacje systemu powinny być zainstalowane na maszynach" zawiera teraz podpodziały](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Strona Zarządzania zasadami w Azure Portal teraz wyświetla stan domyślnych przypisań zasad](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Dodano 29 zaleceń w wersji zapoznawczej w celu zwiększenia pokrycia testu porównawczego zabezpieczeń platformy Azure

Test porównawczy zabezpieczeń platformy Azure to zestaw wytycznych dotyczących najlepszych rozwiązań dotyczących zabezpieczeń i zgodności firmy Microsoft, specyficzny dla platformy Azure, oparty na typowych platformach zgodności. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).

Dodano następujące 29 zaleceń dotyczących wersji zapoznawczej Security Center celu zwiększenia pokrycia tego testu porównawczego.

Rekomendacje dotyczące wersji zapoznawczej nie renderują zasobu w złej kondycji i nie są uwzględniane w obliczeniach wskaźnika bezpieczeństwa. Koryguj je wszędzie tam, gdzie to możliwe, tak aby po zakończeniu okresu zapoznawczego przyczyniły się do oceny. Dowiedz się więcej o tym, jak reagować na te zalecenia, zobacz [Korygowanie zaleceń](security-center-remediate-recommendations.md)w Azure Security Center .

| Kontrola zabezpieczeń                     | Nowe zalecenia                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Szyfrowanie danych podczas przesyłania              | — Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych PostgreSQL<br>— Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL<br>— Należy zaktualizować TLS do najnowszej wersji dla aplikacji interfejsu API<br>— Należy zaktualizować TLS do najnowszej wersji dla aplikacji funkcji<br>— Należy zaktualizować TLS do najnowszej wersji aplikacji internetowej<br>— Protokół FTPS powinien być wymagany w aplikacji interfejsu API<br>— Usługa FTPS powinna być wymagana w aplikacji funkcji<br>— W aplikacji internetowej powinien być wymagany protokół FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Zarządzanie dostępem i uprawnieniami        | — Aplikacje internetowe powinny żądać certyfikatu SSL dla wszystkich żądań przychodzących<br>— Tożsamość zarządzana powinna być używana w aplikacji interfejsu API<br>— Tożsamość zarządzana powinna być używana w aplikacji funkcji<br>— Tożsamość zarządzana powinna być używana w aplikacji internetowej                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Ograniczanie nieautoryzowanego dostępu do sieci | — Dla serwerów PostgreSQL należy włączyć prywatny punkt końcowy<br>— Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB<br>— Prywatny punkt końcowy powinien być włączony dla serwerów MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Włączanie inspekcji i rejestrowania          | — Dzienniki diagnostyczne w App Services powinny być włączone                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń    | — Azure Backup dla maszyn wirtualnych<br>— Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MariaDB<br>- Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL<br>— Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL<br>— Język PHP powinien zostać zaktualizowany do najnowszej wersji dla aplikacji interfejsu API<br>— Język PHP powinien zostać zaktualizowany do najnowszej wersji aplikacji internetowej<br>— Język Java powinien zostać zaktualizowany do najnowszej wersji dla aplikacji interfejsu API<br>— Język Java powinien zostać zaktualizowany do najnowszej wersji dla aplikacji funkcji<br>— Język Java powinien zostać zaktualizowany do najnowszej wersji aplikacji internetowej<br>— Język Python powinien zostać zaktualizowany do najnowszej wersji dla aplikacji interfejsu API<br>— Język Python powinien zostać zaktualizowany do najnowszej wersji dla aplikacji funkcji<br>— Język Python powinien zostać zaktualizowany do najnowszej wersji aplikacji internetowej<br>— Okres przechowywania inspekcji dla serwerów SQL powinien być ustawiony na co najmniej 90 dni |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Powiązane linki:

- [Dowiedz się więcej na temat testu porównawczego zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Dowiedz się więcej o aplikacjach interfejsu API platformy Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Dowiedz się więcej o aplikacjach funkcji platformy Azure](../azure-functions/functions-overview.md)
- [Dowiedz się więcej o aplikacjach internetowych platformy Azure](../app-service/overview.md)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Dodano NIST SP 800 171 R2 Security Center pulpitu nawigacyjnego zgodności z przepisami firmy Security Center firmy

Standard NIST SP 800-171 R2 jest teraz dostępny jako wbudowana inicjatywa do użycia z pulpitem Azure Security Center zgodności z przepisami firmy Azure Security Center. Mapowania kontrolek opisano w teksie Szczegóły wbudowanej inicjatywy [NIST SP 800-171 R2 Regulatory Compliance](../governance/policy/samples/nist-sp-800-171-r2.md). 

Aby zastosować standard do subskrypcji i stale monitorować stan zgodności, skorzystaj z instrukcji z tematu Dostosowywanie zestawu standardów na pulpicie nawigacyjnym [zgodności z przepisami.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 Security Center pulpitu nawigacyjnego zgodności z przepisami firmy Security Center":::

Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Lista zaleceń zawiera teraz filtry

Teraz można filtrować listę zaleceń dotyczących zabezpieczeń zgodnie z zakresem kryteriów. W poniższym przykładzie lista zaleceń została przefiltrowana w celu pokazania zaleceń, które:

- są **ogólnie dostępne** (czyli nie w wersji zapoznawczej)
- są dla **kont magazynu**
- obsługa **szybkiego korygowania** poprawek

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtry listy zaleceń":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Ulepszona i rozszerzona funkcja automatycznego aprowizowania

Funkcja automatycznego aprowizowania pomaga zmniejszyć obciążenie związane z zarządzaniem, instalując wymagane rozszerzenia na nowych i istniejących — maszynach wirtualnych platformy Azure, dzięki czemu mogą oni korzystać Security Center z ochrony maszyny wirtualnej. 

W Azure Security Center coraz więcej rozszerzeń zostało opracowanych i Security Center monitorować większą listę typów zasobów. Narzędzia do automatycznego aprowizowania zostały teraz rozszerzone o obsługę innych rozszerzeń i typów zasobów dzięki wykorzystaniu możliwości Azure Policy.

Teraz możesz skonfigurować automatyczną aprowizowanie:

- Agent usługi Log Analytics
- (Nowy) Azure Policy dodatek dla kubernetes
- (Nowy) Microsoft Dependency Agent

Dowiedz się więcej z [tematu Auto provisioning agents and extensions from Azure Security Center](security-center-enable-data-collection.md)(Automatyczne aprowizowanie agentów i rozszerzeń) Azure Security Center .


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Ocena bezpieczeństwa jest teraz dostępna w eksportie ciągłym (wersja zapoznawcza)

Dzięki ciągłemu eksportowi bezpiecznego wyniku możesz przesyłać strumieniowo zmiany wyniku w czasie rzeczywistym do Azure Event Hubs lub obszaru roboczego usługi Log Analytics. Ta funkcja umożliwia:

- śledzenie oceny bezpieczeństwa w czasie za pomocą raportów dynamicznych
- eksportowanie danych oceny bezpieczeństwa do Azure Sentinel (lub dowolnego innego rozwiązania SIEM)
- zintegruj te dane z dowolnymi procesami, których być może już używasz do monitorowania oceny bezpieczeństwa w organizacji

Dowiedz się więcej na temat [ciągłego eksportowania Security Center danych.](continuous-export.md)


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Zalecenie "Aktualizacje systemu powinny być zainstalowane na maszynach" zawiera teraz podpodziały

Rekomendacja **Aktualizacje systemu powinna zostać zainstalowana na maszynach** została rozszerzona. Nowa wersja zawiera podpodziały dla każdej brakującej aktualizacji i oferuje następujące ulepszenia:

- Przeprojektowane środowisko na Azure Security Center stron Azure Portal. Na maszynach **powinna** zostać zainstalowana strona szczegółów zaleceń dla aktualizacji systemu, która zawiera listę wyników, jak pokazano poniżej. Po wybraniu pojedynczego znajdowania zostanie otwarte okienko szczegółów z linkiem do informacji dotyczących korygowania i listy zasobów, których dotyczy problem.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Otwieranie jednej z podrecommendations w portalu dla zaktualizowanego zalecenia":::

- Wzbogacone dane dla zalecenia z Azure Resource Graph (ARG). ARG to usługa platformy Azure, która została zaprojektowana w celu zapewnienia efektywnej eksploracji zasobów. Możesz użyć usługi ARG do wykonywania zapytań na dużą skalę w danym zestawie subskrypcji, aby efektywnie zarządzać środowiskiem. 

    Na Azure Security Center można użyć usług ARG i [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) do wykonywania zapytań dotyczących szerokiego zakresu danych dotyczących bezpieczeństwa.

    Wcześniej w przypadku zapytania o to zalecenie w ARG jedynymi dostępnymi informacjami było to, że zalecenie musi zostać skorygowane na maszynie. Następujące zapytanie w wersji rozszerzonej zwróci wszystkie brakujące aktualizacje systemu pogrupowane według maszyny.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Strona zarządzania zasadami w Azure Portal teraz wyświetla stan domyślnych przypisań zasad

Teraz możesz sprawdzić, czy subskrypcje mają przypisane domyślne zasady Security Center, na stronie  zasad zabezpieczeń Security Center w Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Strona zarządzania zasadami w Azure Security Center z domyślnymi przypisaniami zasad":::
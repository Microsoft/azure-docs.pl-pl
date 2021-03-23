---
title: Informacje o wersji Azure Security Center
description: Opis nowości i zmian w programie Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.openlocfilehash: f6ec14c577d1203b92085b791f89e4873a97c41a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786101"
---
# <a name="whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Security Center jest aktywnym programowaniem i od bieżąco otrzymuje ulepszenia. Ta strona zawiera informacje o nowych funkcjach, poprawkach błędów i przestarzałych funkcjach, aby zachować aktualność.

Ta strona jest często aktualizowana, więc często należy ją ponownie odwiedzać. 

Aby dowiedzieć się o *planowanych* zmianach, które wkrótce zostaną udostępnione Security Center, zobacz [Ważne zmiany nadchodzące Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Jeśli szukasz elementów starszych niż sześć miesięcy, znajdziesz je w [archiwum, co nowego w programie Azure Security Center](release-notes-archive.md).


## <a name="march-2021"></a>Marzec 2021

Aktualizacje w marcu obejmują:

- [Zarządzanie zaporą platformy Azure zintegrowane z usługą Security Center](#azure-firewall-management-integrated-into-security-center)
- [Ocena luk w zabezpieczeniach programu SQL obejmuje teraz środowisko "Wyłącz regułę" (wersja zapoznawcza)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor skoroszyty zintegrowane z Security Center i trzema udostępnionymi szablonami](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Pulpit nawigacyjny zgodności z przepisami zawiera teraz raporty inspekcji platformy Azure (wersja zapoznawcza)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Dane rekomendacji można wyświetlić na wykresie zasobów platformy Azure za pomocą "Eksploruj w ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Aktualizacje zasad wdrażania automatyzacji przepływu pracy](#updates-to-the-policies-for-deploying-workflow-automation)
- [Dwa starsze zalecenia nie zapisują już danych bezpośrednio w dzienniku aktywności platformy Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Ulepszenia strony rekomendacji](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Zarządzanie zaporą platformy Azure zintegrowane z usługą Security Center

Po otwarciu Azure Security Center pierwsza strona do wyświetlenia jest stroną przegląd. 

Ten interaktywny pulpit nawigacyjny zapewnia ujednolicony wgląd w zabezpieczenia stan obciążeń chmury hybrydowej. Ponadto są wyświetlane alerty zabezpieczeń, informacje o zapotrzebowaniu i inne.

W ramach pomocy przy przeglądaniu stanu zabezpieczeń z poziomu centralnego środowiska firma Microsoft integruje Menedżera zapory platformy Azure z tym pulpitem nawigacyjnym. Teraz można sprawdzić stan pokrycia zapory we wszystkich sieciach i centralnie zarządzać zasadami zapory platformy Azure, rozpoczynając od Security Center.

Dowiedz się więcej o tym pulpicie nawigacyjnym na [stronie przegląd Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Pulpit nawigacyjny omówienia Security Center z kafelkiem dla zapory platformy Azure":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Ocena luk w zabezpieczeniach programu SQL obejmuje teraz środowisko "Wyłącz regułę" (wersja zapoznawcza)

Security Center zawiera wbudowany skaner luk w zabezpieczeniach, który ułatwia odnajdywanie, śledzenie i korygowanie potencjalnych luk w zabezpieczeniach baz danych. Wyniki skanów oceny zawierają omówienie stanu zabezpieczeń maszyn SQL i szczegółowe informacje dotyczące wszelkich ustaleń dotyczących zabezpieczeń.

Jeśli istnieje organizacja, która ma być ignorowana, zamiast skorygować ją, możesz ją wyłączyć. Wyłączone wyniki nie wpływają na swój Bezpieczny wynik ani nie generują niechcianych szumów.

Dowiedz się więcej w temacie [wyłączanie określonych ustaleń](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor skoroszyty zintegrowane z Security Center i trzema udostępnionymi szablonami

W ramach zapłonu ze sprężyną 2021 zapowiedziano zintegrowane Azure Monitor skoroszyty w Security Center.

Możesz użyć nowej integracji, aby zacząć korzystać z wbudowanych szablonów z galerii Security Center. Korzystając z szablonów skoroszytów, można uzyskać dostęp do dynamicznych i wizualnych raportów, aby śledzić stan zabezpieczeń organizacji. Ponadto można tworzyć nowe skoroszyty oparte na danych Security Center lub innych obsługiwanych typach danych oraz szybko wdrażać skoroszyty społeczności ze społeczności usługi GitHub Security Center.

Dostępne są trzy raporty szablonów:

- **Zabezpiecz ocenę w czasie w** celu śledzenia wyników subskrypcji i zmian w zaleceniach dotyczących zasobów
- **Aktualizacje systemu** — wyświetlanie brakujących aktualizacji systemu według zasobów, systemu operacyjnego, ważności i innych
- **Wyniki oceny luk w zabezpieczeniach** — wyświetlanie wyników skanowania zasobów platformy Azure w ramach luk w zabezpieczeniach

Dowiedz się więcej na temat korzystania z tych raportów lub tworzenia własnych [, interaktywnych raportów danych Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Raport dotyczący oceny wyników w czasie":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Pulpit nawigacyjny zgodności z przepisami zawiera teraz raporty inspekcji platformy Azure (wersja zapoznawcza)

Na pasku narzędzi pulpitu nawigacyjnego zgodność z przepisami można teraz pobierać raporty dotyczące platformy Azure i usługi Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Pasek narzędzi pulpitu nawigacyjnego zgodności z przepisami":::

Możesz wybrać kartę dla odpowiednich typów raportów (PCI, SOC, ISO i inne), a następnie użyć filtrów, aby znaleźć wymagane raporty.

Dowiedz się więcej o [zarządzaniu standardami na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrowanie listy dostępnych raportów inspekcji platformy Azure":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Dane rekomendacji można wyświetlić na wykresie zasobów platformy Azure za pomocą "Eksploruj w ARG"

Na stronach szczegółów rekomendacji są teraz dostępne przyciski paska narzędzi "Eksploruj na ARG". Ten przycisk służy do otwierania zapytania grafu zasobów platformy Azure oraz eksplorowania, eksportowania i udostępniania danych rekomendacji.

Usługa Azure Resource Graph (ARG) zapewnia błyskawiczny dostęp do informacji o zasobach w środowiskach chmury z niezawodnymi możliwościami filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób wykonywania zapytań dotyczących informacji w ramach subskrypcji platformy Azure programowo lub z poziomu Azure Portal.

Dowiedz się więcej o [usłudze Azure Resource Graph (arg)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Eksploruj dane rekomendacji na wykresie zasobów platformy Azure.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Aktualizacje zasad wdrażania automatyzacji przepływu pracy

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas potrzebny do zbadania i ograniczenia zdarzeń związanych z bezpieczeństwem.

Firma Microsoft udostępnia trzy Azure Policy zasady "DeployIfNotExist", które tworzą i konfigurują procedury automatyzacji przepływu pracy, dzięki czemu można wdrożyć usługi Automation w całej organizacji:

|Cel  |Zasady  |Identyfikator zasad  |
|---------|---------|---------|
|Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatyzacja przepływu pracy dla zmian zgodności z przepisami|[Wdróż automatyzację przepływu pracy pod kątem zgodności z przepisami Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Istnieją dwie aktualizacje funkcji tych zasad:

- Po przypisaniu te pozycje pozostaną włączone przez wymuszanie.
- Teraz można dostosować te zasady i zaktualizować dowolne parametry nawet po ich wdrożeniu. Na przykład jeśli użytkownik chce dodać inny klucz oceny lub edytować istniejący klucz oceny, może to zrobić.

Wprowadzenie do [szablonów automatyzacji przepływu pracy](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Dowiedz się więcej o [automatyzowaniu odpowiedzi na wyzwalacze Security Center](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Dwa starsze zalecenia nie zapisują już danych bezpośrednio w dzienniku aktywności platformy Azure 

Security Center przekazuje dane dla niemal wszystkich zaleceń dotyczących zabezpieczeń, aby Azure Advisor, które z kolei zapisują je do [dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md).

W przypadku dwóch zaleceń dane są zapisywane równocześnie bezpośrednio w dzienniku aktywności platformy Azure. W przypadku tej zmiany Security Center przestaje zapisywać dane dla tych starszych zaleceń dotyczących zabezpieczeń bezpośrednio do dziennika aktywności. Zamiast tego eksportujemy dane do Azure Advisor tak jak w przypadku wszystkich innych zaleceń.

Istnieją dwa starsze zalecenia:
- Na maszynach należy rozwiązać problemy dotyczące kondycji programu Endpoint Protection
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować

Jeśli uzyskano dostęp do informacji na temat tych dwóch zaleceń w kategorii "zalecenia dotyczące typu TaskDiscovery", to nie jest już dostępne.


### <a name="recommendations-page-enhancements"></a>Ulepszenia strony rekomendacji 

Udostępnimy ulepszoną wersję listy zaleceń, która umożliwia błyskawiczne przedstawienie dodatkowych informacji.

Teraz na wyświetlonej stronie:

1. Maksymalny wynik i bieżący wynik dla każdej kontroli zabezpieczeń.
1. Ikony zamieniające znaczniki takie jak **szybka poprawka** i **wersja zapoznawcza**.
1. Nowa kolumna przedstawiająca [inicjatywę zasad](security-policy-concept.md) powiązaną z każdym zaleceń — widoczna w przypadku wyłączenia opcji "Grupuj według kontrolek".

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Ulepszenia strony zalecenia dotyczące Azure Security Center — marzec 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Udoskonalenia listy &quot;Flat&quot; rekomendacji Azure Security Center — marzec 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Więcej informacji zawiera temat [zalecenia dotyczące zabezpieczeń w Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Luty 2021 r.

Aktualizacje w lutym obejmują:

- [Nowa strona alerty zabezpieczeń w Azure Portal wydawana na potrzeby ogólnej dostępności](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Zalecenia dotyczące ochrony obciążeń Kubernetes, które są udostępniane na potrzeby ogólnej dostępności (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Integracja programu Microsoft Defender dla punktów końcowych z usługą Azure Defender obsługuje teraz systemy Windows Server 2019 i Windows 10 Virtual Desktop (WVD) (w wersji zapoznawczej)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Bezpośrednie łącze do zasad z poziomu strony Szczegóły rekomendacji](#direct-link-to-policy-from-recommendation-details-page)
- [Zalecenie klasyfikacji danych SQL nie ma już wpływu na swój Bezpieczny wynik](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Automatyzacje przepływu pracy mogą być wyzwalane przez zmiany w ocenie zgodności z przepisami (wersja zapoznawcza)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Ulepszenia strony spisu zasobów](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nowa strona alerty zabezpieczeń w Azure Portal wydawana na potrzeby ogólnej dostępności

Strona alertów zabezpieczeń Azure Security Center została przeprojektowana w celu zapewnienia:

- **Udoskonalone środowisko Klasyfikacja dla alertów** — pomaga zmniejszyć zmęczenie alertów i skupić się na najważniejszych zagrożeniach, a lista zawiera dostosowywalne filtry i opcje grupowania.
- **Więcej informacji na liście alertów** — takich jak MITRE ATT&ACK taktykę.
- **Przycisk umożliwiający tworzenie przykładowych alertów** — w celu ocenienia możliwości usługi Azure Defender i testowania alertów. Konfiguracja (na potrzeby integracji SIEM, powiadomień e-mail i automatyzacji przepływu pracy) można tworzyć przykładowe alerty ze wszystkich planów usługi Azure Defender.
- **Wyrównywanie za pomocą doświadczenia ze zdarzeniami platformy Azure** — w przypadku klientów, którzy korzystają z obu tych produktów, przełączają się między nimi
- **Lepsza wydajność** dla list dużych alertów.
- **Nawigacja** przy użyciu klawiatury przez listę alertów.
- **Alerty z grafu zasobów platformy Azure** — możesz wysyłać zapytania dotyczące alertów na wykresie zasobów platformy Azure, interfejsu API przypominającego Kusto dla wszystkich zasobów. Jest to przydatne również w przypadku tworzenia własnych pulpitów nawigacyjnych alertów. [Dowiedz się więcej o usłudze Azure Resource Graph](../governance/resource-graph/index.yml).
- **Funkcja tworzenia przykładowych alertów** — aby utworzyć przykładowe alerty z nowego środowiska alertów, zobacz [generowanie przykładowych alertów usługi Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista alertów zabezpieczeń Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Zalecenia dotyczące ochrony obciążeń Kubernetes, które są udostępniane na potrzeby ogólnej dostępności (GA)

Mamy przyjemność poinformować o ogólnej dostępności zestawu zaleceń dotyczących ochrony obciążeń Kubernetes.

Aby zapewnić, że obciążenia Kubernetes są zabezpieczone domyślnie, Security Center dodaliśmy zalecenia dotyczące poziomu Kubernetes, w tym opcje wymuszania z Kubernetes Admission Control.

Gdy w klastrze usługi Azure Kubernetes Service (AKS) jest zainstalowany dodatek Azure Policy dla programu Kubernetes, każde żądanie do serwera interfejsu API Kubernetes zostanie monitorowane względem wstępnie zdefiniowanego zestawu najlepszych rozwiązań — od 13 zaleceń dotyczących zabezpieczeń — przed utrwaleniem w klastrze. Następnie można skonfigurować w celu wymuszenia najlepszych rozwiązań i ich upoważnienia do przyszłych obciążeń.

Na przykład można przystąpić do tego, że kontenery uprzywilejowane nie powinny być tworzone, a wszystkie przyszłe żądania, które należy wykonać, zostaną zablokowane.

Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Mimo że zalecenia były w wersji zapoznawczej, nie spowodowało to złej kondycji zasobów klastra AKS i nie zostały uwzględnione w obliczeniach bezpiecznego wyniku. w tym ogłoszeniu na ten temat zostanie uwzględniony wynik obliczeń. Jeśli jeszcze ich nie skorygowano, może to spowodować niewielki wpływ na bezpieczny wynik. Skoryguj je wszędzie tam, gdzie to możliwe, zgodnie z opisem zawartym w temacie [Koryguj zalecenia w Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Integracja programu Microsoft Defender dla punktów końcowych z usługą Azure Defender obsługuje teraz systemy Windows Server 2019 i Windows 10 Virtual Desktop (WVD) (w wersji zapoznawczej)

Microsoft Defender for Endpoint to całościowe, dostarczone w chmurze rozwiązanie zabezpieczeń punktu końcowego. Zapewnia oparte na ryzyku zarządzanie i ocenę luk w zabezpieczeniach, a także wykrywanie punktów końcowych i reagowanie na nie (EDR). Aby zapoznać się z pełną listą korzyści wynikających z używania usługi Defender dla punktów końcowych z Azure Security Center, zobacz [Ochrona punktów końcowych za pomocą zintegrowanego rozwiązania Security Center EDR: Microsoft Defender dla punktu końcowego](security-center-wdatp.md).

Po włączeniu usługi Azure Defender dla serwerów w systemie Windows Server licencja na usługę Defender for Endpoint jest dołączona do planu. Jeśli usługa Azure Defender została już włączona dla serwerów i masz serwery z systemem Windows 2019 w ramach subskrypcji, otrzymają oni automatycznie usługę Defender dla punktu końcowego z tą aktualizacją. Nie jest wymagane wykonanie akcji ręcznej. 

Obsługa została teraz rozszerzona o systemy Windows Server 2019 i [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Jeśli włączysz usługę Defender for Endpoint na komputerze z systemem Windows Server 2019, upewnij się, że spełnia on wymagania wstępne opisane w temacie [Włączanie integracji usługi Microsoft Defender for Endpoint](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Bezpośrednie łącze do zasad z poziomu strony Szczegóły rekomendacji

Gdy przeglądasz szczegóły zalecenia, często warto mieć możliwość wyświetlenia podstawowych zasad. Dla każdego zalecenia obsługiwanego przez zasady istnieje nowe łącze na stronie Szczegóły zalecenia:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link do Azure Policy stronie dla określonych zasad wspierających zalecenie":::

Użyj tego linku, aby wyświetlić definicję zasad i przejrzeć logikę oceny. 

Jeśli przeglądasz listę zaleceń w [przewodniku dotyczącym zaleceń dotyczących zabezpieczeń](recommendations-reference.md), zobaczysz także linki do stron definicji zasad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Uzyskiwanie dostępu do strony Azure Policy dla określonych zasad bezpośrednio z poziomu strony informacje o zaleceniach Azure Security Center" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Zalecenie klasyfikacji danych SQL nie ma już wpływu na swój Bezpieczny wynik
Nie ma już wpływu na **ważne dane w bazach danych SQL** . Jest to jedyne zalecenie w kontroli zabezpieczeń **Zastosuj klasyfikację danych** , dzięki czemu kontrolka ma teraz bezpieczną wartość oceny równą 0.

Aby zapoznać się z pełną listą wszystkich kontroli zabezpieczeń w Security Center wraz z ich wynikami oraz listą zaleceń w każdym z nich, zobacz [mechanizmy kontroli zabezpieczeń i ich zalecenia](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Automatyzacje przepływu pracy mogą być wyzwalane przez zmiany w ocenie zgodności z przepisami (wersja zapoznawcza)
Dodaliśmy trzeci typ danych do opcji wyzwalacza dla automatyzacji przepływu pracy: zmiany dotyczące oceny zgodności z przepisami.

Dowiedz się, jak używać narzędzi automatyzacji przepływu pracy w [automatyzowaniu odpowiedzi na Security Center wyzwalacze](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Używanie zmian do oceny zgodności z przepisami w celu wyzwalania automatyzacji przepływu pracy" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Ulepszenia strony spisu zasobów
Strona spisu zasobów Security Center została ulepszona w następujący sposób:

- Podsumowania w górnej części strony zawierają teraz **niezarejestrowane subskrypcje**, pokazując liczbę subskrypcji bez włączonej Security Center.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Liczba niezarejestrowanej subskrypcji w podsumowaniu w górnej części strony spisu zasobów":::

- Filtry zostały rozszerzone i udoskonalone w celu uwzględnienia:
    - **Liczba** — każdy filtr przedstawia liczbę zasobów, które spełniają kryteria każdej kategorii.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Liczba w filtrach na stronie spisu zasobów Azure Security Center":::

    - **Zawiera filtr wykluczeń** (opcjonalnie) — Zawęź wyniki do zasobów, których nie zostały wykluczone. Ten filtr nie jest domyślnie pokazywany, ale jest dostępny z poziomu przycisku **Dodaj filtr** .

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Dodawanie filtru &quot;zawiera zwolnienie&quot; na stronie spisu zasobów Azure Security Center":::

Dowiedz się więcej na temat sposobu [eksplorowania zasobów i zarządzania nimi za pomocą spisu](asset-inventory.md)zasobów.

## <a name="january-2021"></a>Styczeń 2021 r.

Aktualizacje w styczniu obejmują:

- [Usługa Azure Security test jest teraz domyślną inicjatywy zasad dla Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Ocena luk w zabezpieczeniach dla maszyn lokalnych i wielochmurowych jest dostępna na potrzeby ogólnie dostępnej wersji](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [W wersji zapoznawczej jest teraz dostępna stawka zabezpieczeń dla grup zarządzania](#secure-score-for-management-groups-is-now-available-in-preview)
- [Interfejs API oceny zabezpieczeń jest publikowany na potrzeby ogólnej dostępności](#secure-score-api-is-released-for-general-availability-ga)
- [Zawieszonego ochronę DNS dodaną do usługi Azure Defender dla App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Łączniki z obsługą kilku chmur są udostępniane na potrzeby ogólnej dostępności](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Wyłącz wszystkie zalecenia z bezpiecznego wyniku dla subskrypcji i grup zarządzania](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Użytkownicy mogą teraz zażądać widoczności dla całej dzierżawy od administratora globalnego](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 zaleceń dotyczących wersji zapoznawczej dodano w celu zwiększenia zakresu testów zabezpieczeń platformy Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Eksport CSV przefiltrowanej listy zaleceń](#csv-export-of-filtered-list-of-recommendations)
- [Zasoby "nie dotyczy" są teraz zgłaszane jako "zgodne" w Azure Policy oceny](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Eksportuj cotygodniowe migawki bezpiecznego oceny i danych zgodności z przepisami przy użyciu eksportu ciągłego (wersja zapoznawcza)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Usługa Azure Security test jest teraz domyślną inicjatywy zasad dla Azure Security Center

Usługa Azure Security test to zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane do najlepszych rozwiązań w zakresie bezpieczeństwa i zapewniających zgodność. Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

W ostatnich miesiącach Security Center Lista wbudowanych zaleceń dotyczących zabezpieczeń znacznie się zwiększyła, aby zwiększyć nasze pokrycie tego testu porównawczego.

W tej wersji test porównawczy jest podstawą dla zaleceń Security Center i w pełni zintegrowanych jako inicjatywa zasad domyślnych. 

Wszystkie usługi platformy Azure mają stronę odniesienia zabezpieczeń w swojej dokumentacji. Na przykład [jest to Security Center linia bazowa](security-baseline.md). Te linie bazowe są oparte na teście zabezpieczeń platformy Azure.

Jeśli używasz pulpitu nawigacyjnego zgodności z przepisami Security Center, zobaczysz dwa wystąpienia testu porównawczego w okresie przejściowym:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Pulpit nawigacyjny zgodności z przepisami Azure Security Center przedstawiający test wydajności usługi Azure Security":::

Istniejące rekomendacje nie wpływają na siebie i w miarę wzrostu wydajności, zmiany zostaną automatycznie odzwierciedlone w Security Center. 

Aby dowiedzieć się więcej, zobacz następujące strony:

- [Dowiedz się więcej o teście zabezpieczeń Azure](../security/benchmarks/introduction.md)
- [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Ocena luk w zabezpieczeniach dla maszyn lokalnych i wielochmurowych jest dostępna na potrzeby ogólnie dostępnej wersji

W październiku ogłoszono w wersji zapoznawczej skanowanie serwerów z obsługą usługi Azure ARC przy użyciu [usługi Azure Defender na potrzeby](defender-for-servers-introduction.md)zintegrowanego skanera oceny luk w zabezpieczeniach serwerów (obsługiwanego przez Qualys).

Ten element jest teraz udostępniany do ogólnej dostępności (GA).

Gdy usługa Azure Arc została włączona na maszynach spoza platformy Azure, Security Center będzie oferować ręczne wdrażanie skanera zintegrowanej luki w zabezpieczeniach.

Dzięki tej aktualizacji można wyłączyć **usługę Azure Defender dla serwerów** , aby skonsolidować program do zarządzania lukami w zabezpieczeniach dla wszystkich zasobów platformy Azure i spoza platformy Azure.

Główne możliwości:

- Monitorowanie stanu aprowizacji skanera (ocena luk w zabezpieczeniach) na maszynach usługi Azure Arc
- Inicjowanie obsługi zintegrowanego agenta VA do niechronionych maszyn z systemem Windows i Linux na platformie Azure (ręcznie i w skali)
- Otrzymywanie i analizowanie wykrytych luk w zabezpieczeniach wdrożonych agentów (ręcznie i w skali)
- Ujednolicone środowisko dla maszyn wirtualnych platformy Azure i usługi Azure Arc

[Dowiedz się więcej o wdrażaniu zintegrowanego skanera luk w zabezpieczeniach na maszynach hybrydowych](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Dowiedz się więcej o serwerach z obsługą usługi Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>W wersji zapoznawczej jest teraz dostępna stawka zabezpieczeń dla grup zarządzania

Na stronie bezpieczny wynik są teraz wyświetlane zagregowane wyniki zabezpieczeń dla grup zarządzania, a także poziom subskrypcji. Teraz można zobaczyć listę grup zarządzania w organizacji i ocenę dla każdej grupy zarządzania.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Wyświetlanie zabezpieczonych ocen dla grup zarządzania.":::

Dowiedz się więcej o [bezpiecznych kontrolach oceny i zabezpieczeń w Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Interfejs API oceny zabezpieczeń jest publikowany na potrzeby ogólnej dostępności

Teraz możesz uzyskać dostęp do oceny za pośrednictwem [interfejsu API protokołu Secure Score](/rest/api/securitycenter/securescores/). Metody interfejsu API zapewniają elastyczność umożliwiającą wykonywanie zapytań dotyczących danych i Tworzenie własnego mechanizmu raportowania z bezpiecznymi wynikami w czasie. Na przykład:

- Korzystanie z interfejsu API funkcji **Secure Scores** w celu uzyskania wyników dla określonej subskrypcji
- Użyj interfejsu API **kontroli wyników bezpiecznego** , aby wyświetlić listę kontrolek zabezpieczeń i bieżący wynik subskrypcji

Dowiedz się więcej na temat zewnętrznych narzędzi, które są dostępne z interfejsem API oceny zabezpieczeń w [obszarze bezpiecznych punktów społeczności usługi GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Dowiedz się więcej o [bezpiecznych kontrolach oceny i zabezpieczeń w Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Zawieszonego ochronę DNS dodaną do usługi Azure Defender dla App Service

Przejęcia poddomen są typowymi zagrożeniami o wysokiej ważności dla organizacji. Przejęcie poddomeny może wystąpić, gdy istnieje rekord DNS wskazujący na niezainicjowaną witrynę sieci Web. Takie rekordy DNS są również znane jako wpisy "zawieszonego DNS". Rekordy CNAME są szczególnie zagrożone tym zagrożeniem. 

Przejęcia poddomeny umożliwiają uczestnikom zagrożeń przekierowywanie ruchu przeznaczonego dla domeny organizacji do lokacji wykonującej złośliwe działanie.

Usługa Azure Defender dla App Service teraz wykrywa wpisy DNS zawieszonego podczas likwidowania witryny sieci App Service. Jest to moment, w którym wpis DNS wskazuje na nieistniejący zasób, a witryna sieci Web jest narażona na przejęcie poddomeny. Te zabezpieczenia są dostępne niezależnie od tego, czy domeny są zarządzane za pomocą Azure DNS, czy rejestratora domeny zewnętrznej i ma zastosowanie do obu App Service w systemie Windows i App Service na komputerze z systemem Linux.

Więcej informacji:

- [Tabela referencyjna alertów App Service](alerts-reference.md#alerts-azureappserv) — zawiera dwa nowe alerty usługi Azure Defender wyzwalane po wykryciu wpisu DNS zawieszonego
- [Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych](../security/fundamentals/subdomain-takeover.md) — Poznaj zagrożenie związane z przejęciem poddomeny i aspektem DNS zawieszonego
- [Wprowadzenie do usługi Azure Defender dla App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Łączniki z obsługą kilku chmur są udostępniane na potrzeby ogólnej dostępności

W przypadku obciążeń w chmurze zwykle obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą być takie same.

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Połączenie konta AWS lub GCP integruje swoje natywne narzędzia zabezpieczeń, takie jak AWS Security Hub i GCP Security Command Center w Azure Security Center.

Ta możliwość oznacza, że Security Center zapewnia widoczność i ochronę we wszystkich głównych środowiskach chmurowych. Niektóre zalety tej integracji:

- Automatyczne Inicjowanie obsługi agentów — Security Center używa usługi Azure Arc do wdrożenia agenta Log Analytics do wystąpień AWS
- Zarządzanie zasadami
- Zarządzanie lukami w zabezpieczeniach
- Wykrywanie i odpowiedź osadzonych punktów końcowych (EDR)
- Wykrywanie niepożądanych konfiguracji zabezpieczeń
- Pojedynczy widok przedstawiający zalecenia dotyczące zabezpieczeń od wszystkich dostawców chmury
- Uwzględnianie wszystkich zasobów w ramach obliczeń bezpiecznego wyniku Security Center
- Oceny zgodności z przepisami dla zasobów AWS i GCP

Z menu Security Center wybierz pozycję **wiele łączników w chmurze** , a zobaczysz opcje tworzenia nowych łączników:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Przycisk dodawania konta AWS na stronie łączników wielochmurowych Security Center":::

Dowiedz się więcej w:
- [Połącz konta AWS z Azure Security Center](quickstart-onboard-aws.md)
- [Połącz konta GCP z Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Wyłącz wszystkie zalecenia z bezpiecznego wyniku dla subskrypcji i grup zarządzania

Rozszerzamy możliwości wykluczenia, aby uwzględnić wszystkie zalecenia. Udostępnienie dalszych opcji dostosowania zaleceń dotyczących zabezpieczeń, które Security Center dla subskrypcji, grupy zarządzania lub zasobów.

Czasami zasób zostanie wyświetlony jako nieprawidłowy, gdy wiadomo, że problem został rozwiązany przez narzędzie innej firmy, które nie wykryto Security Center. Lub zalecenie będzie widoczne w zakresie, w którym uważasz, że nie należy. Zalecenie może być nieodpowiednie dla określonej subskrypcji. Lub być może Twoja organizacja zdecydowała się zaakceptować ryzyko związane z konkretnym zasobem lub zaleceniem.

Za pomocą tej funkcji w wersji zapoznawczej można teraz utworzyć wykluczenie dla zalecenia:

- **Zwolnij zasób** , aby upewnić się, że nie znajduje się na liście zasobów w złej kondycji w przyszłości i nie ma wpływu na bezpieczny wynik. Zasób będzie wyświetlany jako nie dotyczy, a powód zostanie wyświetlony jako "zwolniony" z określonym uzasadnieniem.

- **Zwolnij subskrypcję lub grupę zarządzania** , aby upewnić się, że zalecenie nie ma wpływu na Twój bezpieczny wynik i nie będzie wyświetlane dla subskrypcji lub grupy zarządzania w przyszłości. Odnosi się to do istniejących zasobów i wszystkich utworzonych w przyszłości. Zalecenie zostanie oznaczone określonym uzasadnieniem wybranym dla wybranego zakresu.

Dowiedz się więcej na temat [zwalniania zasobów i rekomendacji z poziomu bezpiecznego](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Użytkownicy mogą teraz zażądać widoczności dla całej dzierżawy od administratora globalnego

Jeśli użytkownik nie ma uprawnień do wyświetlania Security Center danych, zobaczy teraz link umożliwiający zażądanie uprawnień od administratora globalnego swojej organizacji. Żądanie zawiera rolę, której lubisz, oraz uzasadnienie, dlaczego jest to konieczne.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Transparent informujący użytkownika, że może zażądać uprawnień na poziomie dzierżawy.":::

Zapoznaj się z dodatkowymi [żądaniami w przypadku braku wystarczających uprawnień](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 zaleceń dotyczących wersji zapoznawczej dodano w celu zwiększenia zakresu testów zabezpieczeń platformy Azure

Usługa Azure Security test jest domyślną inicjatywą zasad w Azure Security Center. 

Aby zwiększyć pokrycie tego testu porównawczego, do Security Center dodano następujące zalecenia dotyczące wersji zapoznawczej 35.

> [!TIP]
> Zalecenia dotyczące wersji zapoznawczej nie powodują złej kondycji zasobu i nie są uwzględniane w obliczeniach bezpiecznego wyniku. Skoryguj je wszędzie tam, gdzie to możliwe, aby po zakończeniu okresu korzystania z wersji zapoznawczej doczyniły się do oceny. Dowiedz się więcej o tym, jak odpowiedzieć na te zalecenia w temacie [Koryguj zalecenia w Azure Security Center](security-center-remediate-recommendations.md).

| Kontrola zabezpieczeń                     | Nowe rekomendacje                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włącz szyfrowanie w spoczynku            | — Konta Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w czasie spoczynku<br>-Azure Machine Learning obszary robocze powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)<br>— Należy włączyć ochronę danych klucza dla serwerów MySQL<br>— Należy włączyć ochronę danych klucza dla serwerów PostgreSQL<br>-Cognitive Services konta powinny włączać szyfrowanie danych za pomocą klucza zarządzanego przez klienta (CMK)<br>-Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)<br>-Wystąpienia zarządzane przez program SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w spoczynku<br>— Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych<br>— Konta magazynu powinny używać klucza zarządzanego przez klienta (CMK) do szyfrowania                                                                                                                                                              |
| Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń    | -Subskrypcje powinny mieć adres e-mail kontaktu pod kątem problemów z zabezpieczeniami<br> — Funkcja autoaprowizacji agenta Log Analytics powinna być włączona w ramach subskrypcji<br> -Należy włączyć powiadomienia e-mail o alertach o wysokiej ważności<br> -Wyślij powiadomienie e-mail do właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności.<br> -Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczeniem<br> -Magazyny kluczy powinny mieć włączone usuwanie nietrwałe |
| Zarządzanie dostępem i uprawnieniami        | -Aplikacje funkcji powinny mieć włączone "certyfikaty klienta (przychodzące certyfikaty klienta)" |
| Ochrona aplikacji przed atakami DDoS | — Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway<br> -Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure Front drzwiczk Service |
| Ogranicz nieautoryzowany dostęp do sieci | -Zapora powinna być włączona na Key Vault<br> -Prywatny punkt końcowy powinien być skonfigurowany dla Key Vault<br> -Konfiguracja aplikacji powinna używać prywatnego linku<br> -Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej<br> -Azure Event Grid domeny powinny używać prywatnego linku<br> -Azure Event Grid tematy powinny używać prywatnego linku<br> -Azure Machine Learning obszary robocze powinny używać prywatnego linku<br> — Usługa Azure sygnalizująca powinna używać prywatnego linku<br> — Chmura Wiosenna platformy Azure powinna używać iniekcji sieci<br> -Rejestr kontenerów nie powinien zezwalać na nieograniczony dostęp do sieci<br> -Rejestry kontenerów powinny używać prywatnego linku<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów MariaDB<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów MySQL<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów PostgreSQL<br> — Konto magazynu powinno korzystać z prywatnego połączenia z linkiem<br> — Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej<br> — Szablony konstruktora obrazów maszyn wirtualnych powinny używać prywatnego linku|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Powiązane linki:

- [Dowiedz się więcej o teście zabezpieczeń Azure](../security/benchmarks/introduction.md)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Eksport CSV przefiltrowanej listy zaleceń 

W listopadzie 2020 dodaliśmy filtry do strony rekomendacje ([Lista zaleceń zawiera teraz filtry](#recommendations-list-now-includes-filters)). W grudniu rozwinięto te filtry ([Strona zalecenia ma nowe filtry dotyczące środowiska, ważności i dostępnych odpowiedzi](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

W tym ogłoszeniu zmienimy zachowanie przycisku **Pobierz do formatu CSV** , tak aby eksport CSV zawierał tylko zalecenia aktualnie wyświetlane na liście filtrowanej. 

Na przykład na poniższej ilustracji widać, że lista została przefiltrowana do dwóch zaleceń. Wygenerowany plik CSV zawiera szczegóły stanu każdego zasobu, którego dotyczą te dwa zalecenia.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Eksportowanie zaleceń filtrowanych do pliku CSV":::

Więcej informacji zawiera temat [zalecenia dotyczące zabezpieczeń w Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Zasoby "nie dotyczy" są teraz zgłaszane jako "zgodne" w Azure Policy oceny

Wcześniej zasoby, które zostały ocenione pod kątem rekomendacji i nie są **stosowane** , pojawiły się w Azure Policy jako "niezgodne". Żadna akcja użytkownika nie może zmienić stanu na "zgodny". W przypadku tej zmiany są one zgłaszane jako "zgodne" w celu zwiększenia przejrzystości.

Jedyny wpływ będzie widoczny w Azure Policy, w którym zostanie zwiększona liczba zgodnych zasobów. Nie będzie to miało wpływu na Twój bezpieczny wynik w Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Eksportuj cotygodniowe migawki bezpiecznego oceny i danych zgodności z przepisami przy użyciu eksportu ciągłego (wersja zapoznawcza)

Dodaliśmy nową funkcję w wersji zapoznawczej do narzędzi [eksportu ciągłego](continuous-export.md) do eksportowania cotygodniowych migawek bezpiecznego oceny i danych zgodności z przepisami.

Podczas definiowania eksportu ciągłego należy ustawić częstotliwość eksportowania:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Wybieranie częstotliwości eksportu ciągłego":::

- **Przesyłanie strumieniowe** — oceny zostaną przesłane w czasie rzeczywistym, gdy stan kondycji zasobu zostanie zaktualizowany (jeśli nie wystąpią żadne aktualizacje, żadne dane nie zostaną wysłane).
- **Migawki** — migawka bieżącego stanu wszystkich ocen zgodności z przepisami będzie wysyłana co tydzień (jest to funkcja w wersji zapoznawczej dla cotygodniowych migawek bezpiecznych ocen i danych zgodności z przepisami).

Dowiedz się więcej na temat pełnych możliwości tej funkcji w [sposób ciągły eksport danych Security Center](continuous-export.md).

## <a name="december-2020"></a>Grudzień 2020 r.

Aktualizacje w grudniu obejmują:

- [Usługa Azure Defender dla serwerów SQL na maszynach jest ogólnie dostępna](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Usługa Azure Defender for SQL dla dedykowanej puli SQL usługi Azure Synapse Analytics jest ogólnie dostępna](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Administratorzy globalni mogą teraz udzielić sobie uprawnień na poziomie dzierżawy](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dwa nowe plany usługi Azure Defender: usługa Azure Defender dla systemu DNS i usługi Azure Defender dla Menedżer zasobów (w wersji zapoznawczej)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nowa strona alertów zabezpieczeń w Azure Portal (wersja zapoznawcza)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Security Center środowisko w Azure SQL Database & wystąpieniu zarządzanym SQL](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Zaktualizowano narzędzia i filtry spisu zasobów](#asset-inventory-tools-and-filters-updated)
- [Zalecenie dotyczące aplikacji sieci Web żądających certyfikatów SSL, które nie są już częścią bezpiecznego wyniku](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Strona zalecenia zawiera nowe filtry dotyczące środowiska, ważności i dostępnych odpowiedzi](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Eksport ciągły pobiera nowe typy danych i udoskonalone zasady deployifnotexist](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Usługa Azure Defender dla serwerów SQL na maszynach jest ogólnie dostępna

Azure Security Center oferuje dwa plany usługi Azure Defender dla serwerów SQL:

- **Usługa Azure Defender dla serwerów usługi Azure SQL Database** — obrona natywnych serwerów SQL platformy Azure 
- **Usługa Azure Defender dla serwerów SQL na maszynach** — rozszerza te same zabezpieczenia na serwery SQL w środowiskach hybrydowych, wielochmurowych i lokalnych

Za pomocą tego anonsu **usługa Azure Defender dla programu SQL zapewnia** teraz ochronę baz danych i ich danych, wszędzie tam, gdzie się znajdują.

Usługa Azure Defender dla programu SQL zawiera funkcje oceny luk w zabezpieczeniach. Narzędzie do oceny luk w zabezpieczeniach obejmuje następujące funkcje zaawansowane:

- **Konfiguracja linii bazowej** (New!), aby inteligentnie udoskonalać wyniki skanowania luk w zabezpieczeniach, które mogą reprezentować rzeczywiste problemy z zabezpieczeniami. Po ustaleniu stanu zabezpieczeń linii bazowej narzędzie do oceny luk w zabezpieczeniach zgłasza odchylenia od tego stanu linii bazowej. Wyniki, które pasują do linii bazowej, są uznawane za przekazanie kolejnych skanów. Dzięki temu możesz i analitykom skupić uwagę na miejscu.
- **Szczegółowe informacje na temat testów porównawczych** ułatwiające *zrozumienie* odnalezionych wyników i przyczyn związanych z zasobami.
- **Skrypty korygowania** pomagające ograniczyć określone zagrożenia.

Dowiedz się więcej o [usłudze Azure Defender dla języka SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Usługa Azure Defender for SQL dla dedykowanej puli SQL usługi Azure Synapse Analytics jest ogólnie dostępna

Azure Synapse Analytics (dawniej SQL DW) to Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizy danych Big Data. Dedykowane pule SQL to funkcje magazynowania danych przedsiębiorstwa w usłudze Azure Synapse. Dowiedz się więcej w temacie [co to jest usługa Azure Synapse Analytics (wcześniej SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Usługa Azure Defender dla programu SQL chroni dedykowane pule SQL przy użyciu:

- **Zaawansowana ochrona przed zagrożeniami** w celu wykrywania zagrożeń i ataków 
- **Możliwości oceny luk** w zabezpieczeniach w celu identyfikowania i rozwiązywania problemów z konfiguracją zabezpieczeń

Usługa Azure Defender for SQL dla usług Azure Synapse Analytics jest automatycznie dodawana do pakietu baz danych SQL Azure w Azure Security Center. Nowa karta "Azure Defender for SQL" znajduje się na stronie obszaru roboczego Synapse w Azure Portal.

Dowiedz się więcej o [usłudze Azure Defender dla języka SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Administratorzy globalni mogą teraz udzielić sobie uprawnień na poziomie dzierżawy

Użytkownik z rolą Azure Active Directory **administrator globalny** może korzystać z obowiązków obejmujących dzierżawę, ale nie ma uprawnień platformy Azure do wyświetlania informacji o całej organizacji w Azure Security Center. 

Aby przypisać własne uprawnienia na poziomie dzierżawy, postępuj zgodnie z instrukcjami podanymi w temacie [Przyznawanie uprawnień dla całej dzierżawy](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dwa nowe plany usługi Azure Defender: usługa Azure Defender dla systemu DNS i usługi Azure Defender dla Menedżer zasobów (w wersji zapoznawczej)

Dodaliśmy dwie nowe funkcje natywnej ochrony przed zagrożeniami w chmurze dla środowiska platformy Azure.

Te nowe zabezpieczenia znacznie rozszerzają odporność na ataki od aktorów zagrożeń i znacząco zwiększają liczbę zasobów platformy Azure chronionych przez usługę Azure Defender.

- **Usługa Azure Defender dla Menedżer zasobów** — automatycznie monitoruje wszystkie operacje zarządzania zasobami wykonywane w organizacji. Aby uzyskać więcej informacji, zobacz:
    - [Wprowadzenie do usługi Azure Defender dla Menedżer zasobów](defender-for-resource-manager-introduction.md)
    - [Reagowanie na alerty usługi Azure Defender dla usługi Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista alertów dostarczonych przez usługę Azure Defender dla Menedżer zasobów](alerts-reference.md#alerts-resourcemanager)

- **Usługa Azure Defender dla systemu DNS** — stale monitoruje wszystkie zapytania DNS z zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz:
    - [Wprowadzenie do usługi Azure Defender dla systemu DNS](defender-for-dns-introduction.md)
    - [Reagowanie na alerty usługi Azure Defender dla usługi DNS](defender-for-dns-usage.md)
    - [Lista alertów udostępnianych przez usługę Azure Defender dla systemu DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nowa strona alertów zabezpieczeń w Azure Portal (wersja zapoznawcza)

Strona alertów zabezpieczeń Azure Security Center została przeprojektowana w celu zapewnienia:

- **Udoskonalone środowisko Klasyfikacja dla alertów** — pomaga zmniejszyć zmęczenie alertów i skupić się na najważniejszych zagrożeniach, a lista zawiera dostosowywalne filtry i opcje grupowania
- **Więcej informacji na liście alertów** — takich jak MITRE ATT&ACK taktykę
- **Przycisk umożliwiający tworzenie przykładowych alertów** — aby oszacować możliwości usługi Azure Defender i przetestować konfigurację alertów (na potrzeby integracji Siem, powiadomień e-mail i automatyzacji przepływu pracy), możesz utworzyć przykładowe alerty ze wszystkich planów usługi Azure Defender
- **Wyrównywanie za pomocą doświadczenia ze zdarzeniami platformy Azure** — w przypadku klientów, którzy korzystają z obu tych produktów, przełączenie między nimi jest teraz bardziej prostym doświadczeniem i można łatwo poznać jeden z nich.
- **Lepsza wydajność** dla list z dużymi alertami
- **Nawigacja** przy użyciu klawiatury przez listę alertów
- **Alerty z grafu zasobów platformy Azure** — możesz wysyłać zapytania dotyczące alertów na wykresie zasobów platformy Azure, interfejsu API przypominającego Kusto dla wszystkich zasobów. Jest to przydatne również w przypadku tworzenia własnych pulpitów nawigacyjnych alertów. [Dowiedz się więcej o usłudze Azure Resource Graph](../governance/resource-graph/index.yml).

Aby uzyskać dostęp do nowego środowiska, Użyj linku "Wypróbuj teraz" na transparencie w górnej części strony alertów zabezpieczeń.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Transparent z linkiem do nowego środowiska obsługi alertów w wersji zapoznawczej":::

Aby utworzyć przykładowe alerty z nowego środowiska alertów, zobacz [generowanie przykładowych alertów usługi Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Security Center środowisko w Azure SQL Database & wystąpieniu zarządzanym SQL 

Security Center środowisko w programie SQL zapewnia dostęp do następujących Security Center i usługi Azure Defender dla funkcji SQL:

- **Zalecenia dotyczące zabezpieczeń** — Security Center okresowo analizuje stan zabezpieczeń wszystkich połączonych zasobów platformy Azure w celu zidentyfikowania potencjalnych problemów z konfiguracją zabezpieczeń. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach i ulepszania stan zabezpieczeń organizacji.
- **Alerty zabezpieczeń** — usługa wykrywania, która stale monitoruje działania usługi Azure SQL pod kątem zagrożeń, takich jak iniekcja kodu SQL, ataki z wymuszeniem i nadużycie uprawnień. Ta usługa wyzwala szczegółowe i zorientowane na działania alerty zabezpieczeń w Security Center i oferuje opcje dla kontynuowania badań za pomocą platformy Azure — natywnego rozwiązania SIEM firmy Microsoft.
- **Ustalenia** — usługa oceny luk w zabezpieczeniach, która stale monitoruje konfiguracje SQL platformy Azure i pomaga skorygować luki w zabezpieczeniach. Przeglądy oceny zawierają omówienie stanów zabezpieczeń usługi Azure SQL wraz ze szczegółowymi ustaleniami w zakresie zabezpieczeń.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Funkcje zabezpieczeń Azure Security Center dla programu SQL są dostępne w ramach usługi Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Zaktualizowano narzędzia i filtry spisu zasobów

Strona spisu w Azure Security Center została odświeżona z następującymi zmianami:

- **Przewodniki i opinie** dodane do paska narzędzi. Spowoduje to otwarcie okienka z linkami do powiązanych informacji i narzędzi. 
- **Filtr subskrypcje** został dodany do domyślnych filtrów dostępnych dla Twoich zasobów.
- **Otwórz link zapytania** , aby otworzyć bieżące opcje filtru jako zapytanie wykresu zasobów platformy Azure (dawniej "widok w Eksploratorze grafu zasobów").
- **Opcje operatora** dla każdego filtru. Teraz można wybrać spośród większej liczby operatorów logicznych niż "=". Na przykład możesz chcieć znaleźć wszystkie zasoby z aktywnymi zaleceniami, których tytuły zawierają ciąg "Szyfruj". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Kontrolki dla opcji operatora w filtrach spisu zasobów":::

Dowiedz się więcej na temat spisu w temacie [Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu](asset-inventory.md)zasobów.


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Zalecenie dotyczące aplikacji sieci Web żądających certyfikatów SSL, które nie są już częścią bezpiecznego wyniku

Zalecenie "aplikacje sieci Web powinno zażądać certyfikatu SSL dla wszystkich żądań przychodzących" zostało przeniesione z kontroli zabezpieczeń **Zarządzanie dostępem i uprawnieniami** (wartość maksymalna wynosząca 4) do **implementacji najlepszych** rozwiązań w zakresie zabezpieczeń (które nie uwzględnia żadnych punktów). 

Upewnienie się, że aplikacja internetowa żąda certyfikatu, co sprawia, że jest bezpieczniejsze. Jednak dla publicznych aplikacji sieci Web jest to nieistotne. Jeśli uzyskujesz dostęp do witryny za pośrednictwem protokołu HTTP, a nie HTTPS, nie otrzymasz żadnych certyfikatów klienta. Dlatego jeśli aplikacja wymaga certyfikatów klienta, nie należy zezwalać na żądania do aplikacji za pośrednictwem protokołu HTTP. Dowiedz się więcej w temacie [Konfigurowanie wzajemnego uwierzytelniania TLS dla Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

W przypadku tej zmiany zalecenie jest teraz zalecanym najlepszym rozwiązaniem, które nie ma wpływu na ocenę. 

Dowiedz się, które zalecenia znajdują się w każdej kontroli zabezpieczeń w obszarze kontrola [zabezpieczeń i ich zalecenia](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Strona zalecenia zawiera nowe filtry dotyczące środowiska, ważności i dostępnych odpowiedzi

Azure Security Center monitoruje wszystkie połączone zasoby i generuje zalecenia dotyczące zabezpieczeń. Skorzystaj z tych zaleceń, aby wzmocnić chmurę hybrydową stan i śledzić zgodność z zasadami i normami istotnymi dla Twojej organizacji, branży i kraju.

Ponieważ Security Center w dalszym ciągu rozszerza swój zakres i funkcje, lista zaleceń dotyczących zabezpieczeń rośnie co miesiąc. Na przykład zapoznaj [się z artykułem 29 zaleceń dotyczących wersji zapoznawczych, aby zwiększyć zakres testów zabezpieczeń platformy Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Mając listę rozwijaną, istnieje konieczność filtrowania zaleceń w celu znalezienia najważniejszych korzyści. W listopadzie dodaliśmy filtry do strony zalecenia (zobacz [Lista zaleceń zawiera teraz filtry](#recommendations-list-now-includes-filters)).

Filtry dodane w tym miesiącu zapewniają opcje, aby uściślić listę zaleceń zgodnie z:

- **Środowisko** — zapoznaj się z zaleceniami dotyczącymi zasobów AWS, GCP lub Azure (lub dowolnej kombinacji)
- **Ważność** — Wyświetl zalecenia zgodnie z klasyfikacją ważności ustawioną przez Security Center
- **Akcje odpowiedzi** — wyświetlanie zaleceń zgodnie z dostępnością Security Center opcje odpowiedzi: szybka naprawa, odmowa i wymuszanie

    > [!TIP]
    > Filtr akcje odpowiedzi zastępuje filtr **dostępne szybkie poprawki (tak/nie)** . 
    > 
    > Dowiedz się więcej na temat każdej z tych opcji odpowiedzi:
    > - [Szybkie rozwiązywanie problemów](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Zapobieganie błędom konfiguracji za pomocą wymuszania/odmawiania — zalecenia](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Zalecenia pogrupowane według kontroli zabezpieczeń" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Eksport ciągły pobiera nowe typy danych i udoskonalone zasady deployifnotexist

Narzędzia eksportu ciągłego Azure Security Center umożliwiają eksportowanie zaleceń i alertów Security Center do użycia z innymi narzędziami do monitorowania w środowisku.

Eksport ciągły pozwala w pełni dostosować zawartość, która zostanie wyeksportowana, oraz miejsce, w której zostanie ono przeszukane. Aby uzyskać szczegółowe informacje, zobacz  [ciągły eksport danych Security Center](continuous-export.md).

Te narzędzia zostały ulepszone i rozszerzane w następujący sposób:

- **Ulepszone zaawansowane zasady deployifnotexistymi eksportu**. Zasady teraz:

    - **Sprawdź, czy konfiguracja jest włączona.** Jeśli tak nie jest, zasady będą widoczne jako niezgodne i tworzą zgodne zasoby. Dowiedz się więcej o dostarczonych szablonach Azure Policy na karcie "wdrażanie w skali przy użyciu Azure Policy" w temacie [Konfigurowanie ciągłego eksportowania](continuous-export.md#set-up-a-continuous-export).

    - **Obsługa eksportowania wyników dotyczących zabezpieczeń.** Korzystając z szablonów Azure Policy, można skonfigurować eksport ciągły w taki sposób, aby zawierał wyniki. Jest to istotne w przypadku eksportowania zaleceń, które mają zalecenia "Sub", takie jak wyniki z skanerów oceny luk w zabezpieczeniach lub aktualizacje systemu dla zaleceń "nadrzędnej", należy zainstalować na swoich maszynach.
    
    - **Obsługa eksportowania zabezpieczonych danych oceny.**

- **Dodano dane oceny zgodności z przepisami (w wersji zapoznawczej).** Teraz możesz ciągle eksportować aktualizacje do oceny zgodności z przepisami, w tym dla dowolnych inicjatyw niestandardowych, do Log Analytics obszaru roboczego lub centrum zdarzeń. Ta funkcja jest niedostępna w chmurach narodowych/suwerennych.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Opcje w celu uwzględnienia informacji o ocenie zgodności z przepisami w przypadku danych eksportu ciągłego.":::


## <a name="november-2020"></a>Listopad 2020 r.

Aktualizacje w listopadzie obejmują:

- [29 zaleceń dotyczących wersji zapoznawczej dodanych w celu zwiększenia zakresu testów zabezpieczeń platformy Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 dodano do Security Center pulpitu nawigacyjnego zgodności z przepisami](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Lista rekomendacji zawiera teraz filtry](#recommendations-list-now-includes-filters)
- [Ulepszone i rozszerzone środowisko aprowizacji](#auto-provisioning-experience-improved-and-expanded)
- [Ocena zabezpieczeń jest teraz dostępna w przypadku eksportu ciągłego (wersja zapoznawcza)](#secure-score-is-now-available-in-continuous-export-preview)
- [Zalecenie "aktualizacje systemu powinny być zainstalowane na Twoich komputerach" obejmuje teraz podzalecenia](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Na stronie Zarządzanie zasadami w Azure Portal teraz jest wyświetlany stan domyślnych przypisań zasad](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 zaleceń dotyczących wersji zapoznawczej dodanych w celu zwiększenia zakresu testów zabezpieczeń platformy Azure

Usługa Azure Security test to zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane w oparciu o typowe struktury zgodności. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Do Security Center dodano następujące zalecenia dotyczące wersji zapoznawczej w celu zwiększenia zakresu tego testu porównawczego.

Zalecenia dotyczące wersji zapoznawczej nie powodują złej kondycji zasobu i nie są uwzględniane w obliczeniach bezpiecznego wyniku. Skoryguj je wszędzie tam, gdzie to możliwe, aby po zakończeniu okresu korzystania z wersji zapoznawczej doczyniły się do oceny. Dowiedz się więcej o tym, jak odpowiedzieć na te zalecenia w temacie [Koryguj zalecenia w Azure Security Center](security-center-remediate-recommendations.md).

| Kontrola zabezpieczeń                     | Nowe rekomendacje                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Szyfruj dane podczas przesyłania              | -Wymuszaj połączenie SSL powinno być włączone dla serwerów bazy danych PostgreSQL<br>-Wymuś połączenie SSL dla serwerów baz danych MySQL<br>-TLS należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>-TLS należy zaktualizować do najnowszej wersji aplikacji funkcji<br>-TLS należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>-FTPS powinna być wymagana w aplikacji interfejsu API<br>-FTPS powinna być wymagana w aplikacji funkcji<br>-FTPS powinna być wymagana w aplikacji sieci Web                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Zarządzanie dostępem i uprawnieniami        | -Aplikacje sieci Web powinny zażądać certyfikatu SSL dla wszystkich żądań przychodzących<br>— Tożsamość zarządzana powinna być używana w aplikacji interfejsu API<br>— Tożsamość zarządzana powinna być używana w aplikacji funkcji<br>— Tożsamość zarządzana powinna być używana w aplikacji sieci Web                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Ogranicz nieautoryzowany dostęp do sieci | -Prywatny punkt końcowy powinien być włączony dla serwerów PostgreSQL<br>-Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB<br>-Prywatny punkt końcowy powinien być włączony dla serwerów MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Włącz inspekcję i rejestrowanie          | -Należy włączyć dzienniki diagnostyczne w App Services                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń    | -Azure Backup powinna być włączona dla maszyn wirtualnych<br>-Geograficznie nadmiarowe kopie zapasowe powinny być włączone dla Azure Database for MariaDB<br>-Geograficznie nadmiarowe kopie zapasowe powinny być włączone dla Azure Database for MySQL<br>-Geograficznie nadmiarowe kopie zapasowe powinny być włączone dla Azure Database for PostgreSQL<br>-PHP należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>-PHP należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>-Java należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>— Środowisko Java należy zaktualizować do najnowszej wersji aplikacji funkcji<br>-Java należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>-Python należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>-Python należy zaktualizować do najnowszej wersji aplikacji funkcji<br>-Python należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>-Inspekcja przechowywania dla serwerów SQL powinna wynosić co najmniej 90 dni |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Powiązane linki:

- [Dowiedz się więcej o teście zabezpieczeń Azure](../security/benchmarks/introduction.md)
- [Dowiedz się więcej o usłudze Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Dowiedz się więcej na temat aplikacji funkcji platformy Azure](../azure-functions/functions-overview.md)
- [Dowiedz się więcej o usłudze Azure Web Apps](../app-service/overview.md)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 dodano do Security Center pulpitu nawigacyjnego zgodności z przepisami

Standard NIST SP 800-171 R2 jest teraz dostępny jako wbudowana inicjatywa do używania z pulpitem nawigacyjnym zgodności z przepisami Azure Security Center. Mapowania dla formantów są opisane [szczegółowo w artykule NIST SP 800-171 R2 zgodność z przepisami](../governance/policy/samples/nist-sp-800-171-r2.md). 

Aby zastosować Standard do subskrypcji i stale monitorować stan zgodności, Skorzystaj z instrukcji w temacie [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 na pulpicie nawigacyjnym zgodności z przepisami Security Center":::

Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Lista rekomendacji zawiera teraz filtry

Teraz można filtrować listę zaleceń dotyczących zabezpieczeń zgodnie z zakresem kryteriów. W poniższym przykładzie lista zaleceń została przefiltrowana w celu wyświetlenia zaleceń, które:

- są **ogólnie dostępne** (to nie jest wersja zapoznawcza)
- dotyczy **kont magazynu**
- Pomoc techniczna dotycząca **szybkiego rozwiązywania problemów**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtry listy rekomendacji":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Ulepszone i rozszerzone środowisko aprowizacji

Funkcja autoaprowizacji pozwala zmniejszyć obciążenie związane z zarządzaniem przez zainstalowanie wymaganych rozszerzeń na nowych maszynach wirtualnych platformy Azure, które mogą korzystać z ochrony Security Center. 

W miarę zwiększania Azure Security Center, opracowano więcej rozszerzeń i Security Center może monitorować większą listę typów zasobów. Narzędzia do samoobsługowego udostępniania zostały teraz rozwinięte w celu obsługi innych rozszerzeń i typów zasobów, wykorzystując możliwości Azure Policy.

Teraz można skonfigurować funkcję samoobsługowego udostępniania:

- Agent usługi Log Analytics
- Nowy Dodatek Azure Policy dla Kubernetes
- Nowy Agent zależności firmy Microsoft

Dowiedz się więcej w temacie [Inicjowanie obsługi administracyjnej agentów i rozszerzeń z Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Ocena zabezpieczeń jest teraz dostępna w przypadku eksportu ciągłego (wersja zapoznawcza)

Za pomocą ciągłego eksportowania zabezpieczeń można przesyłać strumieniowo zmiany do oceny w czasie rzeczywistym do usługi Azure Event Hubs lub do Log Analytics obszaru roboczego. Użyj tej funkcji, aby:

- Śledź swój Bezpieczny wynik w czasie dzięki dynamicznym raportom
- Eksportuj dane dotyczące danych oceny do platformy Azure (lub innych SIEM)
- Integruj te dane z dowolnymi procesami, które mogą już być używane do monitorowania bezpiecznego wyniku w organizacji

Dowiedz się więcej o tym, jak [ciągle eksportować Security Center dane](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Zalecenie "aktualizacje systemu powinny być zainstalowane na Twoich komputerach" obejmuje teraz podzalecenia

**Aktualizacje systemu powinny być zainstalowane na** zalecanych komputerach. Nowa wersja obejmuje podzalecenia dla każdej brakującej aktualizacji i oferuje następujące udoskonalenia:

- Ponownie zaprojektowane środowisko na Azure Security Center stronach Azure Portal. **Na swoich maszynach należy zainstalować na** stronie Szczegóły rekomendacji zawierającej listę wyników, jak pokazano poniżej. Po wybraniu pojedynczego wyszukiwania zostanie otwarte okienko Szczegóły z linkiem do informacji o korygowaniu oraz z listą zasobów, których to dotyczy.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Otwieranie jednego z podstanowień w portalu w celu uzyskania zaktualizowanego zalecenia":::

- Wzbogacone dane dla zalecenia z grafu zasobów platformy Azure (ARG). ARG to usługa platformy Azure, która została zaprojektowana w celu zapewnienia wydajnej eksploracji zasobów. Za pomocą argumentu ARG można wykonywać zapytania na dużą skalę w ramach danego zestawu subskrypcji, dzięki czemu można efektywnie zarządzać środowiskiem. 

    Aby uzyskać Azure Security Center, można użyć ARG i [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) do wykonywania zapytań o szeroki zakres danych stan zabezpieczeń.

    Wcześniej, jeśli zbadasz to zalecenie w ARG, jedynymi dostępnymi informacjami było to, że zalecenie należy skorygować na komputerze. Poniższe zapytanie o wersję rozszerzoną zwróci wszystkie brakujące aktualizacje systemu pogrupowane według maszyn.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Na stronie Zarządzanie zasadami w Azure Portal teraz jest wyświetlany stan domyślnych przypisań zasad

Teraz możesz sprawdzić, czy subskrypcje mają przypisane domyślne zasady Security Center na stronie **zasady zabezpieczeń** Security Center w Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Strona Zarządzanie zasadami Azure Security Center wyświetlania domyślnych przypisań zasad":::

## <a name="october-2020"></a>Październik 2020 r.

Aktualizacje w październiku obejmują:
- [Ocena luk w zabezpieczeniach dla maszyn lokalnych i wielochmurowych (wersja zapoznawcza)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Dodane zalecenie dotyczące zapory platformy Azure (wersja zapoznawcza)](#azure-firewall-recommendation-added-preview)
- [Autoryzowane zakresy adresów IP powinny być zdefiniowane w zaleceniu usług Kubernetes Services zaktualizowanym z opcją szybkie rozwiązanie.](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Pulpit nawigacyjny zgodności z przepisami zawiera teraz opcję usuwania standardów](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
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

[Dowiedz się więcej o serwerach z obsługą usługi Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Dodane zalecenie dotyczące zapory platformy Azure (wersja zapoznawcza)

Dodano nowe zalecenie umożliwiające ochronę wszystkich sieci wirtualnych za pomocą zapory platformy Azure.

Zalecenie, **sieci wirtualne powinny być chronione przez zaporę platformy Azure** , zaleca się ograniczyć dostęp do sieci wirtualnych i uniemożliwić potencjalne zagrożenia za pomocą zapory platformy Azure.

Dowiedz się więcej o [zaporze platformy Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Autoryzowane zakresy adresów IP powinny być zdefiniowane w zaleceniu usług Kubernetes Services zaktualizowanym z opcją szybkie rozwiązanie.

**Zatwierdzone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes** teraz mają opcję Szybkie rozwiązanie.

Aby uzyskać więcej informacji na temat tego zalecenia i wszystkie inne zalecenia dotyczące Security Center, zobacz [zalecenia dotyczące zabezpieczeń — Podręcznik referencyjny](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Autoryzowane zakresy adresów IP należy zdefiniować w rekomendacji usług Kubernetes Services z opcją szybkie rozwiązanie":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Pulpit nawigacyjny zgodności z przepisami zawiera teraz opcję usuwania standardów

Pulpit nawigacyjny zgodności z przepisami Security Center zapewnia wgląd w stan zgodności w zależności od tego, jak spełniasz określone wymagania kontroli zgodności.

Pulpit nawigacyjny zawiera domyślny zestaw standardów prawnych. Jeśli którekolwiek z podanych standardów nie są odpowiednie dla Twojej organizacji, jest to prosty proces usuwania ich z interfejsu użytkownika dla subskrypcji. Standardy można usuwać tylko na poziomie *subskrypcji* . to nie jest zakres grupy zarządzania.

Dowiedz się więcej w temacie [usuwanie standardu z pulpitu nawigacyjnego](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabela Microsoft. Security/securityStatuses została usunięta z grafu zasobów platformy Azure (ARG)

Azure Resource Graph to usługa platformy Azure, która została zaprojektowana w celu zapewnienia wydajnej eksploracji zasobów dzięki możliwości wykonywania zapytań na dużą skalę w ramach danego zestawu subskrypcji, dzięki czemu możesz efektywnie zarządzać środowiskiem. 

Aby uzyskać Azure Security Center, można użyć ARG i [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) do wykonywania zapytań o szeroki zakres danych stan zabezpieczeń. Na przykład:

- Wykorzystanie spisu zasobów (ARG)
- Zarejestrowano przykładowe zapytanie ARG dotyczące [identyfikowania kont bez włączonej usługi uwierzytelniania wieloskładnikowego (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

W ciągu ARG istnieją tabele danych, które mogą być używane w zapytaniach.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Eksplorator usługi Azure Resource Graph i dostępne tabele":::

> [!TIP]
> W dokumentacji ARG znajdują się wszystkie dostępne tabele w [tabeli grafów zasobów platformy Azure i odwołania do typu zasobu](../governance/resource-graph/reference/supported-tables-resources.md).

W ramach tej aktualizacji tabela **Microsoft. Security/securityStatuses** została usunięta. Interfejs API securityStatuses jest nadal dostępny.

Wymiana danych może być używana przez tabelę Microsoft. Security/Assessments.

Główna różnica między elementami Microsoft. Security/securityStatuses i Microsoft. Security/Assessments polega na tym, że podczas pierwszego wyświetlania agregacji ocen w sekundach są przechowywane pojedyncze rekordy.

Na przykład firma Microsoft. Security/securityStatuses zwróci wynik z tablicą dwóch policyAssessments:

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
- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)
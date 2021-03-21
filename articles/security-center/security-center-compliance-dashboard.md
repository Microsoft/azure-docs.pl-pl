---
title: 'Samouczek: sprawdzanie zgodności z przepisami — Azure Security Center'
description: 'Samouczek: informacje na temat ulepszania zgodności z przepisami przy użyciu Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370272"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: poprawa zgodności z przepisami

Azure Security Center pomaga usprawnić proces spełnienia wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami**. 

Security Center stale ocenia środowisko chmury hybrydowej, aby analizować czynniki ryzyka zgodnie z kontrolkami i najlepszymi rozwiązaniami dotyczącymi standardów zastosowanych do subskrypcji. Pulpit nawigacyjny odzwierciedla stan zgodności z tymi standardami. 

Włączenie Security Center w ramach subskrypcji platformy Azure powoduje automatyczne przypisanie [testu wydajności platformy Azure](../security/benchmarks/introduction.md) do tej subskrypcji. Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

Pulpit nawigacyjny zgodności z przepisami przedstawia stan wszystkich ocen w danym środowisku zgodnie z wybranymi standardami i regulacjami. Zgodnie z zaleceniami i zmniejszeniem czynników ryzyka w Twoim środowisku stan zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Oceń zgodność z przepisami przy użyciu pulpitu nawigacyjnego zgodności z przepisami
> * Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami
> * Skonfiguruj alerty dotyczące zmian w stan zgodności
> * Eksportowanie danych zgodności jako ciągłego strumienia i w postaci migawek tygodniowych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby przekroczyć funkcje omówione w tym samouczku:

- [Usługa Azure Defender](azure-defender.md) musi być włączona. Usługę Azure Defender można wypróbować bezpłatnie przez 30 dni.
- Użytkownik musi być zalogowany przy użyciu konta, które ma dostęp do czytnika do danych zgodności zasad (**czytnik zabezpieczeń** jest niewystarczający). Rola **globalnego czytnika** dla subskrypcji będzie działała. Należy mieć co najmniej przypisane role **współautor zasad zasobów** i **administratora zabezpieczeń** .

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Pulpit nawigacyjny zgodności z przepisami zawiera wybrane standardy zgodności wraz ze wszystkimi wymaganiami, w których obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Stan tych ocen odzwierciedla zgodność ze standardem.

Korzystając z pulpitu nawigacyjnego zgodność z przepisami, można skoncentrować się na lukach w zabezpieczeniach zgodnie z wybranymi standardami i regulacjami. Ten skoncentrowany widok umożliwia również ciągłe monitorowanie zgodności w środowiskach w chmurze dynamicznej i hybrydowej.

1. Z menu Security Center wybierz pozycję **zgodność z przepisami**.

    W górnej części ekranu znajduje się pulpit nawigacyjny z omówieniem stanu zgodności z zestawem obsługiwanych regulacji zgodności. Zobaczysz ogólny wynik zgodności oraz liczbę ocen zakończonych niepowodzeniem w porównaniu z poszczególnymi standardami.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Wybierz kartę dla normy zgodności, która jest odpowiednia dla Ciebie (1). Zobaczysz, które subskrypcje są stosowane w warstwie Standardowa (2) i listę wszystkich kontrolek dla tego standardu (3). W przypadku odpowiednich kontrolek można wyświetlić szczegóły dotyczące przekazywania i niepowodzenia ocen skojarzonych z tą kontrolką (4) oraz liczbę zasobów, których to dotyczy (5). Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Sprawdź ich wymagania i Oceń je w Twoim środowisku. Niektóre z nich mogą być zależne od procesu, a nie techniczne.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Szczegółowe informacje o zgodności z określonym standardem":::

1. Aby wygenerować raport PDF z podsumowaniem bieżącego stanu zgodności dla konkretnego standardu, wybierz pozycję **Pobierz raport**.

    Raport zawiera podsumowanie stanu zgodności dla wybranego standardu na podstawie danych oceny Security Center. Raport jest zorganizowany zgodnie z kontrolkami tego konkretnego standardu. Raport może być współużytkowany z odpowiednimi uczestnikami projektu i może dostarczyć dowód do wewnętrznych i zewnętrznych audytorów.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Pobierz raport zgodności":::

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Korzystając z informacji na pulpicie nawigacyjnym zgodności z przepisami, Popraw stan zgodności, rozwiązując zalecenia bezpośrednio na pulpicie nawigacyjnym.

1.  Wybierz dowolne z niezakończonych ocen, które są wyświetlane na pulpicie nawigacyjnym, aby wyświetlić szczegóły tego zalecenia. Każde zalecenie zawiera zestaw kroków zaradczych, aby rozwiązać problem.

1.  Wybierz konkretny zasób, aby wyświetlić więcej szczegółów i rozwiązać zalecenia dotyczące tego zasobu. <br>Na przykład w standardzie **Azure CIS 1.1.0** wybierz opcję **szyfrowanie dysku zalecenia należy zastosować na maszynach wirtualnych**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Wybór zalecenia z standardowego potencjalni klienci bezpośrednio do strony szczegółów rekomendacji":::

1. W tym przykładzie po wybraniu opcji **Wykonaj akcję** z poziomu strony Szczegóły rekomendacji przytrzesz do stron maszyny wirtualnej platformy Azure w Azure Portal, w którym można włączyć szyfrowanie z poziomu karty **zabezpieczenia** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Przycisk podejmowania akcji na stronie Szczegóły rekomendacji prowadzi do opcji korygowania":::

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

1.  Po podjęcie działań w celu rozwiązania zaleceń zobaczysz wynik w raporcie pulpitu nawigacyjnego zgodność, ponieważ wynik oceny zgodności zostanie ulepszony.

    > [!NOTE]
    > Oceny są wykonywane co około 12 godzin, dzięki czemu zobaczysz wpływ na dane zgodności dopiero po następnym uruchomieniu odpowiedniej oceny.


## <a name="export-your-compliance-status-data"></a>Eksportowanie danych stanu zgodności

Jeśli chcesz śledzić swój stan zgodności z innymi narzędziami do monitorowania w środowisku, Security Center obejmuje mechanizm eksportu, aby uczynić ten prosty. Skonfiguruj **eksport ciągły** , aby wysyłać wybrane dane do centrum zdarzeń platformy Azure lub do obszaru roboczego log Analytics.

Użyj danych eksportu ciągłego w centrum zdarzeń platformy Azure lub w obszarze roboczym Log Analytics:

- Eksportuj wszystkie dane zgodności z przepisami w **strumieniu ciągłym**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Ciągłe eksportowanie strumienia danych zgodności z przepisami" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Eksportuj **cotygodniowe migawki** danych zgodności z przepisami:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Ciągle Eksportuj cotygodniową migawkę danych zgodności z przepisami" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Możesz również wyeksportować **raport PDF/CSV dotyczący** danych zgodności bezpośrednio z pulpitu nawigacyjnego zgodności z przepisami:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Eksportuj dane zgodności z przepisami jako raport PDF lub CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Dowiedz się więcej na temat [ciągłego eksportowania danych Security Center](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Uruchamianie automatyzacji przepływu pracy w przypadku zmiany zgodności

Funkcja automatyzacji przepływu pracy w Security Center może być wyzwalana Logic Apps każdej zmianie stanu oceny zgodności z przepisami.

Na przykład możesz chcieć, aby Security Center e-mail określonego użytkownika w przypadku niepowodzenia oceny zgodności. Najpierw należy utworzyć aplikację logiki (przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md)), a następnie skonfigurować wyzwalacz w nowej automatyzacji przepływu pracy, jak wyjaśniono w [automatyzowaniu odpowiedzi na wyzwalacze Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Używanie zmian do oceny zgodności z przepisami w celu wyzwalania automatyzacji przepływu pracy" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Często zadawane pytania — pulpit nawigacyjny zgodności z przepisami

- [Jakie standardy są obsługiwane na pulpicie nawigacyjnym zgodności?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Dlaczego niektóre kontrolki są wyszarzone?](#why-do-some-controls-appear-grayed-out)
- [Jak usunąć wbudowaną Standard, na przykład PCI-DSS, ISO 27001 lub SOC2 TSP z pulpitu nawigacyjnego?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Zostały wykonane sugerowane zmiany na podstawie zalecenia, ale nie są one odzwierciedlone na pulpicie nawigacyjnym](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Jakie uprawnienia muszę uzyskać dostęp do pulpitu nawigacyjnego zgodności?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Pulpit nawigacyjny zgodności z przepisami nie jest ładowany dla mnie](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Jak mogę wyświetlić raport o przekazywaniu i nieprawidłowych kontrolkach na Standard na pulpicie nawigacyjnym?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Jak mogę pobrać raport z danymi zgodności w formacie innym niż PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Jak można utworzyć wyjątki dla niektórych zasad na pulpicie nawigacyjnym zgodności z przepisami](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Jakie plany lub licencje usługi Azure Defender muszę używać pulpitu nawigacyjnego zgodności z przepisami?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Jakie standardy są obsługiwane na pulpicie nawigacyjnym zgodności?
Domyślnie pulpit nawigacyjny zgodności z przepisami zawiera wskaźnik wydajności usługi Azure Security. Test porównawczy zabezpieczeń platformy Azure to opracowane przez firmę Microsoft, specyficzne dla platformy Azure wskazówki dotyczące zabezpieczeń i najlepsze rozwiązania w zakresie zgodności w oparciu o typowe struktury zgodności. Dowiedz się więcej na temat [wprowadzenia do usługi Azure Security test](../security/benchmarks/introduction.md).

Aby śledzić zgodność z innymi standardami, musisz jawnie dodać je do pulpitu nawigacyjnego.
 
Możesz dodawać standardy, takie jak Azure CIS 1.1.0 (NOWOŚĆ), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-V2020, Zjednoczone Królestwo brytyjskie i Zjednoczone Królestwo NHS, HIPAA HITRUST, Kanada Federal PBMM, ISO 27001, SOC2-TSP i PCI-DSS 3.2.1.  
 
Do pulpitu nawigacyjnego zostaną dodane więcej standardów i uwzględniono je na stronie [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Dlaczego niektóre kontrolki są wyszarzone?
Dla każdej normy zgodności na pulpicie nawigacyjnym znajduje się lista kontrolek standardowych. W przypadku odpowiednich kontrolek można wyświetlić szczegóły dotyczące przekazywania i niepowodzenia ocen.

Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Niektóre mogą być procedurami lub procesami i w związku z tym nie można ich zweryfikować przez Security Center. Niektóre nie mają jeszcze wdrożonych automatycznych zasad ani ocen, ale będą obowiązywać w przyszłości. Niektóre formanty mogą być odpowiedzialne za platformę, zgodnie z opisem we [wspólnej odpowiedzialności w chmurze](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Jak usunąć wbudowaną Standard, na przykład PCI-DSS, ISO 27001 lub SOC2 TSP z pulpitu nawigacyjnego? 
Aby dostosować pulpit nawigacyjny zgodności z przepisami i skoncentrować się tylko na standardach, które mają zastosowanie do Ciebie, możesz usunąć dowolne z wyświetlanych standardów, które nie są odpowiednie dla Twojej organizacji. Aby usunąć Standard, postępuj zgodnie z instrukcjami w temacie [usuwanie standardu z pulpitu nawigacyjnego](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Zostały wykonane sugerowane zmiany na podstawie zalecenia, ale nie są one odzwierciedlone na pulpicie nawigacyjnym
Po podjęcie działań w celu rozwiązania zaleceń należy zaczekać 12 godzin, aby zobaczyć zmiany w danych zgodności. Oceny są uruchamiane co około 12 godzin, więc efekt dotyczący danych zgodności będzie widoczny dopiero po uruchomieniu ocen.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Jakie uprawnienia muszę uzyskać dostęp do pulpitu nawigacyjnego zgodności?
Aby wyświetlić dane zgodności, musisz mieć co najmniej dostęp **czytelnika** do danych dotyczących zgodności zasad; dlatego czytnik zabezpieczeń nie wystarcza. Jeśli jesteś czytnikiem globalnym w ramach subskrypcji, który będzie zbyt mały.

Minimalny zestaw ról do uzyskiwania dostępu do pulpitu nawigacyjnego i zarządzania standardami to **współautor zasad zasobów** i **administrator zabezpieczeń**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Pulpit nawigacyjny zgodności z przepisami nie jest ładowany dla mnie
Aby korzystać z pulpitu nawigacyjnego zgodność z przepisami, Azure Security Center musi mieć włączoną usługę Azure Defender na poziomie subskrypcji. Jeśli pulpit nawigacyjny nie zostanie poprawnie załadowany, spróbuj wykonać następujące czynności:

1. Wyczyść pamięć podręczną przeglądarki.
1. Wypróbuj inną przeglądarkę.
1. Spróbuj otworzyć pulpit nawigacyjny z poziomu innej lokalizacji sieciowej.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Jak mogę wyświetlić raport o przekazywaniu i nieprawidłowych kontrolkach na Standard na pulpicie nawigacyjnym?
Na głównym pulpicie nawigacyjnym zobaczysz raport z przekazaniem i niepowodzeniem kontroli dla (1) najniższych standardów zgodności na pulpicie nawigacyjnym. Aby wyświetlić wszystkie Stany kontroli przechodzenia/niepowodzeniem, wybierz pozycję (2) **Pokaż wszystkie *x*** (gdzie x to liczba śledzonych standardów). Płaszczyzna kontekstowa wyświetla stan zgodności dla każdego z monitorowanych standardów.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sekcja podsumowania pulpitu nawigacyjnego zgodności z przepisami":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Jak mogę pobrać raport z danymi zgodności w formacie innym niż PDF?
Po wybraniu opcji **Pobierz raport** wybierz opcję standardowy i format (PDF lub CSV). Raport wynikający z tego raportu będzie odzwierciedlał bieżący zestaw subskrypcji, który został wybrany w filtrze portalu.

- Raport PDF zawiera podsumowanie stanu dla wybranego standardu
- Raport CSV zawiera szczegółowe wyniki dla każdego zasobu, który odnosi się do zasad skojarzonych z każdą kontrolką

Obecnie nie jest obsługiwane pobieranie raportu dla zasad niestandardowych; tylko dla dostarczonych standardów regulacyjnych.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Jak można utworzyć wyjątki dla niektórych zasad na pulpicie nawigacyjnym zgodności z przepisami
W przypadku zasad wbudowanych w Security Center i uwzględnionych w bezpiecznym wyniku można utworzyć wykluczenia dla jednego lub większej liczby zasobów bezpośrednio w portalu, jak wyjaśniono w temacie [zwalnianie zasobów i zaleceń z bezpiecznego wyniku](exempt-resource.md).

W przypadku innych zasad można utworzyć wykluczenie bezpośrednio w samej zasadzie, wykonując instrukcje zawarte w [Azure Policy strukturze wykluczeń](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Jakie plany lub licencje usługi Azure Defender muszę używać pulpitu nawigacyjnego zgodności z przepisami?
Jeśli masz jakiekolwiek pakiety usługi Azure Defender włączone dla dowolnego z typów zasobów platformy Azure, masz dostęp do pulpitu nawigacyjnego zgodności z przepisami, ze wszystkimi danymi w Security Center.





## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się korzystać z pulpitu nawigacyjnego zgodności z przepisami Security Center, aby:

> [!div class="checklist"]
> * Wyświetl i monitoruj stan zgodności dotyczące standardów i przepisów, które są dla Ciebie ważne.
> * Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny zgodności z przepisami może znacznie uprościć proces zgodności i znacząco skrócić czas wymagany do zbierania dowodów zgodności dla środowiska Azure, hybrydowego i wielochmurowego.

Aby dowiedzieć się więcej, zobacz następujące powiązane strony:

- [Dostosuj zestaw standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md) , aby dowiedzieć się, jak wybrać standardy, które są wyświetlane na pulpicie nawigacyjnym zgodności z przepisami. 
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak używać zaleceń w programie Security Center, aby pomóc w ochronie zasobów platformy Azure.
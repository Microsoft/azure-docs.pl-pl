---
title: 'Samouczek: sprawdzanie zgodności z przepisami — Azure Security Center'
description: 'Samouczek: dowiedz się, jak poprawić zgodność z przepisami przy użyciu Azure Security Center.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c8ac9079321e47a1e6d9b8689be46bf55bdd4243
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834619"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: poprawa zgodności z przepisami

Azure Security Center usprawnia proces spełniania wymagań dotyczących zgodności z przepisami przy użyciu pulpitu **nawigacyjnego zgodności z przepisami.** 

Security Center stale ocenia środowisko chmury hybrydowej w celu analizowania czynników ryzyka zgodnie z kontrolami i najlepszymi rozwiązaniami w standardach stosowanych do subskrypcji. Pulpit nawigacyjny odzwierciedla stan zgodności z tymi standardami. 

Po włączeniu usługi Security Center subskrypcji platformy Azure test porównawczy zabezpieczeń platformy [Azure](https://docs.microsoft.com/security/benchmark/azure/introduction) jest automatycznie przypisywany do tej subskrypcji. Ten szeroko stosowany test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze.

Pulpit nawigacyjny zgodności z przepisami pokazuje stan wszystkich ocen w środowisku dla wybranych standardów i przepisów. W związku z stosowaniem się do zaleceń i zmniejszeniem czynników ryzyka w środowisku zwiększa się poziom zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Ocena zgodności z przepisami przy użyciu pulpitu nawigacyjnego zgodności z przepisami
> * Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami
> * Konfigurowanie alertów dotyczących zmian w stanie zgodności
> * Eksportowanie danych zgodności jako strumienia ciągłego i migawek tygodniowych

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejść przez funkcje uwzględnione w tym samouczku:

- [Azure Defender](azure-defender.md) musi być włączona. Możesz wypróbować Azure Defender bezpłatnie przez 30 dni.
- Musisz zalogować się przy użyciu konta, które ma dostęp czytelnika do danych zgodności zasad **(Czytelnik zabezpieczeń** jest niewystarczający). Rola **czytelnika globalnego** dla subskrypcji będzie działać. Musisz mieć przypisane role Współautor  zasad zasobów i **Administrator** zabezpieczeń.

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Pulpit nawigacyjny zgodności z przepisami przedstawia wybrane standardy zgodności wraz ze wszystkimi ich wymaganiami, w których obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Stan tych ocen odzwierciedla zgodność ze standardem.

Pulpit nawigacyjny zgodności z przepisami pomaga skupić uwagę na lukach w zgodności z wybranymi standardami i przepisami. Ten skoncentrowany widok umożliwia również ciągłe monitorowanie zgodności w czasie w chmurze dynamicznej i środowiskach hybrydowych.

1. Z Security Center wybierz pozycję Zgodność **z przepisami.**

    W górnej części ekranu znajduje się pulpit nawigacyjny z przeglądem stanu zgodności z zestawem obsługiwanych przepisów dotyczących zgodności. Zobaczysz ogólny wynik zgodności oraz liczbę ocen z wynikiem przekazywania i ocen niepowodzeniem skojarzonych z każdym standardem.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Wybierz kartę standardu zgodności odpowiedniego dla Ciebie (1). Zobaczysz, do których subskrypcji zastosowano standard (2) i listę wszystkich kontrolek dla tego standardu (3). W przypadku odpowiednich kontrolek można wyświetlić szczegóły ocen z przekazywaniem i niepowodzeniem skojarzonych z tą kontrolką (4) oraz liczbę zasobów, których to dotyczy (5). Niektóre kontrolki są wyszarowane. Te kontrolki nie mają żadnych Security Center skojarzonych z nimi ocen. Sprawdź ich wymagania i oceń je w Twoim środowisku. Niektóre z nich mogą być związane z procesem, a nie techniczne.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Eksplorowanie szczegółów zgodności z określonym standardem":::

1. Aby wygenerować raport PDF z podsumowaniem bieżącego stanu zgodności dla określonego standardu, wybierz **pozycję Pobierz raport**.

    Raport zawiera podsumowanie wysokiego poziomu stanu zgodności dla wybranego standardu na podstawie Security Center oceny. Raport jest zorganizowany zgodnie z kontrolkami danego standardu. Raport można udostępnić odpowiednim uczestnikom projektu i przedstawić dowód audytorom wewnętrznym i zewnętrznym.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Pobieranie raportu zgodności":::

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Korzystając z informacji na pulpicie nawigacyjnym zgodności z przepisami, zwiększ swój wskaźnik zgodności, rozwiązując zalecenia bezpośrednio na pulpicie nawigacyjnym.

1.  Wybierz dowolną ocenę, która nie jest wyświetlana na pulpicie nawigacyjnym, aby wyświetlić szczegóły zalecenia. Każde zalecenie zawiera zestaw czynności zaradczych w celu rozwiązania problemu.

1.  Wybierz konkretny zasób, aby wyświetlić więcej szczegółów i rozwiązać problem z zaleceniem dla tego zasobu. <br>Na przykład w **standardzie Azure CIS 1.1.0** wybierz zalecenie Szyfrowanie dysków **powinno być stosowane na maszynach wirtualnych.**

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Wybieranie rekomendacji ze standardowego potencjalnych klientów bezpośrednio do strony szczegółów zalecenia":::

1. W tym przykładzie  po wybraniu opcji Przejmij akcję na stronie szczegółów rekomendacji na stronach maszyny wirtualnej platformy Azure w witrynie Azure Portal, gdzie można włączyć szyfrowanie na **karcie** Zabezpieczenia:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Przycisk Podejmowania akcji na stronie szczegółów zalecenia prowadzi do opcji korygowania":::

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

1.  Po podjęciu akcji w celu rozwiązania zaleceń zobaczysz wynik w raporcie pulpitu nawigacyjnego zgodności, ponieważ wynik zgodności się poprawi.

    > [!NOTE]
    > Oceny są uruchamiane mniej więcej co 12 godzin, więc wpływ na dane zgodności będzie można zobaczyć dopiero po następnym uruchomieniu odpowiedniej oceny.


## <a name="export-your-compliance-status-data"></a>Eksportowanie danych stanu zgodności

Jeśli chcesz śledzić stan zgodności za pomocą innych narzędzi do monitorowania w swoim środowisku, Security Center mechanizm eksportu, aby było to proste. Konfigurowanie **eksportu ciągłego** w celu wysyłania wybranych danych do usługi Azure Event Hub lub obszaru roboczego usługi Log Analytics.

Użyj danych eksportu ciągłego do usługi Azure Event Hub lub obszaru roboczego usługi Log Analytics:

- Eksportuj wszystkie dane zgodności z przepisami w **strumieniu ciągłym:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Ciągłe eksportowanie strumienia danych zgodności z przepisami" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- **Wyeksportuj** cotygodniowe migawki danych zgodności z przepisami:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Ciągłe eksportowanie cotygodniowej migawki danych zgodności z przepisami" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Możesz również wyeksportować raport **PDF/CSV zawierający** dane zgodności bezpośrednio z pulpitu nawigacyjnego zgodności z przepisami:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Eksportowanie danych dotyczących zgodności z przepisami w formacie PDF lub CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Aby dowiedzieć się [więcej, zobacz ciągłe eksportowanie Security Center danych.](continuous-export.md)


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Uruchamianie automatyzacji przepływu pracy w przypadku zmian zgodności

Security Center automatyzacji przepływu pracy może wyzwalać Logic Apps każdej zmianie stanu jednej z ocen zgodności z przepisami.

Na przykład możesz chcieć wysłać wiadomość Security Center e-mail do określonego użytkownika w przypadku niepowodzenia oceny zgodności. Najpierw należy utworzyć aplikację logiki (przy użyciu usługi [Azure Logic Apps), a następnie](../logic-apps/logic-apps-overview.md)skonfigurować wyzwalacz w nowej automatyzacji przepływu pracy, jak wyjaśniono w tece Automatyzowanie odpowiedzi na Security Center [wyzwalaczy](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Używanie zmian do ocen zgodności z przepisami w celu wyzwolenia automatyzacji przepływu pracy" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Często zadawane pytania — pulpit nawigacyjny zgodności z przepisami

- [Jakie standardy są obsługiwane na pulpicie nawigacyjnym zgodności?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Dlaczego niektóre kontrolki są wyszarowane?](#why-do-some-controls-appear-grayed-out)
- [Jak usunąć z pulpitu nawigacyjnego wbudowany standard, taki jak PCI-DSS, ISO 27001 lub SOC2 TSP?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Sugerowane zmiany zostały wprowadzone na podstawie rekomendacji, ale nie są one odzwierciedlane na pulpicie nawigacyjnym](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Jakich uprawnień potrzebuję, aby uzyskać dostęp do pulpitu nawigacyjnego zgodności?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Pulpit nawigacyjny zgodności z przepisami nie jest ładowany](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Jak wyświetlić raport z przekazywaniem i niepowodzeniem kontrolek według standardu na pulpicie nawigacyjnym?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Jak pobrać raport z danymi zgodności w formacie innym niż PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Jak utworzyć wyjątki dla niektórych zasad na pulpicie nawigacyjnym zgodności z przepisami?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Jakie Azure Defender lub licencje są potrzebne do korzystania z pulpitu nawigacyjnego zgodności z przepisami?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Jak mogę, którego testu porównawczego lub standardu użyć?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Jakie standardy są obsługiwane na pulpicie nawigacyjnym zgodności?
Domyślnie pulpit nawigacyjny zgodności z przepisami pokazuje test porównawczy zabezpieczeń platformy Azure. Test porównawczy zabezpieczeń platformy Azure to wytyczne firmy Microsoft dotyczące najlepszych rozwiązań dotyczących zabezpieczeń i zgodności, które są specyficzne dla platformy Azure, oparte na wspólnych platformach zgodności. Dowiedz się więcej z wprowadzenia [do testu porównawczego zabezpieczeń platformy Azure.](../security/benchmarks/introduction.md)

Aby śledzić zgodność z innymi standardami, musisz jawnie dodać je do pulpitu nawigacyjnego.
 
Możesz dodać inne standardy, takie jak Azure CIS 1.3.0, NIST SP 800-53, NIST SP 800-171, SWIFT CSP CSCF-v2020, UK Official i UK NHS, HIPAA, Canada Federal PBMM, ISO 27001, SOC2-TSP i PCI-DSS 3.2.1.  

Więcej standardów zostanie dodanych do pulpitu nawigacyjnego i uwzględnionych w informacjach dotyczących dostosowywania zestawu standardów na pulpicie [nawigacyjnym zgodności z przepisami.](update-regulatory-compliance-packages.md)

### <a name="why-do-some-controls-appear-grayed-out"></a>Dlaczego niektóre kontrolki są wyszarowane?
Dla każdego standardu zgodności na pulpicie nawigacyjnym znajduje się lista kontrolek standardu. W przypadku odpowiednich kontrolek można wyświetlić szczegóły ocen z przekazywaniem i niepowodzeniem.

Niektóre kontrolki są wyszarowane. Te kontrolki nie mają żadnych Security Center skojarzonych z nimi ocen. Niektóre mogą być związane z procedurą lub procesem, dlatego nie mogą być weryfikowane przez Security Center. Niektóre nie mają jeszcze zaimplementowanych żadnych zautomatyzowanych zasad ani ocen, ale będą mieć w przyszłości. Niektóre kontrolki mogą być odpowiedzialne za platformę, jak wyjaśniono w tesłudze [Shared responsibility in the cloud (Wspólna odpowiedzialność w chmurze).](../security/fundamentals/shared-responsibility.md) 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Jak usunąć z pulpitu nawigacyjnego wbudowany standard, taki jak PCI-DSS, ISO 27001 lub SOC2 TSP? 
Aby dostosować pulpit nawigacyjny zgodności z przepisami i skoncentrować się tylko na odpowiednich dla Ciebie standardach, możesz usunąć wszystkie wyświetlane standardy prawne, które nie są istotne dla Twojej organizacji. Aby usunąć standard, wykonaj instrukcje z [tematu Usuwanie standardu z pulpitu nawigacyjnego.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Sugerowane zmiany zostały wprowadzone na podstawie rekomendacji, ale nie są one odzwierciedlane na pulpicie nawigacyjnym
Po podjęciu akcji w celu rozwiązania zaleceń poczekaj 12 godzin, aby zobaczyć zmiany w danych zgodności. Oceny są uruchamiane co około 12 godzin, więc wpływ na dane zgodności będzie można zobaczyć dopiero po uruchomieniu ocen.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Jakich uprawnień potrzebuję, aby uzyskać dostęp do pulpitu nawigacyjnego zgodności?
Aby wyświetlić dane zgodności, musisz mieć co **najmniej** dostęp czytelnika do danych zgodności zasad; Dlatego sam czytelnik zabezpieczeń nie wystarczy. Jeśli jesteś czytelnikiem globalnym subskrypcji, to też wystarczy.

Minimalny zestaw ról do uzyskiwania dostępu do pulpitu nawigacyjnego i zarządzania standardami to Współautor **zasad zasobów** i **Administrator zabezpieczeń.**


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Pulpit nawigacyjny zgodności z przepisami nie jest ładowany
Aby można było korzystać z pulpitu nawigacyjnego zgodności z przepisami, Azure Security Center musi Azure Defender na poziomie subskrypcji. Jeśli pulpit nawigacyjny nie jest ładowany poprawnie, spróbuj wykonać następujące czynności:

1. Wyczyść pamięć podręczną przeglądarki.
1. Wypróbuj inną przeglądarkę.
1. Spróbuj otworzyć pulpit nawigacyjny z innej lokalizacji sieciowej.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Jak wyświetlić raport z przekazywaniem i niepowodzeniem kontrolek według standardu na pulpicie nawigacyjnym?
Na głównym pulpicie nawigacyjnym można zobaczyć raport z przekazywaniem i niepowodzeniem kontrolek dla (1) "4 najniższych" standardów zgodności na pulpicie nawigacyjnym. Aby wyświetlić stan wszystkich kontrolek z przekazywaniem/niepowodzeniem, wybierz pozycję (2) Pokaż wszystkie x (gdzie ***x*** to liczba śledznych standardów). Płaszczyzna kontekstu wyświetla stan zgodności dla każdego śledzone standardy.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sekcja podsumowania pulpitu nawigacyjnego zgodności z przepisami":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Jak pobrać raport z danymi zgodności w formacie innym niż PDF?
Po wybraniu **opcji Pobierz raport** wybierz standard i format (PDF lub CSV). Wynikowy raport będzie odzwierciedlać bieżący zestaw subskrypcji wybranych w filtrze portalu.

- Raport PDF zawiera podsumowanie stanu wybranego standardu
- Raport CSV zawiera szczegółowe wyniki dla każdego zasobu, ponieważ odnosi się do zasad skojarzonych z każdą kontrolką

Obecnie nie jest dostępna obsługa pobierania raportu dla zasad niestandardowych. tylko dla dostarczonych standardów prawnych.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Jak utworzyć wyjątki dla niektórych zasad na pulpicie nawigacyjnym zgodności z przepisami?
W przypadku zasad wbudowanych w Security Center i uwzględnionych w bezpiecznym wyniku można utworzyć wyjątki dla jednego lub większej liczby zasobów bezpośrednio w portalu, jak wyjaśniono w tece Wykluczanie zasobów i zaleceń z bezpiecznego [wyniku.](exempt-resource.md)

W przypadku innych zasad można utworzyć wyłączenie bezpośrednio w zasadach, zgodnie z instrukcjami w Azure Policy [struktury wyieńcj.](../governance/policy/concepts/exemption-structure.md)


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Jakie Azure Defender lub licencje są potrzebne do korzystania z pulpitu nawigacyjnego zgodności z przepisami?
Jeśli dowolny z pakietów usługi Azure Defender jest włączony dla dowolnego typu zasobów platformy Azure, masz dostęp do pulpitu nawigacyjnego zgodności z przepisami wraz ze wszystkimi jego danymi w Security Center.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Jak mogę, którego testu porównawczego lub standardu użyć?
[Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) (ASB) to kanoniczny zestaw zaleceń dotyczących zabezpieczeń i najlepszych rozwiązań zdefiniowanych przez firmę Microsoft, zgodny z wspólnymi platformami kontroli zgodności, w tym [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) i [NIST SP 800-53.](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) Usługa ASB jest bardzo kompleksowym testem porównawczym i została zaprojektowana w celu rekomendacji najbardziej aktualnych możliwości zabezpieczeń szerokiego zakresu usług platformy Azure. Zalecamy korzystanie z usługi ASB klientom, którzy chcą zmaksymalizować stan zabezpieczeń i mieć możliwość dostosowania ich stanu zgodności do standardów branżowych.

Test [porównawczy CIS](https://www.cisecurity.org/benchmark/azure/) jest autorstwa niezależnej jednostki — Center for Internet Security (CIS) — i zawiera zalecenia dotyczące podzbioru podstawowych usług platformy Azure. Pracujemy z CIS, aby upewnić się, że ich rekomendacje są aktualne z najnowszymi ulepszeniami na platformie Azure, ale czasami wpadają w tyle i stają się nieaktualne. Niemniej jednak niektórzy klienci lubi używać tego celu, czyli oceny innej firmy z CIS jako początkowego i podstawowego planu bazowego zabezpieczeń.

Ponieważ wydaliśmy test porównawczy zabezpieczeń platformy Azure, wielu klientów zdecydowało się przeprowadzić migrację do niej jako zamiennik testów porównawczych CIS.


## <a name="next-steps"></a>Następne kroki

W tym samouczku o tym, jak używać pulpitu nawigacyjnego zgodności Security Center do celów:

> [!div class="checklist"]
> * Wyświetlanie i monitorowanie stanu zgodności w odniesieniu do ważnych dla Ciebie standardów i przepisów.
> * Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny zgodności z przepisami może znacznie uprościć proces zgodności i znacznie zmniejszyć czas wymagany do zebrania dowodów zgodności dla środowiska platformy Azure, środowiska hybrydowego i środowiska wielochmurowego.

Aby dowiedzieć się więcej, zobacz następujące powiązane strony:

- [Dostosowywanie zestawu standardów na pulpicie](update-regulatory-compliance-packages.md) nawigacyjnym zgodności z przepisami — dowiedz się, jak wybrać standardy wyświetlane na pulpicie nawigacyjnym zgodności z przepisami. 
- [Zarządzanie zaleceniami zabezpieczeń w Azure Security Center](security-center-recommendations.md) — dowiedz się, jak używać zaleceń w usłudze Security Center, aby chronić zasoby platformy Azure.
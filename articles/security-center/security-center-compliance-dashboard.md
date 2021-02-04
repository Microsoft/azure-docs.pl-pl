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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550933"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: poprawa zgodności z przepisami

Azure Security Center pomaga usprawnić proces spełnienia wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami**. 

Security Center stale ocenia środowisko chmury hybrydowej, aby analizować czynniki ryzyka zgodnie z kontrolkami i najlepszymi rozwiązaniami dotyczącymi standardów zastosowanych do subskrypcji. Pulpit nawigacyjny odzwierciedla stan zgodności z tymi standardami. 

Po włączeniu Security Center w ramach subskrypcji platformy Azure zostanie automatycznie przypisany [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/introduction.md). Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

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
- Musisz zalogować się przy użyciu konta, które ma dostęp do czytnika do danych o zgodności zasad (**czytnik zabezpieczeń** jest za mały). Rola **globalnego czytnika** dla subskrypcji będzie działała. Należy mieć co najmniej przypisane role **współautor zasad zasobów** i **administratora zabezpieczeń** .

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Pulpit nawigacyjny zgodności z przepisami zawiera wybrane standardy zgodności wraz ze wszystkimi wymaganiami, w których obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Stan tych ocen odzwierciedla zgodność ze standardem.

Korzystając z pulpitu nawigacyjnego zgodność z przepisami, można skoncentrować się na lukach w zabezpieczeniach zgodnie z wybranymi standardami i regulacjami. Ten skoncentrowany widok umożliwia również ciągłe monitorowanie zgodności w środowiskach w chmurze dynamicznej i hybrydowej.

1. Z menu Security Center wybierz pozycję **zgodność z przepisami**.

    W górnej części ekranu znajduje się pulpit nawigacyjny z omówieniem stanu zgodności z zestawem obsługiwanych regulacji zgodności. Zobaczysz ogólny wynik zgodności oraz liczbę ocen zakończonych niepowodzeniem w porównaniu z poszczególnymi standardami.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Wybierz kartę dla normy zgodności, która jest odpowiednia dla Ciebie (1). Zobaczysz, które subskrypcje są stosowane w warstwie Standardowa (2) i listę wszystkich kontrolek dla tego standardu (3). W przypadku odpowiednich kontrolek można wyświetlić szczegóły dotyczące przekazywania i niepowodzenia ocen skojarzonych z tą kontrolką (4), a także liczby zasobów, których to dotyczy (5). Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Sprawdź wymagania dotyczące tych i Oceń je we własnym środowisku. Niektóre z nich mogą być zależne od procesu, a nie techniczne.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Szczegółowe informacje o zgodności z określonym standardem":::

1. Aby wygenerować raport PDF z podsumowaniem bieżącego stanu zgodności dla konkretnego standardu, wybierz pozycję **Pobierz raport**.

    Raport zawiera podsumowanie stanu zgodności dla wybranego standardu w oparciu o Security Center oceny danych i jest zorganizowany zgodnie z kontrolkami tego konkretnego standardu. Raport może być współużytkowany z odpowiednimi uczestnikami projektu i może dostarczyć dowód do wewnętrznych i zewnętrznych audytorów.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Pobierz raport zgodności":::

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Korzystając z informacji na pulpicie nawigacyjnym zgodności z przepisami, Popraw stan zgodności, rozwiązując zalecenia bezpośrednio na pulpicie nawigacyjnym.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

1.  Wybierz konkretny zasób, aby wyświetlić więcej szczegółów i rozwiązać zalecenia dotyczące tego zasobu. <br>Na przykład w standardzie **Azure CIS 1.1.0** wybierz opcję **szyfrowanie dysku zalecenia należy zastosować na maszynach wirtualnych**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Wybór zalecenia z standardowego potencjalni klienci bezpośrednio do strony szczegółów rekomendacji":::

1. W tym przykładzie po wybraniu opcji **Wykonaj akcję** z poziomu strony Szczegóły rekomendacji przytrzesz do stron maszyny wirtualnej platformy Azure w Azure Portal, w którym można włączyć szyfrowanie z poziomu karty **zabezpieczenia** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Przycisk podejmowania akcji na stronie Szczegóły rekomendacji prowadzi do opcji korygowania":::

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

1.  Po podjęcie działań w celu rozwiązania zaleceń zobaczysz wpływ na raport pulpitu nawigacyjnego zgodności, ponieważ Ocena zgodności poprawi się.

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

Na przykład możesz chcieć, aby Security Center e-mail określonego użytkownika w przypadku niepowodzenia oceny zgodności. Musisz najpierw utworzyć aplikację logiki (przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md)), a następnie skonfigurować wyzwalacz w nowej automatyzacji przepływu pracy, jak wyjaśniono w [automatyzowaniu odpowiedzi na wyzwalacze Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Używanie zmian do oceny zgodności z przepisami w celu wyzwalania automatyzacji przepływu pracy" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się korzystać z pulpitu nawigacyjnego zgodności z przepisami Security Center, aby:

- Wyświetl i monitoruj stan zgodności dotyczące standardów i przepisów, które są dla Ciebie ważne.
- Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny zgodności z przepisami może znacznie uprościć proces zgodności i znacząco skrócić czas wymagany do zbierania dowodów zgodności dla środowiska Azure, hybrydowego i wielochmurowego.

Aby dowiedzieć się więcej, zobacz następujące powiązane strony:

- [Dostosuj zestaw standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md) , aby dowiedzieć się, jak wybrać standardy, które są wyświetlane na pulpicie nawigacyjnym zgodności z przepisami. 
- [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak używać zaleceń w programie Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.
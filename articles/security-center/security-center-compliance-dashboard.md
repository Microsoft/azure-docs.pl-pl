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
ms.date: 01/28/2021
ms.author: memildin
ms.openlocfilehash: f8d92ff0835948637761d7d2a98ec95a1c6dfccd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944213"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: poprawa zgodności z przepisami

Azure Security Center pomaga usprawnić proces spełnienia wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami**. 

Security Center wykonuje ciągłe oceny środowiska chmury hybrydowej, aby analizować czynniki ryzyka zgodnie z kontrolkami i najlepszymi rozwiązaniami związanymi ze standardami stosowanymi w ramach subskrypcji. Pulpit nawigacyjny odzwierciedla stan zgodności z tymi standardami. 

Po włączeniu Security Center w ramach subskrypcji platformy Azure zostanie automatycznie przypisany [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/introduction.md). Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

Na pulpicie nawigacyjnym zgodności z przepisami można zobaczyć stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub rozporządzenia. Zgodnie z zaleceniami i zmniejszeniem czynników ryzyka w Twoim środowisku stan zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Oceń zgodność z przepisami przy użyciu pulpitu nawigacyjnego zgodności z przepisami
> * Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby przekroczyć funkcje omówione w tym samouczku:

- [Usługa Azure Defender](azure-defender.md) musi być włączona. Usługę Azure Defender można wypróbować bezpłatnie przez 30 dni.
- Musisz zalogować się przy użyciu konta, które ma dostęp do czytnika do danych o zgodności zasad (**czytnik zabezpieczeń** jest za mały). Rola **globalnego czytnika** dla subskrypcji będzie działała. Należy mieć co najmniej przypisane role **współautor zasad zasobów** i **administratora zabezpieczeń** .

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Pulpit nawigacyjny zgodności z przepisami zawiera wybrane standardy zgodności wraz ze wszystkimi wymaganiami, w których obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Stan tych ocen odzwierciedla zgodność ze standardem.

Korzystając z pulpitu nawigacyjnego zgodność z przepisami, można skoncentrować się na lukach w zabezpieczeniach w zakresie zgodności ze standardami i przepisami. Ten skoncentrowany widok umożliwia również ciągłe monitorowanie zgodności w środowiskach w chmurze dynamicznej i hybrydowej.

1. Z menu Security Center wybierz pozycję **zgodność z przepisami**.

    W górnej części ekranu znajduje się pulpit nawigacyjny z omówieniem stanu zgodności z zestawem obsługiwanych regulacji zgodności. Widoczna jest ogólna ocena zgodności oraz liczba ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z każdym standardem.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

1. Wybierz kartę dla normy zgodności, która jest odpowiednia dla Ciebie (1). Zobaczysz, które subskrypcje są stosowane w warstwie Standardowa (2) i listę wszystkich kontrolek dla tego standardu (3). W przypadku odpowiednich kontrolek można wyświetlić szczegóły dotyczące przekazywania i niepowodzenia ocen skojarzonych z tą kontrolką (4), a także liczby zasobów, których to dotyczy (5). Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Sprawdź wymagania dotyczące tych i Oceń je we własnym środowisku. Niektóre z nich mogą być zależne od procesu, a nie techniczne.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Szczegółowe informacje o zgodności z określonym standardem":::

1. Aby wygenerować raport PDF z podsumowaniem bieżącego stanu zgodności dla konkretnego standardu, wybierz pozycję **Pobierz raport**.

    Raport zawiera podsumowanie stanu zgodności dla wybranego standardu w oparciu o Security Center oceny danych i jest zorganizowany zgodnie z kontrolkami tego konkretnego standardu. Raport może być współużytkowany z odpowiednimi uczestnikami projektu i może dostarczyć dowód do wewnętrznych i zewnętrznych audytorów.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Pobierz raport zgodności":::

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Mając informacje na pulpicie nawigacyjnym zgodności z przepisami, można zwiększyć stan zgodności, rozwiązując zalecenia bezpośrednio na pulpicie nawigacyjnym.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

1.  Możesz wybrać określony zasób, aby wyświetlić więcej szczegółów i zastosować rekomendację dotyczącą tego zasobu. <br>Na przykład w standardzie **Azure CIS 1.1.0** można wybrać opcję szyfrowanie dysków zalecanych **na maszynach wirtualnych**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Wybór zalecenia z standardowego potencjalni klienci bezpośrednio do strony szczegółów rekomendacji":::

1. W tym przykładzie po wybraniu opcji **Wykonaj akcję** z poziomu strony Szczegóły rekomendacji przyjdziesz do stron usługi Azure Virtual Machines w Azure Portal, w której można otworzyć kartę **zabezpieczenia** i włączyć szyfrowanie:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Przycisk podejmowania akcji na stronie Szczegóły rekomendacji prowadzi do opcji korygowania":::

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

1.  Gdy zastosujesz się do rekomendacji, w raporcie pulpitu nawigacyjnego zgodności zostanie wyświetlony ich wpływ, ponieważ zwiększy się ocena zgodności.

    > [!NOTE]
    > Oceny są wykonywane co około 12 godzin, dzięki czemu zobaczysz wpływ na dane zgodności dopiero po następnym uruchomieniu odpowiedniej oceny.

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się korzystać z pulpitu nawigacyjnego zgodności z przepisami Security Center, aby:

-   Wyświetlanie i monitorowanie poziomu zgodności względem istotnych dla Ciebie standardów i przepisów.
-   Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny zgodności z przepisami może znacznie uprościć proces zgodności i znacząco skrócić czas wymagany do zbierania dowodów zgodności dla środowiska platformy Azure i hybrydowej.

Aby dowiedzieć się więcej, zobacz następujące artykuły pokrewne:

-   [Zaktualizuj dynamiczne pakiety zgodności na pulpicie nawigacyjnym zgodności z przepisami (wersja zapoznawcza)](update-regulatory-compliance-packages.md) — Dowiedz się więcej na temat tej funkcji w wersji zapoznawczej, która umożliwia zaktualizowanie standardów widocznych na pulpicie nawigacyjnym zgodności z przepisami w celu uzyskania nowych pakietów *dynamicznych* . Możesz również tę samą funkcję w wersji zapoznawczej, aby dodać nowe pakiety zgodności i monitorować swoją zgodność przy użyciu dodatkowych standardów. 
-   [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
-   [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak używać zaleceń w programie Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.
-   [Popraw swój Bezpieczny wynik w Azure Security Center](secure-score-security-controls.md) — Dowiedz się, jak określać priorytety luk w zabezpieczeniach i zalecenia dotyczące zabezpieczeń, aby zwiększyć bezpieczeństwo stan.

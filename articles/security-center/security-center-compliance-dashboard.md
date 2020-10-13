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
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: 3108dd32b9d7338e418a1d10684115ad7265702e
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951200"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: ulepszanie zgodności z przepisami

Azure Security Center pomaga usprawnić proces spełnienia wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami**. Ten pulpit nawigacyjny usługi Security Center zapewnia wgląd w poziom zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Security Center analizuje czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. Na pulpicie nawigacyjnym zgodności z przepisami można zobaczyć stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub rozporządzenia. Zgodnie z zaleceniami i zmniejszeniem czynników ryzyka w Twoim środowisku stan zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Oceń zgodność z przepisami przy użyciu pulpitu nawigacyjnego zgodności z przepisami
> * Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby przekroczyć funkcje omówione w tym samouczku, należy włączyć [usługę Azure Defender](azure-defender.md) . Usługę Azure Defender można wypróbować bezpłatnie przez 30 dni.

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Usługa Security Center w sposób ciągły ocenia konfigurację zasobów, aby identyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. Oceny są prezentowane w postaci rekomendacji, które koncentrują się na poprawie stanu zabezpieczeń. Na pulpicie nawigacyjnym zgodności z przepisami można wyświetlić zestaw standardów zgodności ze wszystkimi wymaganiami, w przypadku których obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Pozwala to sprawdzać poziom zgodności względem standardu w oparciu o stan tych ocen.

Widok pulpitu nawigacyjnego Zgodność z przepisami ułatwia skoncentrowanie uwagi na lukach w zgodności z ważnym dla Ciebie standardem lub przepisem. Ten skoncentrowany widok umożliwia także stałe monitorowanie oceny zgodności w miarę upływu czasu w chmurze dynamicznej i środowiskach hybrydowych.

>[!NOTE]
> Domyślnie Security Center obsługuje następujące standardy prawne: Azure CIS, PCI DSS 3,2, ISO 27001 i SOC TSP. 
>
> Funkcja [dynamiczne pakiety zgodności (wersja zapoznawcza)](update-regulatory-compliance-packages.md) pozwala uaktualnić standardy widoczne na pulpicie nawigacyjnym zgodności z przepisami do nowych pakietów *dynamicznych* . Możesz także użyć tej samej funkcji w wersji zapoznawczej, aby dodać nowe pakiety zgodności i monitorować swoją zgodność przy użyciu dodatkowych standardów. 

1. Z menu Security Center wybierz pozycję **zgodność z przepisami**. <br>
W górnej części ekranu zostanie wyświetlony pulpit nawigacyjny zawierający przegląd stanu zgodności z zestawem obsługiwanych przepisów dotyczących zgodności. Widoczna jest ogólna ocena zgodności oraz liczba ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z każdym standardem.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

1. Wybierz kartę dla normy zgodności, która jest odpowiednia dla Ciebie (1). Zobaczysz, które subskrypcje są stosowane w warstwie Standardowa (2) i listę wszystkich kontrolek dla tego standardu (3). W przypadku odpowiednich kontrolek można wyświetlić szczegóły dotyczące przekazywania i niepowodzenia ocen skojarzonych z tą kontrolką (4), a także liczby zasobów, których to dotyczy (5). Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Sprawdź wymagania dotyczące tych i Oceń je we własnym środowisku. Niektóre z nich mogą być powiązane z procesem, a nie techniczne.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

1. Aby wygenerować i pobrać raport PDF podsumowujący bieżący stan zgodności dla określonego standardu, kliknij przycisk **Pobierz raport**.

    Raport zawiera podsumowanie stanu zgodności dla wybranego standardu w oparciu o Security Center oceny danych i jest zorganizowany zgodnie z kontrolkami tego konkretnego standardu. Raport może być współużytkowany z odpowiednimi uczestnikami projektu i może służyć do zapewnienia dowodu dla wewnętrznych i zewnętrznych audytorów.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Mając informacje na pulpicie nawigacyjnym zgodności z przepisami, można zwiększyć stan zgodności, rozwiązując zalecenia bezpośrednio na pulpicie nawigacyjnym.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

1.  Możesz wybrać określony zasób, aby wyświetlić więcej szczegółów i zastosować rekomendację dotyczącą tego zasobu. <br>Na przykład w **standardzie usługi Azure CIS 1.1.0 (nowy)** można wybrać opcję **szyfrowanie dysków zalecanych na maszynach wirtualnych**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

1. W tym przykładzie po wybraniu opcji **Wykonaj akcję** z poziomu strony Szczegóły rekomendacji przyjdziesz do stron usługi Azure Virtual Machines w Azure Portal, w której można otworzyć kartę **zabezpieczenia** i włączyć szyfrowanie:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Pulpit nawigacyjny zgodności z przepisami":::

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

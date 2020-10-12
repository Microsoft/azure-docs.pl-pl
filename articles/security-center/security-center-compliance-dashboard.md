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
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 7f235f9255df49c096e224e1754ad2bbdf664423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895821"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: ulepszanie zgodności z przepisami
---

Azure Security Center pomaga usprawnić proces spełnienia wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami**. Ten pulpit nawigacyjny usługi Security Center zapewnia wgląd w poziom zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Security Center analizuje czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. Na pulpicie nawigacyjnym zgodności z przepisami można zobaczyć stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub rozporządzenia. Zgodnie z zaleceniami i zmniejszeniem czynników ryzyka w Twoim środowisku stan zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

-   Przeprowadzanie oceny zgodności z przepisami za pomocą pulpitu nawigacyjnego Zgodność z przepisami

-   Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby przekroczyć funkcje omówione w tym samouczku, należy włączyć [usługę Azure Defender](azure-defender.md) . Usługę Azure Defender można wypróbować bezpłatnie przez 30 dni.

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Usługa Security Center w sposób ciągły ocenia konfigurację zasobów, aby identyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. Oceny są prezentowane w postaci rekomendacji, które koncentrują się na poprawie stanu zabezpieczeń. Na pulpicie nawigacyjnym Zgodność z przepisami możesz wyświetlić zestaw standardów zgodności wraz ze wszystkimi wymaganiami. Obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Pozwala to sprawdzać poziom zgodności względem standardu w oparciu o stan tych ocen.

Widok pulpitu nawigacyjnego Zgodność z przepisami ułatwia skoncentrowanie uwagi na lukach w zgodności z ważnym dla Ciebie standardem lub przepisem. Ten skoncentrowany widok umożliwia także stałe monitorowanie oceny zgodności w miarę upływu czasu w chmurze dynamicznej i środowiskach hybrydowych.

>[!NOTE]
> Domyślnie Security Center obsługuje następujące standardy prawne: Azure CIS, PCI DSS 3,2, ISO 27001 i SOC TSP. 
>
> Funkcja [dynamiczne pakiety zgodności (wersja zapoznawcza)](update-regulatory-compliance-packages.md) pozwala uaktualnić standardy widoczne na pulpicie nawigacyjnym zgodności z przepisami do nowych pakietów *dynamicznych* . Możesz także użyć tej samej funkcji w wersji zapoznawczej, aby dodać nowe pakiety zgodności i monitorować swoją zgodność przy użyciu dodatkowych standardów. 

1.  W menu głównym Security Center w obszarze **zasady & zgodność** wybierz pozycję **zgodność z przepisami**. <br>
W górnej części ekranu zostanie wyświetlony pulpit nawigacyjny zawierający przegląd stanu zgodności z zestawem obsługiwanych przepisów dotyczących zgodności. Widoczna jest ogólna ocena zgodności oraz liczba ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z każdym standardem.

    ![Opis komputera — godny zaufania](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Wybierz kartę standardu zgodności, który Cię dotyczy. Zostanie wyświetlona lista wszystkich kontrolek dla tego standardu. W przypadku właściwych kontrolek możesz wyświetlić szczegóły ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z daną kontrolką. Niektóre kontrolki są wyszarzone. Te kontrolki nie mają skojarzonych z nimi ocen Security Center. Sprawdź wymagania dotyczące tych i Oceń je we własnym środowisku. Niektóre z nich mogą być powiązane z procesem, a nie techniczne.

    ![Karta zgodności](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Aby wygenerować i pobrać raport PDF podsumowujący bieżący stan zgodności dla określonego standardu, kliknij przycisk **Pobierz raport**.

    Raport zawiera podsumowanie stanu zgodności dla wybranego standardu w oparciu o Security Center oceny danych i jest zorganizowany zgodnie z kontrolkami tego konkretnego standardu. Raport może być współużytkowany z odpowiednimi uczestnikami projektu i może służyć do zapewnienia dowodu dla wewnętrznych i zewnętrznych audytorów.

    ![pobieranie](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Dzięki informacjom wyświetlanym na pulpicie nawigacyjnym Zgodność z przepisami możesz zwiększyć poziom zgodności, wprowadzając w życie te rekomendacje bezpośrednio z poziomu pulpitu nawigacyjnego.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

1.  Możesz wybrać określony zasób, aby wyświetlić więcej szczegółów i zastosować rekomendację dotyczącą tego zasobu. <br>Na przykład na karcie **Standard Azure CIS** możesz kliknąć rekomendację **Wymagaj bezpiecznego transferu na konto magazynu**.

    ![Wybieranie zalecenia * * Wymagaj bezpiecznego transferu na konto magazynu * *](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Klikanie informacji dotyczących rekomendacji i wybieranie zasobów w złej kondycji prowadzi bezpośrednio do środowiska włączania **bezpiecznego transferu magazynu** w witrynie Azure Portal.

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

    ![Konfiguracja konta magazynu](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Gdy zastosujesz się do rekomendacji, w raporcie pulpitu nawigacyjnego zgodności zostanie wyświetlony ich wpływ, ponieważ zwiększy się ocena zgodności.

    > [!NOTE]
    > Oceny są przeprowadzane mniej więcej co 12 godzin, dlatego wpływ na dane dotyczące zgodności zobaczysz dopiero po uruchomieniu oceny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiono używanie pulpitu nawigacyjnego Zgodność z przepisami usługi Security Center do wykonywania następujących czynności:

-   Wyświetlanie i monitorowanie poziomu zgodności względem istotnych dla Ciebie standardów i przepisów.

-   Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny Zgodność z przepisami może znacznie uprościć proces zapewniania zgodności i zauważalnie skrócić czas wymagany do uzyskania dowodu zgodności dla platformy Azure i środowiska hybrydowego.

Aby dowiedzieć się więcej, zobacz:

-   [Zaktualizuj dynamiczne pakiety zgodności na pulpicie nawigacyjnym zgodności z przepisami (wersja zapoznawcza)](update-regulatory-compliance-packages.md) — Dowiedz się więcej na temat tej funkcji w wersji zapoznawczej, która umożliwia zaktualizowanie standardów widocznych na pulpicie nawigacyjnym zgodności z przepisami w celu uzyskania nowych pakietów *dynamicznych* . Możesz również tę samą funkcję w wersji zapoznawczej, aby dodać nowe pakiety zgodności i monitorować swoją zgodność przy użyciu dodatkowych standardów. 

-   [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.

-   [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak używać zaleceń w programie Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

-   [Popraw swój Bezpieczny wynik w Azure Security Center](secure-score-security-controls.md) — Dowiedz się, jak określać priorytety luk w zabezpieczeniach i zalecenia dotyczące zabezpieczeń, aby zwiększyć bezpieczeństwo stan.
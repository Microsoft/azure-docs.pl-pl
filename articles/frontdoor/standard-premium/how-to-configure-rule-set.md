---
title: 'Moje drzwi platformy Azure: Konfigurowanie zestawu reguł dla drzwi przednich'
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania zestawu reguł.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715602"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Konfigurowanie zestawu reguł przy użyciu platformy Azure Front-Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule przedstawiono sposób tworzenia zestawu reguł oraz pierwszego zestawu reguł w Azure Portal. Następnie dowiesz się, jak skojarzyć zestaw reguł z trasą ze strony zestawu reguł lub z Menedżera punktów końcowych.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby można było skonfigurować zestaw reguł, należy najpierw utworzyć Standard/Premium platformy Azure. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie profilu usługi Azure Front-Standard/Premium](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Konfigurowanie zestawu reguł w Azure Portal

1. W ramach profilu frontu drzwi wybierz pozycję **zestaw reguł** znajdujący się w obszarze **Ustawienia**. Wybierz pozycję **Dodaj** i nadaj jej nazwę zestawu reguł.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Zrzut ekranu strony docelowej zestawu reguł.":::
    
1. Wybierz pozycję **Dodaj regułę** , aby utworzyć pierwszą regułę. Nadaj mu nazwę reguły. Następnie wybierz pozycję **Dodaj warunek** lub **Dodaj akcję** , aby zdefiniować regułę. Można dodać maksymalnie 10 warunków i 5 akcji dla jednej reguły. W tym przykładzie używamy zmiennej serwerowej w celu dodania nagłówka odpowiedzi 8Geo-Country * w przypadku żądań zawierających *contoso* w adresie URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Zrzut ekranu przedstawiający stronę konfiguracji zestawu reguł.":::
    
    > [!NOTE]
    > * Aby usunąć warunek lub akcję z reguły, użyj Kosza można po prawej stronie określonego warunku lub akcji.
    > * Aby utworzyć regułę, która ma zastosowanie do całego ruchu przychodzącego, nie określaj żadnych warunków.
    > * Aby zatrzymać ocenianie pozostałych reguł w przypadku spełnienia określonej reguły, zaznacz pole wyboru **Zatrzymaj szacowanie pozostałej reguły**. Jeśli ta opcja jest zaznaczona, a wszystkie pozostałe reguły w zestawie reguł nie będą wykonywane niezależnie od spełnienia warunków zgodności.  

1. Możesz określić priorytet reguł w ramach zestawu reguł przy użyciu przycisków strzałek, aby przenieść reguły o wyższym lub niższym priorytecie. Lista znajduje się w kolejności rosnącej, więc najważniejsze reguły są wyświetlane jako pierwsze.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Zrzut ekranu z priorytetem zestawu reguł." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Po utworzeniu co najmniej jednej reguły wybierz pozycję **Zapisz** , aby zakończyć tworzenie zestawu reguł.

1. Teraz Skojarz zestaw reguł z trasą, aby mógł on obowiązywać. Można kojarzyć reguły ustawiane za pomocą strony zestawu reguł lub można przejść do Menedżera punktów końcowych, aby utworzyć skojarzenie.
 
    **Strona zestawu reguł**: 
    
    1. Wybierz zestaw reguł, który ma zostać skojarzony.
    
    1. Wybierz *nieskojarzony* link.
     

    1. Następnie na stronie **kojarzenie trasy** wybierz punkt końcowy i trasę, które chcesz skojarzyć z zestawem reguł. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie trasy.":::    
        
    1. Wybierz pozycję *dalej* , aby zmienić kolejność zestawów reguł, jeśli w wybranej trasie znajduje się wiele zestawów reguł. Zestaw reguł zostanie wykonany z góry do dołu. Można zmienić kolejność, wybierając zestaw reguł i przenosząc je w górę lub w dół. Następnie wybierz pozycję *Skojarz*.
    
        > [!Note]
        > Na tej stronie można skojarzyć tylko jeden zestaw reguł z jedną trasą. Aby skojarzyć zestaw reguł z wieloma trasami, należy użyć Menedżera punktów końcowych.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Zrzut ekranu kolejności zestawu reguł.":::
    
    1. Zestaw reguł jest teraz skojarzony z trasą. Można przyjrzeć się nagłówkowi odpowiedzi i zobaczyć, że zostanie dodany kraj geograficzny.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Zrzut ekranu przedstawiający regułę skojarzoną z trasą.":::

   **Menedżer punktów końcowych**: 
    
    1. Przejdź do Menedżera punktów końcowych, wybierz punkt końcowy, który chcesz skojarzyć z zestawem reguł.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Zrzut ekranu przedstawiający Wybieranie punktu końcowego w Menedżerze punktów końcowych." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Wybierz pozycję *Edytuj punkt końcowy*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Zrzut ekranu przedstawiający Wybieranie opcji edytuj punkt końcowy w Menedżerze punktów końcowych." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Wybierz trasę. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Zrzut ekranu przedstawiający wybór trasy.":::
    
    1. Na stronie *Aktualizowanie trasy* w obszarze *reguły* wybierz zestawy reguł, które chcesz skojarzyć z trasą z listy rozwijanej. Następnie możesz zmienić kolejność, przenosząc ustawienia w górę i w dół. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Zrzut ekranu przedstawiający stronę aktualizowanie trasy.":::
    
    1. Następnie wybierz pozycję *Aktualizuj* lub *Dodaj* , aby zakończyć skojarzenie.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Usuwanie zestawu reguł z profilu usługi Azure Front-drzwiczk

W poprzednich krokach została skonfigurowana i skojarzona z zestawem reguł do trasy. Jeśli nie chcesz już kojarzyć zestawu reguł z pierwszymi drzwiami, możesz usunąć zestaw reguł, wykonując następujące czynności:

1. Przejdź do **strony zestaw reguł** w obszarze **Ustawienia** , aby usunąć skojarzenie zestawu reguł ze wszystkich skojarzonych tras.

1. Rozwiń trasę, wybierz trzy kropki. Następnie wybierz pozycję *Edytuj trasę*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Zrzut ekranu rozbudowanego trasy w zestawie reguł.":::

1. Przejdź do sekcji reguły na stronie trasa, wybierz zestaw reguł, a następnie wybierz przycisk *Usuń* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Zrzut ekranu strony aktualizacji trasy do usuwania zestawu reguł." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Wybierz pozycję *Aktualizuj* , a zestaw reguł spowoduje skojarzenie z trasą.

1. Powtórz kroki 2-5, aby usunąć skojarzenie innych tras skojarzonych z tym zestawem reguł, dopóki nie zostanie wyświetlony stan trasy *nieskojarzone*.

1. W przypadku zestawu reguł, który nie jest *skojarzony*, można usunąć zestaw reguł, klikając trzy kropki po prawej stronie i wybierając pozycję *Usuń*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania zestawu reguł.":::

1. Zestaw reguł został usunięty.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać [nagłówki zabezpieczeń z zestawem reguł](how-to-add-security-headers.md).

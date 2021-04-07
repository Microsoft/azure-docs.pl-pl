---
title: Skonfiguruj trasę usługi Azure front-drzwi
description: W tym artykule pokazano, jak skonfigurować trasę między domenami i grupami pochodzenia.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099537"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Konfigurowanie trasy usługi Azure Front-Standard/Premium

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule wyjaśniono wszystkie ustawienia używane podczas tworzenia trasy platformy Azure front-drzwi (AFD) dla istniejącego punktu końcowego. Po dodaniu domeny niestandardowej i pochodzeniu do istniejącego punktu końcowego drzwi platformy Azure należy skonfigurować trasę, aby zdefiniować skojarzenie między domenami i źródłami w celu kierowania ruchu między nimi.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować trasę "drzwi frontonu platformy Azure", należy utworzyć co najmniej jedną grupę pierwotną i jedną domenę niestandardową w bieżącym punkcie końcowym. 

Aby skonfigurować grupę pierwotną, zobacz [Tworzenie nowej grupy pierwotnych drzwi platformy Azure w warstwie Standardowa/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Tworzenie nowej trasy usługi Azure Front-Standard/Premium

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do profilu Standard/Premium drzwi platformy Azure.

1. W obszarze **Ustawienia** wybierz pozycję **Menedżer punktów końcowych** .
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Zrzut ekranu przedstawiający ustawienia Menedżera punktów końcowych frontonu." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Następnie wybierz pozycję **Edytuj punkt końcowy** dla punktu końcowego, dla którego chcesz skonfigurować trasę.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Zrzut ekranu przedstawiający Wybieranie opcji edytuj punkt końcowy.":::

1. Zostanie wyświetlona strona **Edytowanie punktu końcowego** . Wybierz pozycję **+ Dodaj** dla tras.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie trasy na edycji punktu końcowego.":::    
    
1. Na stronie **Dodawanie trasy** wprowadź lub wybierz poniższe informacje.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie trasy." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Ustawienie | Wartość |
    | --- | --- |
    | Nazwa | Wprowadź unikatową nazwę nowej trasy. |   
    | Domena| Wybierz co najmniej jedną domenę, która została zweryfikowana i nie jest skojarzona z inną trasą |
    | Wzorce do dopasowania  | Skonfiguruj wszystkie wzorce ścieżki URL, które będą akceptowane przez tę trasę. Na przykład możesz ustawić tę opcję na `/images/*` , aby akceptowała wszystkie żądania w adresie URL `www.contoso.com/images/*` . AFD spróbuje określić ruch na podstawie dokładnego dopasowania, jeśli nie ma dokładnego dopasowania ścieżki, a następnie poszukaj ścieżki z symbolami wieloznacznymi, które pasują do siebie. Jeśli nie odnaleziono żadnych reguł routingu ze zgodną ścieżką, Odrzuć żądanie i zwróć 400: zły błąd żądania HTTP odpowiedzi. |
    | Akceptowane protokoły | Określ protokoły, które mają być akceptowane przez drzwi platformy Azure, gdy klient zgłasza żądanie. |
    | Przekierowanie | Określ, czy protokół HTTPS jest wymuszany dla żądania przychodzącego z żądaniem HTTP |
    | Grupa pierwotna | Wybierz grupę pierwotną, do której ma zostać przekazana, gdy następuje żądanie powrotu do źródła danych. |
    | Ścieżka do źródła | Wprowadź ścieżkę do zasobów, które mają być buforowane. Aby umożliwić buforowanie dowolnego zasobu w domenie, pozostaw to ustawienie puste. |
    | Protokół przekazywania | Wybierz protokół używany do przekazywania żądania. |
    | Buforowanie | Wybierz tę opcję, aby włączyć buforowanie zawartości statycznej przy użyciu drzwi frontonu platformy Azure. |
    | Reguła | Wybierz zestawy reguł, które zostaną zastosowane do tej trasy. Aby uzyskać więcej informacji o konfigurowaniu reguł, zobacz [Konfigurowanie zestawu reguł dla drzwi platformy Azure](how-to-configure-rule-set.md) . | 

1. Wybierz pozycję **Dodaj** , aby utworzyć nową trasę. Trasa zostanie wyświetlona na liście tras dla punktu końcowego.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Zrzut ekranu listy tras.":::  
    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć trasę, gdy nie jest już potrzebna, wybierz trasę, a następnie wybierz pozycję **Usuń**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania trasy.":::  

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o domenach niestandardowych, przejdź do samouczka dotyczącego dodawania domeny niestandardowej do punktu końcowego drzwi platformy Azure.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej]()

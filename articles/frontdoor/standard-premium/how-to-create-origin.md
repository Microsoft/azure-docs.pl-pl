---
title: Konfigurowanie pochodzenia platformy Azure w warstwie Standardowa/Premium (wersja zapoznawcza)
description: W tym artykule przedstawiono sposób konfigurowania pochodzenia przy użyciu Menedżera punktów końcowych.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741892"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Konfigurowanie pochodzenia platformy Azure w warstwie Standardowa/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule opisano sposób tworzenia pochodzenia usługi Azure Front-Standard/Premium w istniejącej grupie pochodzenia. 

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie można utworzyć punkt początkowy warstwy Standardowa/Premium platformy Azure, musisz utworzyć co najmniej jedną grupę pierwotną.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Utwórz nowy punkt początkowy standardu/Premium platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do profilu Standard/Premium drzwi platformy Azure.

1. Wybierz **grupę pierwotną**. Następnie wybierz pozycję **+ Dodaj** , aby utworzyć nową grupę pierwotną.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Zrzut ekranu strony docelowej grupy pochodzenia.":::

1. Na stronie **Dodawanie grupy pochodzenia** wprowadź unikatową **nazwę** nowej grupy pochodzenia.

1. Następnie wybierz pozycję **+ Dodaj źródło** , aby dodać nowe źródło do tej grupy pochodzenia. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie źródła.":::
  
    | Ustawienie | Wartość |
    | --- | --- |
    | Nazwa | Wprowadź unikatową nazwę nowego źródła drzwi frontonu platformy Azure. |   
    | Typ źródła | Typ zasobu, który ma zostać dodany. Platforma Azure Front-Standard/Premium obsługuje Autowykrywanie pochodzenia aplikacji z usługi App Service, usługi w chmurze lub magazynu. Jeśli chcesz użyć innego zasobu na platformie Azure lub w zaplecze innym niż Azure, wybierz opcję **host niestandardowy**. |
    | Nazwa hosta  | Jeśli nie wybrano **niestandardowego hosta** dla typu hosta źródła, wybierz zaplecze, wybierając nazwę hosta źródła na liście rozwijanej. |
    | Nagłówek hosta źródła | Wprowadź wartość nagłówka hosta, która jest wysyłana do zaplecza dla każdego żądania. Aby uzyskać więcej informacji, zobacz [nagłówek hosta źródła](concept-origin.md#hostheader). |
    | Port HTTP | Wprowadź wartość dla portu obsługiwanego przez źródło protokołu HTTP. |
    | Port HTTPS | Wprowadź wartość dla portu obsługiwanego przez źródło protokołu HTTPS. |
    | Priorytet | Przypisz priorytety do innego źródła, jeśli chcesz użyć podstawowego pochodzenia usługi dla całego ruchu. Ponadto należy podać kopie zapasowe, jeśli pierwotna lub źródłowa kopia zapasowa jest niedostępna. Aby uzyskać więcej informacji, zobacz [priorytet](concept-origin.md#priority). |
    | Waga | Przypisz wagi do różnych źródeł, aby rozpowszechnić ruch między zestawem źródeł, nawet lub zgodnie z współczynnikim wagi. Aby uzyskać więcej informacji, zobacz [wagi](concept-origin.md#weighted). |
    | Stan | Wybierz tę opcję, aby włączyć źródło. |
    | Reguła | Wybierz zestawy reguł, które zostaną zastosowane do tej trasy. Aby uzyskać więcej informacji o konfigurowaniu reguł, zobacz [Konfigurowanie zestawu reguł dla drzwi platformy Azure](how-to-configure-rule-set.md) . | 

    > [!IMPORTANT]
    > W trakcie konfiguracji interfejsy API nie sprawdzają, czy źródło jest niedostępne w środowiskach z przodu. Upewnij się, że tylne drzwi mogą dotrzeć do źródła.

1. Wybierz pozycję **Dodaj** , aby utworzyć nowe źródło. Utworzony początek powinien pojawić się na liście pierwotnej z grupą.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Zrzut ekranu przedstawiający początek w widoku listy.":::

1. Wybierz pozycję **Dodaj** , aby dodać grupę pierwotną do bieżącego punktu końcowego. Grupa pierwotna powinna pojawić się w panelu grupy pierwotnej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć grupę pierwotną, gdy nie jest już potrzebne, kliknij przycisk **...** , a następnie wybierz pozycję **Usuń** z listy rozwijanej.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania grupy pochodzenia.":::

Aby usunąć źródło, gdy nie jest już potrzebne, kliknij przycisk **...** , a następnie wybierz pozycję **Usuń** z listy rozwijanej. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania źródła.":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o domenach niestandardowych, zobacz [Dodawanie domeny niestandardowej](how-to-add-custom-domain.md) do punktu końcowego usługi Azure Front-Standard/Premium.

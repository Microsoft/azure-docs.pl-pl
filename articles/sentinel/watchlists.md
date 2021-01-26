---
title: Korzystanie z platformy Azure — wskaźnik watchlists
description: W tym artykule opisano, jak używać usługi Azure watchlists do badania zagrożeń, importowania danych firmowych, tworzenia list dozwolonych i wzbogacania danych zdarzeń.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: e31128687cfcc1f4e32879328ad3227182efb9ce
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797353"
---
# <a name="use-azure-sentinel-watchlists"></a>Korzystanie z platformy Azure — wskaźnik watchlists

> [!IMPORTANT]
> Funkcja watchlists jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Platforma Azure watchlists umożliwia zbieranie danych z zewnętrznych źródeł danych w celu korelacji ze zdarzeniami w środowisku wskaźnikowym platformy Azure. Po utworzeniu można użyć watchlists w wyszukiwaniu, regułach wykrywania, pokryciu zagrożeń i elementy PlayBook odpowiedzi. Watchlists są przechowywane w obszarze roboczym usługi Azure wskaźnikowym jako pary nazwa-wartość i są buforowane w celu uzyskania optymalnej wydajności zapytań i małych opóźnień.

Typowe scenariusze używania watchlists obejmują:

- Szybkie **Badanie zagrożeń** i reagowanie na incydenty szybko przy użyciu szybkiego importowania adresów IP, skrótów plików i innych danych z plików CSV. Po zaimportowaniu można użyć par nazwa-wartość listy do obejrzenia dla sprzężeń i filtrów w regułach alertów, łowiectwu zagrożeń, skoroszytach, notesach i ogólnych zapytaniach.

- **Importowanie danych firmowych** jako watchlists. Na przykład zaimportuj listy użytkowników z uprzywilejowanym dostępem do systemu lub zakończonego pracownikami, a następnie użyj listy do obejrzenia do utworzenia list dozwolonych i zablokowanych, które będą używane do wykrywania lub zapobiegania logowaniem użytkowników w sieci.

- **Zmniejszenie zmęczenia alertu**. Utwórz listę dozwolonych, aby pomijać alerty od grupy użytkowników, takie jak użytkownicy z autoryzowanych adresów IP wykonujących zadania, które normalnie wyzwalają alert, i Zapobiegaj uruchamianiu niegroźnych zdarzeń.

- **Wzbogacanie danych zdarzeń**. Użyj watchlists, aby wzbogacić dane zdarzenia przy użyciu kombinacji nazw i wartości pochodzących z zewnętrznych źródeł danych.

## <a name="create-a-new-watchlist"></a>Utwórz nowy listy do obejrzenia

1. W Azure Portal przejdź do **usługi Azure**  >    >  **listy do obejrzenia** Configuration, a następnie wybierz pozycję **Dodaj nowe**.

    > [!div class="mx-imgBorder"]
    > ![Nowy listy do obejrzenia](./media/watchlists/sentinel-watchlist-new.png)

1. Na stronie **Ogólne** Podaj nazwę, opis i alias dla listy do obejrzenia, a następnie wybierz przycisk **dalej**.

    > [!div class="mx-imgBorder"]
    > ![Strona ogólna listy do obejrzenia](./media/watchlists/sentinel-watchlist-general.png)

1. Na stronie **Źródło** wybierz typ zestawu danych, Przekaż plik, a następnie wybierz przycisk **dalej**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="Strona źródłowa listy do obejrzenia" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Operacje przekazywania plików są obecnie ograniczone do plików o rozmiarze do 3,8 MB.

1. Przejrzyj informacje, sprawdź, czy są poprawne, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Strona przeglądu listy do obejrzenia](./media/watchlists/sentinel-watchlist-review.png)

    Po utworzeniu listy do obejrzenia pojawi się powiadomienie.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="powiadomienie o pomyślnym utworzeniu listy do obejrzenia" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Używanie watchlists w zapytaniach

1. W Azure Portal przejdź do **usługi Azure**  >    >  **listy do obejrzenia** Configuration, wybierz listy do obejrzenia, którego chcesz użyć, a następnie wybierz pozycję **Widok w log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="Używanie watchlists w zapytaniach" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Elementy w listy do obejrzenia są automatycznie wyodrębniane dla zapytania i pojawią się na karcie **wyniki** . W poniższym przykładzie pokazano wyniki wyodrębniania pól **servername** i **IPAddress** .

    > [!NOTE]
    > Sygnatura czasowa w zapytaniach zostanie zignorowana zarówno w interfejsie użytkownika zapytania, jak i w zaplanowanych alertach.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="zapytania z polami listy do obejrzenia" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. Możesz wykonywać zapytania dotyczące danych w dowolnej tabeli względem danych z listy do obejrzenia, traktując listy do obejrzenia jako tabelę dla sprzężeń i odnośników.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="zapytania względem listy do obejrzenia jako wyszukiwanie":::

## <a name="use-watchlists-in-analytics-rules"></a>Używanie watchlists w regułach analizy

Aby użyć watchlists w regułach analizy, w Azure Portal przejdź do **usługi Azure wskaźnik**  >  **konfiguracji**  >  **analiz** i Utwórz regułę przy użyciu `_GetWatchlist('<watchlist>')` funkcji w zapytaniu.

1. W tym przykładzie Utwórz listy do obejrzenia o nazwie "ipwatchlist" z następującymi wartościami:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="Lista czterech elementów do listy do obejrzenia":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="Utwórz listy do obejrzenia z czterema elementami":::

1. Następnie utwórz regułę analizy.  W tym przykładzie uwzględniamy tylko zdarzenia z adresów IP w listy do obejrzenia:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="Używanie watchlists w regułach analizy":::

## <a name="view-list-of-watchlists-aliases"></a>Wyświetlanie listy aliasów watchlists

Aby uzyskać listę aliasów listy do obejrzenia, w Azure Portal przejdź do ogólnych dzienników **usługi Azure wskaźnikowego**  >    >  i uruchom następujące zapytanie: `_GetWatchlistAlias` . Listę aliasów można zobaczyć na karcie **wyniki** .

> [!div class="mx-imgBorder"]
> ![watchlists listy](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób korzystania z watchlists na platformie Azure wskaźnikowej do wzbogacania danych i ulepszania badań. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
---
title: Uaktualnij wersję Mongo interfejsu API Azure Cosmos DB dla konta MongoDB
description: Jak uaktualnić wersję protokołu MongoDB Wire dla Azure Cosmos DB istniejącego interfejsu API w celu zapewnienia bezproblemowego korzystania z usługi MongoDB
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409667"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Uaktualnij wersję protokołu MongoDB Wire interfejsu API Azure Cosmos DB dla konta MongoDB

W tym artykule opisano sposób uaktualniania wersji protokołu sieci Web interfejsu API Azure Cosmos DB dla konta MongoDB. Po uaktualnieniu wersji protokołu przewodowego można użyć najnowszych funkcji w interfejsie API Azure Cosmos DB dla MongoDB. Proces uaktualniania nie przerywa dostępności Twojego konta i nie używa jednostki RU/s ani nie zmniejsza wydajności bazy danych w dowolnym momencie. Ten proces nie wpłynie na istniejące dane ani indeksy.

>[!Note]
> W tej chwili można uaktualnić tylko konta uprawniające do korzystania z serwera w wersji 3,2 do wersji 3,6. Jeśli na koncie nie jest wyświetlana opcja Uaktualnij, Utwórz [bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Uaktualnianie z wersji 3,2 do 3,6

### <a name="benefits-of-upgrading-to-version-36"></a>Zalety uaktualnienia do wersji 3.6

Poniżej przedstawiono nowe funkcje dostępne w wersji 3,6:
- Udoskonalona wydajność i stabilność
- Obsługa nowych poleceń bazy danych
- Domyślna obsługa potoków agregacji i nowe etapy agregacji
- Obsługa strumieni zmian
- Obsługa indeksów złożonych
- Obsługa wielu partycji dla następujących operacji: Update, DELETE, Count i Sort
- Ulepszona wydajność dla następujących operacji agregowania: $count, $skip, $limit i $group
- Indeksowanie symboli wieloznacznych jest teraz obsługiwane

### <a name="changes-from-version-32"></a>Zmiany z wersji 3,2

- **Błędy RequestRateIsLarge zostały usunięte**. Żądania z aplikacji klienckiej nie będą już zwracały błędów 16500. Żądania zostaną wznowione do momentu zakończenia lub spełnienia limitu czasu.
- Limit czasu na żądanie jest ustawiony na 60 sekund.
- Kolekcje MongoDB utworzone w nowej wersji protokołu sieci będą mieć `_id` Domyślnie indeksowaną właściwość.

### <a name="action-required"></a>Wymagana akcja

W przypadku uaktualnienia do wersji 3,6 sufiks punktu końcowego konta bazy danych zostanie zaktualizowany do następującego formatu:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Należy zastąpić istniejący punkt końcowy w aplikacjach i sterownikach, które łączą się z tym kontem bazy danych. **Tylko połączenia z nowym punktem końcowym będą miały dostęp do funkcji w programie MongoDB w wersji 3,6**. Poprzedni punkt końcowy powinien mieć sufiks `.documents.azure.com` .

>[!Note]
> Ten punkt końcowy może mieć niewielkie różnice, jeśli Twoje konto zostało utworzone w ramach suwerennej, rządowej lub ograniczonej chmury platformy Azure.

### <a name="how-to-upgrade"></a>Jak uaktualnić

1. Najpierw przejdź do Azure Portal i przejdź do bloku Azure Cosmos DB interfejsu API dla konta MongoDB. Sprawdź, czy wersja serwera to `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure Portal z omówieniem konta MongoDB" border="false":::

2. Z opcji po lewej stronie wybierz `Features` blok. Spowoduje to ujawnienie funkcji poziomu konta, które są dostępne dla Twojego konta bazy danych.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal z wyróżnioną pozycją MongoDB z użyciem bloku funkcji" border="false":::

3. Kliknij `Upgrade to Mongo server version 3.6` wiersz. Jeśli ta opcja nie jest widoczna, Twoje konto może nie kwalifikować się do tego uaktualnienia. W takim przypadku prosimy [o zgłoszenie biletu pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Blok funkcji z opcjami." border="false":::

4. Przejrzyj informacje o tym konkretnym uaktualnieniu. Należy pamiętać, że uaktualnienie zostanie ukończone tylko do momentu, gdy aplikacje nie będą używały zaktualizowanego punktu końcowego, jak zostało to wyróżnione w tej sekcji. Kliknij pozycję włączone zaraz po `Enable` przygotowaniu do uruchomienia procesu.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Rozszerzone wskazówki dotyczące uaktualniania." border="false":::

5. Po uruchomieniu procesu w `Features` menu zostanie wyświetlony stan uaktualnienia. Stan przejdzie z `Pending` do, do `In Progress` , do `Upgraded` . Ten proces nie ma wpływu na istniejące funkcje ani operacje na koncie bazy danych.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Stan uaktualnienia po zainicjowaniu." border="false":::

6. Po zakończeniu uaktualniania stan będzie wyświetlany jako `Upgraded` . Kliknij go, aby dowiedzieć się więcej o następnych krokach i akcjach, które należy wykonać, aby zakończyć proces. [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/en-us/support/create-ticket/) , jeśli wystąpił problem podczas przetwarzania żądania.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Stan uaktualnionego konta." border="false":::

7. **Aby rozpocząć korzystanie z uaktualnionej wersji konta bazy danych**, Wróć do `Overview` bloku i skopiuj nowe parametry połączenia do użycia w aplikacji. Aplikacje rozpoczną korzystanie z uaktualnionej wersji zaraz po nawiązaniu połączenia z nowym punktem końcowym. Istniejące połączenia nie będą przerywane i mogą być aktualizowane w Twojej wygodie. Aby zapewnić spójne środowisko, wszystkie aplikacje muszą używać nowego punktu końcowego.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nowy blok przeglądu." border="false":::

## <a name="next-steps"></a>Następne kroki

- Poznaj obsługiwane i nieobsługiwane [funkcje programu MongoDB w wersji 3,6](mongodb-feature-support-36.md).
- Aby uzyskać więcej informacji, zobacz [funkcje wersji Mongo 3,6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)

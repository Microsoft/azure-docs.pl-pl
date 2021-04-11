---
title: Uaktualnij wersję Mongo interfejsu API Azure Cosmos DB dla konta MongoDB
description: Jak uaktualnić wersję protokołu MongoDB Wire dla Azure Cosmos DB istniejącego interfejsu API w celu zapewnienia bezproblemowego korzystania z usługi MongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771825"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Uaktualnij wersję interfejsu API Azure Cosmos DB interfejsu API dla konta MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule opisano sposób uaktualniania wersji interfejsu API usługi Azure Cosmos DB dla konta MongoDB. Po uaktualnieniu można użyć najnowszych funkcji w interfejsie API Azure Cosmos DB dla MongoDB. Proces uaktualniania nie przerywa dostępności Twojego konta i nie używa jednostki RU/s ani nie zmniejsza wydajności bazy danych w dowolnym momencie. Ten proces nie wpłynie na istniejące dane ani indeksy. 

Podczas uaktualniania do nowej wersji interfejsu API Rozpocznij od obciążeń deweloperskich/testowych przed uaktualnieniem obciążeń produkcyjnych. Ważne jest, aby uaktualnić klientów do wersji zgodnej z wersją interfejsu API, którą uaktualniasz, przed uaktualnieniem Azure Cosmos DBego interfejsu API dla konta MongoDB.

>[!Note]
> W tej chwili tylko konta uprawniające do korzystania z serwera w wersji 3,2 można uaktualnić do wersji 3,6 lub 4,0. Jeśli na koncie nie jest wyświetlana opcja Uaktualnij, Utwórz [bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Uaktualnianie do wersji 4.0 lub 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Zalety uaktualnienia do wersji 4.0

Poniżej przedstawiono nowe funkcje dostępne w wersji 4,0:
- Obsługa transakcji wielodokumentowych w ramach kolekcji unsharded.
- Nowe operatory agregacji
- Większa wydajność skanowania
- Szybszy i wydajniejszy magazyn

### <a name="benefits-of-upgrading-to-version-36"></a>Zalety uaktualnienia do wersji 3.6

Poniżej przedstawiono nowe funkcje dostępne w wersji 3,6:
- Udoskonalona wydajność i stabilność
- Obsługa nowych poleceń bazy danych
- Domyślna obsługa potoków agregacji i nowe etapy agregacji
- Obsługa zmiany strumieni
- Obsługa indeksów złożonych
- Obsługa następujących operacji na wielu partycjach: update, delete, count i sort
- Ulepszona wydajność dla następujących operacji agregowania: $count, $skip, $limit i $group
- Indeksowanie symboli wieloznacznych jest teraz obsługiwane

### <a name="changes-from-version-32"></a>Zmiany w stosunku do wersji 3.2

- Domyślnie funkcja [Ponawianie po stronie serwera (SSR)](prevent-rate-limiting-errors.md) jest włączona, aby żądania z aplikacji klienckiej nie zwracały błędów 16500. Zamiast tego żądania będą wznawiane do momentu zakończenia lub przekroczenia limitu czasu (60 sekund).
- Limit czasu na żądanie jest ustawiony na 60 sekund.
- Kolekcje usługi MongoDB utworzone w nowej wersji protokołu Wire będą mieć indeksowaną domyślnie tylko właściwość `_id`.

### <a name="action-required-when-upgrading-from-32"></a>Akcja wymagana w przypadku uaktualniania z wersji 3.2

Podczas uaktualniania z wersji 3.2 sufiks punktu końcowego konta bazy danych zostanie zaktualizowany do następującego formatu:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Jeśli uaktualniasz program z wersji 3,2, musisz zastąpić istniejący punkt końcowy w swoich aplikacjach i sterownikach, które łączą się z tym kontem bazy danych. **Tylko połączenia korzystające z nowego punktu końcowego będą miały dostęp do funkcji w nowej wersji interfejsu API**. Poprzedni punkt końcowy w wersji 3.2 powinien mieć sufiks `.documents.azure.com`.

>[!Note]
> Ten punkt końcowy może mieć niewielkie różnice, jeśli Twoje konto zostało utworzone w ramach suwerennej, rządowej lub ograniczonej chmury platformy Azure.

## <a name="how-to-upgrade"></a>Jak przeprowadzić uaktualnianie

1. Zaloguj się do [Azure Portal.](https://portal.azure.com/)

1. Przejdź do usługi Azure Cosmos DB API dla konta MongoDB. Otwórz okienko **Przegląd** i sprawdź, czy bieżąca **wersja serwera** to 3,2 lub 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Sprawdź bieżącą wersję konta usługi MongoDB z poziomu Azure Portal." border="true":::

1. W menu po lewej stronie Otwórz `Features` okienko. W tym okienku są wyświetlane funkcje poziomu konta dostępne dla Twojego konta bazy danych.

1. Zaznacz wiersz `Upgrade MongoDB server version`. Jeśli ta opcja nie jest widoczna, Twoje konto może nie kwalifikować się do tego uaktualnienia. W takim przypadku prosimy [o zgłoszenie biletu pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Otwórz blok funkcje i Uaktualnij swoje konto." border="true":::

1. Przejrzyj wyświetlone informacje o uaktualnieniu. Wybierz `Set server version to 4.0` (lub 3,6 w zależności od bieżącej wersji).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Przejrzyj wskazówki dotyczące uaktualniania i wybierz pozycję Uaktualnij." border="true":::

1. Po rozpoczęciu uaktualniania menu **funkcji** jest wyszarzone i stan jest ustawiony na *oczekujące*. Uaktualnienie trwa około 15 minut. Ten proces nie ma wpływu na istniejące funkcje ani operacje na koncie bazy danych. Po zakończeniu tej czynności stan **wersji serwera aktualizacji MongoDB** zostanie wyświetlony w wersji uaktualnionej. [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/en-us/support/create-ticket/) , jeśli wystąpił problem podczas przetwarzania żądania.

1. Po uaktualnieniu konta należy wziąć pod uwagę następujące zagadnienia:

    1. Jeśli uaktualniono z 3,2, Wróć do okienka **Przegląd** i skopiuj nowe parametry połączenia do użycia w aplikacji. Stare parametry połączenia działające w wersji 3.2 nie zostaną przerwane. Aby zapewnić spójne środowisko, wszystkie aplikacje muszą korzystać z nowego punktu końcowego.

    1. W przypadku uaktualnienia z wersji 3.6 istniejące parametry połączenia zostaną uaktualnione do określonej wersji i powinny być nadal używane.

## <a name="how-to-downgrade"></a>Jak zmienić wersję na starszą lub mniej zaawansowaną wersję

Możesz również obniżyć poziom konta z 4,0 do 3,6 za pośrednictwem tych samych kroków w sekcji "jak uaktualnić".

Jeśli uaktualniono z 3,2 do (4,0 lub 3,6) i chcesz ponownie obniżyć poziom do wersji 3,2, możesz po prostu przełączyć się z powrotem do używania poprzednich parametrów połączenia (3,2) z hostem, `accountname.documents.azure.com` który pozostaje aktywnym po uaktualnieniu w wersji 3,2.

## <a name="next-steps"></a>Następne kroki

- Poznaj obsługiwane i nieobsługiwane [funkcje bazy danych MongoDB w wersji 4.0](mongodb-feature-support-40.md).
- Poznaj obsługiwane i nieobsługiwane [funkcje bazy danych MongoDB w wersji 3.6](mongodb-feature-support-36.md).
- Aby uzyskać więcej informacji, zobacz [funkcje bazy danych MongoDB w wersji 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)

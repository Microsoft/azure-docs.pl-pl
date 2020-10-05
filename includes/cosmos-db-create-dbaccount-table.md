---
title: plik dołączania
description: plik dołączania
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5015371afc67574a214097f8d3eef661df29c22f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115046"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Tworzenie zasobu w witrynie Azure Portal":::
   
3. Na stronie **Nowy** wybierz pozycję **bazy danych**  >  **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Tworzenie zasobu w witrynie Azure Portal":::
   
3. Na stronie **Tworzenie konta Azure Cosmos DB** wprowadź ustawienia dla nowego konta Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|**Utwórz nową**, a następnie nazwę konta|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia Użyj takiej samej nazwy, jak nazwa konta Azure Cosmos DB. 
    Nazwa konta|Unikatowa nazwa|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB.<br><br>Nazwa konta może zawierać tylko małe litery, cyfry i łączniki (-) i musi mieć długość od 3 do 31 znaków.
    Interfejs API|tabela|Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych programu Graph, MongoDB dla baz danych dokumentów, Azure Table i Cassandra. Należy utworzyć osobne konto dla każdego interfejsu API. <br><br>Wybierz pozycję **tabela platformy Azure**, ponieważ w tym przewodniku szybki start utworzysz tabelę, która współpracuje z interfejs API tabel. <br><br>[Dowiedz się więcej na temat interfejs API tabel](../articles/cosmos-db/table-introduction.md).|
    Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

    Aby uniknąć dodatkowych opłat i pominąć sekcje **sieciowe** i **Tagi** , można pozostawić opcje zapisu **geograficznego** i **wieloregionowego** w pozycji **Wyłącz** .

5. Wybierz pozycję **Przeglądanie+tworzenie**. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć konto. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="Tworzenie zasobu w witrynie Azure Portal":::

6. Utworzenie konta trwa kilka minut. Zobaczysz komunikat informujący o tym, że **wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia, a następnie wybierz pozycję **Przejdź do zasobu**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Tworzenie zasobu w witrynie Azure Portal":::


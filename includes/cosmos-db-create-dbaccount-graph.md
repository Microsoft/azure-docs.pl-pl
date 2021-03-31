---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 736ab630b035ae1a668332eb5ab5aa9530ef0e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972428"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   ![Tworzenie zasobu w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stronie **Nowy** wybierz pozycję **bazy danych**  >  **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > Jeśli na liście nie widzisz Azure Cosmos DB, po prostu wpisz ją w polu wyszukiwania w górnej części strony, a następnie naciśnij klawisz _Enter_ . 

3. Na stronie **Tworzenie konta Azure Cosmos DB** wprowadź ustawienia dla nowego konta Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź taką samą nazwę jak nazwa konta|Wybierz pozycję **Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia Użyj takiej samej nazwy, jak nazwa konta Azure Cosmos DB. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Identyfikator URI konta zostanie *Gremlin.Azure.com* dołączony do unikatowej nazwy konta.<br><br>Nazwa konta może zawierać tylko małe litery, cyfry i łączniki (-) i musi mieć długość od 3 do 31 znaków.
    Interfejs API|Gremlin (graf)|Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych programu Graph, MongoDB dla baz danych dokumentów, Azure Table i Cassandra. Należy utworzyć osobne konto dla każdego interfejsu API. <br><br>Wybierz pozycję **Gremlin (Graph)**, ponieważ w tym przewodniku szybki start tworzysz tabelę, która współpracuje z interfejsem API Gremlin. <br><br>[Dowiedz się więcej o interfejsie API Gremlin](../articles/cosmos-db/graph-introduction.md).|
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.
    Tryb wydajności|Nieobsługiwana przepływność lub bezserwerowy|Wybierz opcję **zainicjowana przepływność** , aby utworzyć konto w trybie [przepływności aprowizacji](../articles/cosmos-db/set-throughput.md) . Wybierz opcję **Bezserwerowa** , aby utworzyć konto w trybie [bezserwerowym](../articles/cosmos-db/serverless.md) .

    Wybierz pozycję **Przeglądanie+tworzenie**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż Portal wyświetli **gratulacje! Twoje konto Azure Cosmos DB zostało utworzone** .
   
   ![Strona tworzenia konta Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)


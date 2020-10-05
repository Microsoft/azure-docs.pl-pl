---
title: plik dołączania
description: plik dołączania
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "77061718"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   ![Tworzenie zasobu w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stronie **Nowy** wybierz pozycję **bazy danych**  >  **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stronie **Tworzenie konta Azure Cosmos DB** wprowadź ustawienia dla nowego konta Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź taką samą nazwę jak nazwa konta|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia Użyj takiej samej nazwy, jak nazwa konta Azure Cosmos DB. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Identyfikator URI konta zostanie *Mongo.Cosmos.Azure.com* dołączony do unikatowej nazwy konta.<br><br>Nazwa konta może zawierać tylko małe litery, cyfry i łączniki (-) i musi mieć długość od 3 do 31 znaków.
    Interfejs API|Azure Cosmos DB dla interfejsu API usługi Mongo DB|Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych programu Graph, Azure Cosmos DB dla interfejsu API usługi Mongo DB dla baz danych dokumentów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz **Azure Cosmos DB dla interfejsu API usługi Mongo DB**  , ponieważ w tym przewodniku szybki start tworzysz kolekcję, która współpracuje z MongoDB.<br><br>[Dowiedz się więcej na temat Azure Cosmos DB interfejsu API MongoDB](../articles/cosmos-db/mongodb-introduction.md).|
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|

    Wybierz pozycję **Przeglądanie+tworzenie**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż Portal wyświetli **gratulacje! Azure Cosmos DB konta interfejsu API usługi Mongo DB jest gotowa** .

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)

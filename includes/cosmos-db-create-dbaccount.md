---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/10/2020
ms.custom: include file
ms.openlocfilehash: 4c6e45eaa816499c7e5969e302a036368bea0937
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81421915"
---
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/), aby utworzyć konto usługi Azure Cosmos DB. Na stronie głównej wybierz pozycję **Utwórz zasób** z panelu **Usługi platformy Azure.**

   ![Tworzenie zasobu](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-resource-1.png)

1. Wyszukaj i wybierz usługę **Azure Cosmos DB**.

   ![Rozwijanie zasobów witryny Azure portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-updated-1.png)

1. Wybierz pozycję **Utwórz**.

   ![Tworzenie zasobu usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account-1.png)

1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź podstawowe ustawienia nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, która będzie identyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanej nazwy w celu utworzenia identyfikatora URI, użyj unikatowej nazwy.<br><br>Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |interfejs API|Typ konta do utworzenia|Wybierz pozycję **Core (SQL)**, aby utworzyć bazę danych dokumentów i wykonać zapytanie przy użyciu składni języka SQL. <br><br>Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB udostępnia pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentu, Gremlin dla danych wykresu, Tabela platformy Azure i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>[Dowiedz się więcej o interfejsie API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    |Zastosuj rabat bezpłatny poziom|Zastosuj lub nie stosuj|Dzięki bezpłatnej warstwie usługi Azure Cosmos DB otrzymasz pierwsze 400 ru/s i 5 GB miejsca na koncie. Dowiedz się więcej o [warstwie bezpłatnej](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
    |Typ konta|Produkcja lub nieprodukcja|Wybierz **opcję Produkcja,** jeśli konto będzie używane dla obciążenia produkcyjnego. Wybierz **opcję Nieprodukcja,** jeśli konto będzie używane do nieprodukcji, np. Jest to ustawienie tagu zasobów platformy Azure, które umożliwia dostrojenie środowiska portalu, ale nie wpływa na podstawowe konto usługi Azure Cosmos DB. Tę wartość można zmienić w dowolnym momencie.|


    > [!NOTE]
    > Możesz mieć maksymalnie jedno bezpłatne konto usługi Azure Cosmos DB na subskrypcję platformy Azure i musisz wyrazić zgodę podczas tworzenia konta. Jeśli nie widzisz opcji zastosowania rabatu warstwy bezpłatnej, oznacza to, że inne konto w subskrypcji zostało już włączone z warstwą bezpłatną.
   
   ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Wybierz pozycję **Przegląd + utwórz**. Sekcje **Sieć** i **Tagi** możesz pominąć.

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Strona konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)

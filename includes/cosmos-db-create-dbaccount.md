---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: efdd4a065e1eab55f5af420585a44754d42a43e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010383"
---
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowa** wyszukaj i wybierz usługę **Azure Cosmos DB**.

1. Na stronie **Azure Cosmos DB** wybierz pozycję **Utwórz**.

1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź podstawowe ustawienia nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, która będzie identyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanej nazwy w celu utworzenia identyfikatora URI, użyj unikatowej nazwy.<br><br>Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 44 znaków.|
    |Interfejs API|Typ konta do utworzenia|Wybierz pozycję **Core (SQL)** , aby utworzyć bazę danych dokumentów i wykonać zapytanie przy użyciu składni języka SQL. <br><br>Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentów, Gremlin dla danych grafów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. |
    |Tryb wydajności|Nieobsługiwana przepływność lub bezserwerowy|Wybierz opcję **zainicjowana przepływność** , aby utworzyć konto w trybie [przepływności aprowizacji](../articles/cosmos-db/set-throughput.md) . Wybierz opcję **Bezserwerowa** , aby utworzyć konto w trybie [bezserwerowym](../articles/cosmos-db/serverless.md) .|
    |Zastosuj rabat na podstawie warstwy Bezpłatna|Zastosuj lub nie stosuj|W ramach warstwy Bezpłatna usługi Azure Cosmos DB uzyskasz bezpłatnie pierwsze 400 jednostek RU/s i 5 GB magazynu na koncie. Dowiedz się więcej o [warstwie Bezpłatna](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
    |Typ konta|Produkcyjne lub nieprodukcyjne|Wybierz pozycję **Produkcyjne**, jeśli konto będzie używane w obciążeniu produkcyjnym. Wybierz pozycję **Nieprodukcyjne**, jeśli konto będzie używane na potrzeby nieprodukcyjne, na przykład programowania, testowania, kontroli jakości lub wdrażania przejściowego. Jest to ustawienie tagu zasobu platformy Azure, które dostosowuje środowisko portalu, ale nie wpływa na bazowe konto usługi Azure Cosmos DB. Tę wartość można zmienić w dowolnym momencie.|

    > [!NOTE]
    > W ramach jednej subskrypcji platformy Azure można korzystać z maksymalnie jednego konta usługi Azure Cosmos DB w warstwie Bezpłatna. Tę opcję należy wybrać podczas tworzenia konta. Jeśli opcja zastosowania rabatu na podstawie warstwy Bezpłatna nie jest widoczna, inne konto w subskrypcji już korzysta z warstwy Bezpłatna.
   
    > [!NOTE]
    > Następujące opcje są niedostępne, jeśli wybierzesz opcję **bezserwerowe** jako **tryb pojemności**:
    > - Zastosuj rabat na podstawie warstwy Bezpłatna
    > - Nadmiarowość geograficzna
    > - Moduły zapisujące obsługujące wiele regionów
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Strona nowego konta usługi Azure Cosmos DB":::

1. Wybierz pozycję **Przegląd + utwórz**. Sekcje **Sieć** i **Tagi** możesz pominąć.

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Okienko Powiadomienia w witrynie Azure Portal":::

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Strona konta usługi Azure Cosmos DB":::

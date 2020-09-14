---
title: Dowiedz się, jak przenieść konto Azure Cosmos DB do innego regionu
description: Dowiedz się, jak przenieść konto Azure Cosmos DB do innego regionu
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059366"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Jak przenieść konto Azure Cosmos DB do innego regionu

W tym artykule opisano, jak przenieść region, w którym dane są replikowane w Azure Cosmos DB lub jak przeprowadzić migrację metadanych konta (Azure Resource Manager), a także dane z jednego regionu do innego.

## <a name="move-data-from-one-region-to-another"></a>Przenoszenie danych z jednego regionu do innego

Azure Cosmos DB obsługuje natywną replikację danych, więc przeniesienie danych z jednego regionu do innego jest proste i może być realizowane przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure i obejmuje następujące kroki:

1. Dodawanie nowego regionu do konta

    Aby dodać nowy region do konta Azure Cosmos DB Zobacz, [Dodaj/Usuń regiony do konta Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Wykonaj ręczną pracę awaryjną w nowym regionie

    W sytuacjach, w których usuwany region jest obecnie regionem zapisu dla konta, konieczne będzie zainicjowanie przejścia w tryb failover do nowego regionu dodanego powyżej. To jest operacja bez przestojów. Jeśli przenosisz region odczytu na koncie wieloregionowym, możesz pominąć ten krok. Aby zainicjować pracę w trybie failover, zobacz Ręczne przełączenie [w tryb failover na koncie usługi Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Usuń pierwotny region

    Aby usunąć region z konta Azure Cosmos DB, zobacz [Dodaj/Usuń regiony do konta Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrowanie metadanych konta Azure Cosmos DB

Azure Cosmos DB nie obsługuje natywnie migrowania metadanych konta z jednego regionu do innego. Aby przeprowadzić migrację zarówno danych metadanych konta, jak i danych klienta z jednego regionu do innego, należy utworzyć nowe konto w żądanym regionie, a następnie ręcznie skopiować dane. Migracja z niemal zerowym przestojem interfejsu API SQL wymaga użycia [ChangeFeed](change-feed.md) lub narzędzia, które go wykorzystuje. W przypadku migrowania interfejsu API MongoDB, Cassandra lub innego interfejsu API lub aby dowiedzieć się więcej na temat opcji migrowania danych między kontami, zobacz [Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB](cosmosdb-migrationchoices.md). Poniższe kroki przedstawiają sposób migrowania konta Azure Cosmos DB dla interfejsu API SQL i jego danych z jednego regionu do innego:

1. Utwórz nowe konto Azure Cosmos DB w żądanym regionie

    Aby utworzyć nowe konto za pośrednictwem Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, [Utwórz konto Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Tworzenie nowej bazy danych i kontenera

    Aby utworzyć nową bazę danych i kontener, zobacz [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md)

1. Migrowanie danych za pomocą narzędzia Migrator danych na żywo Azure Cosmos DB

    Aby przeprowadzić migrację danych z niemal zerowym przestojem, zobacz [Azure Cosmos DB narzędzie Migrator danych dynamicznych](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Zaktualizuj parametry połączenia aplikacji

    Po uruchomieniu narzędzia Migrator na żywo należy zaktualizować informacje o połączeniu w nowym wdrożeniu aplikacji. Punkty końcowe i klucze aplikacji można pobrać z Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Kontrola dostępu (IAM) w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL":::

1. Przekieruj żądania do nowej aplikacji

    Po powiązaniu nowej aplikacji z Azure Cosmos DB można przekierować żądania klientów do nowego wdrożenia.

1. Usuń wszystkie zasoby, które nie są już potrzebne

    W przypadku żądań, które są teraz w pełni przekierowywane do nowego wystąpienia, można następnie usunąć stare konto Azure Cosmos DB i narzędzie Migrator danych na żywo.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi zarządzania kontem usługi Azure Cosmos oraz bazą danych i kontenerami, przeczytaj następujące artykuły:

* [Zarządzanie kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Źródło zmian w Azure Cosmos DB](change-feed.md)

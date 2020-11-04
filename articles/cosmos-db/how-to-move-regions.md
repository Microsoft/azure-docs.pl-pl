---
title: Przenoszenie konta Azure Cosmos DB do innego regionu
description: Dowiedz się, jak przenieść konto Azure Cosmos DB do innego regionu.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342032"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Przenoszenie konta Azure Cosmos DB do innego regionu
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano, jak:

- Przenieś region, w którym dane są replikowane w Azure Cosmos DB.
- Migrowanie metadanych konta (Azure Resource Manager) i danych z jednego regionu do innego.

## <a name="move-data-from-one-region-to-another"></a>Przenoszenie danych z jednego regionu do innego

Azure Cosmos DB obsługuje natywną replikację danych, więc przeniesienie danych z jednego regionu do innego jest proste. Można to zrobić za pomocą Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Obejmuje następujące kroki:

1. Dodaj nowy region do konta.

    Aby dodać nowy region do konta Azure Cosmos DB, zobacz [Dodawanie/usuwanie regionów do konta Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Wykonaj ręczną pracę awaryjną w nowym regionie.

    Gdy usuwany region jest obecnie regionem zapisu dla konta, należy uruchomić tryb failover w nowym regionie dodanym w poprzednim kroku. Jest to operacja bez przestojów. Jeśli przenosisz region odczytu w ramach konta wieloregionowego, możesz pominąć ten krok. 
    
    Aby rozpocząć pracę w trybie failover, zobacz Ręczne przełączenie [w tryb failover na koncie usługi Azure Cosmos](how-to-manage-database-account.md#manual-failover).

1. Usuń pierwotny region.

    Aby usunąć region z konta Azure Cosmos DB, zobacz [Dodawanie/usuwanie regionów z konta Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrowanie metadanych konta Azure Cosmos DB

Azure Cosmos DB nie obsługuje natywnie migracji metadanych konta z jednego regionu do innego. Aby przeprowadzić migrację zarówno metadanych konta, jak i danych klienta z jednego regionu do innego, należy utworzyć nowe konto w żądanym regionie, a następnie ręcznie skopiować dane. 

Migracja typu "Near-zero" dla interfejsu API SQL wymaga użycia [źródła zmian](change-feed.md) lub narzędzia, które go używa. Jeśli migrujesz interfejs API MongoDB, interfejs API Cassandra lub inny interfejs API lub aby dowiedzieć się więcej o opcjach migrowania danych między kontami, zobacz [Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB](cosmosdb-migrationchoices.md). 

W poniższych krokach pokazano, jak migrować konto Azure Cosmos DB dla interfejsu API SQL i jego danych z jednego regionu do innego:

1. Utwórz nowe konto Azure Cosmos DB w żądanym regionie.

    Aby utworzyć nowe konto za pośrednictwem Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie konta Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Utwórz nową bazę danych i kontener.

    Aby utworzyć nową bazę danych i kontener, zobacz [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md).

1. Migrowanie danych za pomocą narzędzia Azure Cosmos DB na żywo usługi.

    Aby przeprowadzić migrację danych z niemal zerowym przestojem, zobacz [Azure Cosmos DB narzędzia Migrator na żywo](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Zaktualizuj parametry połączenia aplikacji.

    Gdy nadal działa narzędzie Data Migrator, należy zaktualizować informacje o połączeniu w nowym wdrożeniu aplikacji. Możesz pobrać punkty końcowe i klucze aplikacji z Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Kontrola dostępu w Azure Portal wykazujące zabezpieczenia bazy danych NoSQL.":::

1. Przekieruj żądania do nowej aplikacji.

    Po powiązaniu nowej aplikacji z Azure Cosmos DB można przekierować żądania klientów do nowego wdrożenia.

1. Usuń wszystkie zasoby, które nie są już potrzebne.

    W przypadku żądań, które są teraz w pełni przekierowywane do nowego wystąpienia, można usunąć stare konto Azure Cosmos DB i narzędzie Migrator danych na żywo.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi zarządzania kontem usługi Azure Cosmos oraz bazami danych i kontenerami, przeczytaj następujące artykuły:

* [Zarządzanie kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Źródło zmian w Azure Cosmos DB](change-feed.md)

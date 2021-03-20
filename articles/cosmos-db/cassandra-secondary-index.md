---
title: Indeksowanie na koncie Azure Cosmos DB interfejs API Cassandra
description: Dowiedz się, jak działa indeks pomocniczy na koncie usługi Azure Azure Cosmos DB interfejs API Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339898"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indeksowanie pomocnicze w Azure Cosmos DB interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w Azure Cosmos DB wykorzystuje podstawową infrastrukturę indeksowania, aby uwidocznić wytrzymałość indeksowania, która jest nieodłączna w danej platformie. Jednak, w przeciwieństwie do podstawowego interfejsu API SQL, interfejs API Cassandra w Azure Cosmos DB domyślnie nie indeksuje wszystkich atrybutów. Zamiast tego obsługuje indeksowanie pomocnicze w celu utworzenia indeksu dla określonych atrybutów, co zachowuje się tak samo jak Apache Cassandra.  

Ogólnie rzecz biorąc nie zaleca się wykonywania zapytań filtru dla kolumn, które nie są partycjonowane. Należy użyć jawnie Zezwalaj na składnię filtrowania, co powoduje wykonanie operacji, która może nie działać prawidłowo. W Azure Cosmos DB można uruchamiać takie zapytania w przypadku atrybutów o niskiej kardynalności, ponieważ umożliwiają one pobieranie wyników.

Nie zaleca się tworzenia indeksu dla często aktualizowanej kolumny. Podczas definiowania tabeli należy ostrożnie utworzyć indeks. Zapewnia to spójny stan danych i indeksów. W przypadku tworzenia nowego indeksu istniejących danych obecnie nie można śledzić zmiany postępu indeksu dla tabeli. Jeśli musisz śledzić postęp tej operacji, musisz zażądać zmiany postępu za pośrednictwem [biletu pomocy technicznej]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Indeks pomocniczy nie jest obsługiwany w następujących obiektach:
> - typy danych, takie jak zablokowane typy kolekcji, Decimal i typy Variant.
> - Kolumny statyczne
> - Klucze klastrowania

## <a name="indexing-example"></a>Przykład indeksowania

Najpierw utwórz przykładowe miejsce i tabelę, uruchamiając następujące polecenia w wierszu powłoki CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Następnie Wstaw przykładowe dane użytkownika przy użyciu następujących poleceń:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Jeśli spróbujesz wykonać następującą instrukcję, wystąpi błąd z prośbą o użycie `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Chociaż interfejs API Cassandra obsługuje filtrowanie, jak wspomniano w poprzedniej sekcji, nie jest to zalecane. Zamiast tego należy utworzyć indeks w sposób pokazany w poniższym przykładzie:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Po utworzeniu indeksu w polu "LastName" można teraz ponownie uruchomić poprzednią kwerendę. W przypadku interfejs API Cassandra w Azure Cosmos DB nie trzeba podawać nazwy indeksu. Używany jest domyślny indeks z formatowaniem `tablename_columnname_idx` . Na przykład ` t1_lastname_idx` jest nazwą indeksu poprzedniej tabeli.

## <a name="dropping-the-index"></a>Usuwanie indeksu 
Musisz wiedzieć, czym jest nazwa indeksu, aby usunąć indeks. Uruchom `desc schema` polecenie, aby uzyskać opis tabeli. Dane wyjściowe tego polecenia zawierają nazwę indeksu w formacie `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Następnie można użyć nazwy indeksu, aby porzucić indeks, jak pokazano w następującym przykładzie:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [Automatyczne indeksowanie](index-overview.md) działa w Azure Cosmos DB
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB](cassandra-support.md)

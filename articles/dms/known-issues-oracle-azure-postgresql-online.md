---
title: 'Znane problemy: Migrowanie z programu Oracle do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informacje o znanych problemach i ograniczeniach migracji z migracją online z programu Oracle do usługi Azure Database for PostgreSQL-Single Server przy użyciu Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 2316e0314c9d8f36aee123d4a516f382f58e83e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291660"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Znane problemy/ograniczenia migracji z migracją online z programu Oracle do usługi Azure DB dla PostgreSQL-Single Server

Znane problemy i ograniczenia związane z migracją online z programu Oracle do usługi Azure Database for PostgreSQL-Single Server zostały opisane w poniższych sekcjach.

## <a name="oracle-versions-supported-as-a-source-database"></a>Wersje programu Oracle obsługiwane jako źródłowa baza danych

Azure Database Migration Service obsługuje łączenie z:

- Oracle w wersji 10g, 11g i 12c.
- Oracle Enterprise, standard, Express i Personal Edition.

Azure Database Migration Service nie obsługuje łączenia z wielodostępnymi bazami danych kontenerów (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Wersje PostgreSQL obsługiwane jako docelowa baza danych

Azure Database Migration Service obsługuje migracje do usługi Azure Database dla programu PostgreSQL-Single Server w wersji 9,5, 9,6, 10 i 11. Zapoznaj się z artykułem [obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) , aby uzyskać aktualne informacje o obsłudze wersji w usłudze Azure database for PostgreSQL-Single Server.

## <a name="datatype-limitations"></a>Ograniczenia typów danych

Następujące typy danych **nie** zostaną zmigrowane:

- DOTYCZĄCE
- Właściwość
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Zagnieżdżone tabele
- Typy danych zdefiniowane przez użytkownika
- Uwagi
- Kolumny wirtualne
- Widoki z materiałami oparte na kolumnie ROWID

Ponadto puste kolumny obiektów BLOB/obiektów CLOB są mapowane na wartość NULL w elemencie docelowym.

## <a name="lob-limitations"></a>Ograniczenia dotyczące obiektów LOB

- Gdy tryb LOB o ograniczonym rozmiarze jest włączony, puste LOB w źródle Oracle są replikowane jako wartości NULL.
- Długie nazwy obiektów (ponad 30 bajtów) nie są obsługiwane.
- Dane w kolumnie LONG i LONG RAW nie mogą przekroczyć 64 KB. Wszystkie dane spoza 64 KB zostaną obcięte.
- Tylko w przypadku programu Oracle 12 wszystkie zmiany w kolumnach LOB nie są obsługiwane (migrowane).
- Aktualizacje kolumn XmlType i LOB nie są obsługiwane (migrowane).

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

- Użytkownik musi mieć uprawnienia administratora bazy danych na serwerze Oracle.
- Zmiany danych pochodzące z operacji partycji/partycji podrzędnej (Dodawanie, usuwanie, wymiana i obcinanie) nie zostaną zmigrowane i mogą spowodować następujące błędy:
  - W przypadku operacji dodawania aktualizacje i usunięcia na dodanych dane mogą zwracać ostrzeżenie "0 wierszy, których dotyczy".
  - W przypadku operacji upuszczania i OBCINAnia nowe wstawienia mogą spowodować błędy "duplikatów".
  - W przypadku operacji programu EXCHANGE mogą wystąpić błędy "ostrzeżenie" i "duplikaty".
- Tabel, których nazwy zawierają apostrofy, nie mogą być replikowane.

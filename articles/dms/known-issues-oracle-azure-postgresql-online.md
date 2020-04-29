---
title: 'Znane problemy: Migrowanie z programu Oracle do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informacje o znanych problemach i ograniczeniach migracji z migracją online z programu Oracle do Azure Database for PostgreSQL-pojedynczego serwera przy użyciu Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235245"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Znane problemy/ograniczenia migracji z migracją online z programu Oracle do usługi Azure DB dla PostgreSQL — pojedynczy serwer

Znane problemy i ograniczenia związane z migracją w trybie online z programu Oracle do Azure Database for PostgreSQL-pojedynczego serwera są opisane w poniższych sekcjach.

## <a name="oracle-versions-supported-as-a-source-database"></a>Wersje programu Oracle obsługiwane jako źródłowa baza danych

Azure Database Migration Service obsługuje łączenie z:

- Oracle w wersji 10g, 11g i 12c.
- Oracle Enterprise, standard, Express i Personal Edition.

Azure Database Migration Service nie obsługuje łączenia z wielodostępnymi bazami danych kontenerów (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Wersje PostgreSQL obsługiwane jako docelowa baza danych

Azure Database Migration Service obsługuje migracje do Azure Database for PostgreSQL-jednego serwera w wersji 9,5, 9,6, 10 i 11. Zapoznaj się z artykułem [obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) , aby uzyskać aktualne informacje o obsłudze wersji na pojedynczym serwerze Azure Database for PostgreSQL.

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

- Aby nawiązać połączenie z bazą danych Oracle, klienci muszą używać SYSDBA.
- Zmiany danych pochodzące z operacji partycji/partycji podrzędnej (Dodawanie, usuwanie, wymiana i obcinanie) nie zostaną zmigrowane i mogą spowodować następujące błędy:
  - W przypadku operacji dodawania aktualizacje i usunięcia na dodanych dane mogą zwracać ostrzeżenie "0 wierszy, których dotyczy".
  - W przypadku operacji upuszczania i OBCINAnia nowe wstawienia mogą spowodować błędy "duplikatów".
  - W przypadku operacji programu EXCHANGE mogą wystąpić błędy "ostrzeżenie" i "duplikaty".
- Tabel, których nazwy zawierają apostrofy, nie mogą być replikowane.

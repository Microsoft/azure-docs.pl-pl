---
title: Śledzenie zmian danych w usłudze Azure SQL Edge
description: Dowiedz się więcej na temat śledzenia zmian i przechwytywania zmian danych w usłudze Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f7d5d5f74a816bf745faf5decf761cd453f40123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900060"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Śledzenie zmian danych w usłudze Azure SQL Edge

Usługa Azure SQL Edge obsługuje dwie SQL Server funkcje, które śledzą zmiany danych w bazie danych: [śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) i [przechwytywanie zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Te funkcje umożliwiają aplikacjom Określanie zmian języka modyfikacji danych (operacji wstawiania, aktualizowania i usuwania), które zostały wprowadzone do tabel użytkowników w bazie danych. Możesz włączyć funkcję przechwytywania zmian danych i śledzenia zmian w tej samej bazie danych. Nie są wymagane żadne specjalne uwagi.

Możliwość wykonywania zapytań dotyczących danych, które uległy zmianie w bazie danych, jest ważnym wymaganiem do wydajnego działania niektórych aplikacji. Zwykle aby określić zmiany danych, deweloperzy aplikacji muszą zaimplementować niestandardowe metody śledzenia w swoich aplikacjach przy użyciu kombinacji wyzwalaczy, kolumn sygnatur czasowych i dodatkowych tabel. Tworzenie tych aplikacji zwykle wymaga dużej ilości pracy do zaimplementowania, prowadzi do aktualizacji schematu i często zapewnia wysoką wydajność.

W przypadku rozwiązania IoT, w którym należy okresowo przenosić dane z krawędzi do chmury lub centrum danych, śledzenie zmian może być bardzo przydatne. Użytkownicy mogą szybko i efektywnie badać tylko zmiany z ostatniej synchronizacji i przekazać te zmiany do chmury lub docelowego centrum danych. Aby uzyskać dodatkowe informacje, zobacz [zalety korzystania z funkcji przechwytywania zmian danych lub śledzenia zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Te dwie funkcje nie są takie same. Aby uzyskać więcej informacji, zobacz [różnice między funkcjami funkcji przechwytywania zmian danych i śledzenia zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Przechwytywanie danych dotyczących zmian

Aby zapoznać się ze szczegółami dotyczącymi sposobu działania tej funkcji, zobacz [Informacje o funkcji przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Aby dowiedzieć się, jak włączyć lub wyłączyć tę funkcję, zobacz [Włączanie i wyłączanie funkcji przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Aby administrować i monitorować tę funkcję, zobacz [administrowanie i monitorowanie przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Aby zrozumieć, jak wykonywać zapytania i współdziałać ze zmienionymi danymi, zobacz [Work with Change Data](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server).

## <a name="change-tracking"></a>Śledzenie zmian

Aby zapoznać się ze szczegółami dotyczącymi sposobu działania tej funkcji, zobacz [Informacje o śledzeniu zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Aby dowiedzieć się, jak włączyć lub wyłączyć tę funkcję, zobacz [Włączanie i wyłączanie śledzenia zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Aby zarządzać tą funkcją, monitorować ją i zarządzać nią, zobacz [administrowanie i monitorowanie śledzenia zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Aby zrozumieć, jak wykonywać zapytania i współdziałać ze zmienionymi danymi, zobacz [Work with Change Data](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Tabele danych czasowych

Usługa Azure SQL Edge obsługuje również funkcję tabel danych czasowych programu SQL Server. Ta funkcja (znana także jako tabele danych czasowych *z systemową obsługą wersji*) oferuje wbudowaną obsługę przekazywania informacji o danych przechowywanych w tabeli w dowolnym momencie. Ta funkcja nie zapewnia po prostu informacji o danych, które są poprawne w bieżącym momencie.

Tabela danych czasowych z systemową obsługą wersji jest typem tabeli użytkownika, która jest przeznaczona do przechowywania pełnej historii zmian danych i pozwala na łatwą analizę punktu w czasie. Ten typ tabeli danych czasowych jest określany jako tabela danych czasowych z systemową obsługą, ponieważ okres ważności każdego wiersza jest zarządzany przez system (to jest aparat bazy danych).

Każda tabela danych czasowych ma dwie jawnie zdefiniowane kolumny, z których każda zawiera `datetime2` dane typu. Te kolumny są określane jako kolumny *okresów* . System używa tych kolumn okresowych wyłącznie do rejestrowania okresu ważności dla każdego wiersza za każdym razem, gdy wiersz jest modyfikowany.

Oprócz tych kolumn okresowych tabela zawiera również odwołanie do innej tabeli z dublowanym schematem. System używa tej tabeli do automatycznego przechowywania poprzedniej wersji wiersza przy każdej aktualizacji lub usunięciu wiersza w tabeli danych czasowych. Ta dodatkowa tabela jest określana jako tabela *historii* , natomiast główna tabela, która przechowuje bieżące (rzeczywiste) wersje wiersza, jest określana jako *Bieżąca* tabela lub po prostu jako tabela danych czasowych. Podczas tworzenia tabeli danych czasowych użytkownicy mogą określić istniejącą tabelę historii (musi być zgodna ze schematem) lub pozwolić systemowi na utworzenie domyślnej tabeli historii.

Aby uzyskać więcej informacji, zobacz [tabele](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)danych czasowych.

## <a name="next-steps"></a>Następne kroki

- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge ](stream-data.md)
- [Uczenie maszynowe i AI z ONNX w usłudze Azure SQL Edge ](onnx-overview.md)
- [Konfigurowanie replikacji do usługi Azure SQL Edge](configure-replication.md)
- [Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge](backup-restore.md)




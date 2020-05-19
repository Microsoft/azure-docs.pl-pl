---
title: Śledzenie zmian danych w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Informacje na temat śledzenia zmian i przechwytywania zmian danych w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597268"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Śledzenie zmian danych w usłudze Azure SQL Edge (wersja zapoznawcza)

Usługa Azure SQL Edge obsługuje dwie SQL Server funkcje, które śledzą zmiany danych w bazie danych:[Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) i [przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Te funkcje umożliwiają aplikacjom Określanie zmian DML (operacji INSERT, Update i Delete), które zostały wprowadzone do tabel użytkowników w bazie danych. Funkcję przechwytywania zmian danych i śledzenia zmian można włączyć w tej samej bazie danych. nie są wymagane żadne specjalne uwagi.

Możliwość wykonywania zapytań dotyczących danych, które uległy zmianie w bazie danych, jest ważnym wymaganiem do wydajnego działania niektórych aplikacji. Zwykle aby określić zmiany danych, deweloperzy aplikacji muszą zaimplementować niestandardowe metody śledzenia w swoich aplikacjach przy użyciu kombinacji wyzwalaczy, kolumn sygnatur czasowych i dodatkowych tabel. Tworzenie tych aplikacji zwykle wymaga dużej ilości pracy do zaimplementowania, prowadzi do aktualizacji schematu i często zapewnia wysoką wydajność. W przypadku rozwiązania IoT, w przypadku którego istnieje potrzeba okresowego przenoszenia danych z krawędzi do chmury lub centrum danych, śledzenie zmian może być bardzo przydatne. Pozwala to na szybkie i efektywne wykonywanie zapytań tylko zmianom różnicowym z ostatniej synchronizacji i przekazanie tych zmian do chmury lub celu centrum danych. Aby uzyskać dodatkowe informacje na temat korzyści z używania Change Tracking i funkcji przechwytywania zmian danych, zobacz [korzyści wynikające z używania funkcji przechwytywania zmian danych lub Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Aby zrozumieć różnice między funkcjami Change Tracking i przechwytywanie zmian danych, zapoznaj się z tematem [różnice między funkcją przechwytywania zmian danych i Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Przechwytywanie zmian danych

Aby poznać szczegóły dotyczące sposobu działania funkcji przechwytywania zmian danych, zapoznaj się z [informacjami na temat funkcji przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Aby dowiedzieć się, jak włączyć lub wyłączyć funkcję przechwytywania zmian danych, zapoznaj się z tematem [Włączanie i wyłączanie funkcji przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) .

Aby administrować i monitorować funkcję przechwytywania zmian danych, zobacz [administrowanie i monitorowanie przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Aby zrozumieć, jak wykonywać zapytania i korzystać ze zmienionych danych, zapoznaj się z tematem [Pracuj z danymi o zmianie](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server) .

## <a name="change-tracking"></a>Śledzenie zmian

Aby zapoznać się ze szczegółami dotyczącymi sposobu działania Change Tracking, zapoznaj się z [tematem Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Aby dowiedzieć się, jak włączyć lub wyłączyć Change Tracking, zobacz [Włączanie i wyłączanie Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Aby administrować, monitorować Change Tracking i zarządzać nim, zobacz [administrowanie i monitorowanie Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Aby zrozumieć, jak wykonywać zapytania i korzystać ze zmienionych danych, zapoznaj się z tematem [Pracuj z danymi o zmianie](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server) .

## <a name="temporal-tables"></a>Tabele danych czasowych

Oprócz obsługi Change Tracking i funkcji przechwytywania zmian danych SQL Server usługa Azure SQL Edge obsługuje również funkcję tabel danych czasowych programu SQL Server. Tabele danych czasowych (znane również jako tabele danych czasowych z systemową obsługą wersji) jako funkcja bazy, która zapewnia wbudowaną obsługę udostępniania informacji o danych przechowywanych w tabeli w dowolnym momencie, a nie tylko danych, które są poprawne w bieżącej chwili w czasie.

Tabela danych czasowych z systemową obsługą wersji jest typem tabeli użytkownika, która jest przeznaczona do przechowywania pełnej historii zmian danych i pozwala na łatwą analizę w czasie. Ten typ tabeli danych czasowych jest określany jako tabela danych czasowych z systemową obsługą, ponieważ okres ważności każdego wiersza jest zarządzany przez system (czyli aparat bazy danych).

Każda tabela danych czasowych ma dwie jawnie zdefiniowane kolumny, z których każda jest typu datetime2. Te kolumny są określane jako kolumny okresów. Te kolumny okresowe są używane wyłącznie przez system do rejestrowania okresu ważności dla każdego wiersza za każdym razem, gdy wiersz jest modyfikowany.

Oprócz tych kolumn okresowych tabela zawiera również odwołanie do innej tabeli z dublowanym schematem. System używa tej tabeli do automatycznego przechowywania poprzedniej wersji wiersza przy każdej aktualizacji lub usunięciu wiersza w tabeli danych czasowych. Ta dodatkowa tabela jest określana jako tabela historii, natomiast główna tabela, która przechowuje bieżące (rzeczywiste) wersje wiersza, jest określana jako bieżąca tabela lub po prostu jako tabela danych czasowych. Podczas tworzenia tabeli danych czasowych użytkownicy mogą określić istniejącą tabelę historii (musi być zgodna ze schematem) lub pozwolić systemowi na utworzenie domyślnej tabeli historii.

Aby uzyskać więcej informacji o tabelach danych czasowych, zobacz [tabele](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) danych czasowych

## <a name="see-also"></a>Zobacz też

- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge (wersja zapoznawcza)](stream-data.md)
- [Uczenie maszynowe i AI z ONNX w usłudze Azure SQL Edge (wersja zapoznawcza)](onnx-overview.md)
- [Konfigurowanie replikacji do usługi Azure SQL Edge (wersja zapoznawcza)](configure-replication.md)
- [Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge (wersja zapoznawcza)](backup-restore.md)




---
title: Kluczowe różnice w usługach uczenia maszynowego (wersja zapoznawcza)
description: W tym temacie opisano kluczowe różnice między usługami uczenia maszynowego bazy danych SQL platformy Azure (z usługami R) i usługami uczenia maszynowego programu SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453153"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Kluczowe różnice między usługami uczenia maszynowego w bazie danych SQL Azure (wersja zapoznawcza) i sql server

Funkcjonalność usług Azure SQL Database Machine Learning Services (z R) w (wersja zapoznawcza) jest podobna do [usług SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej znajdują się pewne kluczowe różnice.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Obsługa języków

SQL Server ma obsługę języka R i Python za pośrednictwem [struktury rozszerzalności](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Baza danych SQL nie obsługuje obu języków. Najważniejsze różnice to:

- R jest jedynym obsługiwanym językiem w bazie danych SQL. W tej chwili język Python nie jest obsługiwany.
- Wersja R to 3.4.4.
- Nie ma potrzeby `external scripts enabled` konfigurowania `sp_configure`za pośrednictwem .

## <a name="package-management"></a>Zarządzanie pakietami

Zarządzanie pakietami języka R i ich instalacja różnią się między bazą danych SQL a programem SQL Server. Różnice te są następujące:

- Pakiety R są instalowane za pośrednictwem [sqlmlutils](https://github.com/Microsoft/sqlmlutils) lub [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakiety nie mogą wykonywać wychodzących wywołań sieciowych. To ograniczenie jest podobne do [domyślnych reguł zapory dla usług uczenia maszynowego](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w programie SQL Server, ale nie można ich zmienić w bazie danych SQL.
- Nie ma obsługi pakietów, które zależą od zewnętrznych czasów pracy (takich jak Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego do instalacji lub użycia.

## <a name="writing-to-a-temporary-table"></a>Pisanie do tabeli tymczasowej

Jeśli używasz rodbc w usłudze Azure SQL Database, a następnie nie można zapisać do tabeli tymczasowej, czy jest tworzony wewnątrz lub na zewnątrz `sp_execute_external_script` sesji. Obejście polega na użyciu [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) i [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (z overwrite=FALSE i append="rows") do `sp_execute_external_script` zapisu w globalnej tabeli tymczasowej utworzonej przed kwerendą.

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe ograniczenie zasobów R za pośrednictwem [zasobo namiaku](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pul zasobów zewnętrznych.

Podczas publicznej wersji zapoznawczej zasoby języka R są ustawiane na maksymalnie 20% zasobów bazy danych SQL i zależą od warstwy usług, którą wybierzesz. Aby uzyskać więcej informacji, zobacz [Modele zakupu usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Niewystarczający błąd pamięci

Jeśli nie ma wystarczającej ilości pamięci dla R, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach to:

- Nie można komunikować się ze środowiska wykonawczego dla skryptu "R" dla identyfikatora żądania: *******. Sprawdź wymagania środowiska wykonawczego "R"
- Wystąpił błąd skryptu "R" podczas wykonywania 'sp_execute_external_script' z HRESULT 0x80004004. ... wystąpił błąd skryptu zewnętrznego: ".. nie można przydzielić pamięci (0 Mb) w funkcji C 'R_AllocStringBuffer'"
- Wystąpił błąd skryptu zewnętrznego: błąd: nie można przydzielić wektora rozmiaru.

Użycie pamięci zależy od tego, ile jest używane w skryptach języka R i liczby wykonywanych zapytań równoległych. Jeśli podasz powyższe błędy, możesz skalować bazę danych do wyższej warstwy usług, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

- Zobacz omówienie usługi [Azure SQL Database Machine Learning Services z R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań o usługi Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [przewodnik Szybki start](sql-database-connect-query-r.md).
- Aby rozpocząć korzystanie z kilku prostych skryptów języka R, zobacz [Tworzenie i uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).

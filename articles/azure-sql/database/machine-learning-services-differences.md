---
title: Kluczowe różnice dotyczące Machine Learning Services (wersja zapoznawcza)
description: W tym temacie opisano kluczowe różnice między Azure SQL Database Machine Learning Services (z R) i SQL Server Machine Learning Services.
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
ms.openlocfilehash: ae913325d3c1fcbe4bd7cbf0005a449f17e722d9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048224"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Kluczowe różnice między Machine Learning Services w Azure SQL Database (wersja zapoznawcza) i SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Funkcje Azure SQL Database Machine Learning Services (z językiem R) w (wersja zapoznawcza) są podobne do [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre kluczowe różnice.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Obsługa języków

SQL Server obsługuje język R i Python za pomocą [struktury rozszerzalności](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nie obsługuje obu języków. Kluczowe różnice są następujące:

- R jest jedynym obsługiwanym językiem w SQL Database. W tej chwili język Python nie jest obsługiwany.
- Wersja języka R to 3.4.4.
- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem programu `sp_configure` .

## <a name="package-management"></a>Zarządzanie pakietami

Zarządzanie pakietami i instalacją w języku R działa inaczej niż SQL Database i SQL Server. Te różnice są następujące:

- Pakiety języka R są instalowane za pośrednictwem [sqlmlutils](https://github.com/Microsoft/sqlmlutils) lub [Create External Library](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakiety nie mogą wykonywać wychodzących wywołań sieciowych. To ograniczenie jest podobne do [domyślnych reguł zapory dla Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w SQL Server, ale nie można go zmienić w SQL Database.
- Pakiety, które są zależne od zewnętrznych środowisk uruchomieniowych (na przykład Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego w celu instalacji lub użycia, nie są obsługiwane.

## <a name="writing-to-a-temporary-table"></a>Zapisywanie w tabeli tymczasowej

Jeśli używasz RODBC w Azure SQL Database, nie możesz zapisywać w tabeli tymczasowej, niezależnie od tego, czy jest ona utworzona w sesji, czy poza nią `sp_execute_external_script` . Obejście polega na użyciu [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) i [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (z zastępowaniem = false i dołączeniem = "wiersze") do zapisu w globalnej tabeli tymczasowej utworzonej przed wykonaniem `sp_execute_external_script` zapytania.

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe ograniczanie zasobów R przy użyciu [regulatora zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pul zasobów zewnętrznych.

W publicznej wersji zapoznawczej zasoby R są ustawione na maksymalnie 20% zasobów SQL Database i zależą od wybranej warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure SQL Database modele zakupu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Błąd niewystarczającej pamięci

Jeśli jest za mało dostępnej pamięci dla języka R, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach to:

- Nie można nawiązać komunikacji ze środowiskiem uruchomieniowym dla skryptu "R" dla identyfikatora żądania: * * * * * * *. Sprawdź wymagania środowiska uruchomieniowego "R"
- Wystąpił błąd skryptu "R" podczas wykonywania elementu "sp_execute_external_script" z wartością HRESULT 0x80004004. ... Wystąpił zewnętrzny błąd skryptu: ".. nie można przydzielić pamięci (0 MB) w funkcji C "R_AllocStringBuffer" "
- Wystąpił błąd zewnętrzny skryptu: błąd: nie można przydzielić wektora rozmiaru.

Użycie pamięci zależy od tego, ile jest używane w skryptach języka R i liczby wykonywanych zapytań równoległych. Jeśli otrzymasz powyższe błędy, możesz skalować bazę danych do wyższej warstwy usług, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

- Zobacz Omówienie [Azure SQL Database Machine Learning Services w języku R (wersja zapoznawcza)](machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań w Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [Przewodnik Szybki Start](connect-query-r.md).
- Aby zacząć korzystać z niektórych prostych skryptów języka R, zobacz [Tworzenie i Uruchamianie prostych skryptów języka r w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](r-script-create-quickstart.md).

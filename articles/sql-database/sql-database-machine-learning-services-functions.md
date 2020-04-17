---
title: Pisanie zaawansowanych funkcji języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak napisać funkcję Języka R dla zaawansowanych obliczeń statystycznych w usłudze Azure SQL Database przy użyciu usług uczenia maszynowego (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453118"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)

W tym artykule opisano sposób osadzania funkcji matematycznych i narzędziowych języka R w procedurze składowanej SQL. Zaawansowane funkcje statystyczne, które są skomplikowane do zaimplementowania w T-SQL można wykonać w języku R tylko z jednego wiersza kodu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeniach, musisz najpierw mieć [włączona usługa Azure SQL Database z włączonymi usługami uczenia maszynowego (z funkcją języka R).](sql-database-machine-learning-services-overview.md)

- Upewnij się, że zainstalowano najnowsze [program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przewodniku Szybki start użyjesz programu SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Tworzenie procedury składowanej w celu generowania liczb losowych

Dla uproszczenia użyjmy `stats` pakietu Języka R, który jest instalowany i ładowany domyślnie z usługą Azure SQL Database przy użyciu usług uczenia maszynowego (wersja zapoznawcza). Pakiet zawiera setki funkcji dla typowych zadań `rnorm` statystycznych, wśród nich funkcja. Ta funkcja generuje określoną liczbę liczb losowych przy użyciu rozkładu normalnego, biorąc pod uwagę odchylenie standardowe i środki.

Na przykład poniższy kod R zwraca 100 liczb na średnią 50, biorąc pod uwagę odchylenie standardowe 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Aby wywołać ten wiersz języka R `sp_execute_external_script` z T-SQL, uruchom i dodaj funkcję R w parametrze skryptu Języka R, w ten sposób:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co zrobić, jeśli chcesz ułatwić generowanie innego zestawu liczb losowych?

Jest to łatwe w połączeniu z SQL. Można zdefiniować procedurę składowaną, która pobiera argumenty od użytkownika, a następnie przekazać te argumenty do skryptu R jako zmienne.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- Pierwszy wiersz definiuje każdy z parametrów wejściowych SQL, które są wymagane podczas wykonywania procedury składowanej.

- Wiersz rozpoczynający `@params` się od definiuje wszystkie zmienne używane przez kod Języka R i odpowiednie typy danych SQL.

- Wiersze, które natychmiast po mapują nazwy parametrów SQL do odpowiednich nazw zmiennych R.

Teraz, gdy funkcja R została opakowana w procedurę składowaną, można łatwo wywołać funkcję i przekazać różne wartości, takie jak:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Używanie funkcji narzędzia R do rozwiązywania problemów

Pakiet, `utils` zainstalowany domyślnie, zapewnia wiele funkcji narzędziowych do badania bieżącego środowiska R. Te funkcje mogą być przydatne, jeśli znajdziesz rozbieżności w sposobie wykonywania kodu języka R w języku SQL i w środowiskach zewnętrznych. Na przykład można użyć `memory.limit()` funkcji R, aby uzyskać pamięć dla bieżącego środowiska R.

Ponieważ `utils` pakiet jest zainstalowany, ale nie jest `library()` ładowany domyślnie, należy użyć funkcji, aby załadować go najpierw.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Wielu użytkowników lubi używać funkcji chronometrażu `system.time` `proc.time`systemu w R, takich jak i , aby uchwycić czas używany przez procesy R i analizować problemy z wydajnością.

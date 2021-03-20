---
title: Udostępniona baza danych
description: Usługa Azure Synapse Analytics oferuje współużytkowany model metadanych, w którym tworzenie bazy danych w puli bezserwerowej Apache Spark będzie dostępne z poziomu puli SQL bezserwerowej i aparatów puli SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934972"
---
# <a name="azure-synapse-analytics-shared-database"></a>Udostępniona baza danych usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obszarów roboczych obliczeniowych w celu udostępniania baz danych i tabel. Obecnie bazy danych i tabele Parquet utworzone w pulach Apache Spark są automatycznie udostępniane z aparatem puli SQL bezserwerowym.

Baza danych utworzona za pomocą zadania Spark stanie się widoczna z tą samą nazwą dla wszystkich bieżących i przyszłych pul platformy Spark w obszarze roboczym, w tym bezserwerowego aparatu puli SQL. Nie można dodawać obiektów niestandardowych (tabel zewnętrznych, widoków, procedur) bezpośrednio w tej zreplikowanej bazie danych przy użyciu puli SQL bezserwerowej.

Domyślna baza danych Spark, wywołana `default` , będzie również widoczna w kontekście puli SQL bezserwerowej jako baza danych o nazwie `default` .

Ze względu na to, że bazy danych są synchronizowane z bezserwerową pulą SQL, nastąpi opóźnienie do momentu ich wyświetlenia.

## <a name="manage-a-spark-created-database"></a>Zarządzanie bazą danych utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Można na przykład usunąć ją za pośrednictwem zadania puli platformy Spark i utworzyć w niej tabele z platformy Spark.

W przypadku tworzenia obiektów w bazie danych platformy Spark przy użyciu puli SQL bezserwerowej lub próby porzucenia bazy danych operacja zakończy się pomyślnie. Jednak oryginalna baza danych Spark nie zostanie zmieniona.

## <a name="how-name-conflicts-are-handled"></a>Jak są obsługiwane konflikty nazw

Jeśli nazwa bazy danych Spark jest w konflikcie z nazwą istniejącej bezserwerowej bazy danych puli SQL, w bezserwerowej puli SQL do bazy danych Spark zostaje dołączony sufiks. Sufiks w puli SQL bezserwerowej to `_<workspace name>-ondemand-DefaultSparkConnector` .

Na przykład jeśli baza danych Spark o nazwie Get została `mydb` utworzona w obszarze roboczym usługi Azure Synapse, `myws` a bezserwerowa baza danych puli SQL o tej nazwie już istnieje, to baza danych Spark w puli SQL bezserwerowej będzie musiała zostać przywoływana przy użyciu nazwy `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Przestroga: nie należy podejmować zależności od tego zachowania.

## <a name="security-model"></a>Model zabezpieczeń

Bazy danych i tabele platformy Spark wraz z synchronizowanymi reprezentacjami w aparacie SQL zostaną zabezpieczone na podstawowym poziomie magazynu.

Podmiot zabezpieczeń, który tworzy bazę danych, jest traktowany jako właściciel tej bazy danych i ma wszystkie prawa do bazy danych i jej obiektów.

Aby udzielić podmiotu zabezpieczeń, takiego jak użytkownik lub grupa zabezpieczeń, uzyskać dostęp do bazy danych, należy udostępnić plik w formacie POSIX i uprawnienia do plików w katalogu i plikach `warehouse` . 

Na przykład, aby podmiot zabezpieczeń mógł odczytać tabelę w bazie danych, wszystkie foldery, które rozpoczynają się w folderze bazy danych w katalogu, `warehouse` muszą mieć `X` `R` przypisane uprawnienia i uprawnień do tego podmiotu zabezpieczeń. Ponadto pliki (takie jak bazowe pliki danych tabeli) wymagają `R` uprawnień. 

Jeśli podmiot zabezpieczeń wymaga możliwości tworzenia obiektów lub upuszczania obiektów w bazie danych, `W` wymagane są dodatkowe uprawnienia do folderów i plików w `warehouse` folderze.

## <a name="examples"></a>Przykłady

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Tworzenie bazy danych Spark i nawiązywanie z nią połączenia przy użyciu puli SQL bezserwerowej

Najpierw utwórz nową bazę danych Spark o nazwie `mytestdb` przy użyciu klastra Spark, który został już utworzony w obszarze roboczym. Można to zrobić na przykład przy użyciu notesu platformy Spark w języku C# z następującą instrukcją programu .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krótkim opóźnieniu można zobaczyć bazę danych z puli SQL bezserwerowej. Na przykład Uruchom poniższą instrukcję z puli SQL bezserwerowej.

```sql
SELECT * FROM sys.databases;
```

Sprawdź, czy `mytestdb` jest uwzględniony w wynikach.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych metadanych usługi Azure Synapse Analytics](overview.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)

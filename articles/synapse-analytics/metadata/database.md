---
title: Udostępniona baza danych usługi Azure Synapse Analytics
description: Usługa Azure Synapse Analytics udostępnia model metadanych udostępnionych, w którym tworzenie bazy danych w programie Apache Spark spowoduje, że będzie ona dostępna z aparatów sql na żądanie (wersja zapoznawcza) i puli SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424146"
---
# <a name="azure-synapse-analytics-shared-database"></a>Udostępniona baza danych usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia różnym obliczeniowym aparatom obszarów roboczych udostępnianie baz danych i tabel między pulami platformy Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pulami SQL.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Baza danych utworzona za pomocą zadania platformy Spark stanie się widoczna o tej samej nazwie dla wszystkich bieżących i przyszłych pul platformy Spark (wersja zapoznawcza) w obszarze roboczym, a także aparat SQL na żądanie.

Jeśli w obszarze roboczym znajdują się pule SQL, w których jest włączona synchronizacja metadanych lub jeśli utworzysz nową pulę SQL z włączoną synchronizacją metadanych, te utworzone przez platformę Spark bazy danych są automatycznie mapowane na specjalne schematy w bazie danych puli SQL. 

Każdy schemat nosi nazwę bazy danych Spark `$` z dodatkowym prefiksem. Zarówno tabele zewnętrzne, jak i zarządzane w bazie danych generowanej przez platformę Spark są udostępniane jako tabele zewnętrzne w odpowiednim schemacie specjalnym.

Domyślna baza danych `default`Platformy Spark, o nazwie , będzie również widoczna w kontekście SQL na żądanie jako baza danych o `default` `$default`nazwie , a w dowolnej bazie danych puli SQL z synchronizacją metadanych włączona jako schemat .

Ponieważ bazy danych są synchronizowane z SQL na żądanie i pul SQL asynchronicznie, będzie opóźnienie, dopóki się nie pojawią.

## <a name="manage-a-spark-created-database"></a>Zarządzanie bazą danych utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Na przykład usuń go za pomocą zadania puli platformy Spark i utwórz w nim tabele z platformy Spark.

Jeśli tworzysz obiekty w bazie danych utworzonej przez program Spark przy użyciu języka SQL na żądanie lub spróbujesz usunąć bazę danych, operacja zakończy się pomyślnie. Ale oryginalna baza danych Platformy Spark nie zostanie zmieniona.

Jeśli spróbujesz upuścić zsynchronizowany schemat w puli SQL lub spróbuj utworzyć w nim tabelę, platforma Azure zwróci błąd.

## <a name="handling-of-name-conflicts"></a>Obsługa konfliktów nazw

Jeśli nazwa bazy danych Platformy Spark jest w konflikcie z nazwą istniejącej bazy danych SQL na żądanie, sufiks jest dołączany do bazy danych SQL na żądanie do bazy danych Platformy Spark. Sufiks w języku SQL `_<workspace name>-ondemand-DefaultSparkConnector`na żądanie to .

Na przykład jeśli baza `mydb` danych Spark o nazwie zostanie `myws` utworzona w obszarze roboczym Usługi Azure Synapse i baza danych SQL na żądanie o tej `mydb_myws-ondemand-DefaultSparkConnector`nazwie już istnieje, wówczas baza danych Spark w języku SQL na żądanie będzie musiała być przywoływana przy użyciu nazwy .

> [!CAUTION]
> Uwaga: Nie należy przyjmować zależności od tego zachowania.

## <a name="security-model"></a>Model zabezpieczeń

Spark baz danych i tabel, wraz z ich zsynchronizowane reprezentacje w aparatach SQL zostaną zabezpieczone na poziomie magazynu podstawowej.

Podmiot zabezpieczeń, który tworzy bazę danych jest uważany za właściciela tej bazy danych i ma wszystkie prawa do bazy danych i jej obiektów.

Aby udzielić podmiotowi zabezpieczeń, takiemu jak użytkownik lub grupa zabezpieczeń, dostępu do bazy danych, podaj odpowiedni `warehouse` folder POSIX i uprawnienia do plików do podstawowych folderów i plików w katalogu. 

Na przykład, aby podmiot zabezpieczeń mógł odczytać tabelę w bazie danych, wszystkie foldery rozpoczynające się w folderze bazy danych w `warehouse` katalogu muszą mieć `X` uprawnienia i `R` uprawnienia przypisane do tego podmiotu zabezpieczeń. Ponadto pliki (takie jak podstawowe pliki danych tabeli) wymagają `R` uprawnień. 

Jeśli podmiot zabezpieczeń wymaga możliwości tworzenia obiektów lub upuszczania obiektów w bazie danych, dodatkowe `W` `warehouse` uprawnienia są wymagane w folderach i plikach w folderze.

## <a name="examples"></a>Przykłady

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Tworzenie & łączenie się z bazą danych Spark — SQL na żądanie

Najpierw utwórz nową `mytestdb` bazę danych platformy Spark o nazwie przy użyciu klastra platformy Spark, który został już utworzony w obszarze roboczym. Można to osiągnąć, na przykład przy użyciu notesu programu Spark C# z następującą instrukcją .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krótkim opóźnieniu można wyświetlić bazę danych z SQL na żądanie. Na przykład uruchom następującą instrukcję z SQL na żądanie.

```sql
SELECT * FROM sys.databases;
```

Sprawdź, `mytestdb` czy jest uwzględniony w wynikach.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Udostępnianie bazy danych platformy Spark w puli SQL

Z bazy danych utworzone w poprzednim przykładzie, teraz utworzyć `mysqlpool` pulę SQL w obszarze roboczym o nazwie, która umożliwia synchronizację metadanych.

Uruchom następującą instrukcję względem puli `mysqlpool` SQL:

```sql
SELECT * FROM sys.schema;
```

Sprawdź schemat nowo utworzonej bazy danych w wynikach.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych metadanych usługi Azure Synapse Analytics](overview.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->

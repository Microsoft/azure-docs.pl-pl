---
title: Udostępniona baza danych usługi Azure Synapse Analytics
description: Usługa Azure Synapse Analytics oferuje współużytkowany model metadanych, przy użyciu którego tworzenie bazy danych w Apache Spark spowoduje udostępnienie jej z poziomu usług SQL na żądanie (wersja zapoznawcza) i aparatów puli SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424146"
---
# <a name="azure-synapse-analytics-shared-database"></a>Udostępniona baza danych usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics pozwala różnym aparatom obszarów roboczych obliczeniowych na udostępnianie baz danych i tabel między jej pulami usługi Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pulami SQL.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Baza danych utworzona za pomocą zadania Spark stanie się widoczna z tą samą nazwą dla wszystkich bieżących i przyszłych pul platformy Spark (wersja zapoznawcza) w obszarze roboczym, a także aparatu SQL na żądanie.

Jeśli w obszarze roboczym znajdują się pule SQL, w których włączono synchronizację metadanych, lub jeśli utworzysz nową pulę SQL z włączoną synchronizacją metadanych, te utworzone bazy danych platformy Spark są automatycznie mapowane na specjalne schematy w bazie danych puli SQL. 

Każdy schemat nosi nazwę po nazwie bazy danych Spark z dodatkowym `$` prefiksem. Zarówno tabela zewnętrzna, jak i zarządzana w bazie danych generowanej przez platformę Spark są udostępniane jako tabele zewnętrzne w odpowiednim schemacie specjalnym.

Domyślna baza danych Spark, wywołana `default`, będzie również widoczna w kontekście SQL na żądanie jako baza danych o nazwie `default`i w dowolnych bazach danych puli SQL z włączoną synchronizacją metadanych jako schemat. `$default`

Ponieważ bazy danych są synchronizowane z usługą SQL na żądanie i w pulach SQL asynchronicznie, nastąpi opóźnienie do momentu ich wyświetlenia.

## <a name="manage-a-spark-created-database"></a>Zarządzanie bazą danych utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Można na przykład usunąć ją za pośrednictwem zadania puli platformy Spark i utworzyć w niej tabele z platformy Spark.

W przypadku tworzenia obiektów w bazie danych platformy Spark przy użyciu programu SQL na żądanie lub próby porzucenia bazy danych operacja zakończy się pomyślnie. Jednak oryginalna baza danych Spark nie zostanie zmieniona.

Jeśli spróbujesz porzucić zsynchronizowany schemat w puli SQL lub spróbować utworzyć w niej tabelę, platforma Azure zwróci błąd.

## <a name="handling-of-name-conflicts"></a>Obsługa konfliktów nazw

Jeśli nazwa bazy danych Spark powoduje konflikt z nazwą istniejącej bazy danych SQL na żądanie, sufiks jest dołączany na żądanie SQL do bazy danych Spark. Sufiks w SQL na żądanie ma wartość `_<workspace name>-ondemand-DefaultSparkConnector`.

Na przykład jeśli baza danych Spark o nazwie `mydb` Get została utworzona w obszarze roboczym `myws` usługi Azure Synapse, a baza danych SQL na żądanie o tej nazwie już istnieje, to baza danych Spark na żądanie będzie musiała zostać przywoływana przy `mydb_myws-ondemand-DefaultSparkConnector`użyciu nazwy.

> [!CAUTION]
> Przestroga: nie należy podejmować zależności od tego zachowania.

## <a name="security-model"></a>Model zabezpieczeń

Bazy danych i tabele platformy Spark wraz z synchronizowanymi reprezentacjami w aparatach SQL zostaną zabezpieczone na podstawowym poziomie magazynu.

Podmiot zabezpieczeń, który tworzy bazę danych, jest traktowany jako właściciel tej bazy danych i ma wszystkie prawa do bazy danych i jej obiektów.

Aby udzielić podmiotu zabezpieczeń, takiego jak użytkownik lub grupa zabezpieczeń, uzyskać dostęp do bazy danych, należy udostępnić plik w formacie POSIX i uprawnienia do plików w `warehouse` katalogu i plikach. 

Na przykład, aby podmiot zabezpieczeń mógł odczytać tabelę w bazie danych, wszystkie foldery, które rozpoczynają się w folderze bazy danych w `warehouse` katalogu, muszą mieć `X` przypisane uprawnienia i `R` uprawnień do tego podmiotu zabezpieczeń. Ponadto pliki (takie jak bazowe pliki danych tabeli) wymagają `R` uprawnień. 

Jeśli podmiot zabezpieczeń wymaga możliwości tworzenia obiektów lub upuszczania obiektów w bazie danych, wymagane są dodatkowe `W` uprawnienia do folderów i plików w `warehouse` folderze.

## <a name="examples"></a>Przykłady

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Tworzenie & łączenie z bazą danych platformy Spark — SQL na żądanie

Najpierw utwórz nową bazę danych Spark o `mytestdb` nazwie przy użyciu klastra Spark, który został już utworzony w obszarze roboczym. Można to zrobić na przykład przy użyciu notesu platformy Spark w języku C# z następującą instrukcją programu .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krótkim opóźnieniu można zobaczyć bazę danych z usługi SQL na żądanie. Na przykład Uruchom poniższą instrukcję z poziomu usługi SQL na żądanie.

```sql
SELECT * FROM sys.databases;
```

Sprawdź, `mytestdb` czy jest uwzględniony w wynikach.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Uwidacznianie bazy danych Spark w puli SQL

Baza danych utworzona w poprzednim przykładzie umożliwia teraz utworzenie puli SQL w obszarze roboczym o nazwie `mysqlpool` , która umożliwia synchronizację metadanych.

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

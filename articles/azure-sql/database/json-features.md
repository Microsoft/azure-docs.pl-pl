---
title: Praca z danymi w formacie JSON
description: Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają analizowanie, wykonywanie zapytań i formatowanie danych w notacji JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782805"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Wprowadzenie do funkcji JSON w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają analizowanie i wykonywanie zapytań o dane reprezentowane w formacie JavaScript Object Notation [(JSON)](https://www.json.org/) i eksportowanie danych relacyjnych jako tekstu JSON. Dostępne są następujące scenariusze JSON:

- [Formatowanie danych relacyjnych w formacie JSON](#formatting-relational-data-in-json-format) przy użyciu `FOR JSON` klauzuli.
- [Praca z danymi w formacie JSON](#working-with-json-data)
- [Wykonywanie zapytań dotyczących danych JSON](#querying-json-data) przy użyciu funkcji skalarnych JSON.
- [Przekształcanie kodu JSON do formatu tabelarycznego](#transforming-json-into-tabular-format) za pomocą `OPENJSON` funkcji.

## <a name="formatting-relational-data-in-json-format"></a>Formatowanie danych relacyjnych w formacie JSON

Jeśli masz usługę sieci Web, która pobiera dane z warstwy bazy danych i zawiera odpowiedź w formacie JSON lub struktury lub biblioteki JavaScript po stronie klienta, które akceptują dane sformatowane jako JSON, możesz sformatować zawartość bazy danych jako plik JSON bezpośrednio w zapytaniu SQL. Nie trzeba już pisać kodu aplikacji, który formatuje wyniki z Azure SQL Database lub wystąpienia zarządzanego Azure SQL jako JSON, lub dołączać bibliotekę serializacji JSON do konwersji wyników zapytania tabelarycznego, a następnie serializować obiekty do formatu JSON. Zamiast tego można użyć klauzuli FOR JSON, aby sformatować wyniki zapytania SQL jako plik JSON i używać go bezpośrednio w aplikacji.

W poniższym przykładzie wiersze z tabeli Sales. Customer są sformatowane jako JSON przy użyciu klauzuli FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzula FOR JSON PATH formatuje wyniki zapytania jako tekst JSON. Nazwy kolumn są używane jako klucze, podczas gdy wartości komórek są generowane jako wartości JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Zestaw wyników jest sformatowany jako tablica JSON, w której każdy wiersz jest sformatowany jako oddzielny obiekt JSON.

ŚCIEŻKA wskazuje, że można dostosować format danych wyjściowych wyniku JSON przy użyciu notacji kropkowej w aliasach kolumn. Następujące zapytanie zmienia nazwę klucza "CustomerName" w wyjściowym formacie JSON i umieszcza numery telefonów i faksów w podobiektie "Contact":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Dane wyjściowe tego zapytania wyglądają następująco:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

W tym przykładzie został zwrócony pojedynczy obiekt JSON zamiast tablicy przez określenie opcji [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) . Możesz użyć tej opcji, Jeśli wiesz, że zwracasz pojedynczy obiekt w wyniku zapytania.

Główną wartością klauzuli FOR JSON jest możliwość zwrócenia złożonych danych hierarchicznych z bazy danych sformatowane jako zagnieżdżone obiekty JSON lub tablice. Poniższy przykład pokazuje, jak uwzględnić wiersze z `Orders` tabeli, która należy do `Customer` jako zagnieżdżoną tablicę `Orders` :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Zamiast wysyłać oddzielne zapytania w celu uzyskania danych klienta, a następnie pobierania listy powiązanych zamówień, można uzyskać wszystkie niezbędne dane za pomocą pojedynczego zapytania, jak pokazano w następujących przykładowych danych wyjściowych:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Praca z danymi w formacie JSON

Jeśli nie masz ściśle uporządkowanych danych, jeśli masz złożone obiekty podrzędne, tablice lub dane hierarchiczne lub jeśli Twoje struktury danych są rozwijane w czasie, format JSON może pomóc w zaprezentowaniu dowolnej złożonej struktury danych.

JSON to format tekstowy, który może być używany jak każdy inny typ ciągu w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL. Dane JSON można wysyłać lub przechowywać jako standardowy NVARCHAR:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Dane JSON używane w tym przykładzie są reprezentowane przy użyciu typu NVARCHAR (MAX). KOD JSON można wstawić do tej tabeli lub podać jako argument procedury składowanej przy użyciu standardowej składni języka Transact-SQL, jak pokazano w następującym przykładzie:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Wszystkie języki lub biblioteki po stronie klienta, które współdziałają z danymi ciągu w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL, będą również działać z danymi JSON. Dane JSON mogą być przechowywane w dowolnej tabeli, która obsługuje typ NVARCHAR, taki jak tabela zoptymalizowana pod kątem pamięci lub tabela z systemową obsługą wersji. Kod JSON nie wprowadza żadnego ograniczenia w kodzie po stronie klienta lub w warstwie bazy danych.

## <a name="querying-json-data"></a>Wykonywanie zapytania dotyczącego danych JSON

Jeśli dane są sformatowane w formacie JSON przechowywanym w tabelach SQL platformy Azure, funkcja JSON pozwala używać tych danych w dowolnym zapytaniu SQL.

Funkcje JSON, które są dostępne w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL, umożliwiają traktowanie danych w formacie JSON jako dowolnego innego typu danych SQL. Można łatwo wyodrębnić wartości z tekstu JSON i użyć danych JSON w dowolnych zapytaniach:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkcja JSON_VALUE wyodrębnia wartość z tekstu JSON przechowywanego w kolumnie dane. Ta funkcja używa ścieżki podobnej do języka JavaScript, aby odwołać wartość w tekście JSON do wyodrębnienia. Wyodrębnionej wartości można użyć w dowolnej części zapytania SQL.

Funkcja JSON_QUERY jest podobna do JSON_VALUE. W przeciwieństwie do JSON_VALUE, ta funkcja wyodrębnia złożone obiekty podrzędne, takie jak tablice lub obiekty, które są umieszczane w tekście JSON.

Funkcja JSON_MODIFY pozwala określić ścieżkę wartości w tekście JSON, która ma zostać zaktualizowana, a także nową wartość, która spowoduje zastąpienie Starego. W ten sposób można łatwo aktualizować tekst JSON bez konieczności przeanalizowania całej struktury.

Ponieważ kod JSON jest przechowywany w tekście standardowym, nie ma gwarancji, że wartości przechowywane w kolumnach tekstowych są poprawnie sformatowane. Można sprawdzić, czy tekst przechowywany w kolumnie JSON jest poprawnie sformatowany przy użyciu standardowych ograniczeń check Azure SQL Database i funkcji isjson:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Jeśli tekst wejściowy jest poprawnie sformatowany w formacie JSON, funkcja isjson zwraca wartość 1. W każdym wstawieniu lub aktualizacji kolumny JSON to ograniczenie sprawdzi, czy nowa wartość tekstowa nie jest źle sformułowanym kodem JSON.

## <a name="transforming-json-into-tabular-format"></a>Przekształcanie kodu JSON w format tabelaryczny

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają również przekształcanie kolekcji JSON w formacie tabelarycznym oraz ładowanie i wykonywanie zapytań dotyczących danych JSON.

OPENJSON to funkcja Table-Value, która analizuje tekst JSON, lokalizuje tablicę obiektów JSON, iteruje elementy tablicy i zwraca jeden wiersz w wyniku danych wyjściowych dla każdego elementu tablicy.

![Tabelaryczny JSON](./media/json-features/image_2.png)

W powyższym przykładzie możemy określić, gdzie należy zlokalizować tablicę JSON, która ma zostać otwarta (w $. Ścieżka Orders), jakie kolumny należy zwrócić jako wynik i gdzie można znaleźć wartości JSON, które będą zwracane jako komórki.

Możemy przekształcić tablicę JSON w @orders zmiennej na zestaw wierszy, analizować ten zestaw wyników lub wstawiać wiersze do tabeli standardowej:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Kolekcja zamówień sformatowana jako tablica JSON i podana jako parametr procedury składowanej można analizować i wstawiać do tabeli Orders.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zintegrować kod JSON z aplikacją, zapoznaj się z następującymi zasobami:

Aby dowiedzieć się więcej na temat różnych scenariuszy integracji kodu JSON z aplikacją, zobacz pokazy w tym [filmie wideo kanału 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) lub Znajdź scenariusz pasujący do przypadku użycia w [wpisach w blogu JSON](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases).
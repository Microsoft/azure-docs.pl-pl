---
title: Praca z danymi w formacie JSON
description: Usługa Azure SQL Database umożliwia analizowanie, wykonywanie zapytań i formatowanie danych w notacji notacji obiektu JavaScript (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 992c981d49e7c6fbf8b6156570f6554a05caab5d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687755"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Wprowadzenie do funkcji JSON w bazie danych SQL usługi Azure
Usługa Azure SQL Database umożliwia analizowanie i wykonywanie zapytań danych reprezentowanych w formacie notacji obiektów JavaScript [(JSON)](https://www.json.org/) oraz eksportowanie danych relacyjnych jako tekstu JSON. Następujące scenariusze JSON są dostępne w usłudze Azure SQL Database:
- [Formatowanie danych relacyjnych w formacie JSON](#formatting-relational-data-in-json-format) przy użyciu `FOR JSON` klauzuli.
- [Praca z danymi w formacie JSON](#working-with-json-data)
- [Wykonywanie zapytań o dane JSON](#querying-json-data) przy użyciu funkcji skalarnych JSON.
- [Przekształcanie JSON w format tabelaryczny](#transforming-json-into-tabular-format) za pomocą `OPENJSON` funkcji.

## <a name="formatting-relational-data-in-json-format"></a>Formatowanie danych relacyjnych w formacie JSON
Jeśli masz usługę sieci web, która pobiera dane z warstwy bazy danych i zapewnia odpowiedź w formacie JSON lub po stronie klienta javascript struktur lub bibliotek, które akceptują dane sformatowane jako JSON, można sformatować zawartość bazy danych jako JSON bezpośrednio w kwerendzie SQL. Nie trzeba już pisać kodu aplikacji, który formatuje wyniki z usługi Azure SQL Database jako JSON lub dołączyć niektóre biblioteki serializacji JSON do konwersji wyników kwerendy tabelaryczne, a następnie serializacji obiektów do formatu JSON. Zamiast tego można użyć klauzuli FOR JSON do formatowania wyników zapytań SQL jako JSON w usłudze Azure SQL Database i używać go bezpośrednio w aplikacji.

W poniższym przykładzie wiersze z tabeli Sales.Customer są formatowane jako JSON przy użyciu klauzuli FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzula FOR JSON PATH formatuje wyniki kwerendy jako tekst JSON. Nazwy kolumn są używane jako klucze, podczas gdy wartości komórek są generowane jako wartości JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Zestaw wyników jest formatowany jako tablica JSON, gdzie każdy wiersz jest sformatowany jako oddzielny obiekt JSON.

Ścieżka wskazuje, że można dostosować format wyjściowy wyniku JSON za pomocą notacji kropkowej w aliasach kolumn. Następująca kwerenda zmienia nazwę klucza "CustomerName" w wyjściowym formacie JSON i umieszcza numery telefonów i faksów w podobiekcie "Kontakt":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Dane wyjściowe tej kwerendy wygląda następująco:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

W tym przykładzie zwróciliśmy pojedynczy obiekt JSON zamiast tablicy, określając opcję [WITHOUT_ARRAY_WRAPPER.](https://msdn.microsoft.com/library/mt631354.aspx) Tej opcji można użyć, jeśli wiadomo, że zwracasz pojedynczy obiekt w wyniku kwerendy.

Główną wartością klauzuli FOR JSON jest to, że umożliwia zwracanie złożonych danych hierarchicznych z bazy danych sformatowanych jako zagnieżdżone obiekty JSON lub tablice. W poniższym przykładzie pokazano, jak `Orders` uwzględnić wiersze z tabeli, które należą do `Customer` tablicy zagnieżdżonej: `Orders`

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Zamiast wysyłać oddzielne zapytania, aby pobrać dane klienta, a następnie pobrać listę powiązanych zamówień, można uzyskać wszystkie niezbędne dane za pomocą jednego zapytania, jak pokazano w poniższym przykładowym wyjściu:

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
Jeśli nie masz ściśle ustrukturyzowanych danych, jeśli masz złożone podniemiarze, tablice lub dane hierarchiczne lub jeśli struktury danych ewoluują w czasie, format JSON może pomóc w reprezentowaniu dowolnej złożonej struktury danych.

JSON jest formatem tekstowym, który może być używany jak każdy inny typ ciągu w usłudze Azure SQL Database. Dane JSON można wysyłać lub przechowywać w standardzie NVARCHAR:

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

Dane JSON używane w tym przykładzie są reprezentowane przy użyciu typu NVARCHAR(MAX). JSON można wstawić do tej tabeli lub podać jako argument procedury składowanej przy użyciu standardowej składni Transact-SQL, jak pokazano w poniższym przykładzie:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Każdy język lub biblioteka po stronie klienta, który współpracuje z danymi ciągu w usłudze Azure SQL Database będzie również współpracować z danymi JSON. JSON mogą być przechowywane w dowolnej tabeli, która obsługuje typ NVARCHAR, takich jak tabela zoptymalizowana pod kątem pamięci lub tabeli wersji systemowej. JSON nie wprowadza żadnych ograniczeń ani w kodzie po stronie klienta lub w warstwie bazy danych.

## <a name="querying-json-data"></a>Wykonywanie zapytań o dane JSON
Jeśli masz dane sformatowane jako JSON przechowywane w tabelach SQL platformy Azure, funkcje JSON umożliwiają użycie tych danych w dowolnej kwerendzie SQL.

Funkcje JSON, które są dostępne w bazie danych SQL platformy Azure umożliwiają traktowanie danych sformatowanych jako JSON jako innego typu danych SQL. Można łatwo wyodrębnić wartości z tekstu JSON i użyć danych JSON w dowolnej kwerendzie:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkcja JSON_VALUE wyodrębnia wartość z tekstu JSON przechowywanego w kolumnie Dane. Ta funkcja używa ścieżki podobnej do języka JavaScript, aby odwołać się do wartości w tekście JSON w celu wyodrębnienia. Wyodrębniona wartość może być używana w dowolnej części kwerendy SQL.

Funkcja JSON_QUERY jest podobna do JSON_VALUE. W przeciwieństwie do JSON_VALUE ta funkcja wyodrębnia złożony obiekt podrzędny, taki jak tablice lub obiekty umieszczone w tekście JSON.

Funkcja JSON_MODIFY umożliwia określenie ścieżki wartości w tekście JSON, która ma zostać zaktualizowana, a także nową wartość, która zastąpi starą. W ten sposób można łatwo aktualizować tekst JSON bez ponownego ponaglenia całej struktury.

Ponieważ JSON jest przechowywany w tekście standardowym, nie ma żadnych gwarancji, że wartości przechowywane w kolumnach tekstowych są poprawnie sformatowane. Można sprawdzić, czy tekst przechowywany w kolumnie JSON jest poprawnie sformatowany przy użyciu standardowych ograniczeń sprawdzania bazy danych SQL azure i funkcji ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Jeśli tekst wejściowy jest poprawnie sformatowany JSON, funkcja ISJSON zwraca wartość 1. Przy każdym wstawieniu lub aktualizacji kolumny JSON to ograniczenie sprawdza, czy nowa wartość tekstowa nie jest zniekształcona JSON.

## <a name="transforming-json-into-tabular-format"></a>Przekształcanie JSON w format tabelaryczny
Usługa Azure SQL Database umożliwia również przekształcanie kolekcji JSON w format tabelaryczny i ładowanie lub wykonywanie zapytań o dane JSON.

OPENJSON jest funkcją wartości tabeli, która analizuje tekst JSON, lokalizuje tablicę obiektów JSON, iteruje za pośrednictwem elementów tablicy i zwraca jeden wiersz w wyniku wyjściowym dla każdego elementu tablicy.

![Tabelaryczny JSON](./media/sql-database-json-features/image_2.png)

W powyższym przykładzie możemy określić, gdzie należy zlokalizować tablicę JSON, która powinna zostać otwarta (w $. Ścieżka zamówień), jakie kolumny powinny być zwracane w wyniku i gdzie znaleźć wartości JSON, które zostaną zwrócone jako komórki.

Możemy przekształcić tablicę JSON @orders w zmiennej w zestaw wierszy, analizować ten zestaw wyników lub wstawić wiersze do standardowej tabeli:

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

Kolekcja zamówień sformatowanych jako tablica JSON i dostarczonych jako parametr procedury składowanej może być analizowana i wstawiana do tabeli Zamówienia.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zintegrować JSON z aplikacją, zapoznaj się z tymi zasobami:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Dokumentacja MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Kanał 9 wideo](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Aby dowiedzieć się więcej o różnych scenariuszach integracji JSON z aplikacją, zobacz wersje demonstracyjne w tym [klipie wideo kanału 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) lub znajdź scenariusz, który pasuje do twojego przypadku użycia w [postach json blogów](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).


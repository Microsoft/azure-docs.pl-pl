---
title: Stosowanie przekształcenia SQL
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu przekształcenie SQL w Azure Machine Learning, aby uruchomić zapytanie oprogramowania SQLite w wejściowych zestawach danych w celu przeprowadzenia transformacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 9a195497b4376633bd3c767d7d0ea029109fdf9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76314542"
---
# <a name="apply-sql-transformation"></a>Stosowanie przekształcenia SQL

W tym artykule opisano moduł programu Azure Machine Learning Designer.

Za pomocą modułu przekształcenie języka SQL można:
  
-   Utwórz tabele dla wyników i Zapisz zestawy danych w przenośnej bazie danych.  
  
-   Wykonaj niestandardowe przekształcenia w typach danych lub Utwórz agregacje.  
  
-   Wykonaj instrukcje zapytania SQL, aby filtrować lub modyfikować dane i zwracać wyniki zapytania jako tabelę danych.  

> [!IMPORTANT]
> Aparat SQL używany w tym module to **SQLite**. Aby uzyskać więcej informacji na temat składni oprogramowania SQLite, zobacz kod SQL, który jest [zrozumiały dla oprogramowania SQLite](https://www.sqlite.org/index.html) , aby uzyskać więcej informacji.  

## <a name="how-to-configure-apply-sql-transformation"></a>Jak skonfigurować zastosowanie transformacji SQL  

Moduł może przyjmować do trzech zestawów danych jako dane wejściowe. W przypadku odwoływania się do zestawów danych połączonych z każdym portem wejściowym należy użyć nazw `t1` , `t2` i `t3` . Numer tabeli wskazuje indeks portu wejściowego.  
  
Pozostała wartość parametru to zapytanie SQL, które używa składni programu SQLite. Wpisując wiele wierszy w polu tekstowym **skrypt SQL** , użyj średnika, aby zakończyć każdą instrukcję. W przeciwnym razie podziały wierszy są konwertowane na spacje.  

Ten moduł obsługuje wszystkie standardowe instrukcje składni programu SQLite. Listę nieobsługiwanych instrukcji można znaleźć w sekcji [Uwagi techniczne](#technical-notes) .

##  <a name="technical-notes"></a>Uwagi techniczne  

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

-   Dane wejściowe są zawsze wymagane na porcie 1.  
  
-   W przypadku identyfikatorów kolumn, które zawierają spację lub inne znaki specjalne, zawsze ujmij Identyfikator kolumny w nawiasy kwadratowe lub podwójne cudzysłowy w przypadku odwoływania się do kolumny w `SELECT` `WHERE` klauzulach or.  
  
### <a name="unsupported-statements"></a>Nieobsługiwane instrukcje  

Chociaż oprogramowanie SQLite obsługuje wiele standardów ANSI SQL, nie obejmuje wielu funkcji obsługiwanych przez komercyjne systemy relacyjnych baz danych. Aby uzyskać więcej informacji, zobacz [SQL jako zrozumiały dla oprogramowania SQLite](http://www.sqlite.org/lang.html). Należy również pamiętać o następujących ograniczeniach podczas tworzenia instrukcji SQL:  
  
- Program SQLite używa tekstu dynamicznego w przypadku wartości zamiast przypisywania typu do kolumny jako w większości systemów relacyjnej bazy danych. Jest on słabo wpisany i umożliwia niejawną konwersję typu.  
  
- `LEFT OUTER JOIN` jest zaimplementowany, ale nie `RIGHT OUTER JOIN` lub `FULL OUTER JOIN` .  

- `RENAME TABLE`Instrukcji i można użyć `ADD COLUMN` z `ALTER TABLE` poleceniem, ale inne klauzule nie są obsługiwane, w tym `DROP COLUMN` , `ALTER COLUMN` i `ADD CONSTRAINT` .  
  
- Możesz utworzyć widok w ramach oprogramowania SQLite, ale te widoki są tylko do odczytu. Nie można wykonać `DELETE` instrukcji, `INSERT` , lub `UPDATE` w widoku. Można jednak utworzyć wyzwalacz uruchamiany przy próbie `DELETE` , `INSERT` , lub `UPDATE` w widoku i wykonać inne operacje w treści wyzwalacza.  
  

Oprócz listy nieobsługiwanych funkcji dostępnych w oficjalnej witrynie programu SQLite, następująca witryna wiki zawiera listę innych nieobsługiwanych funkcji: [SQLite — nieobsługiwane SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

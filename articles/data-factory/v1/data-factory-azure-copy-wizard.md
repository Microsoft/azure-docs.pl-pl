---
title: Kreator kopiowania Data Factory platformy Azure
description: Informacje na temat używania Kreatora kopiowania Data Factory platformy Azure do kopiowania danych z obsługiwanych źródeł danych do ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2dfb5876922fd53c372afe82ecdfa843179fb135
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439014"
---
# <a name="azure-data-factory-copy-wizard"></a>Kreator kopiowania Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Kreator kopiowania Azure Data Factory ułatwia proces pozyskiwania danych, który jest zazwyczaj pierwszym krokiem w kompleksowym scenariuszu integracji danych. Podczas przechodzenia przez Kreatora kopiowania Azure Data Factory nie trzeba zrozumieć żadnych definicji JSON dla połączonych usług, zestawów danych i potoków. Kreator automatycznie tworzy potok w celu skopiowania danych z wybranego źródła danych do wybranego miejsca docelowego. Ponadto Kreator kopiowania pomaga sprawdzić poprawność pozyskanych danych podczas tworzenia. Oszczędza to czas, szczególnie w przypadku pozyskiwania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij kafelek **Kopiuj dane** na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Zaprojektowana do obsługi danych Big Data
Ten Kreator umożliwia łatwe przenoszenie danych z wielu źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu przez kreatora zostanie automatycznie utworzony potok z działaniem kopiowania, wraz z jednostkami zależnymi Data Factory (połączone usługi i zestawy danych). Do utworzenia potoku nie są wymagane żadne dodatkowe kroki.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku dotyczące tworzenia przykładowego potoku do kopiowania danych z obiektu blob platformy Azure do tabeli Azure SQL Database, zobacz [samouczek Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
>
>

Kreator został zaprojektowany z myślą o rozpoczęciu pracy z obsługą różnorodnych danych i typów obiektów. Można tworzyć potoki Data Factory, które przenoszą setki folderów, plików lub tabel. Kreator obsługuje automatyczne podglądy danych, przechwytywanie schematu i mapowanie oraz filtrowanie danych.

## <a name="automatic-data-preview"></a>Automatyczna wersja zapoznawcza danych
Możesz wyświetlić podgląd części danych z wybranego źródła danych, aby sprawdzić, czy dane są co chcesz skopiować. Ponadto, jeśli dane źródłowe są w pliku tekstowym, Kreator kopiowania analizuje plik tekstowy, aby poznać ograniczniki wierszy i kolumn i automatycznie.

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych może być niezgodny ze schematem danych wyjściowych w niektórych przypadkach. W tym scenariuszu należy zmapować kolumny ze schematu źródłowego do kolumn z schematu docelowego.

> [!TIP]
> W przypadku kopiowania danych z SQL Server lub Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse), jeśli tabela nie istnieje w magazynie docelowym, Data Factory obsługuje tworzenie tabel przy użyciu schematu źródła. Dowiedz się więcej od [przenoszenia danych do i z usługi Azure Synapse Analytics przy użyciu Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Użyj listy rozwijanej, aby wybrać kolumnę ze schematu źródłowego do zmapowania do kolumny w schemacie docelowym. Kreator kopiowania próbuje zrozumieć wzorzec dla mapowania kolumn. Stosuje ten sam wzorzec do reszty kolumn, dzięki czemu nie trzeba wybierać każdej z kolumn pojedynczo, aby ukończyć mapowanie schematu. Jeśli wolisz, możesz zastąpić te mapowania przy użyciu list rozwijanych, aby mapować kolumny jeden według jednego. Wzorzec jest dokładniejszy podczas mapowania większej liczby kolumn. Kreator kopiowania ciągle aktualizuje wzorzec i ostatecznie osiągnie prawy wzorzec dla mapowania kolumn, które mają być osiągnięte.     

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko te dane, które muszą zostać skopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL lub plików w folderze obiektów blob platformy Azure przy użyciu [funkcji Data Factory i zmiennych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytanie SQL przy użyciu `Text.Format` funkcji i `WindowStart` zmiennej.

![Weryfikuj wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
W ścieżce folderu można używać zmiennych do kopiowania danych z folderu określonego w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Obsługiwane są następujące zmienne: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{min}** i **{Custom}**. Na przykład: inputfolder/{Year}/{Month}/{Day}.

Załóżmy, że masz foldery wejściowe w następującym formacie:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** dla **pliku lub folderu**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02), a następnie kliknij pozycję **Wybierz**. Powinien być widoczny `2016/03/01/02` w polu tekstowym. Teraz Zastąp **2016** ciąg 2016 **{Year}**, **03** przez **{Month}**, **01** z **{Day}** i **02** z **{Hour}**, a następnie naciśnij klawisz **Tab** . Powinny pojawić się listy rozwijane, aby wybrać format dla tych czterech zmiennych:

![Używanie zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć zmiennej **niestandardowej** i dowolnego [obsługiwanego ciągu formatu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Aby wybrać folder z tą strukturą, najpierw Użyj przycisku **przeglądania** . Następnie Zastąp wartość **{Custom}**, a następnie naciśnij klawisz **Tab** , aby zobaczyć pole tekstowe, w którym można wpisać ciąg formatu.     

![Używanie zmiennej niestandardowej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można wykonać raz lub zgodnie z harmonogramem (co godzinę, codziennie itd.). Obie te opcje mogą służyć do szerokiego zakresu łączników w różnych środowiskach, w tym lokalnej, chmury i kopii na komputerze lokalnym.

Jednorazowa operacja kopiowania umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia kopiowanie danych zgodnie z określonym cyklem. Aby skonfigurować zaplanowaną kopię, można użyć zaawansowanych ustawień (takich jak ponowna próba, limit czasu i alerty).

![Właściwości planowania](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z krótkim przewodnikiem dotyczącym tworzenia potoku za pomocą działania kopiowania przy użyciu Kreatora kopiowania Data Factory, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

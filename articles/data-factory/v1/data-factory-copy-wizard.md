---
title: Łatwe kopiowanie danych za pomocą Kreatora kopiowania — Azure
description: Informacje na temat używania Kreatora kopiowania Data Factory do kopiowania danych z obsługiwanych źródeł danych do ujścia.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3686a22291e8595deabe3e207ce66744c3cdd82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376908"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Łatwe kopiowanie lub przenoszenie danych za pomocą Kreatora kopiowania Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 


Kreator kopiowania Azure Data Factory ma ułatwić proces pozyskiwania danych, który jest zazwyczaj pierwszym krokiem w kompleksowym scenariuszu integracji danych. Podczas przechodzenia przez Kreatora kopiowania Azure Data Factory nie trzeba zrozumieć żadnych definicji JSON dla połączonych usług, zestawów danych i potoków. Jednak po ukończeniu wszystkich kroków kreatora Kreator automatycznie utworzy potok w celu skopiowania danych z wybranego źródła danych do wybranego miejsca docelowego. Ponadto Kreator kopiowania pomaga sprawdzić poprawność pozyskanych danych podczas tworzenia, co pozwala zaoszczędzić dużo czasu, szczególnie w przypadku pozyskiwania danych po raz pierwszy ze źródła danych. Aby uruchomić Kreatora kopiowania, kliknij kafelek **Kopiuj dane** na stronie głównej fabryki danych.

![Kreator kopiowania](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuicyjny kreator służący do kopiowania danych
Ten Kreator umożliwia łatwe przenoszenie danych z wielu źródeł do miejsc docelowych w ciągu kilku minut. Po przejściu przez kreatora zostanie automatycznie utworzony potok z działaniem kopiowania, a także zależne jednostki Data Factory (połączone usługi i zestawy danych). Do utworzenia potoku nie są wymagane żadne dodatkowe kroki.   

![Wybieranie źródła danych](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku dotyczące tworzenia przykładowego potoku w celu kopiowania danych z obiektu blob platformy Azure do tabeli Azure SQL Database, zobacz artykuł poświęcony [kreatorowi kopiowania](data-factory-copy-data-wizard-tutorial.md) . 
> 
> 

Kreator został zaprojektowany z myślą o użyciu danych Big Data. Tworzenie potoków Data Factory i przenoszenie setek folderów, plików lub tabel przy użyciu kreatora Kopiowanie danych jest proste i wydajne. Kreator obsługuje następujące trzy funkcje: automatyczne Podgląd danych, przechwytywanie schematu i mapowanie oraz filtrowanie danych. 

## <a name="automatic-data-preview"></a>Automatyczna wersja zapoznawcza danych
Kreator kopiowania umożliwia przejrzenie części danych z wybranego źródła danych, aby sprawdzić, czy dane mają odpowiednie dane, które mają zostać skopiowane. Ponadto, jeśli dane źródłowe są w pliku tekstowym, Kreator kopiowania analizuje plik tekstowy, aby poznać ograniczniki wierszy i kolumn oraz automatycznie schemat. 

![Ustawienia formatu pliku](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Przechwytywanie schematu i mapowanie
Schemat danych wejściowych może być niezgodny ze schematem danych wyjściowych w niektórych przypadkach. W tym scenariuszu należy zmapować kolumny ze schematu źródłowego do kolumn z schematu docelowego. 

Kreator kopiowania automatycznie mapuje kolumny w schemacie źródłowym do kolumn w schemacie docelowym. Mapowania można zastąpić za pomocą listy rozwijanej (lub) określić, czy kolumna ma być pomijana podczas kopiowania danych.   

![Mapowanie schematu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrowanie danych
Kreator umożliwia filtrowanie danych źródłowych w celu wybrania tylko danych, które muszą zostać skopiowane do magazynu danych miejsca docelowego/ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL (lub) plików w folderze obiektów blob platformy Azure przy użyciu [funkcji Data Factory i zmiennych](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrowanie danych w bazie danych
W przykładzie zapytanie SQL używa `Text.Format` funkcji i `WindowStart` zmiennej. 

![Weryfikuj wyrażenia](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
W ścieżce folderu można używać zmiennych do kopiowania danych z folderu określonego w czasie wykonywania na podstawie [zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables). Obsługiwane są następujące zmienne: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{min}** i **{Custom}**. Przykład: inputfolder/{Year}/{Month}/{Day}.

Załóżmy, że masz foldery wejściowe w następującym formacie:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** dla **pliku lub folderu**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02), a następnie kliknij pozycję **Wybierz**. Powinien być widoczny `2016/03/01/02` w polu tekstowym. Teraz Zamień **2016** na **{Year}**, **03** z **{Month}**, **01** z **{Day}** i **02** z **{Hour}**, a następnie naciśnij klawisz Tab. Powinny pojawić się listy rozwijane, aby wybrać format dla tych czterech zmiennych:

![Używanie zmiennych systemowych](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak pokazano na poniższym zrzucie ekranu, można również użyć zmiennej **niestandardowej** i dowolnego [obsługiwanego ciągu formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Aby wybrać folder z tą strukturą, najpierw Użyj przycisku **przeglądania** . Następnie Zastąp wartość **{Custom}**, a następnie naciśnij klawisz Tab, aby zobaczyć pole tekstowe, w którym można wpisać ciąg formatu.     

![Używanie zmiennej niestandardowej](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Obsługa różnych danych i typów obiektów
Za pomocą Kreatora kopiowania można efektywnie przenosić setki folderów, plików lub tabel.

![Wybieranie tabel, z których mają zostać skopiowane dane](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można wykonać raz lub zgodnie z harmonogramem (co godzinę, codziennie itd.). Obie te opcje mogą służyć do szerokiego zakresu łączników w lokalnej, chmurze i kopii na pulpicie.

Jednorazowa operacja kopiowania umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia kopiowanie danych zgodnie z określonym cyklem. Aby skonfigurować zaplanowaną kopię, można użyć zaawansowanych ustawień (takich jak ponowna próba, limit czasu i alerty).

![Właściwości planowania](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z krótkim przewodnikiem dotyczącym tworzenia potoku za pomocą działania kopiowania przy użyciu Kreatora kopiowania Data Factory, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).
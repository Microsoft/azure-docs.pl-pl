---
title: Kopiowanie danych Azure Data Factory narzędzi
description: Zawiera informacje o narzędziu Kopiowanie danych w interfejsie użytkownika Azure Data Factory
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372998"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Narzędzie do kopiowanie danych w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Narzędzie Azure Data Factory Kopiowanie danych ułatwia i optymalizuje proces pozyskiwania danych w usłudze Data Lake, która jest zazwyczaj pierwszym krokiem w przypadku kompleksowego scenariusza integracji danych.  Oszczędność czasu, szczególnie w przypadku używania Azure Data Factory do pozyskiwania danych ze źródła danych po raz pierwszy. Oto niektóre zalety korzystania z tego narzędzia:

- Korzystając z narzędzia Kopiowanie danych Azure Data Factory, nie potrzebujesz zrozumienia Data Factory definicji dla połączonych usług, zestawów danych, potoków, działań i wyzwalaczy. 
- Przepływ narzędzia Kopiowanie danych jest intuicyjny do ładowania danych do usługi Data Lake. Narzędzie automatycznie tworzy wszystkie zasoby Data Factory niezbędne do skopiowania danych z wybranego źródłowego magazynu danych do wybranego magazynu danych miejsca docelowego/ujścia. 
- Narzędzie Kopiowanie danych pomaga weryfikować dane, które są pozyskiwane w momencie tworzenia, co pomaga uniknąć ewentualnych błędów na początku.
- Jeśli konieczne jest zaimplementowanie złożonej logiki biznesowej w celu załadowania danych do usługi Data Lake, nadal można edytować Data Factory zasoby utworzone przez narzędzie Kopiowanie danych przy użyciu funkcji tworzenia poszczególnych działań w Data Factory interfejsie użytkownika. 

W poniższej tabeli przedstawiono wskazówki dotyczące sytuacji, w których należy użyć narzędzia Kopiowanie danych a tworzenie poszczególnych działań w Data Factory interfejsie użytkownika: 

| Narzędzie do kopiowania danych | Tworzenie na działanie (działanie kopiowania) |
| -------------- | -------------------------------------- |
| Chcesz łatwo utworzyć zadanie ładowania danych bez uczenia się Azure Data Factory jednostek (połączone usługi, zestawy danych, potoki itp.). | Chcesz zaimplementować złożoną i elastyczną logikę do ładowania danych do usługi Lake. |
| Chcesz szybko załadować dużą liczbę artefaktów danych do sieci Data Lake. | Chcesz utworzyć łańcuch działania kopiowania z kolejnymi działaniami do czyszczenia lub przetwarzania danych. |

Aby uruchomić narzędzie Kopiowanie danych, kliknij kafelek **Kopiowanie danych** na stronie głównej fabryki danych.

![Wprowadzenie do narzędzia do Kopiowanie danych](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuicyjny przepływ do ładowania danych do usługi Data Lake
To narzędzie umożliwia łatwe przenoszenie danych z wielu źródeł do miejsc docelowych w ciągu kilku minut z intuicyjnym przepływem:  

1. Skonfiguruj ustawienia **źródłowe**.
2. Skonfiguruj ustawienia dla **miejsca docelowego**. 
3. Skonfiguruj **Ustawienia zaawansowane** dla operacji kopiowania, takie jak mapowanie kolumn, ustawienia wydajności i ustawienia odporności na uszkodzenia. 
4. Określ **harmonogram** zadania ładowania danych. 
5. Przejrzyj **podsumowanie** Data Factory jednostek, które mają zostać utworzone. 
6. **Edytuj** potok, aby zaktualizować ustawienia działania kopiowania zgodnie z wymaganiami. 

   Narzędzie zostało zaprojektowane z myślą o rozpoczęciu pracy z obsługą różnorodnych danych i typów obiektów. Można jej używać do przenoszenia setek folderów, plików lub tabel. Narzędzie obsługuje automatyczne Podgląd danych, przechwytywanie schematu i automatyczne mapowanie oraz filtrowanie danych.

![Narzędzie do kopiowania danych](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatyczna wersja zapoznawcza danych
Możesz wyświetlić podgląd danych z wybranego źródłowego magazynu danych, co umożliwia zweryfikowanie kopiowanych danych. Ponadto, jeśli dane źródłowe są w pliku tekstowym, narzędzie Kopiowanie danych analizuje plik tekstowy w celu automatycznego wykrycia ograniczników wierszy i kolumn oraz schemat.

![Ustawienia pliku](./media/copy-data-tool/file-format-settings.png)

Po wykryciu:

![Wykryto ustawienia plików i Podgląd](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Przechwytywanie schematu i automatyczne mapowanie
Schemat źródła danych może nie być taki sam jak schemat miejsca docelowego danych w wielu przypadkach. W tym scenariuszu należy zmapować kolumny ze schematu źródłowego do kolumn z schematu docelowego.

Narzędzie Kopiowanie danych służy do monitorowania i uczenia się zachowania podczas mapowania kolumn między magazynami źródłowymi i docelowymi. Po wybraniu jednej lub kilku kolumn z magazynu danych źródłowych i zamapowaniu ich na schemat docelowy narzędzie Kopiowanie danych rozpocznie analizowanie wzorców dla par kolumn pobranych z obu stron. Następnie stosuje ten sam wzorzec do reszty kolumn. W związku z tym zobaczysz, że wszystkie kolumny zostały zamapowane na miejsce docelowe w żądany sposób po kilku kliknięciach.  Jeśli nie masz zadowalającego wyboru mapowania kolumn dostarczonego przez narzędzie Kopiowanie danych, możesz je zignorować i kontynuować ręczne Mapowanie kolumn. W tym czasie narzędzie Kopiowanie danych ciągle uczy się i aktualizuje wzorzec, a ostatecznie osiągnie prawy wzorzec dla mapowania kolumn, które chcesz osiągnąć. 

> [!NOTE]
> W przypadku kopiowania danych z SQL Server lub Azure SQL Database do usługi Azure Synapse Analytics, jeśli tabela nie istnieje w magazynie docelowym, narzędzie Kopiowanie danych obsługuje tworzenie tabeli automatycznie przy użyciu schematu źródłowego. 

## <a name="filter-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko te dane, które muszą zostać skopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Narzędzie Kopiowanie danych zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL lub plików w folderze obiektów blob platformy Azure. 

### <a name="filter-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia zapytanie SQL do filtrowania danych.

![Filtrowanie danych w bazie danych](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
W ścieżce folderu można używać zmiennych do kopiowania danych z folderu. Obsługiwane są następujące zmienne: **{Year}**, **{Month}**, **{Day}**, **{Hour}** i **{min}**. Na przykład: inputfolder/{Year}/{Month}/{Day}. 

Załóżmy, że masz foldery wejściowe w następującym formacie: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** dla **pliku lub folderu**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02), a następnie kliknij pozycję **Wybierz**. W polu tekstowym powinna zostać wyświetlona wartość 2016/03/01/02. 

Następnie zastąp  wartość 2016 **{Year}**, **03** przez **{Month}**, **01** z **{Day}** i **02** z **{Hour}**, a następnie naciśnij klawisz **Tab** . Powinny pojawić się listy rozwijane, aby wybrać format dla tych czterech zmiennych:

![Filtruj plik lub folder](./media/copy-data-tool/filter-file-or-folder.png)

Narzędzie Kopiowanie danych generuje parametry z wyrażeniami, funkcjami i zmiennymi systemowymi, które mogą być używane do reprezentowania {Year}, {month}, {Day}, {Hour} i {min} podczas tworzenia potoku.

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można wykonać raz lub zgodnie z harmonogramem (co godzinę, codziennie itd.). Te opcje mogą być używane dla łączników w różnych środowiskach, w tym lokalnie, w chmurze i na pulpicie lokalnym. 

Jednorazowa operacja kopiowania umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma to zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia skopiowanie danych zgodnie z określonym cyklem. Aby skonfigurować zaplanowaną kopię, można użyć zaawansowanych ustawień (takich jak ponowna próba, limit czasu i alerty).

![Opcje planowania](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Następne kroki
Wypróbuj te samouczki korzystające z narzędzia Kopiowanie danych:

- [Szybki Start: Tworzenie fabryki danych przy użyciu narzędzia Kopiowanie danych](quickstart-create-data-factory-copy-data-tool.md)
- [Samouczek: kopiowanie danych na platformie Azure przy użyciu narzędzia Kopiowanie danych](tutorial-copy-data-tool.md) 
- [Samouczek: kopiowanie danych lokalnych na platformę Azure przy użyciu narzędzia Kopiowanie danych](tutorial-hybrid-copy-data-tool.md)

---
title: Kopiuj narzędzie danych Azure Data Factory
description: Zawiera informacje o narzędziu Kopiowanie danych w interfejsie użytkownika usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: df078673aed60086a88961ff64f9bfa596d96346
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414084"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Narzędzie do kopiowanie danych w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Narzędzie Azure Data Factory Copy Data ułatwia i optymalizuje proces pozyskiwania danych do usługi data lake, która jest zwykle pierwszym krokiem w scenariuszu integracji danych end-to-end.  Oszczędza czas, szczególnie w przypadku korzystania z usługi Azure Data Factory do pozyskiwania danych ze źródła danych po raz pierwszy. Niektóre z zalet korzystania z tego narzędzia są:

- Podczas korzystania z narzędzia Azure Data Factory Copy Data, nie trzeba zrozumieć definicje fabryki danych dla połączonych usług, zestawów danych, potoków, działań i wyzwalaczy. 
- Przepływ narzędzia Kopiuj dane jest intuicyjny do ładowania danych do usługi data lake. Narzędzie automatycznie tworzy wszystkie niezbędne zasoby fabryki danych, aby skopiować dane z wybranego magazynu danych źródłowych do wybranego magazynu danych docelowych/ujścia. 
- Narzędzie Kopiuj dane pomaga sprawdzić poprawność danych, które są pozyskiwania w momencie tworzenia, co pomaga uniknąć potencjalnych błędów na początku.
- Jeśli trzeba zaimplementować złożoną logikę biznesową, aby załadować dane do usługi data lake, nadal można edytować zasoby fabryki danych utworzone przez narzędzie Kopiuj dane przy użyciu tworzenia na działanie w interfejsie użytkownika fabryki danych. 

Poniższa tabela zawiera wskazówki dotyczące tego, kiedy należy używać narzędzia Kopiuj dane w porównaniu z tworzeniem na działanie w interfejsie użytkownika fabryki danych: 

| Narzędzie do kopiowania danych | Tworzenie na działanie (działanie kopiowania) |
| -------------- | -------------------------------------- |
| Chcesz łatwo utworzyć zadanie ładowania danych bez uczenia się o jednostkach usługi Azure Data Factory (połączone usługi, zestawy danych, potoki itp.) | Chcesz zaimplementować złożoną i elastyczną logikę ładowania danych do jeziora. |
| Chcesz szybko załadować dużą liczbę artefaktów danych do usługi data lake. | Chcesz łańcuch copy działania z kolejnych działań do czyszczenia lub przetwarzania danych. |

Aby uruchomić narzędzie Kopiuj dane, kliknij kafelek **Kopiuj dane** na stronie głównej fabryki danych.

![Strona Wprowadzenie — łącze do narzędzia Kopiuj dane](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuicyjny przepływ do ładowania danych do źródła danych
To narzędzie pozwala łatwo przenosić dane z wielu różnych źródeł do miejsc docelowych w ciągu kilku minut dzięki intuicyjnemu przepływowi:  

1. Skonfiguruj ustawienia **źródła**.
2. Skonfiguruj ustawienia **miejsca docelowego**. 
3. Skonfiguruj **zaawansowane ustawienia** operacji kopiowania, takie jak mapowanie kolumn, ustawienia wydajności i ustawienia odporności na uszkodzenia. 
4. Określ **harmonogram** zadania ładowania danych. 
5. Przejrzyj **podsumowanie** jednostek usługi Data Factory, które mają zostać utworzone. 
6. **Edytuj** potok, aby w razie potrzeby zaktualizować ustawienia działania kopiowania. 

   Narzędzie zostało zaprojektowane z myślą o dużych zbiorach danych od samego początku, z obsługą różnych typów danych i obiektów. Można go używać do przenoszenia setek folderów, plików lub tabel. Narzędzie obsługuje automatyczny podgląd danych, przechwytywanie schematu i automatyczne mapowanie, a także filtrowanie danych.

![Narzędzie do kopiowania danych](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatyczny podgląd danych
Można wyświetlić podgląd części danych z wybranego magazynu danych źródłowych, co pozwala na sprawdzenie poprawności kopiowanych danych. Ponadto jeśli dane źródłowe znajduje się w pliku tekstowym, narzędzie Kopiuj dane analizuje plik tekstowy, aby automatycznie wykryć ograniczniki wierszy i kolumn oraz schemat.

![Ustawienia plików](./media/copy-data-tool/file-format-settings.png)

Po wykryciu:

![Wykryte ustawienia pliku i podgląd](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Przechwytywanie schematu i automatyczne mapowanie
Schemat źródła danych może nie być taki sam jak schemat miejsca docelowego danych w wielu przypadkach. W tym scenariuszu należy mapować kolumny ze schematu źródłowego do kolumn ze schematu docelowego.

Narzędzie Kopiuj dane monitoruje i uczy się twojego zachowania podczas mapowania kolumn między magazynami źródłowymi i docelowymi. Po wybraniu jednej lub kilku kolumn z magazynu danych źródłowych i zamapowaniu ich na schemat docelowy narzędzie Kopiuj dane rozpocznie analizowanie wzorca dla par kolumn wybranych z obu stron. Następnie stosuje ten sam wzorzec do pozostałych kolumn. W związku z tym widzisz wszystkie kolumny zostały zamapowane do miejsca docelowego w sposób, który ma być tuż po kilku kliknięć.  Jeśli wybór mapowania kolumn jest dostarczany przez narzędzie Kopiuj dane, można go zignorować i kontynuować ręczne mapowanie kolumn. Tymczasem narzędzie Kopiuj dane stale uczy się i aktualizuje wzorzec, a ostatecznie osiąga odpowiedni wzorzec dla mapowania kolumn, które chcesz osiągnąć. 

> [!NOTE]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, narzędzie Kopiuj dane obsługuje automatyczne tworzenie tabeli przy użyciu schematu źródłowego. 

## <a name="filter-data"></a>Filtrowanie danych
Można filtrować dane źródłowe, aby wybrać tylko dane, które muszą zostać skopiowane do magazynu danych ujścia. Filtrowanie zmniejsza ilość danych do skopiowania do magazynu danych ujścia i w związku z tym zwiększa przepływność operacji kopiowania. Narzędzie Kopiuj dane zapewnia elastyczny sposób filtrowania danych w relacyjnej bazie danych przy użyciu języka zapytań SQL lub plików w folderze obiektów blob platformy Azure. 

### <a name="filter-data-in-a-database"></a>Filtrowanie danych w bazie danych
Poniższy zrzut ekranu przedstawia kwerendę SQL do filtrowania danych.

![Filtrowanie danych w bazie danych](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrowanie danych w folderze obiektów blob platformy Azure
Za pomocą zmiennych w ścieżce folderu można kopiować dane z folderu. Obsługiwane zmienne to: **{year}**, **{month}**, **{day}**, **{hour}** i **{minute}**. Na przykład: inputfolder/{year}/{month}/{day}. 

Załóżmy, że masz foldery wejściowe w następującym formacie: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kliknij przycisk **Przeglądaj** **plik lub folder**, przejdź do jednego z tych folderów (na przykład 2016->03->01->02) i kliknij przycisk **Wybierz**. Powinieneś zobaczyć 2016/03/01/02 w polu tekstowym. 

Następnie zamień **rok 2016** **{rok},** **03** **{miesiąc}**, **01** **na {day}** i **02** **na {hour}** i naciśnij klawisz **Tab.** Listy rozwijane powinny zostać wyświetlone, aby wybrać format tych czterech zmiennych:

![Filtr pliku lub folderu](./media/copy-data-tool/filter-file-or-folder.png)

Narzędzie Kopiuj dane generuje parametry z wyrażeniami, funkcjami i zmiennymi systemowymi, które mogą być używane do reprezentowania {year}, {month}, {day}, {hour}i {minute} podczas tworzenia potoku.

## <a name="scheduling-options"></a>Opcje planowania
Operację kopiowania można uruchomić raz lub zgodnie z harmonogramem (co godzinę, codziennie i tak dalej). Te opcje mogą być używane dla łączników w różnych środowiskach, w tym lokalnie, w chmurze i na pulpicie lokalnym. 

Operacja kopiowania jednorazowego umożliwia przenoszenie danych ze źródła do miejsca docelowego tylko raz. Ma ona zastosowanie do danych o dowolnym rozmiarze i dowolnym obsługiwanym formacie. Zaplanowana kopia umożliwia kopiowanie danych na cyklu, który określisz. Do skonfigurowania zaplanowanej kopii można użyć ustawień rozszerzonych (takich jak ponawianie próby, limit czasu i alerty).

![Opcje planowania](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Następne kroki
Wypróbuj te samouczki, które używają narzędzia Kopiuj dane:

- [Szybki start: tworzenie fabryki danych za pomocą narzędzia Kopiuj dane](quickstart-create-data-factory-copy-data-tool.md)
- [Samouczek: kopiowanie danych na platformie Azure przy użyciu narzędzia Kopiuj dane](tutorial-copy-data-tool.md) 
- [Samouczek: kopiowanie danych lokalnych na platformę Azure przy użyciu narzędzia Kopiuj dane](tutorial-hybrid-copy-data-tool.md)

---
title: Skrypty SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)
description: Wprowadzenie skryptów SQL usługi Azure Synapse Studio (wersja zapoznawcza)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431074"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Używanie skryptu SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)

Usługa Azure Synapse Studio (wersja zapoznawcza) udostępnia interfejs sieci web skryptu SQL do tworzenia zapytań SQL. Można połączyć się z pulą SQL (wersja zapoznawcza) lub SQL na żądanie (wersja zapoznawcza). 

## <a name="begin-authoring-in-sql-script"></a>Rozpoczynanie tworzenia w skrypcie SQL 

Istnieje kilka sposobów, aby rozpocząć tworzenie środowiska w skrypcie SQL. Można utworzyć nowy skrypt SQL za pomocą jednej z następujących metod.

1. Wybierz ikonę "+" i wybierz skrypt SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Z menu Akcje w obszarze Tworzenie skryptów SQL Wybierz "Nowy skrypt SQL" z menu "Akcje" w obszarze Tworzenie skryptów SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

lub 

3. Wybierz opcję "Importuj" z menu "Akcje" w obszarze Tworzenie skryptów SQL i wybierz istniejący skrypt SQL z magazynu lokalnego.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Tworzenie skryptu SQL

1. Wybierz nazwę skryptu SQL, wybierając przycisk "Właściwość" i zastępując domyślną nazwę przypisaną do skryptu SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Wybierz określoną pulę SQL lub SQL na żądanie z menu rozwijanego "Połącz się z". Lub jeśli to konieczne, wybierz bazę danych z "Użyj bazy danych".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Rozpocznij tworzenie skryptu SQL przy użyciu funkcji intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uruchamianie skryptu SQL

Wybierz przycisk "Uruchom", aby wykonać skrypt SQL. Wyniki są domyślnie wyświetlane w tabeli.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Eksportowanie wyników

Wyniki można wyeksportować do magazynu lokalnego w różnych formatach (w tym CSV, Excel, JSON, XML), wybierając opcję "Eksportuj wyniki" i wybierając rozszerzenie.

Można również wizualizować wyniki skryptu SQL na wykresie, wybierając przycisk "Wykres". Wybierz "Typ wykresu" i "Kolumna Kategoria". Wykres można wyeksportować do obrazu, wybierając opcję "Zapisz jako obraz". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Eksploruj dane z pliku Parkietu.

Można eksplorować pliki parkietu na koncie magazynu przy użyciu skryptu SQL do podglądu zawartości pliku. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabele SQL, tabele zewnętrzne, widoki

Wybierając menu "Akcje" pod danymi, możesz wybrać kilka akcji, takich jak: "Nowy skrypt SQL", "Wybierz TOP 1000 wierszy", "CREATE", "DROP i CREATE". Eksploruj dostępny gest, klikając prawym przyciskiem myszy węzły puli SQL i sql na żądanie.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia skryptu SQL, zobacz [Usługa Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
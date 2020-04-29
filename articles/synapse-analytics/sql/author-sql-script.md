---
title: Skrypty SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)
description: Wprowadzenie do skryptów SQL platformy Azure Synapse Studio (wersja zapoznawcza)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431074"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Używanie skryptu SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)

Usługa Azure Synapse Studio (wersja zapoznawcza) udostępnia interfejs sieci Web skryptu SQL służący do tworzenia zapytań SQL. Możesz nawiązać połączenie z pulą SQL (wersja zapoznawcza) lub SQL na żądanie (wersja zapoznawcza). 

## <a name="begin-authoring-in-sql-script"></a>Rozpocznij tworzenie w skrypcie SQL 

Istnieje kilka sposobów uruchamiania środowiska tworzenia w skrypcie SQL. Nowy skrypt SQL można utworzyć za pomocą jednej z następujących metod.

1. Wybierz ikonę "+" i wybierz pozycję skrypt SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. W menu Akcje w obszarze Projektowanie skryptów SQL Wybierz pozycję "Nowy skrypt SQL" z menu "akcje" w sekcji Tworzenie skryptów SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

lub 

3. Wybierz pozycję "Importuj" z menu "akcje" w obszarze Projektowanie skryptów SQL i wybierz istniejący skrypt SQL z magazynu lokalnego.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Tworzenie skryptu SQL

1. Wybierz nazwę skryptu SQL, wybierając przycisk "Właściwość" i zastępując domyślną nazwę przypisaną do skryptu SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Wybierz określoną pulę SQL lub SQL na żądanie z menu rozwijanego "Połącz z". W razie potrzeby wybierz bazę danych z okna "Użyj bazy danych".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Rozpocznij tworzenie skryptu SQL przy użyciu funkcji IntelliSense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uruchamianie skryptu SQL

Wybierz przycisk "Uruchom", aby wykonać skrypt SQL. Wyniki są domyślnie wyświetlane w tabeli.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Eksportowanie wyników

Wyniki można wyeksportować do magazynu lokalnego w różnych formatach (w tym CSV, Excel, JSON, XML), wybierając pozycję "Eksportuj wyniki" i wybierając rozszerzenie.

Możesz również wizualizować wyniki skryptu SQL na wykresie, wybierając przycisk "wykres". Wybierz "typ wykresu" i "kolumna kategorii". Możesz wyeksportować wykres do obrazu, wybierając pozycję "Zapisz jako obraz". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Eksplorowanie danych z pliku Parquet.

Możesz eksplorować pliki Parquet na koncie magazynu za pomocą skryptu SQL, aby wyświetlić podgląd zawartości pliku. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabele SQL, tabele zewnętrzne, widoki

Wybierając menu "akcje" w obszarze dane, możesz wybrać kilka akcji, takich jak: "Nowy skrypt SQL", "SELECT TOP 1000 Rows", "CREATE", "DROP and CREATE". Poznaj dostęp do gestu, klikając prawym przyciskiem myszy węzły puli SQL i SQL na żądanie.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia skryptu SQL, zobacz [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
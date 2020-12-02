---
title: Skrypty SQL w programie Synapse Studio
description: Wprowadzenie do skryptów SQL programu Synapse Studio w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 077815d117af0328a3466346727e135caaf8ff21
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457995"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Skrypty SQL Synapse Studio w usłudze Azure Synapse Analytics 

Program Synapse Studio udostępnia interfejs sieci Web skryptu SQL służący do tworzenia zapytań SQL. 

## <a name="begin-authoring-in-sql-script"></a>Rozpocznij tworzenie w skrypcie SQL 

Istnieje kilka sposobów uruchamiania środowiska tworzenia w skrypcie SQL. Nowy skrypt SQL można utworzyć za pomocą jednej z następujących metod.

1. Z menu programowanie wybierz ikonę **"+"** i wybierz pozycję **skrypt SQL**.

2. Z menu **Akcje** wybierz polecenie **Nowy skrypt SQL**.

3. Wybierz pozycję **Importuj** z menu **Akcje** w obszarze Tworzenie skryptów SQL. Wybierz istniejący skrypt SQL z magazynu lokalnego.
![nowe akcje skryptu SQL 3](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Tworzenie skryptu SQL

1. Wybierz nazwę skryptu SQL, wybierając przycisk **Właściwości** i zastępując domyślną nazwę przypisaną do skryptu SQL. 
![Nowa nazwa skryptu SQL](media/author-sql-script/new-sql-script-rename.png)

2. Wybierz konkretną dedykowaną pulę SQL lub bezserwerową pulę SQL z menu rozwijanego **Połącz z** . W razie potrzeby wybierz bazę danych z **bazy danych** programu. 
![Nowa pula SQL — wybór](media/author-sql-script/new-sql-choose-pool.png)

3. Rozpocznij tworzenie skryptu SQL przy użyciu funkcji IntelliSense.

## <a name="run-your-sql-script"></a>Uruchamianie skryptu SQL

Wybierz przycisk **Run (Uruchom** ), aby wykonać skrypt SQL. Wyniki są domyślnie wyświetlane w tabeli.

![Nowa tabela wyników skryptu SQL](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Eksportowanie wyników

Wyniki można wyeksportować do magazynu lokalnego w różnych formatach (w tym CSV, Excel, JSON, XML), wybierając pozycję "Eksportuj wyniki" i wybierając rozszerzenie.

Możesz również wizualizować wyniki skryptu SQL na wykresie, wybierając przycisk **wykresu** . Wybierz kolumnę "typ wykresu" i **kategorię**. Możesz wyeksportować wykres do obrazu, wybierając pozycję **Zapisz jako obraz**. 

![Nowy wykres wyników skryptu SQL](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Eksplorowanie danych z pliku Parquet

Możesz eksplorować pliki Parquet na koncie magazynu za pomocą skryptu SQL, aby wyświetlić podgląd zawartości pliku.

![Nowy skrypt sqlod Parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabele SQL, tabele zewnętrzne, widoki

Wybierając menu **Akcje** w obszarze dane, możesz wybrać kilka akcji, takich jak:

- Nowy skrypt SQL
- Wybierz pierwsze 1000 wierszy
- CREATE
- UPUŚĆ i Utwórz 
 
Aby poznać dostępny gest, kliknij prawym przyciskiem myszy węzły baz danych SQL.
 
![Nowa baza danych skryptów](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Tworzenie folderów i przenoszenie skryptów SQL do folderu

W menu Akcje w obszarze Projektowanie skryptów SQL Wybierz pozycję "nowy folder" z menu "akcje" w sekcji Tworzenie skryptów SQL. I wpisz nazwę nowego folderu w oknie podręcznym. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-create-folder.png)

Aby przenieść skrypt SQL do folderu, możesz wybrać skrypt SQL i wybrać polecenie "Przenieś do" z menu Akcje. Następnie znajdź folder docelowy w nowym oknie i Przenieś skrypt SQL do wybranego folderu. Możesz również szybko przeciągnąć skrypt SQL i upuścić go w folderze.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia skryptu SQL, zobacz [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).

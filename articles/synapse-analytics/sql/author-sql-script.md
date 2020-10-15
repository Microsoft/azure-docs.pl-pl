---
title: Skrypty SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)
description: Wprowadzenie do skryptów SQL platformy Azure Synapse Studio (wersja zapoznawcza)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: cd00c5033d0435b24d4cfb9f413b5afe74da6774
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089013"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Używanie skryptu SQL w usłudze Azure Synapse Studio (wersja zapoznawcza)

Usługa Azure Synapse Studio (wersja zapoznawcza) udostępnia interfejs sieci Web skryptu SQL służący do tworzenia zapytań SQL. Możesz nawiązać połączenie z pulą SQL (wersja zapoznawcza) lub SQL na żądanie (wersja zapoznawcza). 

## <a name="begin-authoring-in-sql-script"></a>Rozpocznij tworzenie w skrypcie SQL 

Istnieje kilka sposobów uruchamiania środowiska tworzenia w skrypcie SQL. Nowy skrypt SQL można utworzyć za pomocą jednej z następujących metod.

1. Z menu programowanie wybierz ikonę **"+"** i wybierz pozycję **skrypt SQL**.

![Nowy skrypt SQL](media/author-sql-script/newsqlscript.png)

2. Z menu **Akcje** wybierz polecenie **Nowy skrypt SQL**.
> [!div class="mx-imgBorder"]
> ![nowe akcje skryptu SQL 2](media/author-sql-script/newsqlscript2actions.png)

Alternatywnie możesz: 

3. Wybierz pozycję **Importuj** z menu **Akcje** w obszarze Tworzenie skryptów SQL. Wybierz istniejący skrypt SQL z magazynu lokalnego.
![nowe akcje skryptu SQL 3](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Tworzenie skryptu SQL

1. Wybierz nazwę skryptu SQL, wybierając przycisk **Właściwości** i zastępując domyślną nazwę przypisaną do skryptu SQL. 
![Nowa nazwa skryptu SQL](media/author-sql-script/newsqlscriptrename.png)

2. Wybierz określoną pulę SQL lub SQL na żądanie z menu rozwijanego **Połącz z** . W razie potrzeby wybierz bazę danych z **bazy danych**programu. 
![Nowa pula SQL — wybór](media/author-sql-script/newsqlchoosepool.png)

3. Rozpocznij tworzenie skryptu SQL przy użyciu funkcji IntelliSense.
![Nowa funkcja IntelliSense SQL](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uruchamianie skryptu SQL

Wybierz przycisk **Run (Uruchom** ), aby wykonać skrypt SQL. Wyniki są domyślnie wyświetlane w tabeli.

![Nowa tabela wyników skryptu SQL](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Eksportowanie wyników

Wyniki można wyeksportować do magazynu lokalnego w różnych formatach (w tym CSV, Excel, JSON, XML), wybierając pozycję "Eksportuj wyniki" i wybierając rozszerzenie.

Możesz również wizualizować wyniki skryptu SQL na wykresie, wybierając przycisk **wykresu** . Wybierz kolumnę "typ wykresu" i **kategorię**. Możesz wyeksportować wykres do obrazu, wybierając pozycję **Zapisz jako obraz**. 

![Nowy wykres wyników skryptu SQL](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Eksplorowanie danych z pliku Parquet

Możesz eksplorować pliki Parquet na koncie magazynu za pomocą skryptu SQL, aby wyświetlić podgląd zawartości pliku.

![Nowy skrypt sqlod Parquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabele SQL, tabele zewnętrzne, widoki

Wybierając menu **Akcje** w obszarze dane, możesz wybrać kilka akcji, takich jak:

- Nowy skrypt SQL
- Wybierz pierwsze 1000 wierszy
- CREATE
- UPUŚĆ i Utwórz 
 
Poznaj dostęp do gestu, klikając prawym przyciskiem myszy węzły puli SQL i SQL na żądanie.
 
![Nowa baza danych skryptów](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia skryptu SQL, zobacz [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).

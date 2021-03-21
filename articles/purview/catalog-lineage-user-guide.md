---
title: Data Catalog Podręcznik użytkownika (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie funkcji powiązanej z katalogiem usługi Azure kontrolą.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 47c5ad18f50bd25dd3bf351090ffc9ef5d54f0b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202913"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Podręcznik użytkownika usługi Azure kontrolą Data Catalog

Ten artykuł zawiera omówienie funkcji odnoszących się do danych w usłudze Azure kontrolą Data Catalog.

## <a name="background"></a>Tło

Jedną z funkcji platformy Azure kontrolą jest możliwość pokazywania elementów pokazanych między zestawami danych utworzonymi przez procesy przetwarzania. Systemy takie jak Data Factory, udział danych i Power BI przechwytują dane w trakcie ich przenoszenia. Niestandardowe raportowanie składowe jest również obsługiwane za pośrednictwem punktów zaczepienia i interfejsu API REST.

## <a name="lineage-collection"></a>Kolekcja elementów bezużytecznych 
 Metadane zebrane w usłudze Azure kontrolą z systemów danych przedsiębiorstwa są połączone w celu pokazania kompleksowego elementu danych. Systemy danych, które zbierają elementy powiązane w kontrolą, są szeroko kategoryzowane według trzech typów.

### <a name="data-processing-system"></a>System przetwarzania danych
Narzędzia integracji danych i ETL umożliwiają wypychanie elementów do usługi Azure kontrolą w czasie wykonywania. Narzędzia, takie jak Data Factory, udział danych, Synapse, Azure Databricks i tak dalej, należą do tej kategorii systemów danych. Systemy przetwarzające dane odwołują się do zestawów danych jako źródła z różnych baz danych i rozwiązań do magazynowania w celu utworzenia docelowych zestawów DataSet. Lista systemów przetwarzania danych, które są obecnie zintegrowane z kontrolą dla elementów podrzędnych, znajduje się w poniższej tabeli.


| System przetwarzania danych | Obsługiwany zakres |
| ---------------------- | ------------|
| Azure Data Factory | [Działanie kopiowania](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [Działanie przepływu danych](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Działanie wykonywania pakietu SSIS](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Udostępnij migawkę](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Systemy przechowywania danych
Bazy danych & rozwiązania magazynu, takie jak SQL Server, Teradata i SAP, zawierają aparaty zapytań do przekształcania danych przy użyciu języka skryptowego. Dane pochodzenie z procedur składowanych są zbierane w kontrolą i połączone z innymi systemami.

| System magazynowania danych | Obsługiwany zakres |
| ---------------------- | ------------|
| Teradata | Procedury składowane

### <a name="data-analytics--reporting-systems"></a>Systemy raportowania & analizy danych
Systemy danych, takie jak Azure ML i Power BI, są objęte usługą Azure kontrolą. Te systemy będą używać zestawów danych z systemów magazynowania i przetwarzać je za pośrednictwem swojego Meta model, aby utworzyć pulpit nawigacyjny analizy biznesowej, eksperymenty ML i tak dalej.

| System raportowania & analizy danych | Obsługiwany zakres |
| ---------------------- | ------------|
| Power BI | [Zestawy danych, dataflows, raporty & pulpitów nawigacyjnych](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Wprowadzenie do programu powiązanego

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE1XVQS]

Elementy powiązane w kontrolą zawierają zestawy danych i procesy. Zestawy danych są również nazywane węzłami, podczas gdy procesy mogą również określać krawędzie:

* **DataSet (węzeł)**: zestaw danych (ze strukturą lub bez struktury) podany jako dane wejściowe procesu. Na przykład tabela SQL, obiekt blob platformy Azure i pliki (takie jak. csv i. xml) są uznawane za zestawy danych. W sekcji "kontrolą" zestawów danych są reprezentowane przez prostokątne pola.

* **Proces (krawędź)**: działanie lub przekształcenie wykonane na elemencie DataSet nosi nazwę procesu. Na przykład działanie kopiowania ADF, migawka udziału danych i tak dalej. W sekcji "kontrolą" procesy są reprezentowane przez okrągłe pola.

Aby uzyskać dostęp do informacji o pozostałej zawartości dla zasobu w kontrolą, wykonaj następujące czynności:

1. W Azure Portal przejdź do [strony kont usługi Azure kontrolą](https://aka.ms/purviewportal).

1. Wybierz z listy konto usługi Azure kontrolą, a następnie wybierz pozycję **Uruchom konto kontrolą** na stronie **Przegląd** .

1. Na stronie **głównej** usługi Azure kontrolą Wyszukaj nazwę zestawu danych lub nazwę procesu, taką jak kopiowanie APD lub działanie przepływu danych. A następnie naciśnij klawisz ENTER.

1. Z wyników wyszukiwania wybierz element zawartości i **Wybierz jego kartę** Oddziel.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania karty powiązanej." border="true":::

## <a name="asset-level-lineage"></a>Elementy powiązane poziomu zasobów

Usługa Azure kontrolą obsługuje elementy powiązane z poziomem zasobów dla zestawów danych i procesów. Aby wyświetlić pochodzenie poziomu **zasobów, przejdź do karty** powiązanej bieżącego elementu zawartości w wykazie. Wybierz węzeł zasobów bieżący zestaw danych. Domyślnie lista kolumn należących do danych jest wyświetlana w okienku po lewej stronie.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania kolumn widoku na stronie powiązanej" border="true":::

## <a name="dataset-column-lineage"></a>Kolumna zestawu danych

Aby wyświetlić element zestawu danych na poziomie kolumny, przejdź **do karty obiekt** odłożenia bieżącego elementu zawartości w wykazie i wykonaj poniższe czynności:

1. Gdy jesteś na karcie elementy powiązane, w okienku po lewej stronie zaznacz pole wyboru obok każdej kolumny, która ma być wyświetlana w odróżnieniu od danych.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania kolumn do wyświetlania na stronie powiązanej." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Umieść kursor nad zaznaczoną kolumną w lewym okienku lub w zestawie danych kanwy z dołączeniem, aby zobaczyć Mapowanie kolumn. Wszystkie wystąpienia kolumn są wyróżnione.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Zrzut ekranu pokazujący, jak umieścić wskaźnik myszy nad nazwą kolumny w celu wyróżnienia przepływu kolumn w ścieżce lokalizacji danych." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Jeśli liczba kolumn jest większa niż wartość, którą można wyświetlić w okienku po lewej stronie, użyj opcji filtru, aby wybrać określoną kolumnę według nazwy. Alternatywnie możesz przewijać listę za pomocą myszy.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Zrzut ekranu przedstawiający sposób filtrowania kolumn według nazwy kolumny na stronie powiązanej." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Jeśli Kanwa dostosowana zawiera więcej węzłów i krawędzi, Użyj filtru, aby wybrać zasób danych lub węzeł procesu według nazwy. Alternatywnie możesz użyć myszy do kadrowania wokół okna powiązanego.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Zrzut ekranu przedstawiający węzły zasobów danych według nazwy na stronie elementów zależnych." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Użyj przełącznika w okienku po lewej stronie, aby podświetlić listę zestawów danych na kanwie. Jeśli wyłączysz przełącznik, zostanie wyświetlony każdy element zawartości, który zawiera co najmniej jedną z wybranych kolumn. Po włączeniu przełącznika zostaną wyświetlone tylko zestawy danych, które zawierają wszystkie kolumny.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Zrzut ekranu przedstawiający sposób użycia przełącznika do filtrowania listy węzłów na stronie powiązanej." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Przetwórz elementy powiązane kolumny
Proces danych może przyjmować jeden lub więcej zestawów danych wejściowych w celu utworzenia co najmniej jednego wyjścia. W kontrolą, na poziomie kolumny jest dostępny dla węzłów procesu. 
1. Przełączanie między wejściowymi i wyjściowymi zestawami danych z listy rozwijanej w panelu kolumny.
2. Wybierz kolumny z co najmniej jednej tabeli, aby zobaczyć elementy powiązane z wejściowego zestawu danych do odpowiedniego wyjściowego zestawu danych.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="Zrzut ekranu przedstawiający kolumny jako elementy podrzędne węzła procesu." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Przeglądanie zasobów w elemencie pochodzenie
1. Wybierz pozycję **Przełącz do elementu zawartości** dla dowolnego elementu zawartości, aby wyświetlić odpowiednie metadane z widoku elementów zależnych. Wykonanie tej czynności jest efektywnym sposobem na przechodzenie do innego zasobu w katalogu z widoku elementu powiązanego.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Zrzut ekranu, w jaki sposób można wybrać pozycję Przełącz do elementu zawartości w obszarze zasobów dane." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. Kanwa pozostała może stać się złożona dla popularnych zestawów danych. Aby uniknąć bałaganu, w widoku domyślnym będą widoczne tylko pięć poziomów elementów dla elementu zawartości. Resztę elementu pozostałego można rozwinąć, klikając bąbelki na kanwie. Odbiorcy danych mogą również ukrywać zasoby na kanwie, które nie są interesujące. Aby jeszcze bardziej ograniczyć bałagan, wyłącz opcję Przełącz **więcej** elementów w górnej części kanwy powiązanej. Ta akcja spowoduje ukrycie wszystkich bąbelków na kanwie powiązanej.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Zrzut ekranu przedstawiający sposób przełączania większej liczby elementów." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Użyj inteligentnych przycisków na kanwie powiązanej, aby uzyskać optymalny widok elementu powiązanego. Funkcja Autoukład, Powiększ do dopasowania, powiększanie i powiększanie, pełny ekran i Mapa nawigacji są dostępne dla środowiska niewidocznych elementów w wykazie.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Zrzut ekranu pokazujący, jak wybrać przyciski inteligentne." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Następne kroki

* [Link do Azure Data Factory dla elementu powiązanego](how-to-link-azure-data-factory.md)
* [Link do udziału danych platformy Azure dla elementu powiązanego](how-to-link-azure-data-share.md)
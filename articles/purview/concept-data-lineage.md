---
title: Odchodzenie danych w usłudze Azure kontrolą (wersja zapoznawcza)
description: Opisuje koncepcje związane z danymi.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200728"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Odchodzenie danych w usłudze Azure kontrolą Data Catalog Client

Ten artykuł zawiera omówienie odnoszące się do danych w usłudze Azure kontrolą Data Catalog. Szczegółowo opisano, jak systemy danych można zintegrować z wykazem, aby przechwytywać dane odnoszące się do danych. Kontrolą może przechwycić elementy odnoszące się do danych w różnych częściach danych organizacji i na różnych poziomach przygotowania, w tym:

- Całkowicie surowe dane przygotowane z różnych platform
- Przekształcone i przygotowane dane
- Dane używane przez platformy wizualizacji.

## <a name="use-cases"></a>Przypadki użycia

Dane odłożone są szeroko zrozumiałe jako cykl życia, który obejmuje pochodzenie danych, i miejsce, w którym jest przenoszone między obszarem danych. Jest on używany do różnych rodzajów scenariuszy z poprzednimi wersjami, takich jak Rozwiązywanie problemów, śledzenie głównej przyczyny w potokach danych i debugowaniu. Elementy powiązane są również używane do analizy jakości danych, zgodności i scenariuszy "co jeśli" często określa się jako analiza wpływu. Element źródłowy jest prezentowany wizualnie, aby pokazać dane przenoszone z lokalizacji źródłowej do docelowej, w tym sposób przekształcania danych. Ze względu na złożoność większości środowisk danych przedsiębiorstwa, te widoki mogą być trudne do zrozumienia bez konieczności konsolidacji lub maskowania punktów danych peryferyjnych.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Środowisko powiązane w usłudze Azure kontrolą Data Catalog

Kontrolą Data Catalog będą łączyć się z innymi systemami przetwarzania danych, magazynowania i analizy, aby wyodrębnić informacje o powiązaniu. Informacje są łączone w taki sposób, aby reprezentować ogólne, charakterystyczne dla scenariusza środowisko środowiska autonomicznego w wykazie.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="elementy pokazywane na końcu końca pokazujące dane skopiowane z magazynu obiektów BLOB do Power BI pulpitu nawigacyjnego":::

Dane mogą obejmować systemy, które umożliwiają wyodrębnianie danych, przekształcanie (systemy ETL/ELT), analizowanie i wizualizację. Każdy z tych systemów przechwytuje bogate statyczne i operacyjne metadane, które opisują stan i jakość danych w ramach granicy systemów. Celem elementów odnoszących się do usługi Wykaz danych jest wyodrębnienie metadanych przenoszenia, transformacji i operacyjnych z każdego systemu danych przy najniższym możliwym ziarna.

Poniższy przykład jest typowym przypadkiem użycia danych przenoszonych między wieloma systemami, gdzie Data Catalog będzie łączyć się z każdym systemem w celu uzyskania elementu.

- Data Factory kopiuje dane ze strefy Premium/RAW do strefy wyładunkowej w chmurze. 
- Systemy przetwarzania danych, takie jak Synapse, datakosteks, przetwarzą i przekształcają dane z strefy wyładunkowej do strefy nadzorowanej przy użyciu notesów.
- Dalsze przetwarzanie danych w modelach analitycznych w celu uzyskania optymalnej wydajności i agregacji zapytań. 
- Systemy służące do wizualizacji danych będą zużywać zestaw danych i przetwarzać przez ich meta model, aby utworzyć pulpit nawigacyjny analizy biznesowej, eksperymenty ML i tak dalej.

## <a name="lineage-granularity"></a>Stopień szczegółowości

Ta sekcja zawiera szczegółowe informacje na temat stopnia szczegółowości informacji o tym, które są zbierane przez wykaz danych. Ten stopień szczegółowości może się różnić w zależności od systemów danych, które są.

### <a name="entity-level-lineage-sources--process--targets"></a>Elementy powiązane poziomu jednostki: źródłowe > procesu > elementów docelowych 

- Element podrzędny jest reprezentowany jako Graf, zwykle zawiera jednostki źródłowe i docelowe w systemach magazynowania danych, które są połączone przez proces wywoływany przez system obliczeniowy. 
- Systemy danych nawiązują połączenie z usługą Data Catalog w celu wygenerowania i zgłoszenia unikatowego obiektu odwołującego się do fizycznego obiektu bazowego systemu danych na przykład: procedura składowana SQL, Notesy i tak dalej.
- O wysokiej wierności elementy powiązane z dodatkowymi metadanymi, takimi jak własność, są przechwytywane w celu pokazania elementu w formacie odczytanym przez człowieka dla jednostek docelowych & źródłowych. na przykład: elementy powiązane na poziomie tabeli Hive zamiast partycji lub poziomu pliku.

### <a name="column-or-attribute-level-lineage"></a>Atrybut poziomu kolumny lub atrybutu

Zidentyfikuj atrybuty jednostki źródłowej, która jest używana do tworzenia lub wyprowadzania atrybutów w jednostce docelowej. Nazwa atrybutu źródłowego można zachować lub zmienić jego nazwę w obiekcie docelowym. Systemy takie jak ADF mogą wykonać kopię jednokrotną ze środowiska lokalnego do chmury. Na przykład: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Stan wykonywania procesu

Aby zapewnić obsługę analiz głównych przyczyn i scenariuszy jakości danych, przechwytuje stan wykonywania zadań w systemach przetwarzania danych. To wymaganie nie ma nic do zrobienia w przypadku wymiany możliwości monitorowania innych systemów przetwarzania danych, a ich celem nie jest zastąpienie. 

## <a name="summary"></a>Podsumowanie

Element podstawowy jest krytyczną funkcją kontrolą Data Catalog do obsługi scenariuszy dotyczących jakości, zaufania i inspekcji. Celem wykazu danych jest skompilowanie niezawodnej struktury, w której wszystkie systemy danych w danym środowisku mogą w sposób naturalny łączyć się z elementem głównym. Po udostępnieniu metadanych wykaz danych może połączyć metadane dostarczone przez systemy danych z przypadkami użycia w celu zarządzania danymi.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal](create-catalog-portal.md)
* [Szybki Start: Tworzenie konta usługi Azure kontrolą za pomocą Azure PowerShell/interfejsu wiersza polecenia platformy Azure](create-catalog-powershell.md)
* [Szybki Start: korzystanie z programu kontrolą Studio](use-purview-studio.md)

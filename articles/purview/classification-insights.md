---
title: Raportowanie klasyfikacji danych przy użyciu usługi kontrolą Insights (wersja zapoznawcza)
description: W tym przewodniku opisano sposób wyświetlania i używania raportów klasyfikacji kontrolą Insights na danych.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: bb3c7cc3f51eae90c5b712d224407e639b232fbc
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938889"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Klasyfikacja informacji o danych z platformy Azure kontrolą

W tym przewodniku opisano, jak uzyskać dostęp do Kontroląych raportów klasyfikacji danych i ich wyświetlać oraz filtrować je.

Obsługiwane źródła danych obejmują: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (dawniej SQL DW), Azure SQL Database, wystąpienie zarządzane Azure SQL, SQL Server

W tym przewodniku krok po kroku dowiesz się, jak:

> [!div class="checklist"]
> - Uruchamianie konta kontrolą z poziomu platformy Azure
> - Wyświetlanie szczegółowych informacji o klasyfikacji danych
> - Przejdź do szczegółów, aby poznać szczegóły dotyczące klasyfikacji danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

- Skonfiguruj zasoby platformy Azure i wypełnianie odpowiednich kont danymi testowymi

- Skonfiguruj i Ukończ skanowanie danych testowych w każdym źródle danych 

Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Użyj szczegółowych informacji o klasyfikacji kontrolą

W usłudze Azure kontrolą klasyfikacje są podobne do tagów podmiotu i służą do oznaczania i identyfikowania danych określonego typu, które znajdują się w obrębie danych podczas skanowania.

Kontrolą używa tych samych wrażliwych typów informacji co Microsoft 365, co pozwala na rozciąganie istniejących zasad zabezpieczeń i ochronę całej ochrony danych.

> [!NOTE]
> Po przeskanowaniu typów źródłowych Przekaż informacje o **klasyfikacji** przez kilka godzin, aby odzwierciedlić nowe zasoby.

**Aby wyświetlić szczegółowe informacje o klasyfikacji:**

1. Przejdź do ekranu wystąpienia **usługi Azure kontrolą** [w Azure Portal](https://aka.ms/purviewportal) i wybierz swoje konto kontrolą.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Uruchom konto kontrolą** .

1. W kontrolą wybierz element menu usługi **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: po lewej stronie, aby uzyskać dostęp do obszaru **wglądu** w dane.

1. W obszarze usługi **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: Wybierz pozycję **Klasyfikacja** , aby wyświetlić raport dotyczący **klasyfikacji** kontrolą.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Raport dotyczący klasyfikacji" lightbox="media/insights/select-classification-labeling.png":::

   Na stronie główne **Informacje o klasyfikacji** są wyświetlane następujące obszary:

   |Obszar  |Opis  |
   |---------|---------|
   |**Przegląd źródeł z klasyfikacjami**     |Wyświetla kafelki, które udostępniają: <br>-Liczba subskrypcji znalezionych w danych <br>-Liczba unikatowych klasyfikacji znalezionych w danych <br>-Liczba znalezionych źródeł sklasyfikowanych <br>-Liczba znalezionych plików sklasyfikowanych <br>-Znaleziono liczbę sklasyfikowanych tabel         |
   |**Najpopularniejsze źródła z niesklasyfikowanymi danymi (ostatnie 30 dni)**     |Pokazuje trend w ciągu ostatnich 30 dni od liczby źródeł znalezionych z danymi niesklasyfikowanymi.            |
   |**Najważniejsze Kategorie klasyfikacji według źródeł**     |Pokazuje liczbę źródeł znalezionych w kategorii klasyfikacji, takich jak **finansowe** lub **rządowe**.      |
   |**Najważniejsze klasyfikacje dla plików**     |Pokazuje najważniejsze klasyfikacje stosowane do plików w danych, takie jak numery kart kredytowych lub krajowe numery identyfikacyjne.         |
   |**Najważniejsze klasyfikacje dla tabel**     | Pokazuje górne klasyfikacje stosowane do tabel w danych, takie jak osobiste informacje identyfikacyjne. |   
   |  **Działanie klasyfikacji** <br>(pliki i tabele) |  Wyświetla osobne wykresy dla plików i tabel, z których każda pokazuje liczbę plików lub tabel sklasyfikowanych w wybranym przedziale czasu. <br>**Wartość domyślna**: 30 dni<br>Wybierz filtr **czasu** powyżej wykresów, aby wybrać inny przedział czasu do wyświetlenia.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Szczegółowe informacje o klasyfikacji

Na dowolnym z następujących grafów **klasyfikacji Insights** wybierz link **Wyświetl więcej** , aby przejść do szczegółów, aby uzyskać więcej szczegółów:

- **Najważniejsze Kategorie klasyfikacji według źródeł**
- **Najważniejsze klasyfikacje dla plików**
- **Najważniejsze klasyfikacje dla tabel**
- **Działanie klasyfikacji > dane klasyfikacji**

Na przykład:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Wyświetl wszystkie klasyfikacje" lightbox="media/insights/view-classifications.png":::

Aby dowiedzieć się więcej, wykonaj jedną z następujących czynności:

|Opcja  |Opis  |
|---------|---------|
|**Filtrowanie danych**     |  Użyj filtrów powyżej siatki, aby odfiltrować wyświetlane dane, w tym nazwę klasyfikacji, nazwę subskrypcji lub typ źródła. <br><br>Jeśli nie masz pewności co do dokładnej nazwy klasyfikacji, możesz wprowadzić część lub całą nazwę w polu **Filtruj według słowa kluczowego** .       |
|**Sortuj siatkę** |Wybierz nagłówek kolumny, aby posortować siatkę według tej kolumny. | 
|**Edytuj kolumny**     |  Aby wyświetlić więcej lub mniej kolumn w siatce, wybierz opcję **Edytuj kolumny** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , a następnie wybierz kolumny, które chcesz wyświetlić lub zmienić kolejność.   |
|**Przechodzenie do szczegółów**     | Aby przejść do konkretnej klasyfikacji, wybierz nazwę w kolumnie **Klasyfikacja** , aby wyświetlić raport **Klasyfikacja według źródła** . <br><br>Ten raport wyświetla dane dla wybranej klasyfikacji, w tym nazwę źródła, typ źródła, Identyfikator subskrypcji oraz liczbę sklasyfikowanych plików i tabel.      |
|**Przeglądaj zasoby**     |  Aby przeglądać zasoby znalezione z określoną klasyfikacją lub źródłem, wybierz klasyfikację lub źródło, w zależności od wyświetlanego raportu, a następnie wybierz pozycję **Przeglądaj zasoby** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: powyżej filtrów. <br><br>Wyniki wyszukiwania zawierają wszystkie sklasyfikowane zasoby znalezione dla wybranego filtru.  Aby uzyskać więcej informacji, zobacz [Wyszukiwanie w usłudze Azure kontrolą Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights
> [!div class="nextstepaction"]
> [Szczegółowe informacje na temat słownika](glossary-insights.md)

> [!div class="nextstepaction"]
> [Skanuj szczegółowe dane](scan-insights.md)

> [!div class="nextstepaction"]
> [Szczegółowe informacje o etykietowaniu](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Szczegółowe informacje o rozszerzeniu pliku](file-extension-insights.md)
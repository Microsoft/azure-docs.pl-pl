---
title: Informacje o zasobach dotyczące danych w usłudze Azure kontrolą (wersja zapoznawcza)
description: W tym przewodniku opisano sposób wyświetlania i używania raportowania zasobów usługi kontrolą Insights na danych.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ac93d835b8dbdd5a12d031825dcb879160df5e95
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575350"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Informacje o zasobach dotyczące danych w usłudze Azure kontrolą

W tym przewodniku opisano sposób uzyskiwania dostępu do raportów szczegółowych informacji o zasobach i ich wyświetlania oraz filtrowania ich kontrolą.

W tym przewodniku krok po kroku dowiesz się, jak:

> [!div class="checklist"]
> * Wyświetl szczegółowe informacje na podstawie konta usługi kontrolą.
> * Uzyskaj wgląd w dane z lotu ptaka.
> * Więcej szczegółów dotyczących liczby zasobów znajduje się w dalszej części.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

* Skonfiguruj zasoby platformy Azure i wypełnij konto danymi.

* Skonfiguruj i wykonaj skanowanie na typ źródła.

Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Korzystanie z usługi kontrolą Insights

W usłudze Azure kontrolą można rejestrować i skanować typy źródłowe. Po zakończeniu skanowania można wyświetlić dystrybucję zasobów w usłudze Insights, która informuje o stanie danych według klasyfikacji i zestawów zasobów. Informuje także o ewentualnej zmianie rozmiaru danych.

> [!NOTE]
> Po przeskanowaniu typów źródłowych podawanie informacji o zasobach do godziny w celu odzwierciedlenia nowych zasobów.

1. Przejdź do zasobu usługi Azure kontrolą w Azure Portal.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Otwórz program kontrolą Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Uruchom kontrolą z Azure Portal":::

1. Na stronie **głównej** kontrolą wybierz kafelek **Wyświetl szczegółowe** dane, aby uzyskać dostęp do obszaru **wglądu** w dane :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Wyświetlanie szczegółowych informacji w Azure Portal":::

1. W obszarze usługi **Insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: Wybierz pozycję **zasoby** , aby wyświetlić raport usługi kontrolą **Asset Insights** .

### <a name="view-asset-insights"></a>Wyświetlanie szczegółowych informacji o zasobach

1. Na stronie podstawowe **Informacje o zasobach** są wyświetlane następujące obszary:

2. Kluczowy wskaźnik wydajności do wyświetlania typów źródłowych, sklasyfikowanych zasobów i odnalezionych zasobów
 
3. Pierwszy wykres przedstawia **elementy zawartości na typ źródła**.

4. Wyświetlanie dystrybucji zasobów według typu źródła. Wybierz klasyfikację lub całą kategorię klasyfikacji, aby wyświetlić dystrybucję zasobów według typu źródła. 
 
5. Aby wyświetlić więcej, wybierz pozycję **Wyświetl więcej**, co spowoduje wyświetlenie tabelarycznego formularza typów źródłowych i zasobów. Filtry klasyfikacji są przenoszone na Tę stronę.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Wyświetlanie wskaźników KPI i grafu w usłudze Asset Insights":::
 
6. Wybierz konkretne źródło, dla którego chcesz zobaczyć górne foldery z liczbami zasobów. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Wybierz typ źródła":::
 
7. Wybierz łączną sumę zasobów z górnym folderem w wybranym powyżej typie źródłowym.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Wyświetl ścieżki plików":::

8. Wyświetl listę plików w folderze. Przejdź wstecz do szczegółowych informacji przy użyciu chleba Crumbs.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Wyświetl listę zasobów":::  

### <a name="file-based-source-types"></a>Typy źródeł oparte na plikach
Kolejne kilka grafów w usłudze Asset Insights pokazują dystrybucję typów źródeł opartych na plikach. Pierwszy wykres o nazwie **trend rozmiaru (GB) typu pliku w typach źródłowych** pokazuje trend rozmiaru typu pliku o najwyższej wartości w ciągu ostatnich 30 dni. 
 
1. Wybierz typ źródła, aby wyświetlić typ pliku w źródle. 
 
1. Wybierz pozycję **Wyświetl więcej** , aby wyświetlić bieżący rozmiar danych, Zmień rozmiar, aktualną liczbę zasobów i Zmień liczbę elementów zawartości.
 
   > [!NOTE]
   > Jeśli skanowanie zostało uruchomione tylko raz w ciągu ostatnich 30 dni lub jakakolwiek zmiana katalogu, taka jak dodanie/usunięcie klasyfikacji, zakończyła się tylko raz w okresie 30 dni, wówczas te informacje o zmianie są wyświetlane jako puste.

1. Po kliknięciu pozycji Typ źródła Zobacz najpopularniejsze foldery z opcją zmień górne zasoby.

1. Wybierz ścieżkę, aby wyświetlić listę zasobów.

Drugi wykres w typach źródłowych opartych na plikach to **_pliki, które nie są skojarzone z zestawem zasobów_**. Jeśli spodziewasz się, że wszystkie pliki powinny być rzutowane na zestaw zasobów, ten wykres może pomóc w zrozumieniu, które zasoby nie zostały rzutowane. Brakujące elementy zawartości mogą być oznaczeniem nieprawidłowego wzorca pliku w folderze. Wykonaj te same czynności jak w przypadku innych wykresów, aby wyświetlić więcej szczegółów na temat plików.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Wyświetlanie zasobów na podstawie plików":::  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights z informacjami o [skanowaniu](./scan-insights.md)

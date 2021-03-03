---
title: Monitoruj Azure Cosmos DB przy użyciu Azure Monitor dla Cosmos DB | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Cosmos DB, która zapewnia Cosmos DB właściciele z szybką wiedzą na temat problemów z wydajnością i wykorzystaniem ich kont CosmosDB.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: d88bf65f1bd94e29bd9f60f5597d655f0040623b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725804"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Eksploruj Azure Monitor dla Azure Cosmos DB

Azure Monitor dla Azure Cosmos DB stanowi widok ogólnej wydajności, niepowodzeń, pojemności i kondycji operacyjnej wszystkich zasobów Azure Cosmos DB w ujednoliconym środowisku interaktywnym. Ten artykuł pomoże Ci zrozumieć korzyści płynące z nowego środowiska monitorowania oraz jak można modyfikować i dostosowywać środowisko, aby odpowiadało unikatowym potrzebom organizacji.   

## <a name="introduction"></a>Wprowadzenie

Przed wprowadzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje. 

Zapewnia:

* **Na dużą skalę** zasobów Azure Cosmos DB w ramach wszystkich subskrypcji w jednej lokalizacji, z możliwością selektywnego określania zakresu tylko do subskrypcji i zasobów, które chcesz oceniać.

* **Przechodzenie do szczegółów** konkretnego zasobu usługi Azure CosmosDB w celu ułatwienia zdiagnozowania problemów lub wykonywania szczegółowej analizy według kategorii, niepowodzeń, pojemności i operacji. Wybranie jednej z tych opcji zapewnia szczegółowy widok odpowiednich metryk Azure Cosmos DB.  

* **Dostosowywalne** — to środowisko zostało utworzone na podstawie Azure monitor szablonów skoroszytów, co umożliwia zmianę sposobu wyświetlania metryk, zmodyfikowanie lub ustawienie progów, które są wyrównane z limitami, a następnie zapisywanie w skoroszycie niestandardowym. Wykresy w skoroszytach można następnie przypinać do pulpitów nawigacyjnych platformy Azure.  

Ta funkcja nie wymaga włączenia ani skonfigurowania żadnych elementów, te Azure Cosmos DB metryki są domyślnie zbierane.

>[!NOTE]
>Dostęp do tej funkcji nie jest naliczany, a opłaty są naliczane tylko za Azure Monitor podstawowe funkcje, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegóły cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Wyświetl metryki użycia i wydajności dla Azure Cosmos DB

Aby wyświetlić użycie i wydajność kont magazynu we wszystkich subskrypcjach, wykonaj następujące czynności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj ciąg **monitor** i wybierz pozycję **Monitoruj**.

    ![Pole wyszukiwania z słowem "Monitor" oraz listą rozwijaną "Monitor" z obrazem stylu prędkościomierza](./media/cosmosdb-insights-overview/search-monitor.png)

3. Wybierz **Cosmos DB**.

    ![Zrzut ekranu przedstawiający skoroszyt omówienia Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Omówienie

W obszarze **Przegląd** w tabeli są wyświetlane metryki interakcyjne Azure Cosmos DB. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Są wyświetlane subskrypcje obsługujące tylko **subskrypcje** , które mają zasób Azure Cosmos DB.  

* **Cosmos DB** — można wybrać wszystkie, podzestaw lub pojedynczy zasób Azure Cosmos DB.

* **Zakres czasu** — domyślnie wyświetla ostatnie 4 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika pod listą rozwijaną podsumowuje łączną liczbę zasobów Azure Cosmos DB w wybranych subskrypcjach. Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które raportują metryki transakcji. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach. 

Wybranie strzałki listy rozwijanej obok jednego z zasobów Azure Cosmos DB spowoduje wyświetlenie podziału metryk wydajności na poziomie kontenera poszczególnych baz danych:

![Rozwinięto listę rozwijaną z ujawnianiem poszczególnych kontenerów bazy danych i powiązanego podziału wydajności](./media/cosmosdb-insights-overview/container-view.png)

Wybranie nazwy zasobu Azure Cosmos DB wyróżnionej na niebiesko spowoduje przejście do domyślnego **omówienia** dla skojarzonego konta Azure Cosmos DB. 

### <a name="failures"></a>Błędy

Wybierz pozycję **Błędy** w górnej części strony i zostanie otwarty fragment **błędów** szablonu skoroszytu. Pokazuje ona łączne żądania z rozkładem odpowiedzi składających się na te żądania:

![Zrzut ekranu przedstawiający awarie z podziałem według typu żądania HTTP](./media/cosmosdb-insights-overview/failures.png)

| Kod |  Opis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z następujących operacji REST została wykonana pomyślnie: </br>— Pobierz zasób. </br> — Umieść w zasobie. </br> -Publikuj w zasobie. </br> — Opublikuj w zasobie procedury składowanej, aby wykonać procedurę składowaną.|
| `201 Created` | Operacja POST w celu utworzenia zasobu zakończyła się pomyślnie. |
| `404 Not Found` | Operacja podejmuje próbę wykonania operacji na zasobie, który już nie istnieje. Na przykład zasób mógł już zostać usunięty. |

Aby zapoznać się z pełną listą kodów stanu, zapoznaj się z [artykułem kod stanu HTTP Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Pojemność

Wybierz pozycję **pojemność** w górnej części strony i zostanie otwarta część **pojemności** szablonu skoroszytu. Przedstawiono w nim liczbę dokumentów, wzrost dokumentu w czasie, użycie danych i łączną ilość dostępnego miejsca do magazynowania.  Może to służyć do identyfikowania potencjalnych problemów z magazynowaniem i wykorzystaniem danych.

![Skoroszyt pojemności](./media/cosmosdb-insights-overview/capacity.png) 

Podobnie jak w przypadku skoroszytu z omówieniem, wybranie listy rozwijanej obok zasobu Azure Cosmos DB w kolumnie **subskrypcja** spowoduje wyświetlenie podziału poszczególnych kontenerów tworzących bazę danych.

### <a name="operations"></a>Operacje

Wybierz pozycję **operacje** w górnej części strony, a zostanie otwarta część **operacje** szablonu skoroszytu. Daje ona możliwość wyświetlenia żądań, które zostały podzielone według typu żądań.

Tak więc w poniższym przykładzie widzisz, że `eastus-billingint` jest on głównie otrzymywał żądania odczytu, ale z niewielką liczbą żądań upsert i Create. Program `westeurope-billingint` jest tylko do odczytu z perspektywy żądania, co najmniej w ciągu ostatnich czterech godzin, do których ten skoroszyt jest obecnie objęty zakresem czasu.

![Skoroszyt operacji](./media/cosmosdb-insights-overview/operation.png)

## <a name="view-from-an-azure-cosmos-db-resource"></a>Wyświetl z zasobu Azure Cosmos DB

1. Wyszukaj lub Wybierz dowolne z istniejących kont Azure Cosmos DB.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-search.png" alt-text="Wyszukaj Azure Cosmos DB." border="true":::

2. Gdy przejdziesz do konta Azure Cosmos DB, w sekcji monitorowanie wybierz pozycję **wgląd (wersja zapoznawcza)** lub **skoroszyty** , aby przeprowadzić dalsze analizy dotyczące przepływności, żądań, magazynu, dostępności, opóźnień, systemu i zarządzania kontami.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-overview.png" alt-text="Omówienie usługi Cosmos DB Insights." border="true":::

### <a name="time-range"></a>Przedział czasu

Domyślnie w polu **zakres czasu** są wyświetlane dane z **ostatnich 24 godzin**. Możesz zmodyfikować zakres czasu, aby wyświetlić dane w dowolnym miejscu od 5 ostatnich minut do ostatnich siedmiu dni. Selektor zakresu czasu zawiera również **niestandardowy** tryb umożliwiający wpisywanie dat rozpoczęcia/zakończenia w celu wyświetlenia niestandardowego przedziału czasowego na podstawie dostępnych danych dla wybranego konta.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-time-range.png" alt-text="Cosmos DB zakres czasu." border="true":::

### <a name="insights-overview"></a>Przegląd szczegółowych informacji

Karta **Przegląd** zawiera najbardziej typowe metryki dla wybranego konta Azure Cosmos DB, w tym:

* Łączna liczba żądań
* Nieudane żądania (429s)
* Znormalizowane użycie RU (max)
* Użycie indeksu & danych
* Metryki konta Cosmos DB według kolekcji

**Łączna liczba żądań:** Ten wykres przedstawia łączną liczbę żądań dla konta, które zostały podzielone według kodu stanu. Jednostki w dolnej części wykresu są sumą łącznej liczby żądań w danym okresie.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-total-requests.png" alt-text="Cosmos DB wykres łącznej liczby żądań." border="true":::

**Żądania zakończone niepowodzeniem (429s)**: ten wykres zawiera widok żądań zakończonych niepowodzeniem z kodem stanu 429. Jednostki w dolnej części wykresu są sumą całkowitej liczby nieudanych żądań w danym okresie.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-429.png" alt-text="Wykres Cosmos DB żądań zakończonych niepowodzeniem." border="true":::

**Znormalizowane użycie ru (max)**: ten wykres zawiera maksymalną wartość procentową między 0-100% znormalizowanych jednostek zużycia RU w określonym przedziale czasu.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-normalized-ru.png" alt-text="Cosmos DB znormalizowane użycie RU." border="true":::

## <a name="pin-export-and-expand"></a>Przypnij, Eksportuj i rozwiń

Możesz przypiąć każdą z sekcji metryk do [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md) , wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru PIN sekcji Metryka na pulpit nawigacyjny](./media/cosmosdb-insights-overview/pin.png)

Aby wyeksportować dane do formatu programu Excel, wybierz ikonę strzałki w dół znajdującą się po lewej stronie ikony pinezki.

![Ikona eksportowania skoroszytu](./media/cosmosdb-insights-overview/export.png)

Aby rozwinąć lub zwinąć wszystkie widoki rozwijane w skoroszycie, wybierz ikonę rozwiń z lewej strony ikony eksportu:

![Rozwiń ikonę skoroszytu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Dostosuj Azure Monitor dla Azure Cosmos DB

Ponieważ to środowisko zostało utworzone na podstawie Azure monitor szablonów skoroszytów, można **dostosować**  >  **Edytowanie** i **zapisywać** kopię zmodyfikowanej wersji w skoroszycie niestandardowym. 

![Dostosuj pasek](./media/cosmosdb-insights-overview/customize.png)

Skoroszyty są zapisywane w obrębie grupy zasobów, w sekcji **Moje raporty** prywatnej dla użytkownika lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Uruchom galerię skoroszytów z poziomu paska poleceń](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

## <a name="next-steps"></a>Następne kroki

* Konfigurowanie [alertów metryk](../alerts/alerts-metric.md) i [powiadomień o kondycji usług](../../service-health/alerts-activity-log-service-notifications-portal.md) w celu skonfigurowania zautomatyzowanego alertu w celu uzyskania pomocy w wykrywaniu problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../visualize/workbooks-overview.md).

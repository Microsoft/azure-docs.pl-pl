---
title: Monitoruj Azure Cosmos DB przy użyciu Azure Monitor dla Cosmos DB | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Cosmos DB, która zapewnia Cosmos DB właściciele z szybką wiedzą na temat problemów z wydajnością i wykorzystaniem ich kont CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 8043dfb7fdda972d4ede6798d07355936b23bf93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498920"
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

W obszarze **Przegląd**w tabeli są wyświetlane metryki interakcyjne Azure Cosmos DB. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

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

| Kod      |  Opis       | 
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

* Konfigurowanie [alertów metryk](../platform/alerts-metric.md) i [powiadomień o kondycji usług](../../service-health/alerts-activity-log-service-notifications-portal.md) w celu skonfigurowania zautomatyzowanego alertu w celu uzyskania pomocy w wykrywaniu problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../platform/workbooks-overview.md).

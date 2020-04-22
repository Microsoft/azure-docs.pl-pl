---
title: Przypadki użycia dla wbudowanej analizy za pomocą usługi Azure Cosmos DB.
description: Dowiedz się, jak korzystać z wbudowanej analizy z usługą Azure Cosmos DB w różnych przypadkach użycia.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: adf1db2d737de7004b5ab71c8e0e44d61d1da8c9
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768522"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Przypadki użycia dla wbudowanej analizy za pomocą usługi Azure Cosmos DB

Następujące przypadki użycia można osiągnąć przy użyciu wbudowanej analizy za pomocą platformy Apache Spark w usłudze Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scenariusze HTAP

Wbudowana analiza w usłudze Azure Cosmos DB może służyć do:

* Wykrywanie oszustw podczas przetwarzania transakcji.
* Udostępniaj rekomendacje kupującym podczas przeglądania sklepu ECommerce.
* Ostrzegaj operatorów o zbliżającej się awarii krytycznego elementu produkcji.
* Twórz szybki i użyteczny wgląd, osadzając analizy w czasie rzeczywistym bezpośrednio na danych operacyjnych.

Usługa Azure Cosmos DB obsługuje scenariusze hybrydowego przetwarzania transakcyjnego/analitycznego (HTAP) przy użyciu natywnie wbudowanej platformy Spark apache. Usługa Azure Cosmos DB usuwa separację operacyjną i analityczną, która jest dostępna w przypadku tradycyjnych systemów.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globalnie rozproszone jezioro danych bez konieczności

Dzięki natywnie wbudowanej aplikacji Apache Spark usługa Azure Cosmos DB zapewnia szybki, prosty i skalowalny sposób tworzenia globalnie rozproszonego źródła danych, które zapewnia pojedynczy obraz systemu. Globalnie rozproszone dane wielo modelowe eliminują konieczność inwestowania w drogie potoki i skale ETL na żądanie, rewolucjonizując sposób, w jaki zespoły danych analizują swoje zestawy danych.

## <a name="time-series-analytics-over-historic-data"></a>Analiza szeregów czasowych nad danymi historycznymi

W niektórych przypadkach może być konieczne udzielenie odpowiedzi na pytania na podstawie danych w określonym momencie w czasie w przypadku zdarzeń zakończonych w przeszłości. Na przykład, aby uzyskać liczbę stanów działania CRM w określonym dniu. Jeśli raport został uruchomiony tydzień temu, liczba stanów będzie taka, jak na stanach każdego działania w tym momencie. Prowadzenie tego samego sprawozdania dzisiaj daje liczbę działań, których statusy są takie, jakie są dzisiaj, które mogą ulec zmianie od zeszłego tygodnia, ponieważ przechodzą przez ich cykl życia od otwartego do zamkniętego. Tak, należy raport na migawki na każdym etapie cyklu życia sprawy.

Dzięki usłudze Azure Cosmos DB użytkownicy mają możliwość zaimplementowania koncepcji podróży w czasie, możliwość wykonywania zapytań i uruchamiania analizy danych retrospektywnie i pytania o wygląd danych w określonym punkcie czasu w historii. Oznacza to, że użytkownicy mogą łatwo przeglądać zarówno bieżące, jak i historyczne widoki danych i uruchamiać na nich analizę.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globalnie rozproszone uczenie maszynowe i ai

Ponieważ firmy zmagają się z szybko rosnącą ilością danych i rosnącą różnorodnością typów i formatów danych, możliwość uzyskania głębszych i dokładniejszych informacji staje się prawie niemożliwa w skali petabajtów na całym świecie. Dzięki natywnie wbudowanej platformie Apache Spark usługa Azure Cosmos DB zapewnia globalnie rozproszoną platformę analityczną, która oferuje obszerną bibliotekę algorytmów uczenia maszynowego. Interaktywnych notesów Jupyter można używać do tworzenia i szkolenia modeli oraz funkcji zarządzania klastrami. Te możliwości umożliwiają aprowizowanie wysoce dostrojonych i autoelastyczna klastrów Platformy Spark na żądanie.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Głębokie uczenie na wielosmodelowych danych rozproszonych globalnie

Uczenie głębokie to idealny sposób na dostarczanie rozwiązań do analizy predykcyjnej dużych zbiorów danych w miarę wzrostu ilości i złożoności danych. Dzięki głębokiemu uczeniu firmy mogą wykorzystać moc nieustrukturyzowanych i częściowo ustrukturyzowanych danych, aby dostarczać przypadki użycia, które wykorzystują techniki, takie jak AI, przetwarzanie języka naturalnego i inne.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Raportowanie (integrowanie się z aplikacjami power, usługa Power BI)

Usługa Power BI udostępnia interaktywne wizualizacje z możliwością samoobsługowej analizy biznesowej, umożliwiając użytkownikom końcowym samodzielne tworzenie raportów i pulpitów nawigacyjnych. Korzystając z wbudowanego łącznika platformy Spark, można połączyć program Power BI Desktop z klastrami Platformy Spark apache w usłudze Azure Cosmos DB. Ten łącznik umożliwia użycie zapytania bezpośredniego do odciążania przetwarzania do platformy Apache Spark w usłudze Azure Cosmos DB, co jest doskonałe, gdy masz ogromną ilość danych, które nie chcesz ładować do usługi Power BI lub gdy chcesz przeprowadzić analizę w czasie zbliżonym do rzeczywistego.

## <a name="iot-analytics-at-global-scale"></a>Analiza IoT w skali globalnej

Dane generowane przez rosnące czujniki sieciowe zapewniają bezprecedensowy wgląd w wcześniej nieprzezroczyste systemy i procesy. Kluczem jest znalezienie użytecznych spostrzeżeń w tym potoku informacji, niezależnie od tego, gdzie urządzenia IoT są dystrybuowane na całym świecie. Usługa Azure Cosmos DB umożliwia firmom IOT analizowanie czujników o dużej prędkości i danych szeregów czasowych w czasie rzeczywistym w dowolnym miejscu na świecie. Pozwala wykorzystać prawdziwą wartość połączonego świata, aby zapewnić lepsze doświadczenia klientów, wydajność operacyjną i nowe możliwości uzyskania przychodów.

## <a name="stream-processing-and-event-analytics"></a>Przetwarzanie strumienia i analiza zdarzeń 

Od plików dziennika po dane z czujników , firmy coraz częściej muszą radzić sobie z "strumieniami" danych. Te dane docierają w stałym strumieniu, często z wielu źródeł jednocześnie. Chociaż jest to możliwe do przechowywania tych strumieni danych na dysku i analizować je retrospektywnie, czasami może być rozsądne lub ważne do przetwarzania i działania na danych, jak nadejdzie. Na przykład strumienie danych związanych z transakcjami finansowymi mogą być przetwarzane w czasie rzeczywistym w celu zidentyfikowania i odrzucenia potencjalnie oszukańczych transakcji.

## <a name="interactive-analytics"></a>Interaktywna analityka

Oprócz uruchamiania wstępnie zdefiniowanych zapytań w celu tworzenia statycznych pulpitów nawigacyjnych dla sprzedaży, produktywności lub cen akcji, możesz interaktywnie eksplorować dane. Interaktywna analiza pozwala zadawać pytania, przeglądać wyniki, zmieniać pytanie początkowe na podstawie odpowiedzi lub pogłębiać wyniki. Apache Spark w usłudze Azure Cosmos DB obsługuje interaktywne zapytania, szybko odpowiadając i dostosowując się.

## <a name="data-exploration-using-jupyter-notebooks"></a>Eksploracja danych przy użyciu notebooków Jupyter

Jeśli masz nowy zestaw danych, przed zapoznaniem się z uruchomionymi modelami i testami, musisz sprawdzić swoje dane. Innymi słowy, należy przeprowadzić analizę danych odkrywczych. Eksploracja danych może stanowić podstawę do podjęcia kilku decyzji. Na przykład można znaleźć szczegóły, takie jak metody, które są odpowiednie do użycia w danych, czy dane spełniają określone założenia modelowania, czy dane powinny być czyszczone, zrestrukturyzowane itp. Korzystając z natywnie wbudowanych notesów Jupyter i platformy Apache Spark usługi Azure Cosmos DB, można wykonać szybką i skuteczną analizę danych eksploracyjnych na danych transakcyjnych i analitycznych.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z tymi przypadkami użycia w następnej wiadomości, przejdź do następujących artykułów:

* [Wbudowane notesy Jupyter w usłudze Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Jak włączyć notesy dla kont usługi Azure Cosmos](enable-notebooks.md)
* [Tworzenie notesu do analizowania i wizualizowania danych](create-notebook-visualize-data.md)
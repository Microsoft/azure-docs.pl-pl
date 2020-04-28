---
title: Przypadki użycia dla wbudowanej analizy z Azure Cosmos DB.
description: Dowiedz się, jak używać wbudowanych analiz z Azure Cosmos DB w różnych przypadkach użycia.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 04/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 9b1c3435222ada52c01f21c2c242dc886f566a81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192854"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Przypadki użycia dla wbudowanej analizy z Azure Cosmos DB

Następujące przypadki użycia można osiągnąć przy użyciu wbudowanej analizy z Apache Spark w Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scenariusze HTAP

Wbudowana analiza w Azure Cosmos DB może służyć do:

* Wykrywaj oszustwo podczas przetwarzania transakcji.
* Podaj zalecenia do Kupujący podczas przeglądania magazynu handlu elektronicznego.
* Operatorzy alertów w przypadku nieoczekiwanej awarii krytycznego sprzętu produkcyjnego.
* Szybko Twórz szczegółowe dane umożliwiające podejmowanie działań, osadzając analizy w czasie rzeczywistym bezpośrednio na danych operacyjnych.

Azure Cosmos DB obsługuje scenariusze hybrydowego przetwarzania transakcyjnego/analitycznego (HTAP) przy użyciu natywnie wbudowanego Apache Spark. Azure Cosmos DB usuwa separacje i analityczne, które są dostarczane z tradycyjnymi systemami.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globalnie dystrybuowana Usługa Data Lake bez konieczności używania ETL

Korzystając z natywnie wbudowanej Apache Spark, Azure Cosmos DB zapewnia szybki, prosty i skalowalny sposób tworzenia globalnie rozproszonej platformy Data Lake, która zapewnia pojedynczy obraz systemu. Globalnie dystrybuowane dane wielomodelowe eliminują potrzebę inwestowania w kosztowne potoki ETL i skalować je na żądanie, revolutionizing w sposób, w jaki zespoły danych analizują zestawy danych.

## <a name="time-series-analytics-over-historic-data"></a>Analiza szeregów czasowych dla danych historycznych

W niektórych przypadkach może być konieczne udzielenie odpowiedzi na pytania na podstawie danych, jak w określonym punkcie czasu, w przypadku zdarzeń zakończonych w przeszłości. Na przykład, aby uzyskać liczbę stanów działania CRM w określonym dniu. Jeśli raport został uruchomiony w tygodniu temu, liczba Stanów będzie taka sama jak stan poszczególnych działań w tym momencie. Uruchomienie tego samego raportu spowoduje udostępnienie liczby działań, których Stany są aktualne, które mogły ulec zmianie od ostatniego tygodnia, ponieważ przechodzą przez cykl życia od otwarcia do zamknięcia. Dlatego należy zgłosić migawkę na każdym etapie cyklu życia sprawy.

Dzięki Azure Cosmos DB użytkownicy mogą wdrożyć koncepcję czasu podróży, dzięki czemu można wykonywać zapytania i uruchamiać analizę danych z mocą wsteczną oraz uzyskać informacje na temat sposobu, w jaki dane są oglądane w określonym punkcie czasu w historii. Oznacza to, że użytkownicy mogą łatwo wyświetlać zarówno bieżące, jak i historyczne widoki danych, a następnie uruchamiać na nich analizy.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globalnie rozproszona usługa Machine Learning i AI

Ponieważ firmy będą konkurować o z szybko rosnącą ilością danych i rozszerzają różne typy danych i formaty, możliwość uzyskania dokładniejszych i dokładniejszych informacji na skalę petabajtów na całym świecie. Korzystając z natywnie wbudowanej Apache Spark, Azure Cosmos DB oferuje globalnie dystrybuowaną platformę analityczną, która oferuje szeroką gamę algorytmów uczenia maszynowego. Interaktywnych notesów Jupyter można używać do tworzenia i uczenia modeli oraz funkcji zarządzania klastrami. Te funkcje umożliwiają udostępnianie wysoce dostrojonych i elastycznych samochodów Spark na żądanie.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Szczegółowa nauka dla wielomodelowych danych dystrybuowanych globalnie

Uczenie głębokie to idealny sposób zapewnienia rozwiązań do analizy predykcyjnej danych Big Data, jak ilość danych i złożoność nadal rośnie. Dzięki głębokiej uczeniu firmy mogą korzystać z możliwości danych bez struktury i z częściową strukturą w celu dostarczania przypadków użycia wykorzystujących takie techniki jak AI, przetwarzanie języka naturalnego i inne.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Raportowanie (Integrowanie z aplikacjami zaawansowanymi, Power BI)

Power BI udostępnia interaktywne wizualizacje z funkcjami analizy biznesowej samoobsługowych, co umożliwia użytkownikom końcowym samodzielne tworzenie raportów i pulpitów nawigacyjnych. Korzystając z wbudowanego łącznika Spark, można połączyć Power BI Desktop z klastrami Apache Spark w Azure Cosmos DB. Ten łącznik umożliwia użycie zapytania bezpośredniego do odciążenia przetwarzania do Apache Spark w Azure Cosmos DB, co jest doskonałe w przypadku dużej ilości danych, które nie mają być ładowane do Power BI lub gdy chcesz przeprowadzić analizę niemal w czasie rzeczywistym.

## <a name="iot-analytics-at-global-scale"></a>Analiza IoT na skalę globalną

Dane generowane przez zwiększenie czujników sieci zapewniają niezrównaną widoczność wcześniej nieprzezroczystych systemów i procesów. Kluczem jest znalezienie szczegółowych informacji z możliwością podejmowania działań w tym torrentu, niezależnie od tego, gdzie urządzenia IoT są rozproszone na całym świecie. Azure Cosmos DB umożliwia firmom IOT analizowanie czujnika wysokiej prędkości i danych szeregów czasowych w czasie rzeczywistym w dowolnym miejscu na całym świecie. Pozwala to na wykorzystanie prawdziwej wartości połączonego świata w celu zapewnienia ulepszonego środowiska klienta, efektywności operacyjnej i nowych możliwości przychodów.

## <a name="stream-processing-and-event-analytics"></a>Przetwarzanie strumienia i analiza zdarzeń 

Z plików dziennika do danych czujników, firmy coraz częściej mają potrzebę poradzić sobie z "strumieniami" danych. Te dane docierają do stałego strumienia, często z wielu źródeł jednocześnie. Chociaż jest możliwe, że te strumienie danych są przechowywane na dysku i przeanalizowane z mocą wsteczną, może być czasem rozsądne lub ważne, aby przetwarzać i podejmować działania dotyczące danych w miarę ich odbierania. Na przykład strumienie danych związanych z transakcjami finansowymi można przetwarzać w czasie rzeczywistym w celu identyfikowania i odrzucania potencjalnie fałszywych transakcji.

## <a name="interactive-analytics"></a>Interaktywna analiza

Oprócz uruchamiania wstępnie zdefiniowanych zapytań w celu tworzenia statycznych pulpitów nawigacyjnych do sprzedaży, produktywności lub cen giełdowych możesz chcieć interaktywnie eksplorować dane. Interaktywna analiza umożliwia zadawanie pytań, wyświetlanie wyników, zmianę początkowego pytania na podstawie odpowiedzi lub dokładniejsze przechodzenie do wyników. Apache Spark w Azure Cosmos DB obsługuje interaktywne zapytania przez szybkie reagowanie i adaptację.

## <a name="data-exploration-using-jupyter-notebooks"></a>Eksploracja danych przy użyciu notesów Jupyter

Jeśli masz nowy zestaw danych, przed szczegółowe do uruchamiania modeli i testów musisz sprawdzić dane. Innymi słowy, należy przeprowadzić analizę danych. Eksploracja danych może informować kilka decyzji. Na przykład można znaleźć szczegółowe informacje, takie jak metody, które są odpowiednie do użycia w danych, niezależnie od tego, czy dane są zgodne z pewnymi założeniami modelowania, czy dane mają być czyszczone, poddane restrukturyzacji itd. Korzystając Azure Cosmos DB z natywnie wbudowanych notesów Jupyter i Apache Spark, można szybko i wydajnie analizować dane w firmie i danych analitycznych.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z tymi przypadkami użycia, przejdź do następujących artykułów:

* [Wbudowane notesy Jupyter w Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Jak włączyć notesy dla kont usługi Azure Cosmos](enable-notebooks.md)
* [Tworzenie notesu do analizowania i wizualizowania danych](create-notebook-visualize-data.md)
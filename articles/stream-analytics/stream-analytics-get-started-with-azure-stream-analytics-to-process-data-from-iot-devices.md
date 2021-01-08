---
title: Przetwarzanie strumieni danych IoT w czasie rzeczywistym za pomocą Azure Stream Analytics
description: Używanie tagów czujników IoT i strumieni danych z analizą strumieni i przetwarzaniem danych w czasie rzeczywistym
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012686"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Przetwarzanie strumieni danych IoT w czasie rzeczywistym za pomocą Azure Stream Analytics

W tym artykule dowiesz się, jak utworzyć logikę przetwarzania strumieniowego w celu zbierania danych z urządzeń Internet rzeczy (IoT). Używasz rzeczywistego przypadku użycia Internet rzeczy (IoT), aby zademonstrować, jak szybko i ekonomicznie kompilować swoje rozwiązanie.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz bezpłatną [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Pobierz przykładowe pliki zapytań i danych z usługi [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenariusz

Contoso to firma z branży automatyki przemysłowej, która w pełni zautomatyzowała swój proces produkcji. Maszyny w tym zakładzie mają czujniki mogące emitować strumienie danych w czasie rzeczywistym. W tym scenariuszu menedżer warsztatu produkcyjnego chce mieć wgląd w czasie rzeczywistym w dane czujników w celu szukania wzorców i podejmowania na ich podstawie odpowiednich działań. Aby znaleźć interesujące wzorce z przychodzącego strumienia danych, można użyć Stream Analytics Query Language (SAQL) za pośrednictwem danych czujników.

W tym przykładzie dane są generowane z urządzenia Texas Instruments sensor tag. Ładunek danych jest w formacie JSON i może wyglądać następująco:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

W rzeczywistym scenariuszu mogą istnieć setki takich czujników generujących zdarzenia jako strumień. W idealnym przypadku urządzenie bramy będzie uruchamiać kod służący do wypychania tych zdarzeń do usługi [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Zadanie usługi Stream Analytics pozyska te zdarzenia z usługi Event Hubs i uruchomi zapytania analityki w czasie rzeczywistym względem strumieni. Następnie będzie można wysłać wyniki do jednego z [obsługiwanych wyjść](stream-analytics-define-outputs.md).

W celu ułatwienia pracy w tym przewodniku z wprowadzeniem udostępniono przykładowy plik danych przechwycony z rzeczywistych urządzeń Sensor Tag. Możesz uruchamiać zapytania dotyczące przykładowych danych i przeglądać ich wyniki. W kolejnych samouczkach dowiesz się, jak łączyć zadania z wejściami i wyjściami, a następnie wdrażać je w usłudze platformy Azure.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **+ Utwórz zasób** z menu nawigacji po lewej stronie. Następnie wybierz **zadanie Stream Analytics** z **analizy**.
   
    ![Tworzenie nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Wprowadź unikatową nazwę zadania i sprawdź, czy subskrypcja jest poprawna dla zadania. Utwórz nową grupę zasobów lub wybierz istniejącą z subskrypcji.

1. Wybierz lokalizację dla zadania. Użyj tej samej lokalizacji dla grupy zasobów i wszystkich zasobów, aby zwiększyć szybkość przetwarzania i zmniejszyć koszty. Po dokonaniu konfiguracji wybierz pozycję **Utwórz**.
   
    ![Tworzenie szczegółów nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Tworzenie zapytania usługi Azure Stream Analytics
Następnym krokiem po utworzeniu zadania jest napisanie zapytania. Można testować zapytania względem przykładowych danych bez łączenia danych wejściowych lub wyjściowych z zadaniem.

Pobierz [HelloWorldASA-InputStream.js](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) z witryny GitHub. Następnie przejdź do zadania Azure Stream Analytics w Azure Portal.

Wybierz pozycję **zapytanie** w obszarze **topologia zadania** z menu po lewej stronie. Następnie wybierz pozycję **Przekaż przykładowe dane wejściowe**. Przekaż `HelloWorldASA-InputStream.json` plik, a następnie wybierz **przycisk OK**.

![Kafelek zapytania pulpitu nawigacyjnego Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Zwróć uwagę, że Podgląd danych jest automatycznie wypełniany w tabeli **podglądu danych wejściowych** .

![Podgląd przykładowych danych wejściowych](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Zapytanie: archiwizowanie danych pierwotnych

Najprostszą postacią zapytania jest przekazywanie, które powoduje archiwizowanie wszystkich danych wejściowych w określonej lokalizacji wyjściowej. To zapytanie jest domyślnym zapytaniem w nowym zadaniu Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Wybierz **kwerendę testową** i Wyświetl wyniki w tabeli **wyników testu** .

![Wyniki testu dla zapytania Stream Analyticsowego](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Zapytanie: filtrowanie danych na podstawie warunku

Spróbujmy przefiltrować wyniki na podstawie warunku. Chcemy wyświetlić wyniki tylko dla tych zdarzeń, które pochodzą z "czujnika".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Wklej zapytanie w edytorze i wybierz polecenie **Testuj zapytanie** , aby przejrzeć wyniki.

![Filtrowanie strumienia danych](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Zapytanie: wysyłanie alertów w celu wyzwolenia biznesowego przepływu pracy

Zwiększmy szczegółowość naszego zapytania. Dla każdego typu czujnika chcemy monitorować średnią temperaturę co 30 sekund i wyświetlać wyniki tylko wtedy, gdy przekracza ona 100 stopni.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Zapytanie z filtrowaniem co 30 sekund](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Powinny być widoczne wyniki, które zawierają tylko 245 wierszy i nazw czujników, gdzie średnia temperatura jest większa niż 100. W tym zapytaniu strumień zdarzeń został pogrupowany według wartości **dspl**, czyli nazwy czujnika, i wartości **Okno wirowania** równej 30 sekund. Zapytania czasowe muszą określać, jak należy postępować. Za pomocą klauzuli **timestamp by** została określona kolumna **OUTPUTTIME** , która ma zostać skojarzona z wszystkimi obliczeniami czasowymi. Aby uzyskać szczegółowe informacje, przeczytaj temat [Zarządzanie czasem](/stream-analytics-query/time-management-azure-stream-analytics) i [funkcje okna](/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Zapytanie: wykrywanie braku zdarzeń

Jak napisać zapytanie w celu określenia braku zdarzeń wejściowych? Znajdźmy ostatni czas, w którym czujnik przesłał dane, a następnie nie wysłał zdarzeń w ciągu następnych 5 sekund.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Wykrywanie braku zdarzeń](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

W tym przypadku zostanie użyte sprzężenie **LEFT OUTER** względem tego samego strumienia danych (samosprzężenie). W przypadku sprzężenia **INNER** wynik jest zwracany tylko wtedy, gdy zostanie znalezione dopasowanie.  Natomiast w przypadku sprzężenia **LEFT OUTER**, jeśli zdarzenie z lewej strony sprzężenia jest niedopasowane, dla wszystkich kolumn po prawej stronie wiersza jest zwracany wiersz z wartością NULL. Ta technika jest bardzo przydatna do wyszukiwania braku zdarzeń. Aby uzyskać więcej informacji, zobacz [Join](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Podsumowanie

Celem tego artykułu jest przedstawienie sposobu pisania różnych zapytań dotyczących języka zapytań Stream Analytics i wyświetlania wyników w przeglądarce. Można to jednak zrobić dopiero po rozpoczęciu pracy. Usługa Stream Analytics obsługuje różne dane wejściowe oraz wyjściowe i może nawet korzystać z funkcji w usłudze Azure Machine Learning, co czyni ją doskonałym narzędziem do analizowania strumieni danych. Aby uzyskać więcej informacji na temat sposobu pisania zapytań, przeczytaj artykuł dotyczący [typowych wzorców zapytań](stream-analytics-stream-analytics-query-patterns.md).
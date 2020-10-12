---
title: Tworzenie rozwiązania IoT przy użyciu Azure Stream Analytics
description: Samouczek z wprowadzeniem do Stream Analytics rozwiązania IoT scenariusza Tollbooth
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e0e2244d8c70ca2e6d379e741d543d9cd260b7f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044587"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Tworzenie rozwiązania IoT przy użyciu Stream Analytics

## <a name="introduction"></a>Wprowadzenie
W tym rozwiązaniu dowiesz się, jak za pomocą Azure Stream Analytics uzyskać wgląd w dane w czasie rzeczywistym. Deweloperzy mogą łatwo łączyć strumienie danych, na przykład klikać strumienie, dzienniki i zdarzenia generowane przez urządzenia, z rekordami historycznymi lub danymi referencyjnymi w celu uzyskania wglądu w dane biznesowe. Jako w pełni zarządzana usługa obliczeniowa strumienia w czasie rzeczywistym hostowana w Microsoft Azure, Azure Stream Analytics zapewnia wbudowaną odporność, małe opóźnienia i skalowalność, aby ułatwić rozpoczęcie pracy w ciągu kilku minut.

Po zakończeniu tego rozwiązania można:

* Zapoznaj się z portalem Azure Stream Analytics.
* Skonfiguruj i Wdróż zadanie przesyłania strumieniowego.
* Ideach rzeczywiste problemy i rozwiązuj je przy użyciu języka zapytań Stream Analytics.
* Twórz rozwiązania przesyłania strumieniowego dla klientów, korzystając z Stream Analytics z pewnością.
* Użyj środowiska monitorowania i rejestrowania, aby rozwiązać problemy.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć to rozwiązanie, potrzebne są następujące wymagania wstępne:
* [Subskrypcja platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Wprowadzenie do scenariusza: "Hello, opłaty!"
Stacja opłat jest często spotykana. Napotykasz je na wielu expresswaysach, mostach i tunelach na całym świecie. Każda stacja z opłatami za połączenie ma wiele bezpłatnych kabin. W ręcznym kabinie zatrzymasz opłaty za połączenie z usługą. W przypadku zautomatyzowanych kabin czujnik w górnej części każdego kabiny skanuje kartę RFID, która jest dołączana do Windshield pojazdu, gdy przejdziesz do Ciebie. Można łatwo wizualizować przepływy pojazdów za pośrednictwem tych stacji bezpłatnych jako strumień zdarzeń, nad którym mogą być wykonywane interesujące operacje.

![Zdjęcia samochodów w kabinach](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dane przychodzące
To rozwiązanie współpracuje z dwoma strumieniami danych. Czujniki zainstalowane w wejściu i wyjściu z stacji opłat nie tworzą pierwszego strumienia. Drugi strumień to statyczny zestaw danych wyszukiwania, który zawiera dane rejestracji pojazdu.

### <a name="entry-data-stream"></a>Strumień danych wejściowych
Strumień danych wprowadzania zawiera informacje o samochodach, które wprowadzają opłaty za połączenia. Zdarzenia danych wyjściowych są przesyłane strumieniowo do kolejki centrum zdarzeń z aplikacji sieci Web dołączonej do przykładowej aplikacji.

| TollID | EntryTime | LicensePlate | State | Marka | Model | Nr pojazdu | VehicleWeight | Płatny | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4 |1 |0 |6 |321987654 |

Poniżej znajduje się krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator kabiny płatnej, który jednoznacznie identyfikuje połączenie płatne |
| EntryTime |Data i godzina wprowadzenia pojazdu do telefonu w formacie UTC |
| LicensePlate |Numer płyty licencji dla pojazdu |
| State |Stan w Stany Zjednoczone |
| Marka |Producent urządzenia przenośnego |
| Model |Numer modelu samochodów |
| Nr pojazdu |1 w przypadku pojazdów pasażerskich lub 2 dla pojazdów komercyjnych |
| Waga |Waga pojazdu w tonach; 0 w przypadku pojazdów podróżujących |
| Płatny |Wartość opłaty za połączenie w USD |
| Tag |Tag e-The-Mobile, który automatyzuje płatność; puste miejsce, w którym płatność została wykonana ręcznie |

### <a name="exit-data-stream"></a>Zakończ strumień danych
Strumień danych wyjściowych zawiera informacje o samochodach, które opuszczają stację opłat. Zdarzenia danych wyjściowych są przesyłane strumieniowo do kolejki centrum zdarzeń z aplikacji sieci Web dołączonej do przykładowej aplikacji.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Poniżej znajduje się krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator kabiny płatnej, który jednoznacznie identyfikuje połączenie płatne |
| ExitTime |Data i godzina wyprowadzenia pojazdu z telefonu opłat w formacie UTC |
| LicensePlate |Numer płyty licencji dla pojazdu |

### <a name="commercial-vehicle-registration-data"></a>Dane rejestracyjne pojazdów komercyjnych
Rozwiązanie używa statycznej migawki bazy danych rejestracji pojazdu komercyjnego. Te dane są zapisywane jako plik JSON w usłudze Azure Blob Storage, zawarte w przykładzie.

| LicensePlate | Identyfikator rejestracji | Wygasłe |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BK 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Poniżej znajduje się krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| LicensePlate |Numer płyty licencji dla pojazdu |
| Identyfikator rejestracji |Identyfikator rejestracji pojazdu |
| Wygasłe |Stan rejestracji pojazdu: 0, jeśli rejestracja pojazdu jest aktywna, 1 Jeśli rejestracja wygasła |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Skonfiguruj środowisko dla Azure Stream Analytics
Do ukończenia tego rozwiązania potrzebna jest subskrypcja Microsoft Azure. Jeśli nie masz konta platformy Azure, możesz [zażądać bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

Upewnij się, że wykonaj kroki opisane w sekcji "Wyczyść konto platformy Azure" na końcu tego artykułu, aby można było korzystać z środków na korzystanie z platformy Azure.

## <a name="deploy-the-sample"></a>Wdróż przykład
Istnieje kilka zasobów, które można łatwo wdrożyć w grupie zasobów razem z kilkoma kliknięciami. Definicja rozwiązania jest hostowana w repozytorium GitHub pod adresem [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Wdróż szablon TollApp w Azure Portal
1. Aby wdrożyć środowisko TollApp na platformie Azure, użyj tego linku do [wdrożenia szablonu TollApp platformy Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Jeśli zostanie wyświetlony monit, zaloguj się do Azure Portal.

3. Wybierz subskrypcję, w ramach której są naliczane opłaty za różne zasoby.

4. Określ nową grupę zasobów z unikatową nazwą, na przykład `MyTollBooth` .

5. Wybierz lokalizację platformy Azure.

6. Określ **Interwał** jako liczbę sekund. Ta wartość jest używana w przykładowej aplikacji sieci Web, w której często można wysyłać dane do centrum zdarzeń.

7. **Zaznacz** , aby wyrazić zgodę na warunki i postanowienia.

8. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby później można było łatwo znaleźć zasoby.

9. Wybierz pozycję **Kup** , aby wdrożyć przykładowy szablon.

10. Po kilku chwilach zostanie wyświetlone powiadomienie informujące o **pomyślnym wdrożeniu**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Przeglądanie Azure Stream Analytics zasobów TollApp

1. Logowanie się do witryny Azure Portal

2. Znajdź grupę zasobów, która została nazwana w poprzedniej sekcji.

3. Sprawdź, czy w grupie zasobów wymienione są następujące zasoby:
   - Jedno konto Cosmos DB
   - Jedno Azure Stream Analytics zadanie
   - Jedno konto usługi Azure Storage
   - Jedno centrum zdarzeń platformy Azure
   - Dwie Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Badanie przykładowego zadania TollApp
1. Rozpoczynając od grupy zasobów w poprzedniej sekcji, wybierz zadanie Stream Analytics streaming, rozpoczynając od nazwy **tollapp** (nazwa zawiera znaki losowe w celu zapewnienia unikatowości).

2. Na stronie **Przegląd** zadania Zwróć uwagę na pole **zapytania** , aby wyświetlić składnię zapytania.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Aby paraphrase zamiar zapytania, Załóżmy, że należy policzyć liczbę pojazdów, które wchodzą w połączenie płatne. Ze względu na to, że połączenie autostradowe ma ciągły strumień pojazdów, są to zdarzenia wejścia analogiczne do strumienia, który nigdy się nie zatrzyma. Aby ustalić ilość strumienia, należy zdefiniować "okres czasu" do pomiaru. Przyjrzyjmy się dalszemu zagadnieniu, do "jak wiele pojazdów wchodzi w połączenie płatne co trzy minuty?". Jest to często określane jako liczba wirowania.

   Jak widać, Azure Stream Analytics używa języka zapytań, takiego jak SQL, i dodaje kilka rozszerzeń, aby określić aspekty dotyczące czasu zapytania.  Aby uzyskać więcej informacji, przeczytaj informacje o [zarządzaniu czasem](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) i konstrukcjach [okien](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) używanych w zapytaniu.

3. Przeanalizuj dane wejściowe zadania przykładowego TollApp. Tylko dane wejściowe EntryStream są używane w bieżącym zapytaniu.
   - **EntryStream** input to połączenie centrum zdarzeń, które kolejkuje dane, które są przedstawiane za każdym razem, gdy samochód przejdzie do Tollbooth na autostradie. Aplikacja sieci Web, która jest częścią przykładu, tworzy zdarzenia, a dane są umieszczane w kolejce w tym centrum zdarzeń. Należy zauważyć, że dane wejściowe są wysyłane w klauzuli FROM zapytania przesyłania strumieniowego.
   - **ExitStream** input to połączenie centrum zdarzeń, które kolejkuje dane, które są przedstawiane za każdym razem, gdy samochód opuszcza Tollbooth na autostradie. To dane wejściowe przesyłania strumieniowego są używane w późniejszych odmianach składni zapytania.
   - Dane wejściowe **rejestracji** to połączenie usługi Azure Blob Storage wskazujące statyczny registration.jsw pliku, używany do wyszukiwania w razie potrzeby. To dane wejściowe referencyjne są używane w późniejszych odmianach składni zapytania.

4. Obejrzyj dane wyjściowe zadania przykładowego TollApp.
   - **Cosmos DB** Output to kontener bazy danych Cosmos, który odbiera zdarzenia wyjściowego ujścia. Należy zauważyć, że te dane wyjściowe są używane w klauzuli INTO zapytania przesyłania strumieniowego.

## <a name="start-the-tollapp-streaming-job"></a>Uruchamianie zadania przesyłania strumieniowego TollApp
Wykonaj następujące kroki, aby uruchomić zadanie przesyłania strumieniowego:

1. Na stronie **Przegląd** zadania wybierz pozycję **Rozpocznij**.

2. W okienku **Uruchamianie zadania** wybierz pozycję **teraz**.

3. Po kilku chwilach, gdy zadanie jest uruchomione, na stronie **Przegląd** zadania przesyłania strumieniowego Wyświetl wykres **monitorowania** . Wykres powinien zawierać kilka tysięcy zdarzeń wejściowych i dziesiątki zdarzeń wyjściowych.

## <a name="review-the-cosmosdb-output-data"></a>Przeglądanie danych wyjściowych CosmosDB
1. Znajdź grupę zasobów zawierającą zasoby TollApp.

2. Wybierz konto Azure Cosmos DB z wzorcem nazwy **tollapp \<random\> -Cosmos**.

3. Wybierz **Eksplorator danych** nagłówek, aby otworzyć stronę Eksplorator danych.

4. Rozwiń dokumenty **tollAppDatabase**  >  **tollAppCollection**  >  **Documents**.

5. Na liście identyfikatorów kilka dokumentów jest pokazywanych po udostępnieniu danych wyjściowych.

6. Wybierz każdy identyfikator, aby przejrzeć dokument JSON. Zwróć uwagę na każdy tollid, czas windowend i liczbę samochodów z tego okna.

7. Po kolejnych trzech minutach dostępny jest inny zestaw czterech dokumentów na tollid.


## <a name="report-total-time-for-each-car"></a>Łączny czas dla każdego samochodu
Średni czas potrzebny na przekazanie samochodu przez połączenie telefoniczne ułatwia ocenę wydajności procesu i środowiska klienta.

Aby znaleźć łączny czas, Dołącz strumień EntryTime za pomocą strumienia ExitTime. Dołącz dwa strumienie wejściowe na równych dopasowaniach kolumn TollId i LicencePlate. Operator **Join** wymaga określenia Leeway czasowego, który opisuje akceptowalną różnicę czasu między przyłączonymi zdarzeniami. Użyj funkcji **DateDiff** , aby określić, że zdarzenia nie powinny mieć więcej niż 15 minut od siebie. Zastosuj również funkcję **DateDiff** , aby wyjść i wprowadzać czasy, aby obliczyć rzeczywisty czas spędzony przez samochód w stacji opłat. Zwróć uwagę na różnice użycia elementu **DateDiff** , gdy jest on używany w instrukcji **SELECT** , a nie jako warunek **sprzężenia** .

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aby zaktualizować składnię zapytania zadania przesyłania strumieniowego TollApp:

1. Na stronie **Przegląd** zadania wybierz pozycję **Zatrzymaj**.

2. Poczekaj chwilę na powiadomienie, że zadanie zostało zatrzymane.

3. Pod nagłówkiem TOPOLOGIa zadania wybierz pozycję **< > zapytanie**

4. Wklej dostosowane zapytanie SQL przesyłania strumieniowego.

5. Wybierz pozycję **Zapisz** , aby zapisać zapytanie. Potwierdź **,** aby zapisać zmiany.

6. Na stronie **Przegląd** zadania wybierz pozycję **Rozpocznij**.

7. W okienku **Uruchamianie zadania** wybierz pozycję **teraz**.

### <a name="review-the-total-time-in-the-output"></a>Przejrzyj łączny czas w danych wyjściowych
Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć dane wyjściowe CosmosDB z zadania przesyłania strumieniowego. Zapoznaj się z najnowszymi dokumentami JSON.

Na przykład w tym dokumencie przedstawiono przykład samochodu z określoną płytą licencyjną, EntryTime i zakończenia oraz pole DATEDIFF obliczeniowe durationinminutes przedstawiające czas trwania kabiny w ciągu dwóch minut:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Zgłoś pojazdy z wygasłą rejestracją
Azure Stream Analytics mogą używać migawek statycznych danych referencyjnych do dołączania do strumieni danych czasowych. Aby zademonstrować tę możliwość, użyj poniższego przykładowego pytania. Dane wejściowe rejestracji to statyczny plik JSON obiektu BLOB, który zawiera listę wygaśnięć tagów licencji. Łącząc się z płytą licencyjną, dane referencyjne są porównywane z poszczególnymi pojazdami, które przechodzą przez połączenie w obu kierunkach.

Jeśli pojazd komercyjny jest zarejestrowany w firmie opłat, może przechodzić przez połączenie płatne bez zatrzymania w celu przeprowadzenia inspekcji. Za pomocą tabeli odnośników rejestracji można zidentyfikować wszystkie pojazdy komercyjne, które utraciły ważność rejestracji.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Powtórz kroki opisane w poprzedniej sekcji, aby zaktualizować składnię zapytania zadania TollApp Streaming.

2. Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć dane wyjściowe CosmosDB z zadania przesyłania strumieniowego.

Przykładowe dane wyjściowe:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Skalowanie w poziomie zadania
Azure Stream Analytics jest zaprojektowany do elastycznego skalowania, dzięki czemu może obsługiwać duże ilości danych. Zapytanie Azure Stream Analytics może użyć klauzuli **Partition by** , aby poinformować system, że ten krok skaluje się. **PartitionID** jest specjalną kolumną, którą system dodaje do identyfikatora partycji danych wejściowych (centrum zdarzeń).

Aby skalować zapytanie do partycji, Edytuj składnię zapytania do następującego kodu:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Aby skalować zadanie przesyłania strumieniowego do większej liczby jednostek przesyłania strumieniowego:

1. **Zatrzymaj** bieżące zadanie.

2. Zaktualizuj składnię zapytania na stronie ** kwerendy< > ** i Zapisz zmiany.

3. W obszarze Konfigurowanie nagłówka w zadaniu przesyłania strumieniowego wybierz pozycję **Skala**.

4. Przesuń suwak **jednostki przesyłania strumieniowego** z 1 do 6. Jednostki przesyłania strumieniowego definiują ilość mocy obliczeniowej, którą może odbierać zadanie. Wybierz pozycję **Zapisz**.

5. **Rozpocznij** zadanie przesyłania strumieniowego, aby zademonstrować dodatkową skalę. Azure Stream Analytics dystrybuuje prace w większej ilości zasobów obliczeniowych i osiągają lepszą przepływność, co pozwala na partycjonowanie pracy w obrębie zasobów przy użyciu kolumny wyznaczonej w klauzuli PARTITION BY.

## <a name="monitor-the-job"></a>Monitorowanie zadania
Obszar **monitora** zawiera dane statystyczne dotyczące uruchomionego zadania. Konfiguracja pierwszego czasu jest wymagana do korzystania z konta magazynu w tym samym regionie (imię i nazwisko, takie jak reszta tego dokumentu).

![Monitorowanie zadania Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Możesz również uzyskać dostęp do **dzienników aktywności** z obszaru **Ustawienia** pulpitu nawigacyjnego zadań.

## <a name="clean-up-the-tollapp-resources"></a>Czyszczenie zasobów TollApp
1. Zatrzymaj zadanie Stream Analytics w Azure Portal.

2. Znajdź grupę zasobów zawierającą osiem zasobów związanych z szablonem TollApp.

3. Wybierz pozycję **Usuń grupę zasobów**. Wpisz nazwę grupy zasobów, aby potwierdzić usunięcie.

## <a name="conclusion"></a>Podsumowanie
To rozwiązanie wprowadza do usługi Azure Stream Analytics. Przedstawiono w nim sposób konfigurowania danych wejściowych i wyjściowych dla zadania Stream Analytics. Za pomocą scenariusza danych o cle, rozwiązanie objaśniono typowe typy problemów, które powstają w przypadku danych w ruchu i jak można je rozwiązać przy użyciu prostych zapytań przypominających SQL w Azure Stream Analytics. Rozwiązanie opisane w opisie rozszerzeń SQL do pracy z danymi czasowymi. Pokazano, jak sprzęgać strumienie danych, jak wzbogacać strumień danych ze statycznymi danymi referencyjnymi oraz jak skalować zapytanie w celu uzyskania większej przepływności.

Chociaż to rozwiązanie zapewnia dobre wprowadzenie, nie jest ono uzupełniane żadnymi sposobami. Więcej wzorców zapytań można znaleźć przy użyciu języka SAQL w [przykładach zapytania dla typowych wzorców użycia Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).

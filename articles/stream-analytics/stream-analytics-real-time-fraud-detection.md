---
title: Samouczek — analizowanie fałszywych danych wywołań za pomocą Azure Stream Analytics i wizualizacji wyników na pulpicie nawigacyjnym Power BI
description: Ten samouczek zawiera kompleksowy opis sposobu używania usługi Azure Stream Analytics do analizowania fałszywych połączeń w strumieniu połączeń telefonicznych.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018874"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Samouczek: analizowanie fałszywych danych wywołań za pomocą Stream Analytics i wizualizacji wyników na pulpicie nawigacyjnym Power BI

W tym samouczku pokazano, jak analizować dane połączeń telefonicznych przy użyciu Azure Stream Analytics. Dane połączenia telefonicznego generowane przez aplikację kliencką zawierają fałszywe wywołania, które są filtrowane według zadania Stream Analytics. Możesz użyć technik z tego samouczka, aby uzyskać inne typy wykrywania oszustw, takie jak oszustwa kart kredytowych lub kradzieży tożsamości.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Generuj przykładowe dane połączenia telefonicznego i wyślij je do usługi Azure Event Hubs.
> * Tworzenie zadania usługi Stream Analytics
> * Skonfiguruj dane wejściowe i wyjściowe zadania.
> * Zdefiniuj zapytania do filtrowania fałszywych wywołań.
> * Przetestuj i uruchom zadanie.
> * Wizualizuj wyniki w Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały wykonane następujące czynności:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Pobierz aplikację generatora zdarzeń połączeń telefonicznych [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z Centrum pobierania Microsoft lub uzyskaj kod źródłowy z witryny [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Musisz mieć konto usługi Power BI.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Zanim usługa Stream Analytics będzie mogła przeanalizować strumień danych fałszywych połączeń, należy wysłać dane do platformy Azure. W tym samouczku dane zostaną wysłane do platformy Azure przy użyciu usługi [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Utwórz centrum zdarzeń usługi Event Hub i wyślij do niego dane połączeń, wykonując poniższe czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **Event Hubs**.

   ![Tworzenie centrum Azure Event Hub w portalu](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Wypełnij okienko **Tworzenie przestrzeni nazw** następującymi wartościami:

   |**Ustawienie**  |**Sugerowana wartość** |**Opis**  |
   |---------|---------|---------|
   |Nazwa     | asaTutorialEventHub        |  Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.       |
   |Subskrypcja     |   \<Your subscription\>      |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć centrum zdarzeń.      |
   |Grupa zasobów     |   MyASADemoRG      |  Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta.       |
   |Lokalizacja     |   Zachodnie stany USA 2      |    Lokalizacja, w której można wdrożyć przestrzeń nazw centrum zdarzeń.     |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Przegląd + Utwórz**. Następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie.

   ![Tworzenie przestrzeni nazw centrum zdarzeń w witrynie Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Po zakończeniu wdrażania obszaru nazw przejdź do pozycji **wszystkie zasoby** i Znajdź *asaTutorialEventHub* na liście zasobów platformy Azure. Wybierz pozycję *asaTutorialEventHub* , aby ją otworzyć.

6. Następnie wybierz pozycję **+ centrum zdarzeń** , a następnie wprowadź **nazwę** centrum zdarzeń. Ustaw **liczbę partycji** na 2.  Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**. Następnie poczekaj na pomyślne zakończenie wdrożenia.

   ![Konfiguracja centrum zdarzeń w witrynie Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby aplikacja mogła wysyłać dane do usługi Azure Event Hubs, centrum zdarzeń musi mieć zasady zezwalające na dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1. Przejdź do centrum zdarzeń utworzonego w poprzednim kroku *MyEventHub*. Wybierz pozycję **Zasady dostępu współużytkowanego** w obszarze **Ustawienia**, a następnie wybierz pozycję **+ Dodaj**.

2. Nadaj zasadom nazwę **MyPolicy** i upewnij się, że zaznaczono opcję **Zarządzaj**. Następnie wybierz pozycję **Utwórz**.

   ![Tworzenie zasad dostępu współdzielonego centrum zdarzeń](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Po utworzeniu zasad wybierz nazwę zasad, aby otworzyć zasady. Znajdź **Parametry połączenia — klucz podstawowy**. Wybierz przycisk **Kopiuj** obok parametrów połączenia.

   ![Zapisz parametry połączenia zasad dostępu współużytkowanego](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Wklej parametry połączenia do edytora tekstu. Te parametry połączenia będą potrzebne w następnej sekcji.

   Parametry połączenia wyglądają następująco:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Zauważ, że parametry połączenia zawierają wiele par klucz-wartość oddzielonych średnikami: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** i **EntityPath**.

## <a name="start-the-event-generator-application"></a>Uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować do przesyłania danych do usługi Azure Event Hubs utworzonej wcześniej.

1. Wyodrębnij zawartość pliku [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Otwórz `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` plik w edytorze tekstu, aby wybrać więcej niż jeden `.config` plik. Upewnij się, że otwarto poprawny.

3. Zaktualizuj element `<appSettings>` w pliku config przy użyciu następujących szczegółów:

   * Ustaw wartość klucza *EventHubName* na wartość EntityPath w parametrach połączenia.
   * Ustaw wartość klucza *Microsoft.ServiceBus.ConnectionString* na wartość parametrów połączenia bez wartości EntityPath. Nie zapomnij usunąć średnika, który poprzedza wartość EntityPath.

4. Zapisz plik.

5. Następnie otwórz okno polecenia i przejdź do folderu, w którym znajduje się wyodrębniona aplikacja TelcoGenerator. Wprowadź następujące polecenie:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   To polecenie przyjmuje następujące parametry:
   * Liczba rekordów danych połączeń na godzinę.
   * Procentowe prawdopodobieństwo oszustwa, czyli jak często aplikacja powinna symulować fałszywe połączenia. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.
   * Czas trwania w godzinach, czyli liczba godzin, przez jaką aplikacja ma być uruchomiona. Możesz również zatrzymać aplikację w dowolnym momencie, kończąc proces (**Ctrl + C**) w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń. Dane połączenia telefonicznego zawierają następujące pola:

   |**Rekord**  |**Definicja**  |
   |---------|---------|
   |CallrecTime    |  Znacznik czasu godziny rozpoczęcia połączenia.       |
   |SwitchNum     |  Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie przełączniki są ciągami reprezentującymi kraj/region pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia).       |
   |CallingNum     |  Numer telefonu dzwoniącego.       |
   |CallingIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator dzwoniącego.       |
   |CalledNum     |   Numer telefonu odbiorcy połączenia.      |
   |CalledIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator odbiorcy połączenia.       |

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy masz strumień zdarzeń połączeń, możesz utworzyć zadanie usługi Stream Analytics, które odczytuje dane z centrum zdarzeń.

1. Aby utworzyć zadanie usługi Stream Analytics, przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** i wyszukaj **zadanie Stream Analytics**. Wybierz kafelek **zadania Stream Analytics** i wybierz pozycję * Utwórz * *.

3. Wypełnij formularz **nowego zadania Stream Analytics** przy użyciu następujących wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Nazwa zadania     |  ASATutorial       |   Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.      |
   |Subskrypcja    |  \<Your subscription\>   |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć zadanie.       |
   |Grupa zasobów   |   MyASADemoRG      |   Wybierz pozycję **Użyj istniejącej**, a następnie wprowadź nazwę nowej grupy zasobów dla swojego konta.      |
   |Lokalizacja   |    Zachodnie stany USA 2     |      Lokalizacja, w której można wdrożyć zadanie. Zaleca się umieszczenie zadania i centrum zdarzeń w tym samym regionie, aby uzyskać najlepszą wydajność i nie płacić za transfer danych między regionami.      |
   |Środowisko hostingu    | Chmura        |     Zadania usługi Stream Analytics można wdrożyć w chmurze lub na urządzeniu brzegowym. Chmura umożliwia wdrażanie w chmurze platformy Azure, a usługa Edge umożliwia wdrażanie na urządzeniu IoT Edge.    |
   |Jednostki przesyłania strumieniowego     |    1       |      Jednostki przesyłania strumieniowego reprezentują zasoby obliczeniowe, które są wymagane do wykonania zadania. Domyślnie to ustawienie ma wartość 1. Aby dowiedzieć się więcej na temat skalowania jednostek przesyłania strumieniowego, zobacz artykuł [Understanding and adjusting streaming units (Opis i dostosowywanie jednostek przesyłania strumieniowego)](stream-analytics-streaming-unit-consumption.md).      |

4. Użyj opcji domyślnych w pozostałych ustawieniach, wybierz pozycję **Utwórz** i poczekaj na pomyślne wdrożenie.

   ![Tworzenie zadania usługi Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

Następnym krokiem jest określenie źródła danych wejściowych dla zadania w celu odczytywania danych za pomocą centrum zdarzeń utworzonego w poprzedniej sekcji.

1. W Azure Portal Otwórz stronę **wszystkie zasoby** i znajdź zadanie Stream Analytics *o nazwie asatutorial* .

2. W sekcji **topologia zadania** zadania Stream Analytics wybierz pozycję **dane wejściowe**.

3. Wybierz pozycje **+ Dodaj wejście strumienia** i **Centrum zdarzeń**. Wypełnij formularz wejściowy następującymi wartościami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych     |  CallStream       |  Podaj przyjazną nazwę identyfikującą dane wejściowe. Alias danych wejściowych może zawierać tylko znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3 do 63 znaków.       |
   |Subskrypcja    |   \<Your subscription\>      |   Wybierz subskrypcję platformy Azure, w której zostało utworzone centrum zdarzeń. Centrum zdarzeń może znajdować się w tej samej subskrypcji co zadanie usługi Stream Analytics lub w innej.       |
   |Przestrzeń nazw centrum zdarzeń    |  asaTutorialEventHub       |  Wybierz przestrzeń nazw centrum zdarzeń utworzoną w poprzedniej sekcji. Wszystkie przestrzenie nazw centrum zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa centrum zdarzeń    |   MyEventHub      |  Wybierz centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie centra zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa zasad centrum zdarzeń   |  Moje zasady       |  Wybierz zasady dostępu współdzielonego centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie zasady centrów zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Zapisz**.

   ![Konfigurowanie danych wejściowych usługi Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania

Ostatnim krokiem jest zdefiniowanie ujścia danych wyjściowych, gdzie zadanie może zapisać przekształcone dane. W tym samouczku dane wyjściowe są wizualizowane przy użyciu usługi Power BI.

1. W Azure Portal Otwórz **wszystkie zasoby** i wybierz zadanie Stream Analytics *o nazwie asatutorial* .

2. W sekcji **topologia zadania** zadania Stream Analytics wybierz opcję dane **wyjściowe** .

3. Wybierz pozycję **+ Dodaj**  >  **Power BI**. Następnie wybierz pozycję **Autoryzuj** i postępuj zgodnie z monitami, aby uwierzytelnić Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="przycisk Autoryzuj dla Power BI":::

4. Wypełnij formularz danych wyjściowych następującymi szczegółami i wybierz pozycję **Zapisz**:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Alias danych wyjściowych  |  MyPBIoutput  |
   |Obszar roboczy grupy| Mój obszar roboczy |
   |Nazwa zestawu danych  |   ASAdataset  |
   |Nazwa tabeli |  ASATable  |
   | Tryb uwierzytelniania | Token użytkownika |

   ![Konfigurowanie danych wyjściowych usługi Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   W tym samouczku jest stosowany tryb uwierzytelniania *tokenu użytkownika* . Aby użyć tożsamości zarządzanej, zobacz [Używanie tożsamości zarządzanej do uwierzytelniania zadania Azure Stream Analytics w Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Tworzenie zapytań w celu przekształcania danych w czasie rzeczywistym

W tym momencie masz zadanie Stream Analytics skonfigurowane do odczytywania przychodzącego strumienia danych. Następnym krokiem jest utworzenie zapytania, które analizuje dane w czasie rzeczywistym. Zapytania korzystają z języka przypominającego SQL, który ma Niektóre rozszerzenia specyficzne dla Stream Analytics.

W tej części samouczka utworzysz i testujesz kilka zapytań, aby dowiedzieć się więcej na temat tego, w jaki sposób można przekształcić strumień wejściowy na potrzeby analizy. 

Zapytania utworzone w tym miejscu będą po prostu wyświetlać przekształcone dane na ekranie. W dalszej części należy napisać przekształcone dane do Power BI.

Aby dowiedzieć się więcej o języku, zobacz [informacje dotyczące języka zapytań Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Testowanie przy użyciu kwerendy przekazującej

Jeśli chcesz zarchiwizować każde zdarzenie, możesz użyć zapytania przekazującego, aby odczytać wszystkie pola w ładunku zdarzenia.

1. Przejdź do zadania Stream Analytics w Azure Portal a następnie wybierz pozycję **zapytanie** w obszarze *topologia zadania*. 

2. W oknie zapytania wprowadź następujące zapytanie:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Podobnie jak w przypadku języka SQL słowa kluczowe nie są rozróżniane wielkości liter, a odstępy nie są istotne.

    W tym zapytaniu `CallStream` jest alias, który został określony podczas tworzenia danych wejściowych. Jeśli użyto innego aliasu, Użyj tej nazwy zamiast tego.

3. Wybierz **kwerendę testową**.

    Zadanie Stream Analytics uruchamia zapytanie dotyczące przykładowych danych z wejścia i wyświetla dane wyjściowe w dolnej części okna. Wyniki wskazują, że centrum zdarzeń i zadanie usługi Stream Analytics są prawidłowo skonfigurowane.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Przykładowe dane wyjściowe zapytania testowego":::

    Dokładna liczba rekordów, które widzisz, zależy od liczby rekordów przechwyconych w przykładzie.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Zmniejsz liczbę pól przy użyciu projekcji kolumny

W wielu przypadkach analiza nie potrzebuje wszystkich kolumn ze strumienia wejściowego. Można użyć zapytania do zaprojektowania mniejszego zestawu zwracanych pól niż w zapytaniu przekazującym.

Uruchom następujące zapytanie i zwróć uwagę na dane wyjściowe.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Liczba wywołań przychodzących według regionu: okno wirowania z agregacją

Załóżmy, że chcesz policzyć liczbę wywołań przychodzących na region. W przypadku przesyłania strumieniowego danych, gdy chcesz wykonać funkcje agregujące, takie jak zliczanie, musisz podzielić strumień na jednostki czasowe, ponieważ sam strumień danych jest efektywnie nieskończony. Można to zrobić przy użyciu [funkcji okna](stream-analytics-window-functions.md)analizy przesyłania strumieniowego. Następnie można współpracować z danymi wewnątrz tego okna jako jednostką.

W przypadku tej transformacji potrzebna jest sekwencja okien czasowych, które nie nakładają się — każde okno będzie miało dyskretny zestaw danych, które można zgrupować i agregować. Ten typ okna jest określany mianem *okna wirowania*. W oknie wirowania można uzyskać liczbę wywołań przychodzących pogrupowanych według `SwitchNum` , która reprezentuje kraj/region, z którego pochodzi wywołanie.

1. Wklej następujące zapytanie w edytorze zapytań:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    To zapytanie używa `Timestamp By` słowa kluczowego w `FROM` klauzuli do określenia pola sygnatury czasowej w strumieniu wejściowym, który ma być używany do definiowania okna wirowania. W takim przypadku okno dzieli dane na segmenty według `CallRecTime` pola w każdym rekordzie. (Jeśli nie określono żadnego pola, operacja okna używa czasu, w którym każde zdarzenie dociera do centrum zdarzeń. Zobacz "godzina dostarczenia a czas aplikacji" w [Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference). 

    Projekcja zawiera `System.Timestamp` , która zwraca sygnaturę czasową końca każdego okna. 

    Aby określić, że chcesz użyć okna wirowania, użyj funkcji [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) w `GROUP BY` klauzuli. W funkcji należy określić jednostkę czasu (w dowolnym miejscu od mikrosekundowych do dnia) i rozmiar okna (liczbę jednostek). W tym przykładzie okno wirowania składa się z 5-sekundowych interwałów, dzięki czemu będzie można obliczyć liczbę według kraju/regionu dla każdego 5-sekundowego wywołania.

2. Wybierz **kwerendę testową**. W wynikach należy zauważyć, że sygnatury czasowe w obszarze **WindowEnd** są w odstępach 5-sekundowych.

### <a name="detect-sim-fraud-using-a-self-join"></a>Wykrywanie oszustwa karty SIM przy użyciu samosprzężenia

W tym przykładzie Rozważ użycie fałszywych wywołań, które pochodzą z tego samego użytkownika, ale w różnych lokalizacjach w ciągu 5 sekund od siebie. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii.

Aby sprawdzić te przypadki, można użyć samosprzężenia danych przesyłanych strumieniowo w celu dołączenia strumienia do samego siebie na podstawie `CallRecTime` wartości. Następnie można wyszukać rekordy wywołań, w których `CallingIMSI` wartość (numer źródłowy) jest taka sama, ale `SwitchNum` wartość (kraj/region pochodzenia) nie jest taka sama.

W przypadku użycia sprzężenia z danymi przesyłania strumieniowego, sprzężenie musi zawierać pewne ograniczenia dotyczące czasu, w którym można oddzielić pasujące wiersze. Jak wspomniano wcześniej, dane przesyłane strumieniowo są efektywnie nieograniczone. Granice czasu dla relacji są określane wewnątrz `ON` klauzuli sprzężenia przy użyciu `DATEDIFF` funkcji. W takim przypadku sprzężenie jest oparte na 5-sekundowym interwale danych wywołań.

1. Wklej następujące zapytanie w edytorze zapytań:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    To zapytanie jest podobne do żadnego sprzężenia SQL, z wyjątkiem `DATEDIFF` funkcji w sprzężeniu. Ta wersja programu `DATEDIFF` jest specyficzna dla usługi Stream Analytics i musi znajdować się w tej samej `ON...BETWEEN` klauzuli. Parametry są jednostką czasu (w sekundach) i aliasy dwóch źródeł dla sprzężenia. Różni się to od standardowej funkcji języka SQL `DATEDIFF` .

    `WHERE`Klauzula zawiera warunek, który flaguje oszukańcze wywołanie: Przełączniki źródłowe nie są takie same.

2. Wybierz **kwerendę testową**. Przejrzyj dane wyjściowe, a następnie wybierz pozycję **Zapisz zapytanie**.

## <a name="start-the-job-and-visualize-output"></a>Uruchamianie zadania i wizualizacja danych wyjściowych

1. Aby uruchomić zadanie, przejdź do **omówienia** zadania i wybierz pozycję **Uruchom**.

2. Wybierz wartość **Teraz** jako godzinę rozpoczęcia generowania danych wyjściowych zadania, a następnie wybierz pozycję **Uruchom**. Stan zadania możesz wyświetlić na pasku powiadomień.

3. Po pomyślnym zakończeniu zadania przejdź do usługi [Power BI](https://powerbi.com/) i zaloguj się przy użyciu konta służbowego. Jeśli zapytanie zadania usługi Stream Analytics generuje wyniki, utworzony zestaw danych *ASAdataset* znajduje się na karcie **Zestawy danych**.

4. W obszarze roboczym usługi Power BI wybierz pozycję **+ Utwórz**, aby utworzyć nowy pulpit nawigacyjny o nazwie *Fraudulent Calls* (Fałszywe połączenia).

5. W górnej części okna wybierz pozycję **Edytuj** i **Dodaj kafelek**. Następnie wybierz pozycje **Niestandardowe dane przesyłane strumieniowo** i **Dalej**. Wybierz pozycję **ASAdataset** w obszarze **Twoje zestawy danych**. Wybierz **kartę** z listy rozwijanej **typ wizualizacji** i Dodaj **fałszywe wywołania** do **pól**. Wybierz pozycję **Dalej**, aby wprowadzić nazwę kafelka, a następnie wybierz pozycję **Zastosuj**, aby utworzyć kafelek.

   ![Tworzenie kafelków pulpitu nawigacyjnego usługi Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Wykonaj ponownie krok 5, wybierając następujące opcje:
   * Gdy pojawi się opcja Typ wizualizacji, wybierz wartość Wykres liniowy.
   * Dodaj oś i wybierz pozycję **windowend**.
   * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
   * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.

7. Po dodaniu obydwu kafelków pulpit nawigacyjny powinien wyglądać jak na poniższym przykładzie. Zwróć uwagę, że jeśli aplikacja nadawcy centrum zdarzeń i aplikacja usługi Stream Analytics są uruchomione, pulpit nawigacyjny Power BI okresowo aktualizuje jako nowe dane.

   ![Wyświetlanie wyników na pulpicie nawigacyjnym usługi Power BI](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Osadzanie pulpitu nawigacyjnego Power BI w aplikacji sieci Web

W tej części samouczka użyjesz przykładowej aplikacji sieci Web [ASP.NET](https://asp.net/) utworzonej przez zespół Power BI, aby osadzić pulpit nawigacyjny. Aby uzyskać więcej informacji na temat osadzania pulpitów nawigacyjnych, zobacz artykuł [Osadzanie za pomocą usługi Power BI](/power-bi/developer/embedding).

Aby skonfigurować aplikację, przejdź do repozytorium usługi [PowerBI — Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) w witrynie GitHub i postępuj zgodnie z instrukcjami w sekcji **Informacje o użytkowniku** . Użyj adresów URL przekierowania i strony głównej w podsekcji **Integruj-Web-App** . Ponieważ korzystamy z przykładu pulpitu nawigacyjnego, użyj kodu przykładowego **Integruj-Web-App** znajdującego się w [repozytorium GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Po uruchomieniu aplikacji w przeglądarce wykonaj następujące kroki, aby osadzić utworzony wcześniej pulpit nawigacyjny na stronie internetowej:

1. Wybierz pozycję **Zaloguj się do Power BI**, co spowoduje przyznanie aplikacji dostępu do pulpitów nawigacyjnych na koncie Power BI.

2. Wybierz przycisk **Get Dashboards (Pobierz pulpity nawigacyjne)**, aby wyświetlić pulpity nawigacyjne Twojego konta w tabeli. Znajdź nazwę pulpitu nawigacyjnego utworzonego wcześniej, **powerbi-embedded-dashboard**, i skopiuj odpowiedni element **EmbedUrl**.

3. Na koniec wklej wartość **EmbedUrl** w odpowiednim polu tekstowym i wybierz pozycję **Embed Dashboard (Osadź pulpit nawigacyjny)**. Możesz teraz wyświetlić ten sam pulpit nawigacyjny osadzony w aplikacji internetowej.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono proste zadanie usługi Stream Analytics, przeanalizowano dane przychodzące i przedstawiono wyniki na pulpicie nawigacyjnym usługi Power BI. Aby dowiedzieć się więcej na temat zadań usługi Stream Analytics, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Uruchamianie funkcji Azure Functions w ramach zadań usługi Stream Analytics](stream-analytics-with-azure-functions.md)

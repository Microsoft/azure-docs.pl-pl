---
title: Analiza tonacji w czasie rzeczywistym w usłudze Twitter przy użyciu Azure Stream Analytics
description: W tym artykule opisano, jak używać Stream Analytics do analizy tonacji w czasie rzeczywistym. Wskazówki krok po kroku dotyczące generowania zdarzeń do danych na aktywnym pulpicie nawigacyjnym.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 6a461ad906f7611c8a13e2ee495f4d2f62fedd53
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734846"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analiza opinii w usłudze Twitter w czasie rzeczywistym za pomocą usługi Azure Stream Analytics

W tym artykule przedstawiono sposób tworzenia rozwiązania do analizy mediów społecznościowych tonacji przez umieszczenie zdarzeń w czasie rzeczywistym w usłudze Azure Event Hubs. Napisz Azure Stream Analytics zapytanie, aby analizować dane i przechowywać wyniki do późniejszego użycia, lub utworzyć pulpit nawigacyjny [Power BI](https://powerbi.com/) , aby uzyskać wgląd w dane w czasie rzeczywistym.

Narzędzia analizy mediów społecznościowych pomagają organizacjom zrozumieć tematy trendów. Trendy dotyczące trendów to tematy i stanowiska, które mają wysoką ilość ogłoszeń na mediach społecznościowych. Analiza tonacji, która jest również nazywana *opinią*, używa narzędzi analitycznych mediów społecznościowych do określenia postanowień w kierunku produktu lub pomysłu. 

Analiza trendów w czasie rzeczywistym jest doskonałym przykładem narzędzia analitycznego, ponieważ model subskrypcji hasztagów pozwala na nasłuchiwanie określonych słów kluczowych (hasztagów) i opracowywanie analizy tonacjii źródła danych.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenariusz: Analiza tonacji mediów społecznościowych w czasie rzeczywistym

Firma, która ma witrynę internetową multimediów, chce uzyskać korzyści w stosunku do swoich konkurentów przez wypróbowanie zawartości witryny, która jest bezpośrednio istotna dla czytelników. Firma korzysta z analizy mediów społecznościowych dla tematów, które są istotne dla czytelników, wykonując analizę tonacji danych w czasie rzeczywistym.

Aby identyfikować tematy trendów w czasie rzeczywistym w serwisie Twitter, firma potrzebuje analiz w czasie rzeczywistym o woluminie tweetu i tonacji dla kluczowych tematów.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku jak korzystać z aplikacji klienckiej, która nawiązuje połączenie z usługą Twitter, i wyszukuje tweety, które mają określone hasztagów (które można ustawić). Aby uruchomić aplikację i analizować tweety przy użyciu usługi Azure Streaming Analytics, musisz mieć następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Konto w usłudze [Twitter](https://twitter.com) .

* Aplikacja TwitterClientCore, która odczytuje kanał informacyjny usługi Twitter. Aby pobrać tę aplikację, Pobierz [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Zainstaluj [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools/?tabs=netcore2x) w wersji 2.1.0.

Poniżej znajduje się Architektura rozwiązania, które ma zostać wdrożone.

   ![Diagram przedstawiający różne fragmenty usług i aplikacji użytych do skompilowania rozwiązania.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Diagram rozwiązania")

## <a name="create-an-event-hub-for-streaming-input"></a>Tworzenie centrum zdarzeń na potrzeby danych wejściowych przesyłania strumieniowego

Przykładowa aplikacja generuje zdarzenia i wypycha je do centrum zdarzeń platformy Azure. Event Hubs platformy Azure to preferowana metoda pozyskiwania zdarzeń dla Stream Analytics. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Tworzenie centrum zdarzeń w przestrzeni nazw i centrum zdarzeń
W tej sekcji utworzysz przestrzeń nazw centrum zdarzeń i dodasz centrum zdarzeń do tej przestrzeni nazw. Przestrzenie nazw centrum zdarzeń są używane do logicznego pogrupowania powiązanych wystąpień magistrali zdarzeń. 

1. Zaloguj się do Azure Portal i wybierz pozycję **Utwórz zasób**. Następnie. Wyszukaj **Event Hubs** a następnie wybierz pozycję **Utwórz**.

2. Na stronie **Tworzenie przestrzeni nazw** wprowadź nazwę przestrzeni nazw. Możesz użyć dowolnej nazwy dla przestrzeni nazw, ale nazwa musi być prawidłowa dla adresu URL i musi być unikatowa na platformie Azure. 
    
3. Wybierz warstwę cenową i subskrypcję, a następnie Utwórz lub wybierz grupę zasobów. Następnie wybierz lokalizację i wybierz pozycję **Utwórz**. 
 
4. Po zakończeniu wdrażania obszaru nazw przejdź do grupy zasobów i Znajdź przestrzeń nazw centrum zdarzeń na liście zasobów platformy Azure. 

5. Z nowej przestrzeni nazw wybierz pozycję **+ &nbsp; centrum zdarzeń**. 

6. Nazwij nowe centrum zdarzeń *socialtwitter-EH*. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ potrzebujesz tej nazwy później. Nie trzeba ustawiać żadnych innych opcji centrum zdarzeń.
 
7. Wybierz przycisk **Utwórz**.

### <a name="grant-access-to-the-event-hub"></a>Udzielanie dostępu do centrum zdarzeń

Aby proces mógł wysyłać dane do centrum zdarzeń, centrum zdarzeń musi mieć zasady zezwalające na dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  Na pasku nawigacyjnym po lewej stronie przestrzeni nazw centrów zdarzeń wybierz pozycję **Event Hubs**, która znajduje się w sekcji **jednostki** . Następnie wybierz właśnie utworzony centrum zdarzeń.

2.  Na pasku nawigacyjnym po lewej stronie wybierz pozycję **zasady dostępu współdzielonego** w obszarze **Ustawienia**.

    >[!NOTE]
    >W obszarze dla przestrzeni nazw centrum zdarzeń i centrum zdarzeń jest dostępna opcja zasad dostępu współdzielonego. Upewnij się, że Pracujesz w kontekście centrum zdarzeń, a nie do ogólnej przestrzeni nazw centrum zdarzeń.

3.  Na stronie zasady dostępu wybierz pozycję **+ Dodaj**. Następnie wprowadź *socialtwitter — dostęp* do **nazwy zasad** i zaznacz pole wyboru **Zarządzaj** .
 
4.  Wybierz przycisk **Utwórz**.

5.  Po wdrożeniu zasad wybierz zasady z listy zasad dostępu współdzielonego.

6.  Znajdź pole z etykietą **Parametry połączenia klucz podstawowy** i wybierz przycisk Kopiuj obok parametrów połączenia.
 
7.  Wklej parametry połączenia do edytora tekstu. Te parametry połączenia są potrzebne dla następnej sekcji po wprowadzeniu niewielkich zmian.

   Parametry połączenia wyglądają następująco:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Zwróć uwagę, że parametry połączenia zawierają wiele par klucz-wartość, oddzielone średnikami: `Endpoint` , `SharedAccessKeyName` , `SharedAccessKey` i `EntityPath` .  

   > [!NOTE]
   > W celu zapewnienia bezpieczeństwa części ciągu połączenia w przykładzie zostały usunięte.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurowanie i uruchamianie aplikacji klienckiej usługi Twitter

Aplikacja kliencka pobiera zdarzenia tweetu bezpośrednio z usługi Twitter. Aby to zrobić, musi mieć uprawnienia do wywoływania interfejsów API przesyłania strumieniowego w usłudze Twitter. Aby skonfigurować to uprawnienie, należy utworzyć aplikację w usłudze Twitter, która generuje unikatowe poświadczenia (na przykład token OAuth). Następnie można skonfigurować aplikację kliencką tak, aby korzystała z tych poświadczeń podczas wykonywania wywołań interfejsu API. 

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Jeśli nie masz jeszcze aplikacji usługi Twitter, której możesz użyć do tego przewodnika, możesz ją utworzyć. Musisz mieć już konto w usłudze Twitter.

> [!NOTE]
> Dokładny proces w serwisie Twitter służący do tworzenia aplikacji i uzyskiwania kluczy, wpisów tajnych i tokenu może ulec zmianie. Jeśli te instrukcje nie pasują do zawartości widocznej w witrynie Twitter, zapoznaj się z dokumentacją dla deweloperów w usłudze Twitter.

1. W przeglądarce internetowej przejdź do usługi [Twitter dla deweloperów](https://developer.twitter.com/en/apps), Utwórz konto dewelopera i wybierz pozycję **Utwórz aplikację**. Może zostać wyświetlony komunikat informujący o konieczności zastosowania konta dewelopera usługi Twitter. Możesz to zrobić bezpłatnie, a po zatwierdzeniu aplikacji powinna zostać wyświetlona wiadomość e-mail z potwierdzeniem. Zatwierdzenie konta dewelopera może potrwać kilka dni.

   ![Zrzut ekranu przedstawia przycisk Utwórz aplikację.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Szczegóły aplikacji usługi Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

   ![Zrzut ekranu przedstawia okienko szczegółów aplikacji, w którym można wprowadzać wartości dla aplikacji.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Szczegóły aplikacji usługi Twitter")

3. Na stronie aplikacja wybierz kartę **klucze i tokeny** , a następnie skopiuj wartości **klucza interfejsu API klienta** i **klucza tajnego interfejsu API konsumenta**. Ponadto w celu wygenerowania tokenów dostępu wybierz pozycję **Utwórz** w obszarze **klucz dostępu token i dostęp do klucza tajnego tokenu** dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

   Zapisz wartości dotyczące aplikacji usługi Twitter. Potrzebujesz wartości później.

> [!NOTE]
> Klucze i wpisy tajne aplikacji usługi Twitter zapewniają dostęp do konta usługi Twitter. Traktuj te informacje jako poufne, tak samo jak hasło usługi Twitter. Na przykład nie osadzaj tych informacji w aplikacji, którą przekazujesz innym osobom. 

### <a name="configure-the-client-application"></a>Konfigurowanie aplikacji klienckiej

Utworzyliśmy aplikację kliencką, która nawiązuje połączenie z danymi usługi Twitter przy użyciu [interfejsów API przesyłania strumieniowego usługi Twitter](https://dev.twitter.com/streaming/overview) w celu zbierania zdarzeń tweetów dotyczących określonego zestawu tematów.

Przed uruchomieniem aplikacji wymagane są pewne informacje, takie jak klucze serwisu Twitter i parametry połączenia centrum zdarzeń.

1. Upewnij się, że pobrano aplikację [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) , zgodnie z opisem w sekcji wymagania wstępne.

2. Użyj edytora tekstów, aby otworzyć plik *App.config* . Wprowadź następujące zmiany do `<appSettings>` elementu:

   * Ustaw wartość `oauth_consumer_key` klucza klienta usługi Twitter (klucz interfejsu API). 
   * Ustaw `oauth_consumer_secret` na wpis tajny klienta usługi Twitter (klucz tajny interfejsu API).
   * Ustaw `oauth_token` na token dostępu w usłudze Twitter.
   * Ustaw `oauth_token_secret` klucz tajny tokenu dostępu w usłudze Twitter.
   * Ustaw `EventHubNameConnectionString` Parametry połączenia.
   * Ustaw `EventHubName` nazwę centrum zdarzeń (która jest wartością ścieżki jednostki).

3. Otwórz wiersz polecenia i przejdź do katalogu, w którym znajduje się aplikacja TwitterClientCore. Użyj polecenia, `dotnet build` Aby skompilować projekt. Następnie użyj polecenia, `dotnet run` Aby uruchomić aplikację. Aplikacja wysyła tweety do centrum zdarzeń.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy zdarzenia tweetu są przesyłane strumieniowo w czasie rzeczywistym z serwisu Twitter, można skonfigurować zadanie Stream Analytics, aby analizować te zdarzenia w czasie rzeczywistym.

1. W Azure Portal przejdź do grupy zasobów, a następnie wybierz pozycję **+ Dodaj**. Następnie wyszukaj **zadanie Stream Analytics** i wybierz pozycję **Utwórz**.

2. Nazwij zadanie `socialtwitter-sa-job` i określ subskrypcję, grupę zasobów i lokalizację.

    Dobrym pomysłem jest umieszczenie zadania i centrum zdarzeń w tym samym regionie w celu uzyskania najlepszej wydajności, aby nie zapłaciła za transfer danych między regionami.

3. Wybierz przycisk **Utwórz**. Następnie przejdź do zadania po zakończeniu wdrożenia.

## <a name="specify-the-job-input"></a>Określ dane wejściowe zadania

1. W zadaniu Stream Analytics wybierz pozycję **dane wejściowe** z menu po lewej stronie w obszarze **topologia zadania**.

2. Wybierz pozycję Dodaj centrum zdarzeń **+ &nbsp; wejścia strumienia**  >  . Wypełnij nowy formularz **wejściowy** z następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych| *TwitterStream* | Wprowadź alias dla danych wejściowych. |
   |Subskrypcja  | \<Your subscription\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Przestrzeń nazw centrum zdarzeń | *ASA-Twitter-eventhub* |
   |Nazwa centrum zdarzeń | *socialtwitter — EH* | Wybierz pozycję *Użyj istniejącej*. Następnie wybierz utworzony centrum zdarzeń.|
   |Typ kompresji zdarzenia| GZip | Typ kompresji danych.|

   Pozostaw pozostałe wartości domyślne i wybierz pozycję **Zapisz**.

## <a name="specify-the-job-query"></a>Określ zapytanie dotyczące zadania

Stream Analytics obsługuje prosty, deklaratywny model zapytań, który opisuje przekształcenia. Aby dowiedzieć się więcej o języku, zobacz [informacje dotyczące języka zapytań Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). Ten przewodnik ułatwia tworzenie i testowanie kilku zapytań za pośrednictwem danych usługi Twitter.

Aby porównać liczbę wzmianki między tematami, można użyć [okna wirowania](/stream-analytics-query/tumbling-window-azure-stream-analytics) , aby uzyskać liczbę wzmianki według tematu co pięć sekund.

1. W **przeglądzie** zadania wybierz pozycję **Edytuj zapytanie** w prawym górnym rogu pola zapytania. Na platformie Azure są wyświetlane dane wejściowe i wyjściowe, które są skonfigurowane dla zadania, a następnie można utworzyć zapytanie w celu przekształcenia strumienia wejściowego w taki sposób, w jaki jest wysyłany do danych wyjściowych.

2. Zmień zapytanie w edytorze zapytań na następujące:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Dane zdarzenia z komunikatów powinny pojawić się w oknie **podglądu danych wejściowych** poniżej zapytania. Upewnij się, że **Widok** jest ustawiony na **Format JSON**. Jeśli nie widzisz żadnych danych, upewnij się, że Generator danych wysyła zdarzenia do centrum zdarzeń i że wybrano **gzip** jako typ kompresji dla danych wejściowych.

4. Wybierz **kwerendę testową** i zwróć uwagę na wyniki w oknie **wyników testu** poniżej zapytania.

5. Zmień zapytanie w edytorze kodu na następujące i wybierz **zapytanie testowe**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. To zapytanie zwraca wszystkie tweety, które zawierają słowo kluczowe *Azure*.

## <a name="create-an-output-sink"></a>Tworzenie ujścia danych wyjściowych

Zdefiniowano teraz strumień zdarzeń, dane wejściowe centrum zdarzeń do zdarzeń pozyskiwania oraz zapytanie w celu przeprowadzenia transformacji przez strumień. Ostatnim krokiem jest zdefiniowanie ujścia danych wyjściowych dla zadania.  

W tym przewodniku krok po kroku napiszesz zagregowane zdarzenia tweetu z kwerendy zadań do usługi Azure Blob Storage.  Możesz również wypchnąć wyniki do Azure SQL Database, Azure Table Storage, Event Hubs lub Power BI, w zależności od potrzeb aplikacji.

## <a name="specify-the-job-output"></a>Określ dane wyjściowe zadania

1. W sekcji **topologia zadania** w menu nawigacji po lewej stronie wybierz pozycję dane **wyjściowe**. 

2. Na stronie dane **wyjściowe** kliknij pozycje **+ &nbsp; Dodaj** i **Magazyn obiektów BLOB/Data Lake Storage Gen2**:

   * **Alias wyjściowy**: Użyj nazwy `TwitterStream-Output` . 
   * **Opcje importowania**: Wybierz **pozycję Wybierz magazyn z subskrypcji**.
   * **Konto magazynu**. Wybierz swoje konto magazynu.
   * **Kontener**. Wybierz pozycję **Utwórz nowy** i wprowadź `socialtwitter` .
   
4. Wybierz pozycję **Zapisz**.   

## <a name="start-the-job"></a>Uruchamianie zadania

Określono dane wejściowe zadania, zapytania i wyjścia. Możesz przystąpić do uruchomienia zadania Stream Analytics.

1. Upewnij się, że aplikacja TwitterClientCore jest uruchomiona. 

2. W obszarze przegląd zadania wybierz pozycję **Rozpocznij**.

3. Na stronie **Uruchamianie zadania** , w polu **czas rozpoczęcia zadania**, wybierz pozycję **teraz** , a następnie wybierz pozycję **Uruchom**.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)

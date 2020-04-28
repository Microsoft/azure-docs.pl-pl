---
title: Przesyłanie strumieniowe danych przy użyciu integracji Stream Analytics (wersja zapoznawcza)
description: Używanie Azure Stream Analytics do przesyłania strumieniowego danych do bazy danych Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820840"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Przesyłanie strumieniowe danych przy użyciu integracji Stream Analytics Azure SQL Database (wersja zapoznawcza)

Użytkownicy mogą teraz pozyskiwanie, przetwarzać, wyświetlać i analizować dane przesyłane strumieniowo w czasie rzeczywistym w tabeli bezpośrednio z bazy danych SQL w Azure Portal przy użyciu [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). To środowisko umożliwia korzystanie z wielu różnych scenariuszy, takich jak połączone samochodu, zdalne monitorowanie, wykrywanie oszustw i wiele innych. W Azure Portal można wybrać źródło zdarzeń (centrum zdarzeń/IoT Hub), wyświetlić przychodzące zdarzenia w czasie rzeczywistym i wybrać tabelę do przechowywania zdarzeń. Możesz również napisać zapytania dotyczące języka zapytań Stream Analytics w portalu, aby przekształcić zdarzenia przychodzące i przechowywać je w wybranej tabeli. Ten nowy punkt wejścia stanowi uzupełnienie środowiska tworzenia i konfiguracji, które już istnieją w Stream Analytics. To środowisko jest uruchamiane z kontekstu bazy danych, dzięki czemu można szybko skonfigurować zadanie Stream Analytics i bezproblemowo nawigować między Azure SQL Database i Stream Analytics środowiska.

![Przepływ Stream Analytics](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

- Minimalne przełączanie kontekstu: można zacząć od SQL Database w portalu i rozpoczynać pozyskiwanie danych w czasie rzeczywistym do tabeli bez przełączania się do innej usługi. 
- Zmniejszona liczba kroków: kontekst bazy danych i tabeli służy do wstępnego skonfigurowania zadania Stream Analytics.
- Dodatkowa łatwość użycia z podglądem danych: Podgląd danych przychodzących ze źródła zdarzeń (centrum zdarzeń/IoT Hub) w kontekście wybranej tabeli 


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/). 
- Baza danych SQL. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie pojedynczej bazy danych w Azure SQL Database](sql-database-single-database-get-started.md).
- Reguła zapory zezwalająca komputerowi na łączenie się z serwerem Azure SQL. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurowanie integracji usługi Stream Analytics

1. Zaloguj się do witryny Azure Portal. 
2. Przejdź do bazy danych SQL, w której chcesz pozyskać dane przesyłane strumieniowo. Wybierz pozycję **Stream Analytics (wersja zapoznawcza)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Aby rozpocząć pozyskiwanie danych przesyłanych strumieniowo do tej bazy danych SQL, wybierz pozycję **Utwórz** i nadaj jej nazwę zadanie przesyłania strumieniowego, a następnie wybierz kolejno pozycje **Dalej: dane wejściowe**. 

    ![Tworzenie zadania Stream Analytics](media/sql-database-stream-analytics/create-job.png)

4. Wprowadź szczegóły źródła zdarzeń, a następnie wybierz pozycję **Dalej: dane wyjściowe**.

   - **Typ danych wejściowych**: centrum zdarzeń/IoT Hub
   - **Alias wejściowy**: Wprowadź nazwę identyfikującą źródło zdarzeń 
   - **Subskrypcja**: taka sama jak subskrypcja SQL Database 
   - **Przestrzeń nazw centrum zdarzeń**: nazwa dla przestrzeni nazw 
   - **Nazwa centrum zdarzeń**: nazwa centrum zdarzeń w wybranej przestrzeni nazw 
   - **Nazwa zasad centrum zdarzeń** (domyślnie tworzenia nowej): nadaj nazwę zasadom 
   - **Grupa konsumentów centrum zdarzeń** (domyślnie do utworzenia nowego): nadaj nazwę grupie odbiorców  
     - Zalecamy utworzenie grupy odbiorców i zasad dla każdego nowego zadania Azure Stream Analytics utworzonego w tym miejscu. Grupy konsumentów zezwalają tylko na pięć współbieżnych czytników, dlatego udostępnienie dedykowanej grupy odbiorców dla każdego zadania spowoduje uniknięcie wszelkich błędów, które mogą wynikać z przekroczenia tego limitu. Dedykowane zasady umożliwiają obracanie klucza lub Odwoływanie uprawnień bez wpływu na inne zasoby.

    ![Tworzenie zadania Stream Analytics](media/sql-database-stream-analytics/create-job-output.png)

5. Wybierz tabelę, do której chcesz pozyskać dane przesyłane strumieniowo. Po zakończeniu wybierz pozycję **Utwórz**.
   - **Nazwa użytkownika**, **hasło**: Wprowadź poświadczenia dla uwierzytelniania programu SQL Server. Wybierz przycisk **Weryfikuj**.
   - **Tabela**: wybierz pozycję **Utwórz nową** lub **Użyj istniejącej**. W tym przepływie Wybierzmy pozycję **Utwórz**. Spowoduje to utworzenie nowej tabeli podczas uruchamiania zadania usługi Stream Analytics.

    ![Tworzenie zadania Stream Analytics](media/sql-database-stream-analytics/create.png)

6. Zostanie otwarta strona zapytania z następującymi szczegółami:

   - Twoje dane **wejściowe** (Źródło zdarzeń wejściowych), z którego będą pozyskiwane dane  
   - Dane **wyjściowe** (Tabela wyjściowa), w której będą przechowywane przekształcone dane 
   - Przykładowe [zapytanie SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) z instrukcją SELECT. 
   - **Podgląd danych wejściowych**: zawiera migawkę najnowszych danych przychodzących ze źródła zdarzeń wejściowych.  
     - Typ serializacji w danych jest wykrywany automatycznie (JSON/CSV). Można ją zmienić ręcznie również na format JSON/CSV/AVRO. 
     - Dane przychodzące można wyświetlać w formacie tabeli lub w formacie nieprzetworzonym. 
     - Jeśli wyświetlane dane nie są aktualne, wybierz pozycję **Odśwież** , aby wyświetlić najnowsze zdarzenia. 
     - Wybierz pozycję **Wybierz zakres czasu** , aby przetestować zapytanie względem określonego zakresu czasu zdarzeń przychodzących. 
     - Wybierz pozycję **Przekaż przykładowe dane wejściowe** , aby przetestować zapytanie przez przekazanie przykładowego pliku JSON/CSV. Aby uzyskać więcej informacji na temat testowania zapytania SAQL, zobacz [testowanie zadania Azure Stream Analytics przy użyciu przykładowych danych](../stream-analytics/stream-analytics-test-query.md). 

    ![zapytanie testowe](media/sql-database-stream-analytics/test-query.png)


   - **Wyniki testu**: Wybierz **kwerendę testową** i możesz zobaczyć wyniki zapytania przesyłania strumieniowego 

    ![wyniki testu](media/sql-database-stream-analytics/test-results.png)

   - **Schemat wyników testu**: pokazuje schemat wyników zapytania przesyłania strumieniowego po przetestowaniu. Upewnij się, że schemat wyników testu jest zgodny z schematem wyjściowym. 

    ![Schemat wyników testu](media/sql-database-stream-analytics/test-results-schema.png)


   - **Schemat wyjściowy**: zawiera schemat tabeli wybranej w kroku 5 (nowy lub istniejący).
     - Utwórz nowy: w przypadku wybrania tej opcji w kroku 5 schemat nie będzie jeszcze widoczny do momentu uruchomienia zadania przesyłania strumieniowego. Podczas tworzenia nowej tabeli wybierz odpowiedni indeks tabeli. Aby uzyskać więcej informacji na temat indeksowania tabeli, zobacz [opisane indeksy klastrowane i nieklastrowane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Użyj istniejącej: Jeśli w kroku 5 wybrano tę opcję, zostanie wyświetlony schemat wybranej tabeli. 
 
7. Po zakończeniu tworzenia & przetestowaniu zapytania wybierz pozycję **Zapisz zapytanie**. Wybierz pozycję **rozpocznij Stream Analytics zadanie** , aby rozpocząć pozyskiwanie przekształconych danych do tabeli SQL. Po sfinalizowaniu poniższych pól **Uruchom** zadanie. 
   - **Godzina rozpoczęcia produkcji**: definiuje godzinę pierwszego wyjścia zadania.  
     - Teraz: zadanie zostanie uruchomione teraz i przetworzy nowe przychodzące dane.
     - Niestandardowo: zadanie zostanie uruchomione teraz, ale będzie przetwarzać dane z określonego punktu w czasie (może to być w przeszłości lub w przyszłości). Aby uzyskać więcej informacji, zobacz [jak uruchomić zadanie Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Jednostki przesyłania strumieniowego**: Azure Stream Analytics są wyceniane według liczby jednostek przesyłania strumieniowego wymaganych do przetworzenia danych w usłudze. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Obsługa błędów danych wyjściowych**:  
     - Ponów próbę: w przypadku wystąpienia błędu Azure Stream Analytics ponawianie próby zapisu zdarzenia przez czas do momentu pomyślnego zapisu. Nie ma limitu czasu dla ponownych prób. Ostatecznie wszystkie kolejne zdarzenia są blokowane przed przetwarzaniem przez zdarzenie, które jest ponawiane. Ta opcja jest domyślną zasadą obsługi błędów wyjścia. 
     - Upuść: Azure Stream Analytics spowoduje porzucenie zdarzenia wyjściowego, które powoduje błąd konwersji danych. Porzuconych zdarzeń nie można odzyskać na potrzeby ponownego przetwarzania później. Wszystkie błędy przejściowe (na przykład błędy sieciowe) są ponawiane niezależnie od konfiguracji wyjściowych zasad obsługi błędów. 
   - **SQL Database Ustawienia wyjściowe**: opcja dziedziczenia schematu partycjonowania poprzedniego kroku zapytania, aby włączyć w pełni równoległą topologię z wieloma składnikami zapisywania do tabeli. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics danych wyjściowych Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Maksymalna liczba partii**: zalecany górny limit liczby rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.  
    Aby uzyskać więcej informacji na temat obsługi błędów wyjściowych, zobacz [wyjściowe zasady błędów w Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![Uruchom zadanie](media/sql-database-stream-analytics/start-job.png)

8. Po uruchomieniu zadania zobaczysz uruchomione zadanie na liście i możesz wykonać następujące czynności: 
   - **Uruchom/Zatrzymaj zadanie**: Jeśli zadanie jest uruchomione, możesz zatrzymać zadanie. Jeśli zadanie zostało zatrzymane, można je uruchomić. 
   - **Edytuj zadanie**: można edytować zapytanie. Jeśli chcesz wprowadzić więcej zmian w zadaniu, Dodaj więcej danych wejściowych/wyjściowych, a następnie otwórz zadanie w Stream Analytics. Opcja Edytuj jest wyłączona, gdy zadanie jest uruchomione. 
   - **Wyświetl podgląd tabeli wyjściowej**: można wyświetlić podgląd tabeli w edytorze zapytań SQL. 
   - **Otwórz w Stream Analytics**: Otwórz zadanie w usłudze Stream Analytics, aby wyświetlić monitorowanie, debugowanie szczegółów zadania. 


    ![zadania usługi Stream Analytics](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Następne kroki

- [Dokumentacja usługi Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Wzorce rozwiązań usługi Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)

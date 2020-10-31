---
title: Użyj kanału informacyjnego zmiany Azure Cosmos DB, aby wizualizować analizę danych w czasie rzeczywistym
description: W tym artykule opisano, jak źródło danych zmian może być używane przez firmę handlową do zrozumienia wzorców użytkowników, wykonywania analiz i wizualizacji w czasie rzeczywistym
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 1206d67b6a9d3823220b1ce1b7bd5b4b45e672fe
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93072709"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Użyj kanału informacyjnego zmiany Azure Cosmos DB, aby wizualizować analizę danych w czasie rzeczywistym
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kanał informacyjny zmiany Azure Cosmos DB jest mechanizmem do uzyskiwania ciągłego i przyrostowego źródła rekordów z kontenera usługi Azure Cosmos, ponieważ te rekordy są tworzone lub modyfikowane. Obsługa kanału informacyjnego zmiany działa przez nasłuchiwanie w kontenerze dla wszelkich zmian. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Aby dowiedzieć się więcej na temat źródła zmian, zobacz artykuł [Praca ze źródłem zmian](change-feed.md) . 

W tym artykule opisano sposób, w jaki dane mogą być używane przez firmę handlu elektronicznego do zrozumienia wzorców użytkowników, wykonywania analizy danych w czasie rzeczywistym i wizualizacji. Będziesz analizować zdarzenia, takie jak Użytkownik przeglądający element, Dodawanie elementu do swojego koszyka lub kupowanie elementu. Gdy wystąpi jedno z tych zdarzeń, zostanie utworzony nowy rekord, a źródło zmian rejestruje ten rekord. Zmień źródło, a następnie wyzwala serię kroków, co prowadzi do wizualizacji metryk, które analizują wydajność i aktywność firmy. Przykładowe metryki, które można wizualizować, obejmują przychody, unikatowych odwiedzających witrynę, najpopularniejsze elementy i średnią cenę elementów, które są wyświetlane i dodawane do koszyka, a w przeciwieństwie do zakupu. Te przykładowe metryki mogą pomóc firmie handlu elektronicznego w ocenie popularności witryny, opracowywaniu strategii dotyczących reklam i cen oraz podejmowanie decyzji dotyczących rodzaju spisu, w którym należy się zainwestować.

Interesuje Cię Obejrzyj film wideo dotyczący rozwiązania przed rozpoczęciem pracy, zobacz następujące wideo:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Składniki rozwiązania
Na poniższym diagramie przedstawiono przepływ danych i składniki wykorzystywane w rozwiązaniu:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Wizualizacja projektu" border="false":::
 
1. **Generowanie danych:** Symulator danych służy do generowania danych detalicznych, które reprezentują zdarzenia, takie jak Użytkownik przeglądający element, Dodawanie elementu do swojego koszyka i kupowanie elementu. Można wygenerować duży zestaw przykładowych danych przy użyciu generatora danych. Wygenerowane przykładowe dane zawierają dokumenty w następującym formacie:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Wygenerowane dane są przechowywane w kontenerze usługi Azure Cosmos.  

3. **Źródło zmian:** Źródło zmian będzie nasłuchiwać zmian w kontenerze usługi Azure Cosmos. Za każdym razem, gdy nowy dokument zostanie dodany do kolekcji (jest to spowodowane tym, że zdarzenie jest wyświetlane przez użytkownika, dodanie elementu do swojego koszyka lub zakupienie elementu), Źródło zmian spowoduje wyzwolenie [funkcji platformy Azure](../azure-functions/functions-overview.md).  

4. **Funkcja platformy Azure:** Funkcja platformy Azure przetwarza nowe dane i wysyła je do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-about.md).  

5. **Centrum zdarzeń:** Usługa Azure Event Hub przechowuje te zdarzenia i wysyła je do [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) w celu przeprowadzenia dalszej analizy.  

6. **Azure Stream Analytics:** Azure Stream Analytics definiuje zapytania do przetwarzania zdarzeń i wykonywania analizy danych w czasie rzeczywistym. Te dane są następnie wysyłane do [Power BI firmy Microsoft](/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI służy do wizualizacji danych wysyłanych przez Azure Stream Analytics. Możesz utworzyć pulpit nawigacyjny, aby zobaczyć, jak metryki zmieniają się w czasie rzeczywistym.  

## <a name="prerequisites"></a>Wymagania wstępne

* Microsoft .NET Framework 4.7.1 lub wyższy

* Microsoft .NET Core 2,1 (lub nowszy)

* Visual Studio z programowaniem platforma uniwersalna systemu Windows, programowanie aplikacji klasycznych dla platformy .NET i ASP.NET i programowanie dla sieci Web

* Subskrypcja Microsoft Azure

* Konto Power BI firmy Microsoft

* Pobierz [Azure Cosmos DB laboratorium zmiany źródła danych](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z usługi GitHub. 

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure 

Utwórz zasoby platformy Azure — Azure Cosmos DB, konto magazynu, centrum zdarzeń Stream Analytics wymagane przez rozwiązanie. Te zasoby zostaną wdrożone za poorednictwem szablonu Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć te zasoby: 

1. Ustaw zasady wykonywania programu Windows PowerShell na **nieograniczone** . Aby to zrobić, Otwórz program **Windows PowerShell jako administrator** i uruchom następujące polecenia:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z repozytorium GitHub pobranego w poprzednim kroku przejdź do folderu **Azure Resource Manager** , a następnie otwórz plik o nazwie **parameters.jsna** pliku.  

3. Podaj wartości dla cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name i parametrów, jak wskazano w **parameters.js** pliku. Musisz użyć nazw przystosowanych do każdego z zasobów później.  

4. W programie **Windows PowerShell** przejdź do folderu **Azure Resource Manager** i uruchom następujące polecenie:

   ```powershell
   .\deploy.ps1
   ```
5. Po wyświetleniu monitu wprowadź **Identyfikator subskrypcji** platformy Azure, **changefeedlab** jako nazwę grupy zasobów, a **Run1** dla nazwy wdrożenia. Po rozpoczęciu wdrażania zasobów ukończenie tego procesu może potrwać do 10 minut.

## <a name="create-a-database-and-the-collection"></a>Tworzenie bazy danych i kolekcji

Teraz utworzysz kolekcję, która będzie przechowywać zdarzenia w witrynie handlu elektronicznego. Gdy użytkownik przegląda element, dodaje element do swojego koszyka lub kupuje element, kolekcja otrzyma rekord zawierający akcję ("Wyświetlono", "dodano" lub "zakupione"), nazwę danego elementu, cenę danego elementu i numer IDENTYFIKACYJNy danego koszyka użytkownika.

1. Przejdź do [Azure Portal](https://portal.azure.com/) i znajdź **konto Azure Cosmos DB** utworzone w ramach wdrożenia szablonu.  

2. W okienku **Eksplorator danych** wybierz pozycję **Nowa kolekcja** i wypełnij formularz poniższymi informacjami:  

   * W polu **Identyfikator bazy danych** wybierz pozycję **Utwórz nowy** , a następnie wprowadź **changefeedlabdatabase** . Nie zaznaczaj pola wyboru **zainicjuj przepływność bazy danych** .  
   * W polu Identyfikator **kolekcji** wprowadź **changefeedlabcollection** .  
   * W polu **klucz partycji** wpisz **/Item** . Jest to rozróżniana wielkość liter, dlatego upewnij się, że wprowadzono ją poprawnie.  
   * W polu **przepływność** wprowadź wartość **10000** .  
   * Wybierz przycisk **OK** .  

3. Następnie utwórz kolejną kolekcję o nazwie **dzierżawy** na potrzeby przetwarzania strumieniowego źródła zmian. Kolekcja dzierżawcy koordynuje przetwarzanie źródła zmian przez wielu pracowników. Oddzielna kolekcja jest używana do przechowywania dzierżaw z jedną dzierżawą na partycję.  

4. Wróć do okienka **Eksplorator danych** i wybierz pozycję **Nowa kolekcja** i wypełnij formularz następującymi szczegółami:

   * W polu **Identyfikator bazy danych** wybierz pozycję **Użyj istniejącej** , a następnie wprowadź **changefeedlabdatabase** .  
   * W polu **identyfikator kolekcji** wprowadź **dzierżawy** .  
   * W obszarze **pojemność magazynu** wybierz pozycję **naprawione** .  
   * Pozostaw wartość domyślną pola **przepływność** .  
   * Wybierz przycisk **OK** .

## <a name="get-the-connection-string-and-keys"></a>Pobierz parametry połączenia i klucze

### <a name="get-the-azure-cosmos-db-connection-string"></a>Pobierz parametry połączenia Azure Cosmos DB

1. Przejdź do [Azure Portal](https://portal.azure.com/) i znajdź **konto Azure Cosmos DB** utworzone w ramach wdrożenia szablonu.  

2. Przejdź do okienka **klucze** , skopiuj podstawowe parametry połączenia i skopiuj je do Notatnika lub innego dokumentu, do którego będziesz mieć dostęp w środowisku laboratoryjnym. Należy oznaczyć ją jako **Cosmos DB parametry połączenia** . Musisz najpierw skopiować ciąg do kodu, więc Zanotuj i Zapamiętaj, gdzie są przechowywane.

### <a name="get-the-storage-account-key-and-connection-string"></a>Pobieranie klucza konta magazynu i parametrów połączenia

Konta usługi Azure Storage umożliwiają użytkownikom przechowywanie danych. W tym laboratorium będziesz używać konta magazynu do przechowywania danych używanych przez funkcję platformy Azure. Funkcja platformy Azure jest wyzwalana po dokonaniu modyfikacji w kolekcji.

1. Wróć do grupy zasobów i Otwórz utworzone wcześniej konto magazynu  

2. Z menu po lewej stronie wybierz pozycję **klucze dostępu** .  

3. Skopiuj wartości z **klucza 1** do Notatnika lub innego dokumentu, do którego będziesz mieć dostęp w środowisku laboratoryjnym. Należy oznaczyć **klucz** jako **klucz magazynu** i **Parametry połączenia** jako **Parametry połączenia magazynu** . Musisz później skopiować te ciągi do kodu, więc Zanotuj i Zapamiętaj, gdzie są przechowywane.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Pobierz parametry połączenia przestrzeni nazw centrum zdarzeń

Centrum zdarzeń platformy Azure odbiera dane zdarzenia, przechowuje je, przetwarza i przekazuje dane. W tym laboratorium centrum zdarzeń Azure będzie otrzymywać dokument za każdym razem, gdy wystąpi nowe zdarzenie (tj. element zostanie wyświetlony przez użytkownika, dodany do koszyka użytkownika lub zakupiony przez użytkownika), a następnie przekaże ten dokument do Azure Stream Analytics.

1. Wróć do grupy zasobów i Otwórz **przestrzeń nazw centrum zdarzeń** , która została utworzona i nazwana w prelab.  

2. Z menu po lewej stronie wybierz pozycję **zasady dostępu współdzielonego** .  

3. Wybierz pozycję **RootManageSharedAccessKey** . Skopiuj **Parametry połączenia — klucz podstawowy** do Notatnika lub innego dokumentu, do którego będziesz mieć dostęp w środowisku laboratoryjnym. Należy oznaczyć etykietą IT parametry połączenia **przestrzeni nazw centrum zdarzeń** . Musisz najpierw skopiować ciąg do kodu, więc Zanotuj i Zapamiętaj, gdzie są przechowywane.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Skonfiguruj funkcję platformy Azure w celu odczytania źródła zmian

Po utworzeniu nowego dokumentu lub zmodyfikowaniu bieżącego dokumentu w kontenerze Cosmos, Źródło zmian automatycznie dodaje zmodyfikowany dokument do historii zmian kolekcji. Teraz zostanie utworzona i uruchomiona funkcja platformy Azure, która przetwarza Źródło zmian. Po utworzeniu lub zmodyfikowaniu dokumentu w utworzonej kolekcji funkcja platformy Azure zostanie wyzwolona przez źródło zmian. Następnie funkcja platformy Azure wyśle zmodyfikowany dokument do centrum zdarzeń.

1. Wróć do repozytorium, które zostało sklonowane na urządzeniu.  

2. Kliknij prawym przyciskiem myszy plik o nazwie **ChangeFeedLabSolution. sln** i wybierz polecenie **Otwórz za pomocą programu Visual Studio** .  

3. Przejdź do **local.settings.js** w programie Visual Studio. Następnie należy użyć zarejestrowanych wcześniej wartości, aby wypełnić puste.  

4. Przejdź do **ChangeFeedProcessor.cs** . W parametrach funkcji **Run** wykonaj następujące czynności:  

   * Zastąp tekst **nazwą kolekcji w tym miejscu** nazwą kolekcji. Jeśli wykonano wcześniejsze instrukcje, nazwa kolekcji to changefeedlabcollection.  
   * Zamień tekst **kolekcji DZIERŻAW tutaj** na nazwę kolekcji dzierżaw. Jeśli wykonano wcześniejsze instrukcje, nazwa kolekcji dzierżawy jest **dzierżawy** .  
   * Upewnij się, że w górnej części programu Visual Studio pole projekt startowy po lewej stronie zielonej strzałki ma wartość **ChangeFeedFunction** .  
   * Wybierz pozycję **Rozpocznij**  u góry strony, aby uruchomić program.  
   * Można potwierdzić, że funkcja jest uruchomiona, gdy aplikacja konsoli mówi "uruchomiono hosta zadania".

## <a name="insert-data-into-azure-cosmos-db"></a>Wstaw dane do Azure Cosmos DB 

Aby dowiedzieć się, jak kanał informacyjny zmiany przetwarza nowe akcje w witrynie handlu elektronicznego, należy symulować dane reprezentujące użytkowników, którzy wyświetlają elementy z katalogu produktów, dodając te elementy do ich koszyków, a następnie kupując elementy w ich koszykach. Te dane są dowolne i na potrzeby replikowania danych w witrynie handlu elektronicznego.

1. Przejdź z powrotem do repozytorium w Eksploratorze plików, a następnie kliknij prawym przyciskiem myszy **ChangeFeedFunction. sln** , aby otworzyć go ponownie w nowym oknie programu Visual Studio.  

2. Przejdź do pliku **App.config** . W `<appSettings>` bloku Dodaj punkt końcowy i unikatowy **klucz podstawowy** , który został wcześniej pobrany z konta Azure Cosmos DB.  

3. Dodaj do **kolekcji** i nazw **baz danych** . (Nazwy te powinny mieć **changefeedlabcollection** i **changefeedlabdatabase** , chyba że użytkownik zdecyduje się na inną nazwę).

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Wizualizacja projektu":::
 
4. Zapisz zmiany we wszystkich edytowanych plikach.  

5. W górnej części programu Visual Studio upewnij się, że pole **projekt startowy** po lewej stronie zielonej strzałki mówi **datagenerator** . Następnie wybierz pozycję **Rozpocznij** w górnej części strony, aby uruchomić program.  
 
6. Poczekaj, aż program zostanie uruchomiony. Gwiazdki oznaczają, że dane są dostępne! Kontynuuj działanie programu — ważne jest, aby zebrać wiele danych.  

7. Jeśli przejdziesz do [Azure Portal](https://portal.azure.com/) , do konta Cosmos DB w grupie zasobów, a następnie do **Eksplorator danych** , zostaną wyświetlone losowo zaimportowane dane w **changefeedlabcollection** .
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Wizualizacja projektu":::

## <a name="set-up-a-stream-analytics-job"></a>Konfigurowanie zadania usługi Stream Analytics

Azure Stream Analytics to w pełni zarządzana usługa w chmurze umożliwiająca przetwarzanie danych przesyłanych strumieniowo w czasie rzeczywistym. W tym laboratorium będziesz używać usługi Stream Analytics do przetwarzania nowych zdarzeń z centrum zdarzeń (tj. gdy element zostanie wyświetlony, dodany do koszyka lub zakupione), Uwzględnij te zdarzenia w czasie rzeczywistym analizy danych i wyślij je do Power BI do wizualizacji.

1. W [Azure Portal](https://portal.azure.com/)przejdź do grupy zasobów, a następnie **streamjob1** (zadanie usługi Stream Analytics utworzone w prelab).  

2. Wybierz **dane wejściowe** , jak pokazano poniżej.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Wizualizacja projektu":::

3. Wybierz pozycję **+ Dodaj dane wejściowe strumienia** . Następnie wybierz pozycję **centrum zdarzeń** z menu rozwijanego.  

4. Wypełnij nowy formularz wejściowy z następującymi szczegółami:

   * W polu Alias **wejściowy** wprowadź **dane wejściowe** .  
   * Wybierz opcję **wybierania centrum zdarzeń z subskrypcji** .  
   * Ustaw pole **subskrypcja** na subskrypcję.  
   * W polu **przestrzeń nazw centrum zdarzeń** wprowadź nazwę przestrzeni nazw centrum zdarzeń, która została utworzona podczas prelab.  
   * W polu **nazwa centrum zdarzeń** wybierz opcję **Użyj istniejącej** i wybierz polecenie **Event-Hub1** z menu rozwijanego.  
   * Pozostaw wartość domyślną pola Nazwa **zasad centrum zdarzeń** .  
   * Pozostaw **format serializacji zdarzenia** jako plik **JSON** .  
   * Pozostaw **pole kodowania** ustawione na wartość **UTF-8** .  
   * Pole **Typ kompresji zdarzenia** pozostaw wartość **Brak** .  
   * Wybierz ikonę **Zapisz** .

5. Przejdź z powrotem do strony zadanie usługi Stream Analytics i wybierz pozycję dane **wyjściowe** .  

6. Wybierz pozycję **+ Dodaj** . Następnie wybierz pozycję **Power BI** z menu rozwijanego.  

7. Aby utworzyć nowe dane wyjściowe Power BI wizualizacji średniej ceny, wykonaj następujące czynności:

   * W polu **alias danych wyjściowych** wprowadź **averagePriceOutput** .  
   * Pozostaw pole **obszar roboczy grupy** ustawienie **Autoryzuj połączenie, aby załadować obszary robocze** .  
   * W polu **Nazwa zestawu danych** wprowadź **averagePrice** .  
   * W polu **Nazwa tabeli** wpisz **averagePrice** .  
   * Wybierz przycisk **Autoryzuj** , a następnie postępuj zgodnie z instrukcjami, aby autoryzować połączenie do Power BI.  
   * Wybierz ikonę **Zapisz** .  

8. Następnie wróć do **streamjob1** i wybierz pozycję **Edytuj zapytanie** .

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Wizualizacja projektu" na "uruchomiony".

## <a name="connect-to-power-bi"></a>Łączenie z usługą Power BI

Power BI to pakiet narzędzi do analityki biznesowej, który pozwala analizować dane i dzielić się szczegółowymi informacjami. Jest to doskonały przykład, jak można strategicznie wizualizować przeanalizowane dane.

1. Zaloguj się do Power BI i przejdź do **obszaru mój obszar roboczy** , otwierając menu po lewej stronie strony.  

2. Wybierz pozycję **+ Utwórz** w prawym górnym rogu, a następnie wybierz pozycję **pulpit nawigacyjny** , aby utworzyć pulpit nawigacyjny.  

3. Wybierz pozycję **+ Dodaj kafelek** w prawym górnym rogu.  

4. Wybierz pozycję **niestandardowe dane przesyłane strumieniowo** , a następnie wybierz przycisk **dalej** .  
 
5. Wybierz pozycję **averagePrice** z **zestawów danych** , a następnie wybierz przycisk **dalej** .  

6. W polu **typ wizualizacji** wybierz **klastrowany wykres słupkowy** z menu rozwijanego. W obszarze **oś** Dodaj akcję. Pomiń **legendę** bez dodawania jakichkolwiek elementów. Następnie w następnej sekcji o nazwie **wartość** Dodaj **średnią** . Wybierz pozycję **dalej** , a następnie tytuł wykresu i wybierz pozycję **Zastosuj** . Na pulpicie nawigacyjnym powinien zostać wyświetlony nowy wykres.  

7. Teraz, jeśli chcesz wizualizować więcej metryk, możesz wrócić do **streamjob1** i utworzyć trzy dodatkowe dane wyjściowe z następującymi polami.

   a. **Alias wyjściowy:** incomingRevenueOutput, nazwa zestawu danych: incomingRevenue, nazwa tabeli: incomingRevenue  
   b. **Alias wyjściowy:** top5Output, nazwa zestawu danych: TOP5, nazwa tabeli: Top5  
   c. **Alias wyjściowy:** uniqueVisitorCountOutput, nazwa zestawu danych: uniqueVisitorCount, nazwa tabeli: uniqueVisitorCount

   Następnie wybierz pozycję **Edytuj zapytanie** i wklej następujące zapytania **powyżej** tego, już napisanego.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   5 NAJPOPULARNIEJSZych zapytań oblicza 5 najważniejszych elementów uporządkowanych według liczby zakupionych wystąpień. Ta Metryka może pomóc firmom handlu elektronicznego w ocenie, które elementy są najbardziej popularne i może mieć wpływ na decyzje firmy, ceny i spisu.

   Zapytanie PRZYCHODu oblicza przychód, sumując ceny wszystkich elementów zakupionych co minutę. Ta Metryka może pomóc firmom handlu elektronicznemu w ocenie wydajności finansowej, a także zrozumieć, jakie pory dnia przyczyniają się do większości przychodów. Może to mieć wpływ na ogólną strategię firmy, w szczególności Marketing.

   Kwerenda UNIKATOWYch osób odwiedzających oblicza, ile unikatowych odwiedzających znajduje się w witrynie co 5 sekund przez wykrycie unikatowego identyfikatora koszyka. Ta Metryka może pomóc firmom handlu elektronicznemu w ocenie aktywności witryny i ułożeniu się, jak uzyskać więcej klientów.

8. Teraz można również dodawać kafelki dla tych zestawów danych.

   * Dla pierwszych 5 warto mieć sens, aby wykres kolumnowy grupowany z elementami jako oś i liczbą jako wartość.  
   * W przypadku przychodu warto określić wykres liniowy z wartością czasu jako oś i sumę cen jako wartość. Przedział czasu, który ma być wyświetlany, powinien być największą możliwą w celu zapewnienia możliwie największej ilości informacji.  
   * W przypadku unikatowych osób odwiedzających warto utworzyć wizualizację karty z liczbą unikatowych odwiedzających jako wartość.

   Oto jak wygląda przykładowy pulpit nawigacyjny z następującymi wykresami:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Wizualizacja projektu":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcjonalne: Wizualizuj w witrynie handlu elektronicznego

Teraz zobaczysz, jak można użyć nowego narzędzia do analizy danych, aby nawiązać połączenie z rzeczywistą witryną handlu elektronicznego. Aby zbudować witrynę handlu elektronicznego, należy użyć bazy danych usługi Azure Cosmos do przechowywania listy kategorii produktów (kobiety, mężczyźni, Unisex), katalogu produktów i listy najpopularniejszych elementów.

1. Przejdź z powrotem do [Azure Portal](https://portal.azure.com/), a następnie do **konta Cosmos DB** , aby **Eksplorator danych** .  

   Dodaj dwie kolekcje w **changefeedlabdatabase** obszarze  -  **produkty** i **Kategorie** changefeedlabdatabase o stałej pojemności magazynu.

   Dodaj kolejną kolekcję w obszarze **changefeedlabdatabase** o nazwie **topItems** i **/Item** jako klucz partycji.

2. Wybierz kolekcję **topItems** , a w obszarze **Skala i ustawienia** ustaw wartość **czasu wygaśnięcia** na **30 sekund** , aby topItems aktualizacje co 30 sekund.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Wizualizacja projektu":::

3. Aby wypełnić kolekcję **topItems** z najczęściej zakupionymi elementami, przejdź z powrotem do **streamjob1** i Dodaj nowe **dane wyjściowe** . Wybierz **Cosmos DB** .

4. Wypełnij wymagane pola, tak jak pokazano poniżej.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Wizualizacja projektu":::
 
5. W przypadku dodania opcjonalnego zapytania TOP 5 w poprzedniej części laboratorium należy przejoć do części 5a. W przeciwnym razie należy przejoć do części 5B.

   5a. W **streamjob1** wybierz pozycję **Edytuj zapytanie** i wklej następujące zapytanie w edytorze zapytań Azure Stream Analytics poniżej 5 najważniejszych zapytań, ale powyżej reszty zapytań.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. W **streamjob1** wybierz pozycję **Edytuj zapytanie** i wklej następujące zapytanie w edytorze zapytań Azure Stream Analytics powyżej wszystkich innych zapytań.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Otwórz plik **EcommerceWebApp. sln** i przejdź do pliku **Web.config** w **Eksplorator rozwiązań** .  

7. W `<appSettings>` bloku Dodaj **Identyfikator URI** i **klucz podstawowy** , który został wcześniej zapisany, gdzie tutaj znajduje się **Identyfikator URI** i **klucz podstawowy** . Następnie Dodaj **nazwę bazy danych** i **nazwę kolekcji** w określony sposób. (Nazwy te powinny być **changefeedlabdatabase** i **changefeedlabcollection** , chyba że wybrano opcję nazywania inaczej).

   Wypełnij pola **Nazwa kolekcji produktów** , **Nazwa kolekcji kategorii** i **Nazwa kolekcji Top Items** , jak wskazano. (Nazwy te powinny być **produktami, kategoriami i topItems,** chyba że wybrano opcję nazywania inaczej).  

8. Przejdź do **folderu wyewidencjonowywanie** i otwórz go w obszarze **EcommerceWebApp. sln.** Następnie otwórz plik **Web.config** w tym folderze.  

9. W `<appSettings>` bloku Dodaj **Identyfikator URI** i **klucz podstawowy** , który został wcześniej zapisany, tam gdzie wskazano. Następnie Dodaj **nazwę bazy danych** i **nazwę kolekcji** w określony sposób. (Nazwy te powinny być **changefeedlabdatabase** i **changefeedlabcollection** , chyba że wybrano opcję nazywania inaczej).  

10. Naciśnij klawisz **Start** w górnej części strony, aby uruchomić program.  

11. Teraz można grać w witrynie handlu elektronicznego. Gdy przeglądasz element, dodajesz element do koszyka, zmienisz ilość elementu w koszyku lub kupisz element, te zdarzenia zostaną przesłane za pomocą źródła danych Cosmos DB zmian do centrum zdarzeń, ASA, a następnie Power BI. Zalecamy kontynuowanie uruchamiania generatora danych w celu wygenerowania znaczących dane o ruchu w sieci Web i zapewnienia realistycznego zestawu "gorące produkty" w witrynie handlu elektronicznego.

## <a name="delete-the-resources"></a>Usuwanie zasobów

Aby usunąć zasoby utworzone w ramach tego laboratorium, przejdź do grupy zasobów na [Azure Portal](https://portal.azure.com/), a następnie wybierz pozycję **Usuń grupę zasobów** z menu w górnej części strony i postępuj zgodnie z podanymi instrukcjami.

## <a name="next-steps"></a>Następne kroki 
  
* Aby dowiedzieć się więcej na temat źródła zmian, zobacz [Praca z obsługą źródła zmian w Azure Cosmos DB](change-feed.md)
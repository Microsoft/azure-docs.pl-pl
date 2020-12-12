---
title: Samouczek Power BI dla łącznika Azure Cosmos DB
description: Skorzystaj z tego samouczka Power BI, aby zaimportować dane JSON, utworzyć raporty usługi Insights i wizualizacji danych przy użyciu łącznika Azure Cosmos DB i Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359867"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Wizualizowanie danych usługi Azure Cosmos DB przy użyciu łącznika usługi Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/) to usługa online, w której można tworzyć i udostępniać pulpity nawigacyjne i raporty. Power BI Desktop to narzędzie do tworzenia raportów, które umożliwia pobieranie danych z różnych źródeł danych. Usługa Azure Cosmos DB jest jednym ze źródeł danych, którego możesz użyć w aplikacji Power BI Desktop. Możesz połączyć aplikację Power BI Desktop z kontem usługi Azure Cosmos DB przy użyciu łącznika usługi Azure Cosmos DB dla usługi Power BI.  Po zaimportowaniu danych usługi Azure Cosmos DB do usługi Power BI możesz przekształcić je, utworzyć raporty i opublikować te raporty w usłudze Power BI.   

W tym artykule opisano kroki wymagane do połączenia konta usługi Azure Cosmos DB z aplikacją Power BI Desktop. Po nawiązaniu połączenia możesz przejść do dowolnej kolekcji, wyodrębnić dane, przekształcić dane JSON na format tabelaryczny i opublikować raport w usłudze Power BI.

> [!NOTE]
> Łącznik Power BI dla Azure Cosmos DB nawiązuje połączenie z Power BI Desktop. Raporty utworzone w Power BI Desktop mogą być publikowane w usłudze PowerBI.com. Nie można wykonać bezpośredniej ekstrakcji danych Azure Cosmos DB z PowerBI.com. 

> [!NOTE]
> Nawiązywanie połączenia z Azure Cosmos DB za pomocą łącznika Power BI jest obecnie obsługiwane tylko Azure Cosmos DB w przypadku kont interfejsu API SQL i Gremlin.

## <a name="prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji przedstawionych w tym samouczku Power BI upewnij się, że masz dostęp do następujących zasobów:

* [Pobierz najnowszą wersję Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Pobierz [przykładowe dane Volcano](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) z usługi GitHub.

* [Utwórz konto bazy danych usługi Azure Cosmos](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) i zaimportuj dane Volcano za pomocą [Narzędzia do migracji danych Azure Cosmos DB](import-data.md). Podczas importowania danych należy wziąć pod uwagę następujące ustawienia źródłowe i docelowe w narzędziu migracja danych:

   * **Parametry źródła** 

       * **Importuj z:** Pliki JSON

   * **Parametry docelowe** 

      * **Parametry połączenia:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Klucz partycji:** /Country 

      * **Przepływność kolekcji:** 1000 

Aby udostępnić raporty w programie PowerBI.com, musisz mieć konto w usłudze PowerBI.com.  Aby dowiedzieć się więcej na temat Power BI i Power BI Pro, zobacz [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Zacznijmy
W tym samouczku Załóżmy, że jesteś Geologist studium Volcanoes na całym świecie. Dane Volcano są przechowywane na koncie Azure Cosmos DB, a format dokumentu JSON jest następujący:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Dane Volcano można pobrać z konta Azure Cosmos DB i wizualizować dane w raporcie Power BI interakcyjnym.

1. Uruchom Power BI Desktop.

2. Możesz **pobrać dane**, zobaczyć **ostatnie źródła** lub **otworzyć inne raporty** bezpośrednio z ekranu powitalnego. Wybierz "X" w prawym górnym rogu, aby zamknąć ekran. Zostanie wyświetlony widok **raport** Power BI Desktop.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Widok raportu Power BI Desktop — łącznik Power BI":::

3. Wybierz Wstążkę **Narzędzia główne** , a następnie kliknij pozycję **Pobierz dane**.  Powinno zostać wyświetlone okno **pobieranie danych** .

4. Kliknij pozycję **Azure**, wybierz pozycję **Azure Cosmos dB (beta)**, a następnie kliknij pozycję **Połącz**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop pobrać łącznika Power BI danych":::

5. Na stronie **łącznik w wersji zapoznawczej** kliknij pozycję **Kontynuuj**. Zostanie wyświetlone okno **Azure Cosmos DB** .

6. Określ adres URL punktu końcowego konta Azure Cosmos DB, z którego chcesz pobrać dane, jak pokazano poniżej, a następnie kliknij przycisk **OK**. Aby użyć własnego konta, możesz pobrać adres URL z pola URI w bloku **klucze** Azure Portal. Opcjonalnie możesz podać nazwę bazy danych, nazwę kolekcji lub użyć nawigatora do wybrania bazy danych i kolekcji, w której pochodzą dane.
   
7. Jeśli łączysz się z tym punktem końcowym po raz pierwszy, zostanie wyświetlony monit o podanie klucza konta. W przypadku własnego konta Pobierz klucz z pola **klucz podstawowy** w bloku **klucze tylko do odczytu** w Azure Portal. Wprowadź odpowiedni klucz, a następnie kliknij przycisk **Połącz**.
   
   Podczas kompilowania raportów zalecamy użycie klucza tylko do odczytu. Zapobiega to niepotrzebnemu narażeniu klucza podstawowego na potencjalne zagrożenia bezpieczeństwa. Klucz tylko do odczytu jest dostępny w bloku **klucze** Azure Portal. 
    
8. Po pomyślnym nawiązaniu połączenia zostanie wyświetlone okienko **Nawigator** . **Nawigator** pokazuje listę baz danych w ramach konta.

9. Kliknij i rozwiń bazę danych, z której pochodzą dane raportu, a następnie wybierz pozycję **volcanodb** (nazwa bazy danych może się różnić).   

10. Teraz wybierz kolekcję zawierającą dane do pobrania, wybierz pozycję **volcano1** (nazwa kolekcji może być inna).
    
    W okienku podglądu zostanie wyświetlona lista elementów **rekordów** .  Dokument jest reprezentowany jako typ **rekordu** w Power BI. Podobnie zagnieżdżony blok JSON wewnątrz dokumentu jest również **rekordem**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Samouczek Power BI łącznika Azure Cosmos DB Power BI — okno nawigatora":::

12. Kliknij przycisk **Edytuj** , aby uruchomić Edytor zapytań w nowym oknie, aby przekształcić dane.

## <a name="flattening-and-transforming-json-documents"></a>Spłaszczanie i transformowanie dokumentów JSON
1. Przejdź do okna edytora zapytań Power BI, w którym kolumna **dokument** znajduje się w środkowym okienku.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Edytor zapytań programu Power BI Desktop":::

1. Kliknij Ekspander w prawej części nagłówka kolumny **dokumentu** .  Zostanie wyświetlone menu kontekstowe z listą pól.  Wybierz pola, które są potrzebne do raportu, na przykład Volcano, nazwa, kraj, region, lokalizacja, podniesienie, typ, stan i Ostatnia znana wartość. Usuń zaznaczenie pola **Użyj oryginalnej nazwy kolumny jako prefiksu** , a następnie kliknij przycisk **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Samouczek Power BI łącznika Azure Cosmos DB Power BI — rozwiń węzeł dokumenty":::

1. W środkowym okienku zostanie wyświetlony podgląd wyniku z wybranymi polami.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Samouczek Power BI dla łącznika Azure Cosmos DB Power BI — Spłaszcz wyniki":::

1. W naszym przykładzie właściwość Location jest blokiem GEOJSON w dokumencie.  Jak widać, lokalizacja jest reprezentowana jako typ **rekordu** w Power BI Desktop.  

1. Kliknij Ekspander znajdujący się po prawej stronie nagłówka kolumny Document. Location.  Menu kontekstowe z polami Type i współrzędne pojawia się.  Wybierzmy pole współrzędne, upewnij się, że **nazwa oryginalnej kolumny** nie jest zaznaczona, a następnie kliknij przycisk **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Samouczek Power BI dotyczący łącznika Azure Cosmos DB Power BI — rekord lokalizacji":::

1. W środkowym okienku zostanie wyświetlona kolumna współrzędne typu **listy** .  Jak pokazano na początku samouczka, dane GEOJSON w tym samouczku są typu punkt z wartościami szerokości geograficznej i długością geograficzną zarejestrowanymi w tablicy współrzędnych.
   
   Element współrzędne [0] reprezentuje wartość długości geograficznej, podczas gdy współrzędne [1] reprezentuje wartość szerokości geograficznej.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Samouczek Power BI dla łącznika Azure Cosmos DB Power BI — Lista współrzędnych":::

1. Aby spłaszczyć tablicę współrzędnych, Utwórz **kolumnę niestandardową** o nazwie latlong.  Wybierz Wstążkę **Dodaj kolumnę** i kliknij **kolumnę niestandardową**.  Zostanie wyświetlone okno **kolumna niestandardowa** .

1. Podaj nazwę nowej kolumny, np. LatLong.

1. Następnie określ formułę niestandardową dla nowej kolumny.  W naszym przykładzie będziemy łączyć wartości szerokości i długości geograficznej oddzielone przecinkami, jak pokazano poniżej, przy użyciu następującej formuły: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Kliknij przycisk **OK**.
   
   Aby uzyskać więcej informacji na temat wyrażeń analizy danych (DAX), w tym funkcji języka DAX, zobacz [podstawy języka DAX w Power BI Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Samouczek Power BI łącznika Azure Cosmos DB Power BI — Dodawanie kolumny niestandardowej":::

1. Teraz w środkowym okienku wyświetlane są nowe kolumny LatLong wypełnione wartościami.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Samouczek Power BI dla łącznika Azure Cosmos DB Power BI — niestandardowa kolumna LatLong":::
    
    Jeśli w nowej kolumnie pojawia się błąd, upewnij się, że zastosowane kroki w obszarze Ustawienia zapytania są zgodne z następującą ilustracją:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Zastosowane kroki powinny być źródła, nawigacji, rozwinięte dokumenty, rozwinięte dokumenty. lokalizacja, dodano niestandardowe":::
    
    Jeśli czynności są różne, Usuń dodatkowe kroki i spróbuj ponownie dodać kolumnę niestandardową. 

1. Kliknij przycisk **Zamknij i Zastosuj** , aby zapisać model danych.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Samouczek Power BI łącznika Azure Cosmos DB Power BI — Zamknij & Zastosuj":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Tworzenie raportów

Widok raportu Power BI Desktop to miejsce, w którym można rozpocząć tworzenie raportów w celu wizualizacji danych.  Raporty można tworzyć przez przeciąganie i upuszczanie pól na kanwę **raportu** .

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Widok raportu Power BI Desktop — przeciągnij i upuść wymagane pola":::

W widoku Raport należy znaleźć następujące informacje:

1. W okienku **pola** można zobaczyć listę modeli danych z polami, które mogą być używane w raportach.
1. Okienko **wizualizacje** . Raport może zawierać jedną lub wiele wizualizacji.  Wybierz typy **wizualizacji** , dopasowane do Twoich potrzeb, za pomocą okienka wizualizowania.
1. Na kanwie **raportu** jest to miejsce, w którym można tworzyć wizualizacje raportu.
1. Strona **raportu** . Można dodać wiele stron raportu w Power BI Desktop.

Poniżej przedstawiono podstawowe kroki tworzenia prostego raportu interakcyjnego widoku mapy.

1. W naszym przykładzie utworzymy widok mapy pokazujący lokalizację każdego volcanou.  W okienku **wizualizacje** kliknij typ wizualizacji mapa, jak pokazano na poniższym zrzucie ekranu.  Na kanwie **raportu** powinien zostać wyświetlony typ wizualizacji mapy.  Okienko **wizualizacji** powinno również wyświetlać zestaw właściwości powiązanych z typem wizualizacji mapy.
1. Teraz przeciągnij i upuść pole LatLong z okienka **pola** do właściwości **Location** w okienku **wizualizacje** .
1. Następnie przeciągnij i upuść pole Volcano Name na Właściwość **Legend** .  
1. Następnie przeciągnij i upuść pole podniesienia uprawnień do właściwości **rozmiar** .  
1. Powinna zostać wyświetlona Wizualizacja mapy pokazująca zestaw bąbelków wskazujący lokalizację każdego Volcano z rozmiarem bąbelków skorelowanych do podniesienia uprawnień Volcano.
1. Utworzono raport podstawowy.  Możesz jeszcze bardziej dostosować raport, dodając więcej wizualizacji.  W naszym przypadku dodaliśmy fragmentator typu Volcano, aby raport był interaktywny.  
   
1. W menu plik kliknij polecenie **Zapisz** i Zapisz plik jako PowerBITutorial. pbix.

## <a name="publish-and-share-your-report"></a>Publikowanie i Udostępnianie raportu
Aby udostępnić raport, musisz mieć konto w usłudze PowerBI.com.

1. W Power BI Desktop kliknij na Wstążce **Narzędzia główne** .
1. Kliknij przycisk **Opublikuj**.  Zostanie wyświetlony monit o podanie nazwy użytkownika i hasła do konta PowerBI.com.
1. Gdy poświadczenie zostanie uwierzytelnione, raport zostanie opublikowany w wybranym miejscu docelowym.
1. Kliknij pozycję **Otwórz plik "PowerBITutorial. pbix" w Power BI** , aby zobaczyć i udostępnić raport w witrynie PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Publikowanie w celu Power BI pomyślne! Otwórz samouczek w Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Utworzenie pulpitu nawigacyjnego w usłudze PowerBI.com
Teraz, gdy masz raport, możesz go udostępnić w witrynie PowerBI.com

Po opublikowaniu raportu z Power BI Desktop do PowerBI.com generuje on **raport** i **zestaw danych** w dzierżawie PowerBI.com. Na przykład po opublikowaniu raportu o nazwie **PowerBITutorial** do PowerBI.com zobaczysz PowerBITutorial w sekcjach **raporty** i **zestawy danych** na PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Zrzut ekranu przedstawiający nowy raport i zestaw danych w PowerBI.com":::

Aby utworzyć udostępniony pulpit nawigacyjny, kliknij przycisk **Przypnij stronę dynamiczną** w raporcie PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Zrzut ekranu przedstawiający sposób przypinania raportu do PowerBI.com":::

Następnie postępuj zgodnie z instrukcjami w temacie [Przypinanie kafelka z raportu](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) , aby utworzyć nowy pulpit nawigacyjny. 

Przed utworzeniem pulpitu nawigacyjnego można także przeprowadzić modyfikacje ad hoc w raporcie. Zaleca się jednak użycie Power BI Desktop do wykonania modyfikacji i ponownego opublikowania raportu w programie PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat Power BI, zobacz Rozpoczynanie [pracy z Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Aby dowiedzieć się więcej na temat Azure Cosmos DB, zapoznaj się ze stroną docelową [Azure Cosmos DB dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/).

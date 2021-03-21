---
title: Eksportuj do Power BI z poziomu platformy Azure Application Insights | Microsoft Docs
description: Zapytania analityczne mogą być wyświetlane w Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 4bdae09d193d5ae9c59fe9352e07b8219a76841d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584318"
---
# <a name="feed-power-bi-from-application-insights"></a>Power BI kanału informacyjnego z Application Insights
[Power BI](https://www.powerbi.com/) to pakiet narzędzi firmy, który ułatwia analizowanie danych i udostępnianie szczegółowych informacji. Rozbudowane pulpity nawigacyjne są dostępne na każdym urządzeniu. Możesz połączyć dane z wielu źródeł, w tym zapytania analityczne z [usługi Azure Application Insights](./app-insights-overview.md).

Istnieją trzy metody eksportowania danych Application Insights do Power BI:

* [**Eksportuj zapytania analityczne**](#export-analytics-queries). Jest to preferowana metoda. Napisz dowolne zapytanie i wyeksportuj je do Power BI. To zapytanie można umieścić na pulpicie nawigacyjnym wraz z innymi danymi.
* [**Eksport ciągły i Azure Stream Analytics**](./export-stream-analytics.md). Ta metoda jest przydatna, jeśli chcesz przechowywać dane przez długi czas. Jeśli nie masz wymagania rozszerzonego przechowywania danych, użyj metody zapytania Export Analytics. Eksport ciągły i Stream Analytics obejmuje więcej pracy w celu skonfigurowania i dodatkowego narzutu za magazyn.
* **Adapter Power BI**. Zestaw wykresów jest wstępnie zdefiniowany, ale możesz dodać własne zapytania z innych źródeł.

> [!NOTE]
> Karta Power BI jest obecnie **przestarzała**. Wstępnie zdefiniowane wykresy dla tego rozwiązania są wypełniane przez statyczne zapytania nieedytowalne. Nie masz możliwości edytowania tych zapytań i w zależności od niektórych właściwości danych, istnieje możliwość, że połączenie Power BI pomyślne, ale żadne dane nie zostaną wypełnione. Jest to spowodowane kryteriami wykluczenia ustawionymi w ramach zapytania stałe. Mimo że to rozwiązanie może działać w przypadku niektórych klientów, ze względu na brak elastyczności karty zaleca się użycie funkcji [**zapytania eksportu analitycznego**](#export-analytics-queries) .

## <a name="export-analytics-queries"></a>Eksportuj zapytania analityczne
Ta trasa umożliwia zapisanie dowolnego zapytania analizy lub wyeksportowanie z lejków użycia, a następnie wyeksportowanie tego elementu do Power BI pulpitu nawigacyjnego. (Możesz dodać do pulpitu nawigacyjnego utworzonego przez kartę).

### <a name="one-time-install-power-bi-desktop"></a>Jednorazowo: Zainstaluj Power BI Desktop
Aby zaimportować zapytanie Application Insights, należy użyć wersji programu Power BI. Następnie można opublikować ją w sieci Web lub w obszarze roboczym usługi Power BI Cloud. 

Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Eksportowanie zapytania analitycznego
1. [Otwórz analizę i napisz zapytanie](../logs/log-analytics-tutorial.md).
2. Przetestuj i Uściślij zapytanie do momentu, aż będziesz zadowolony z wyników. Przed wyeksportowaniem zapytania upewnij się, że zostało prawidłowo uruchomione w analizie.
3. W menu **Eksportuj** wybierz **Power BI (M)**. Zapisz plik tekstowy.
   
    ![Zrzut ekranu analizy z wyróżnionym menu Eksportuj](./media/export-power-bi/analytics-export-power-bi.png)
4. W Power BI Desktop wybierz pozycję **Pobierz dane**  >  **puste zapytanie**. Następnie w edytorze zapytań w obszarze **Widok** wybierz pozycję **Edytor zaawansowany**.

    Wklej wyeksportowany skrypt języka M do Edytor zaawansowany.

    ![Zrzut ekranu przedstawiający Power BI Desktop, z wyróżnioną Edytor zaawansowany](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Aby umożliwić Power BI dostęp do platformy Azure, może być konieczne podanie poświadczeń. Użyj **konta organizacyjnego** , aby zalogować się przy użyciu konto Microsoft.
   
    ![Zrzut ekranu przedstawiający okno dialogowe Ustawienia zapytania Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Jeśli musisz zweryfikować poświadczenia, użyj polecenia menu **Ustawienia źródła danych** w edytorze zapytań. Pamiętaj, aby określić poświadczenia używane na platformie Azure, które mogą różnić się od poświadczeń dla Power BI.
6. Wybierz wizualizację dla zapytania i wybierz pola dla osi x, osi y i segmentacji.
   
    ![Zrzut ekranu przedstawiający opcje wizualizacji Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Opublikuj raport w obszarze roboczym usługi Power BI Cloud. Z tego miejsca można osadzić zsynchronizowaną wersję na innych stronach sieci Web.
   
    ![Zrzut ekranu przedstawiający Power BI Desktop, z wyróżnionym przyciskiem Publikuj](./media/export-power-bi/publish-power-bi.png)
8. Odśwież raport ręcznie w określonych odstępach czasu lub Skonfiguruj zaplanowane odświeżanie na stronie Opcje.

### <a name="export-a-funnel"></a>Eksportowanie lejka
1. [Ustaw swój lejek](./usage-funnels.md).
2. Wybierz **Power BI**.

   ![Zrzut ekranu przedstawiający przycisk Power BI](./media/export-power-bi/button.png)

3. W Power BI Desktop wybierz pozycję **Pobierz dane**  >  **puste zapytanie**. Następnie w edytorze zapytań w obszarze **Widok** wybierz pozycję **Edytor zaawansowany**.

   ![Zrzut ekranu przedstawiający Power BI Desktop, z wyróżnionym pustym przyciskiem zapytania](./media/export-power-bi/blankquery.png)

   Wklej wyeksportowany skrypt języka M do Edytor zaawansowany. 

   ![Zrzut ekranu przedstawia Power BI Desktop z wyróżnioną Edytor zaawansowany](./media/export-power-bi/advancedquery.png)

4. Wybierz elementy z zapytania, a następnie wybierz wizualizację lejka.

   ![Zrzut ekranu przedstawia opcje wizualizacji lejka Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Zmień tytuł w taki sposób, aby był zrozumiały, i Opublikuj raport w obszarze roboczym usługi Power BI Cloud. 

   ![Zrzut ekranu przedstawiający Power BI Desktop z wyróżnioną zmianą tytułu](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Mogą wystąpić błędy związane z poświadczeniami lub rozmiarem zestawu danych. Poniżej znajdują się informacje o tych błędach.

### <a name="unauthorized-401-or-403"></a>Nieautoryzowane (401 lub 403)
Taka sytuacja może wystąpić, jeśli token odświeżania nie został zaktualizowany. Wykonaj te kroki, aby upewnić się, że nadal masz dostęp:

1. Zaloguj się do Azure Portal i upewnij się, że masz dostęp do zasobu.
2. Spróbuj odświeżyć poświadczenia dla pulpitu nawigacyjnego.
3. Spróbuj wyczyścić pamięć podręczną z Power BI Desktop.


   Jeśli masz dostęp i odświeżanie poświadczeń nie działa, Otwórz bilet pomocy technicznej.

### <a name="bad-gateway-502"></a>Zła brama (502)
Jest to zazwyczaj spowodowane przez zapytanie analityczne zwracające zbyt dużą ilość danych. Spróbuj użyć mniejszego zakresu czasu dla zapytania. 

Jeśli zmniejszenie zestawu danych pochodzącego z zapytania analitycznego nie spełnia wymagań, rozważ użycie [interfejsu API](https://dev.applicationinsights.io/documentation/overview) w celu ściągnięcia większego zestawu danych. Poniżej przedstawiono sposób konwersji eksportu z kwerendy M-Query w celu użycia interfejsu API.

1. Utwórz [klucz interfejsu API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Zaktualizuj Power BI M skrypt wyeksportowany z analizy, zastępując adres URL Azure Resource Manager za pomocą interfejsu API Application Insights.
   * Zamień **https: \/ /Management.Azure.com/Subscriptions/...**
   * z, **https: \/ /API.ApplicationInsights.IO/beta/Apps/...**
3. Na koniec zaktualizuj poświadczenia do warstwy Podstawowa i Użyj klucza interfejsu API.

**Istniejący skrypt**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Zaktualizowany skrypt**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informacje o próbkowaniu
W zależności od ilości danych wysyłanych przez aplikację możesz chcieć użyć funkcji próbkowania adaptacyjnego, która wysyła tylko wartość procentową telemetrii. To samo jest prawdziwe, jeśli ręcznie ustawiono próbkowanie w zestawie SDK lub w trybie pozyskiwania. [Dowiedz się więcej na temat próbkowania](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adapter Power BI (przestarzałe)
Ta metoda umożliwia utworzenie kompletnego pulpitu nawigacyjnego telemetrii. Początkowy zestaw danych jest wstępnie zdefiniowany, ale można dodać do niego więcej danych.

### <a name="get-the-adapter"></a>Pobierz kartę
1. Zaloguj się do usługi [Power BI](https://app.powerbi.com/).
2. Otwórz zrzut ekranu **pobieranie danych** ![ z ikony GetData w lewym dolnym rogu ](./media/export-power-bi/001.png) , **usługi**.

    ![Zrzuty ekranu przedstawiają przycisk Pobierz w oknie usługi.](./media/export-power-bi/002.png)

3. Wybierz pozycję **Pobierz teraz** w obszarze Application Insights.

   ![Zrzuty ekranu przedstawiające pobieranie ze źródła danych Application Insights](./media/export-power-bi/003.png)
4. Podaj szczegóły zasobu Application Insights, a następnie **Zaloguj się**.

    ![Zrzut ekranu przedstawia okno łączenie z Application Insights.](./media/export-power-bi/005.png)

     Te informacje można znaleźć w okienku Przegląd Application Insights:

     ![Zrzut ekranu przedstawiający pobieranie ze źródła danych Application Insights](./media/export-power-bi/004.png)

5. Otwórz nowo utworzoną Application Insights Power BI aplikacji.

6. Zaczekaj minutę lub dwie, aby zaimportować dane.

    ![Zrzut ekranu karty Power BI](./media/export-power-bi/010.png)

Można edytować pulpit nawigacyjny, łącząc Application Insights wykresy z innymi źródłami oraz z kwerendami analitycznymi. Więcej wykresów można znaleźć w galerii wizualizacji, a każdy wykres zawiera parametry, które można ustawić.

Po początkowym imporcie pulpit nawigacyjny i raporty są nadal aktualizowane codziennie. Możesz kontrolować harmonogram odświeżania zestawu danych.

## <a name="next-steps"></a>Następne kroki
* [Power BI — informacje](https://www.powerbi.com/learning/)
* [Samouczek analizy](../logs/log-analytics-tutorial.md)
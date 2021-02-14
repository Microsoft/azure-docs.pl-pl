---
title: Adnotacje wydania dla Application Insights | Microsoft Docs
description: Dodaj znaczniki wdrożenia lub kompilacji do wykresów Eksploratora metryk w Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: c23c973bc02482e7000cfd1d066a322fff93542a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386037"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryk w Application Insights

Adnotacje pokazują, gdzie wdrożono nową kompilację lub inne istotne zdarzenia. Adnotacje ułatwiają sprawdzenie, czy zmiany miały wpływ na wydajność aplikacji. Mogą być tworzone automatycznie przez system kompilacji [Azure Pipelines](/azure/devops/pipelines/tasks/) . Możesz również utworzyć adnotacje, aby oflagować dowolne wydarzenie, tworząc je za pomocą programu PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Adnotacje wersji z kompilacją Azure Pipelines

Adnotacje wydań to funkcja Azure Pipelines opartej na chmurze usługi Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (jednorazowo)

Aby można było tworzyć adnotacje wersji, należy zainstalować jeden z wielu rozszerzeń platformy Azure DevOps dostępnych w Visual Studio Marketplace.

1. Zaloguj się do projektu [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. Na stronie [rozszerzenia Visual Studio Marketplace wersje adnotacji](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję **Zainstaluj** , aby dodać rozszerzenie do organizacji usługi Azure DevOps.
   
   ![Wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję Zainstaluj.](./media/annotations/1-install.png)
   
Wystarczy zainstalować rozszerzenie tylko raz dla organizacji usługi Azure DevOps. Teraz można skonfigurować adnotacje wydania dla dowolnego projektu w organizacji.

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji wersji

Utwórz oddzielny klucz interfejsu API dla każdego z Azure Pipelines szablonów wydań.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i otwórz zasób Application Insights, który monitoruje aplikację. Lub jeśli go nie masz, [Utwórz nowy zasób Application Insights](./app-insights-overview.md).
   
1. Otwórz kartę **dostęp do interfejsu API** i skopiuj **Identyfikator Application Insights**.
   
   ![W obszarze dostęp do interfejsu API Skopiuj identyfikator aplikacji.](./media/annotations/2-app-id.png)

1. W osobnym oknie przeglądarki Otwórz lub Utwórz szablon zlecenia, który zarządza wdrożeniami Azure Pipelines.
   
1. Wybierz pozycję **Dodaj zadanie**, a następnie w menu wybierz zadanie **adnotacji Application Insights wydania** .
   
   ![Wybierz pozycję Dodaj zadanie i wybierz pozycję Application Insights adnotacja wydania.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Zadanie adnotacji wydania obecnie obsługuje tylko agentów opartych na systemie Windows; nie będzie działać w systemach Linux, macOS i innych typach agentów.
   
1. W obszarze **Identyfikator aplikacji** wklej identyfikator Application Insights skopiowany z karty dostęp do **interfejsu API** .
   
   ![Wklej identyfikator Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Wróć do okna Application Insights **dostęp do interfejsu API** wybierz pozycję **Utwórz klucz interfejsu API**. 
   
   ![Na karcie dostęp do interfejsu API wybierz pozycję Utwórz klucz interfejsu API.](./media/annotations/5-create-api-key.png)
   
1. W oknie **Tworzenie klucza interfejsu API** wpisz opis, wybierz pozycję **Zapisz adnotacje**, a następnie wybierz pozycję **Generuj klucz**. Skopiuj nowy klucz.
   
   ![W oknie Tworzenie klucza interfejsu API wpisz opis, wybierz pozycję Zapisz adnotacje, a następnie wybierz pozycję Generuj klucz.](./media/annotations/6-create-api-key.png)
   
1. W oknie szablon zlecenia na karcie **zmienne** wybierz pozycję **Dodaj** , aby utworzyć definicję zmiennej dla nowego klucza interfejsu API.

1. W polu **Nazwa** wprowadź `ApiKey` i w obszarze **wartość** Wklej klucz interfejsu API skopiowany z karty dostęp do **interfejsu API** .
   
   ![Na karcie zmienne usługi Azure DevOps wybierz pozycję Dodaj, nazwij zmienną ApiKey i Wklej klucz interfejsu API w polu wartość.](./media/annotations/7-paste-api-key.png)
   
1. Wybierz pozycję **Zapisz** w oknie głównym szablonu zlecenia, aby zapisać szablon.


   > [!NOTE]
   > Limity dla kluczy interfejsu API są opisane w [dokumentacji limitów szybkości interfejsu API REST](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Wyświetl adnotacje


   > [!NOTE]
   > Adnotacje wersji nie są obecnie dostępne w okienku metryki Application Insights

Teraz za każdym razem, gdy użyjesz szablonu zlecenia do wdrożenia nowej wersji, adnotacja zostanie wysłana do Application Insights. Adnotacje można przeglądać w następujących lokalizacjach:

Okienko **użycie** , w którym można także ręcznie tworzyć adnotacje wersji:

![Zrzut ekranu przedstawiający wykres słupkowy z liczbą wizyt użytkowników wyświetlaną w danym okresie czasu. Adnotacje wersji są wyświetlane jako zielone znaczniki wyboru powyżej wykresu wskazujące moment, w którym wystąpiło wydanie](./media/annotations/usage-pane.png)

W każdym zapytaniu w skoroszycie opartym na dzienniku, gdzie wizualizacja wyświetla czas wzdłuż osi x.

![Zrzut ekranu przedstawiający okienko skoroszytów z kwerendą opartą na dzienniku szeregów czasowych z wyświetlanymi adnotacjami](./media/annotations/workbooks-annotations.png)

Aby włączyć adnotacje w skoroszycie, przejdź do pozycji **Ustawienia zaawansowane** i wybierz pozycję **Pokaż adnotacje**.

![Zrzut ekranu przedstawiający menu Ustawienia zaawansowane z wyrazami Pokaż adnotacje z wyróżnionym znacznikiem wyboru obok ustawienia, aby je włączyć.](./media/annotations/workbook-show-annotations.png)

Wybierz dowolny znacznik adnotacji, aby otworzyć szczegółowe informacje o wydaniu, w tym żądający, gałąź kontroli źródła, potok wersji i środowisko.

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie adnotacji niestandardowych w programie PowerShell
Możesz użyć skryptu programu [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell z usługi GitHub, aby utworzyć adnotacje z dowolnego procesu, który ma być używany, bez używania platformy Azure DevOps. 

1. Wykonaj kopię lokalną [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Wykonaj kroki opisane w poprzedniej procedurze, aby pobrać identyfikator Application Insights i utworzyć klucz interfejsu API z karty Application Insights **dostęp do interfejsu API** .
   
1. Wywołaj skrypt programu PowerShell przy użyciu następującego kodu, zastępując symbole zastępcze w nawiasach kwadratowych wartościami. `-releaseProperties`Są opcjonalne. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Możesz zmodyfikować skrypt, na przykład aby utworzyć adnotacje dla przeszłości.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie elementów roboczych](./diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](./powershell.md)


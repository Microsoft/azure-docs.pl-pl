---
title: Zbieranie dzienników zasobów z zasobów platformy Azure i analizowanie ich przy użyciu Azure Monitor
description: Samouczek dotyczący konfigurowania ustawień diagnostycznych do zbierania dzienników zasobów z zasobu platformy Azure w obszarze roboczym Log Analytics, w którym można je analizować przy użyciu zapytania dziennika.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d3c3eff32120438fd891cb32fe98d73424f82b7f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734149"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Samouczek: zbieranie i analizowanie dzienników zasobów z zasobów platformy Azure

Dzienniki zasobów zapewniają wgląd w szczegółowe operacje związane z zasobem platformy Azure i są przydatne do monitorowania ich kondycji i dostępności. Zasoby platformy Azure automatycznie generują dzienniki zasobów, ale należy skonfigurować lokalizacje, w których mają być zbierane. Ten samouczek przeprowadzi Cię przez proces tworzenia ustawień diagnostycznych w celu zbierania dzienników zasobów dla zasobu w ramach subskrypcji platformy Azure i analizowania go za pomocą zapytania dziennika.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz obszar roboczy Log Analytics w Azure Monitor
> * Tworzenie ustawienia diagnostycznego w celu zbierania dzienników zasobów 
> * Tworzenie prostego zapytania dziennika w celu analizowania dzienników


## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności w tym samouczku potrzebny jest zasób platformy Azure do monitorowania. Możesz użyć dowolnego zasobu w subskrypcji platformy Azure, który obsługuje ustawienia diagnostyczne. Aby określić, czy zasób obsługuje ustawienia diagnostyczne, przejdź do jego menu w Azure Portal i sprawdź, czy w sekcji **monitorowanie** menu znajduje się opcja **Ustawienia diagnostyczne** .


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Obszar roboczy Log Analytics w Azure Monitor zbiera i indeksuje dane dzienników z różnych źródeł i umożliwia zaawansowaną analizę przy użyciu zaawansowanego języka zapytań. Aby można było wysłać do niego dane, obszar roboczy Log Analytics musi istnieć. Możesz użyć istniejącego obszaru roboczego w ramach subskrypcji platformy Azure lub utworzyć go za pomocą poniższej procedury. 

> [!NOTE]
> Chociaż możesz korzystać z danych w Log Analytics obszarach roboczych w menu **Azure monitor** , tworzysz obszary robocze i zarządzaj nimi w menu **log Analytics obszary robocze** .

1. W obszarze **wszystkie usługi** wybierz pozycję **log Analytics obszary robocze**.
2. Kliknij przycisk **Dodaj** w górnej części ekranu i podaj następujące informacje dotyczące obszaru roboczego:
   - **Log Analytics obszar roboczy**: Nazwa nowego obszaru roboczego. Ta nazwa musi być globalnie unikatowa w ramach wszystkich subskrypcji Azure Monitor.
   - **Subskrypcja**: wybierz subskrypcję do przechowywania obszaru roboczego. Nie musi to być ta sama subskrypcja, która jest taka sama jak monitorowany zasób.
   - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub kliknij pozycję **Utwórz nową** , aby utworzyć nową. Nie musi to być ta sama Grupa zasobów, która jest taka sama jak monitorowany zasób.
   - **Lokalizacja**: Wybierz region platformy Azure lub Utwórz nowy. Nie musi to być ta sama lokalizacja, która jest taka sama jak monitorowany zasób.
   - **Warstwa cenowa**: wybierz pozycję *płatność zgodnie z rzeczywistym* użyciem jako warstwę cenową. Tę warstwę cenową można zmienić później. Kliknij link **log Analytics cennika** , aby dowiedzieć się więcej o różnych warstwach cenowych.

    ![Nowy obszar roboczy](media/tutorial-resource-logs/new-workspace.png)

3. Kliknij przycisk **OK** , aby utworzyć obszar roboczy.

## <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne
[Ustawienia diagnostyczne](../essentials/diagnostic-settings.md) definiują, gdzie należy wysyłać dzienniki zasobów dla określonego zasobu. Pojedyncze ustawienie diagnostyczne może mieć wiele [miejsc docelowych](../essentials/diagnostic-settings.md#destinations), ale w tym samouczku będziemy używać obszaru roboczego log Analytics.

1. W sekcji **monitorowanie** menu zasobu wybierz pozycję **Ustawienia diagnostyczne**.
2. Powinien być komunikat "brak zdefiniowanych ustawień diagnostycznych". Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

    ![Ustawienia diagnostyczne](media/tutorial-resource-logs/diagnostic-settings.png)

3. Każde ustawienie diagnostyczne ma trzy podstawowe części:
 
   - **Nazwa**: nie ma znaczących efektów i należy po prostu mieć opis.
   - **Miejsca docelowe**: co najmniej jeden miejsce docelowe do wysłania dzienników. Wszystkie usługi platformy Azure współdzielą ten sam zestaw trzech możliwych miejsc docelowych. Każde ustawienie diagnostyczne może definiować jeden lub więcej miejsc docelowych, ale nie więcej niż jedno miejsce docelowe określonego typu. 
   - **Kategorie**: kategorie dzienników do wysłania do każdego miejsca docelowego. Zestaw kategorii będzie różny dla każdej usługi platformy Azure.

4. Wybierz pozycję **Wyślij do log Analytics obszarze roboczym** , a następnie wybierz utworzony przez siebie obszar roboczy.
5. Wybierz kategorie, które chcesz zebrać. Zapoznaj się z dokumentacją dla każdej usługi, aby zapoznać się z definicją dostępnych kategorii.

    ![Ustawienie diagnostyczne](media/tutorial-resource-logs/diagnostic-setting.png)

6. Kliknij przycisk **Zapisz** , aby zapisać ustawienia diagnostyczne.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Pobieranie dzienników przy użyciu zapytania dziennika
Dane są pobierane z obszaru roboczego Log Analytics przy użyciu zapytania dziennika zarejestrowanego w języku zapytań Kusto (KQL). Szczegółowe informacje i rozwiązania w Azure Monitor będą udostępniać zapytania dzienników do pobierania danych dla określonej usługi, ale można bezpośrednio korzystać z zapytań dzienników i ich wyników w Azure Portal z Log Analytics. 

1. W sekcji **monitorowanie** menu zasobu wybierz pozycję **dzienniki**.
2. Log Analytics otwiera puste okno zapytania z zakresem ustawionym na zasób. Wszystkie zapytania będą zawierać tylko rekordy z tego zasobu.

    > [!NOTE]
    > W przypadku otwarcia dzienników z menu Azure Monitor zakres zostanie ustawiony na obszar roboczy Log Analytics. W takim przypadku wszystkie zapytania będą zawierać wszystkie rekordy w obszarze roboczym.
   
    ![Zrzut ekranu przedstawia Dzienniki aplikacji logiki zawierającej nowe zapytanie z wyróżnioną nazwą aplikacji logiki.](media/tutorial-resource-logs/logs.png)

4. Usługa pokazana w przykładzie zapisuje dzienniki zasobów w tabeli **AzureDiagnostics** , ale inne usługi mogą zapisywać w innych tabelach. Zapoznaj się z [obsługiwanymi usługami, schematami i kategoriami dzienników zasobów platformy Azure](../essentials/resource-logs-schema.md) dla tabel używanych przez różne usługi platformy Azure.

    > [!NOTE]
    > Wiele usług zapisuje dzienniki zasobów w tabeli AzureDiagnostics. Po rozpoczęciu Log Analytics z menu Azure Monitor, należy dodać `where` instrukcję z `ResourceProvider` kolumną, aby określić konkretną usługę. Po rozpoczęciu Log Analytics z menu zasobów zakres jest ustawiany na tylko rekordy z tego zasobu, więc ta kolumna nie jest wymagana. Zapoznaj się z dokumentacją usługi dla przykładowych zapytań.


5. Wpisz zapytanie i kliknij przycisk **Uruchom** , aby sprawdzić wyniki. 
6. Zobacz Rozpoczynanie [pracy z dziennikami zapytań w Azure monitor](../logs/get-started-queries.md) , aby zapoznać się z samouczkiem dotyczącym pisania zapytań dzienników.

    ![Zapytanie dziennika](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak zbierać dzienniki zasobów w obszarze roboczym Log Analytics, Ukończ samouczek dotyczący pisania zapytań dzienników w celu przeanalizowania tych danych.

> [!div class="nextstepaction"]
> [Wprowadzenie do zapytań dotyczących dzienników w usłudze Azure Monitor](../logs/get-started-queries.md)
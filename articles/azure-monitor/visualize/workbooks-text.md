---
title: Azure Monitor parametry tekstu skoroszytu
description: Uprość złożone raportowanie za pomocą wstępnie skompilowanych i niestandardowych skoroszytów z parametrami. Dowiedz się więcej na temat parametrów tekstu skoroszytu.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725530"
---
# <a name="workbook-text-parameters"></a>Parametry tekstu skoroszytu

Parametry TextBox umożliwiają prostą metodę zbierania danych tekstowych od użytkowników ze skoroszytów. Są one używane, gdy nie jest praktyczne użycie listy rozwijanej w celu zebrania danych wejściowych (na przykład arbitralnego progu lub filtrów ogólnych). Skoroszyty umożliwiają autorom pobieranie wartości domyślnej pola tekstowego z zapytania. Pozwala to na interesujące scenariusze, takie jak ustawienie domyślnego progu na podstawie p95 metryki.

Typowym zastosowaniem pól tekstowych jest jako zmienne wewnętrzne używane przez inne kontrolki skoroszytu. Odbywa się to przy użyciu zapytania dla wartości domyślnych i sprawia, że formant wejściowy jest niewidoczny w trybie odczytu. Na przykład użytkownik może chcieć, aby próg pochodzi z formuły (nie użytkownika), a następnie użyć progu w kolejnych zapytaniach.

## <a name="creating-a-text-parameter"></a>Tworzenie parametru tekstowego
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `SlowRequestThreshold`
    2. Typ parametru: `Text`
    3. Wymagane: `checked`
    4. Pobierz wartość domyślną z zapytania: `unchecked`
5. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający tworzenie parametru tekstowego](./media/workbooks-text/text-create.png)

W ten sposób skoroszyt będzie wyglądał jak w trybie odczytu.

![Obraz przedstawiający parametr tekstowy w trybie odczytu](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Odwoływanie się do parametru tekstowego
1. Dodaj kontrolkę zapytania do skoroszytu, wybierając niebieskie `Add query` łącze i wybierając zasób Application Insights.
2. W polu KQL Dodaj następujący fragment kodu:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Za pomocą parametru Text o wartości 500 połączonej z kontrolką zapytania efektywnie uruchamiasz poniższe zapytanie:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Uruchom zapytanie, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr tekstowy, do którego odwołuje się element KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> W powyższym przykładzie `{SlowRequestThreshold}` reprezentuje wartość całkowitą. Jeśli wykonano zapytanie dotyczące ciągu, takiego jak należy `{ComputerName}` zmodyfikować zapytanie Kusto, aby dodać cudzysłowy, aby `"{ComputerName}"` pole parametru akceptowało dane wejściowe bez cudzysłowów.

## <a name="setting-default-values"></a>Ustawianie wartości domyślnych
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `SlowRequestThreshold`
    2. Typ parametru: `Text`
    3. Wymagane: `checked`
    4. Pobierz wartość domyślną z zapytania: `checked`
5. W polu KQL Dodaj następujący fragment kodu:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    To zapytanie ustawia wartość domyślną pola tekstowego na czas trwania używany 95. percentyl dla wszystkich żądań w aplikacji.
6. Uruchom zapytanie, aby zobaczyć wynik
7. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający parametr tekstowy z wartością domyślną z KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Podczas tego przykładowych zapytań Application Insights danych podejście może być używane dla każdego źródła danych opartego na dzienniku — Log Analytics, grafu zasobów platformy Azure itd.

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
---
title: Wizualizuj dzienniki i metryki za pomocą Power BI
description: Wizualizuj dzienniki i metryki usługi Azure Wyszukiwanie poznawcze przy użyciu Power BI.
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: fbbeb861e50abfd393b416ddc46ff147fffb7b8e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581637"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Wizualizuj dzienniki i metryki usługi Azure Wyszukiwanie poznawcze przy użyciu Power BI

[Usługa azure wyszukiwanie poznawcze](./search-what-is-azure-search.md) może wysyłać dzienniki operacji i metryki usług do konta usługi Azure Storage, które można następnie wizualizować w Power BI. W tym artykule opisano kroki i sposób używania aplikacji Power BI Template do wizualizacji danych. Szablon może pomóc uzyskać szczegółowe informacje o usłudze wyszukiwania, w tym informacje o zapytaniach, indeksowanie, operacje i metryki usług.

Możesz znaleźć szablon Power BI App **Wyszukiwanie poznawcze Azure: Analizuj dzienniki i metryki** w [witrynie Power BI Apps Marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="set-up-the-app"></a>Konfigurowanie aplikacji

1. Włącz rejestrowanie metryk i zasobów dla usługi wyszukiwania:

    1. Utwórz lub Zidentyfikuj istniejące [konto usługi Azure Storage](../storage/common/storage-account-create.md) , na którym można archiwizować dzienniki
    1. Przejdź do usługi Azure Wyszukiwanie poznawcze w Azure Portal
    1. W sekcji monitorowanie w lewej kolumnie Wybierz pozycję **Ustawienia diagnostyczne** .

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania ustawień diagnostycznych w sekcji Monitorowanie usługi Azure Wyszukiwanie poznawcze." border="false":::

    1. Wybierz **+ Dodaj ustawienie diagnostyczne**
    1. Sprawdź **Archiwum na koncie magazynu**, podaj informacje o koncie magazynu i sprawdź **OperationLogs** i **AllMetrics**

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania metryk i rejestrowania zasobów na stronie ustawień diagnostycznych.":::
    1. Wybierz pozycję **Zapisz**

1. Po włączeniu rejestrowania Użyj usługi wyszukiwania, aby rozpocząć generowanie dzienników i metryk. Trwa to godzinę, zanim kontenery pojawią się w magazynie obiektów BLOB za pomocą tych dzienników. Zobaczysz kontener **Insights-Logs-operationlogs** dla dzienników ruchu wyszukiwania i kontenerów **Insights-Metrics-pt1m** for Metrics.

1. Znajdź aplikację Power BI Wyszukiwanie poznawcze platformy Azure w [witrynie marketplace Power BI Apps](https://appsource.microsoft.com/marketplace/apps) i zainstaluj ją w nowym obszarze roboczym lub istniejącym obszarze roboczym. Aplikacja jest nazywana **platformą Azure wyszukiwanie poznawcze: Analizuj dzienniki i metryki**.

1. Po zainstalowaniu aplikacji wybierz aplikację z listy aplikacji w Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Zrzut ekranu przedstawiający aplikację Wyszukiwanie poznawcze platformy Azure do wybrania z listy aplikacji.":::

1. Wybierz pozycję **Połącz** , aby połączyć swoje dane

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Zrzut ekranu przedstawiający sposób nawiązywania połączenia z danymi w aplikacji Wyszukiwanie poznawcze platformy Azure.":::

1. Wprowadź nazwę konta magazynu zawierającego dzienniki i metryki. Domyślnie aplikacja będzie wyglądać od ostatnich 10 dni, ale tę wartość można zmienić za pomocą parametru **Days** .

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Zrzut ekranu przedstawiający sposób wprowadzania nazwy konta magazynu i liczby dni do wykonania zapytania na stronie łączenie z usługą Azure Wyszukiwanie poznawcze.":::

1. Wybierz **klucz** jako metodę uwierzytelniania i Podaj klucz konta magazynu. Wybierz pozycję **prywatny** jako poziom prywatności. Kliknij przycisk Zaloguj i rozpocznij proces ładowania.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Zrzut ekranu pokazujący, jak wprowadzić metodę uwierzytelniania, klucz konta i poziom prywatności na stronie Połącz z usługą Azure Wyszukiwanie poznawcze.":::

1. Poczekaj na odświeżenie danych. Może to potrwać pewien czas w zależności od ilości posiadanych danych. Możesz sprawdzić, czy dane nadal są odświeżane na podstawie poniższego wskaźnika.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Zrzut ekranu przedstawiający sposób odczytywania informacji na stronie odświeżanie danych.":::

1. Po zakończeniu odświeżania danych wybierz pozycję **Raport usługi Azure wyszukiwanie poznawcze** , aby wyświetlić raport.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania raportu usługi Azure Wyszukiwanie poznawcze na stronie odświeżanie danych.":::

1. Pamiętaj, aby odświeżyć stronę po otwarciu raportu, aby otworzyć go wraz z danymi.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Zrzut ekranu przedstawiający raport Power BI Wyszukiwanie poznawcze platformy Azure.":::

## <a name="modify-app-parameters"></a>Modyfikowanie parametrów aplikacji

Jeśli chcesz wizualizować dane z innego konta magazynu lub zmienić liczbę dni, które mają być zapytania, wykonaj poniższe kroki, aby zmienić parametry **dni** i **StorageAccount** .

1. Przejdź do aplikacji Power BI, Znajdź aplikację Azure Wyszukiwanie poznawcze i wybierz przycisk **Edytuj aplikację** , aby wyświetlić obszar roboczy.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania przycisku Edytuj aplikację dla aplikacji Wyszukiwanie poznawcze platformy Azure.":::

1. Wybierz pozycję **Ustawienia** z opcji zestaw danych.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania ustawień z opcji zestawu danych Wyszukiwanie poznawcze platformy Azure.":::

1. Na karcie zestawy danych Zmień wartości parametrów i wybierz pozycję **Zastosuj**. Jeśli wystąpił problem z połączeniem, zaktualizuj poświadczenia źródła danych na tej samej stronie.

1. Wróć do obszaru roboczego i wybierz pozycję **Odśwież teraz** z poziomu opcji zestawu danych.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania opcji Odśwież teraz z poziomu zestawu danych Wyszukiwanie poznawcze platformy Azure.":::

1. Otwórz raport, aby wyświetlić zaktualizowane dane. Może być również konieczne odświeżenie raportu w celu wyświetlenia najnowszych danych.

## <a name="troubleshooting-report-issues"></a>Rozwiązywanie problemów z raportami

Jeśli okaże się, że dane nie są widoczne, wykonaj następujące kroki rozwiązywania problemów:

1. Otwórz raport i Odśwież stronę, aby upewnić się, że oglądasz najnowsze dane. Istnieje możliwość odświeżenia danych w raporcie. Wybierz tę opcję, aby uzyskać najnowsze dane.

1. Upewnij się, że podana nazwa konta magazynu i klucz dostępu są poprawne. Nazwa konta magazynu powinna być zgodna z kontem skonfigurowanym na potrzeby dzienników usługi wyszukiwania.

1. Upewnij się, że konto magazynu zawiera informacje o kontenerach **Insights-Logs-operationlogs** i **Insights-Metrics-pt1m** i każdy kontener zawiera dane. Dzienniki i metryki będą znajdować się w kilku warstwach folderów.

1. Sprawdź, czy zestaw danych nadal jest odświeżany. Wskaźnik stanu odświeżania jest przedstawiony w kroku 8 powyżej. Jeśli nadal trwa odświeżanie, poczekaj na zakończenie odświeżania, aby otworzyć i odświeżyć raport.

## <a name="next-steps"></a>Następne kroki

+ [Monitorowanie operacji i aktywności wyszukiwania](search-monitor-usage.md)
+ [Co to jest usługa Power BI?](/power-bi/fundamentals/power-bi-overview)
+ [Podstawowe pojęcia dla projektantów w usłudze Power BI](/power-bi/service-basic-concepts)
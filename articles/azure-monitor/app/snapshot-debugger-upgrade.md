---
title: Uaktualnianie usługi Azure Application Insights Snapshot Debugger
description: Jak uaktualnić Snapshot Debugger dla aplikacji .NET do najnowszej wersji na platformie Azure App Services lub za pośrednictwem pakietów NuGet
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671396"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uaktualnianie Snapshot Debugger

Aby zapewnić najwyższy możliwy bezpieczeństwo danych, firma Microsoft nie korzysta z protokołów TLS 1,0 i TLS 1,1, które zostały wystawione jako podatne na wyznaczone osoby atakujące. Jeśli używasz starszej wersji rozszerzenia witryny, będzie wymagane uaktualnienie, aby kontynuować pracę. W tym dokumencie przedstawiono kroki wymagane do uaktualnienia debugera migawek do najnowszej wersji. Istnieją dwie podstawowe ścieżki uaktualniania w zależności od tego, czy włączono Snapshot Debugger przy użyciu rozszerzenia witryny, czy użyto zestawu SDK/NuGet dodanego do aplikacji. Obie ścieżki uaktualniania zostały omówione poniżej. 

## <a name="upgrading-the-site-extension"></a>Uaktualnianie rozszerzenia witryny

> [!IMPORTANT]
> Starsze wersje Application Insights używały rozszerzenia witryny prywatnej o nazwie _Application Insights Extension dla Azure App Service_. Bieżące środowisko Application Insights jest włączane przez ustawienie ustawienia aplikacji, aby wyrównać wstępnie zainstalowane rozszerzenie lokacji.
> Aby uniknąć konfliktów, co może spowodować, że lokacja przestanie działać, należy najpierw usunąć rozszerzenie lokacji prywatnej. Zobacz Krok 4 poniżej.

Jeśli debuger migawek został włączony przy użyciu rozszerzenia witryny, można przeprowadzić uaktualnienie przy użyciu następującej procedury:

1. Zaloguj się w witrynie Azure Portal.
2. Przejdź do zasobu z włączonym debugerem Application Insights i migawek. Na przykład w przypadku aplikacji sieci Web przejdź do App Service zasobu:

   ![Zrzut ekranu poszczególnych zasobów App Service o nazwie DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po przejściu do zasobu kliknij blok rozszerzenia i poczekaj na zapełnienie listy rozszerzeń:

   ![Zrzut ekranu przedstawiający rozszerzenia App Service pokazujące Application Insights rozszerzenie dla Azure App Service zainstalowanego](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Jeśli jest zainstalowana dowolna wersja _rozszerzenia Application Insights Azure App Service_ , zaznacz ją i kliknij przycisk Usuń. Potwierdź **usunięcie** rozszerzenia i poczekaj na zakończenie usuwania przed przejściem do następnego kroku.

   ![Zrzut ekranu przedstawiający rozszerzenia App Service pokazujące Application Insights rozszerzenie dla Azure App Service z wyróżnionym przyciskiem Usuń](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Przejdź do bloku przegląd zasobu i kliknij Application Insights:

   ![Zrzut ekranu przedstawiający trzy przyciski. Wybrano środkowy przycisk o nazwie Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Jeśli po raz pierwszy Oglądasz blok Application Insights dla tego App Service, zostanie wyświetlony monit o włączenie Application Insights. Wybierz pozycję **włącz Application Insights**.
 
   ![Zrzut ekranu przedstawiający pierwsze środowisko w bloku Application Insights z wyróżnionym przyciskiem Włącz Application Insights](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Zostaną wyświetlone bieżące ustawienia Application Insights. Jeśli chcesz zmienić ustawienia, możesz pozostawić je w stanie takim, w jakim się znajdują. Przycisk **Zastosuj** w dolnej części bloku nie jest domyślnie włączony i konieczne będzie przełączenie jednego z ustawień, aby aktywować przycisk. Nie musisz zmieniać żadnych rzeczywistych ustawień, zamiast tego możesz zmienić ustawienie, a następnie natychmiast zmienić je ponownie. Zalecamy przełączenie ustawienia profilera, a następnie wybranie pozycji **Zastosuj**.

   ![Zrzut ekranu strony konfiguracji App Service Application Insights z wyróżnionym przyciskiem Zastosuj w kolorze czerwonym](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Po kliknięciu przycisku **Zastosuj** zostanie wyświetlony monit o potwierdzenie zmian.

    > [!NOTE]
    > Lokacja zostanie ponownie uruchomiona w ramach procesu uaktualniania.

   ![Zrzut ekranu przedstawiający wiersz App Service zastosowania monitorowania. Pole tekstowe wyświetla komunikat: "teraz stosujemy zmiany w ustawieniach aplikacji i zainstalujemy narzędzia do łączenia zasobów Application Insights z aplikacją sieci Web. Spowoduje to ponowne uruchomienie witryny. Czy chcesz kontynuować? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Kliknij przycisk **tak** , aby zastosować zmiany i poczekać na ukończenie procesu.

Witryna została teraz uaktualniona i jest gotowa do użycia.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uaktualnianie Snapshot Debugger przy użyciu zestawu SDK/NuGet

Jeśli aplikacja korzysta ze `Microsoft.ApplicationInsights.SnapshotCollector` starszej wersji 1.3.1, należy przeprowadzić uaktualnienie do [nowszej wersji](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , aby kontynuować pracę.

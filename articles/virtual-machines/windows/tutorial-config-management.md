---
title: Samouczek — zarządzanie konfiguracją maszyny wirtualnej z systemem Windows na platformie Azure
description: W tym samouczku dowiesz się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: db7c0f2dbc547d78e3caa9ab37450c86278929bd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555945"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Samouczek: monitorowanie zmian i aktualizowanie maszyny wirtualnej z systemem Windows na platformie Azure

Dzięki platformie Azure [Change Tracking](../../automation/change-tracking/overview.md) i [Update Management](../../automation/update-management/overview.md)można łatwo identyfikować zmiany w maszynach wirtualnych z systemem Windows na platformie Azure oraz zarządzać aktualizacjami systemu operacyjnego dla tych maszyn wirtualnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zarządzaj aktualizacjami systemu Windows.
> * Monitoruj zmiany i spis.

## <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Ma ona wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem platformy Azure.

Aby otworzyć blok kodu w Cloud Shell, po prostu wybierz opcję **Wypróbuj** w prawym górnym rogu tego bloku kodu.

Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do karty Cloud Shell i wybierz klawisz ENTER, aby uruchomić kod.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby w tym samouczku móc skonfigurować monitorowanie i zarządzanie aktualizacjami na platformie Azure, konieczne jest posiadanie maszyny wirtualnej z systemem Windows na platformie Azure.

Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną przy użyciu polecenie [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` w `East US` lokalizacji. Jeśli jeszcze nie istnieją, Grupa zasobów `myResourceGroupMonitor` i pomocnicze zasoby sieciowe są tworzone:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.

## <a name="manage-windows-updates"></a>Zarządzanie aktualizacjami systemu Windows

Update Management ułatwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure. Bezpośrednio z poziomu maszyny wirtualnej można szybko:

- Ocenianie stanu dostępnych aktualizacji.
- Planowanie instalacji wymaganych aktualizacji.
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz [Cennik usługi Automation dla zarządzania aktualizacjami](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Aby włączyć Update Management dla maszyny wirtualnej:

1. Na lewej stronie okna wybierz pozycję **maszyny wirtualne**.
1. Z listy wybierz maszynę wirtualną.
1. W okienku **operacje** okna maszyny wirtualnej wybierz pozycję **Update Management**.
1. Zostanie otwarte okno **włącz Update Management** .

Sprawdzanie poprawności jest wykonywane, aby określić, czy Update Management jest włączona dla tej maszyny wirtualnej. Sprawdzanie poprawności obejmuje sprawdzanie Log Analytics obszaru roboczego, dla połączonego konta usługi Automation oraz tego, czy rozwiązanie znajduje się w obszarze roboczym.

Obszar roboczy [log Analytics](../../azure-monitor/logs/log-query-overview.md) służy do zbierania danych generowanych przez funkcje i usługi, takie jak Update Management. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

Aby wykonać dodatkowe akcje na maszynach wirtualnych, które wymagają aktualizacji, można użyć Azure Automation do uruchamiania elementów Runbook na maszynach wirtualnych. Takie czynności obejmują pobieranie lub stosowanie aktualizacji.

Proces walidacji sprawdza również, czy maszyna wirtualna jest obsługiwana za pomocą Microsoft Monitoring Agent (MMA) i hybrydowego procesu roboczego elementu Runbook usługi Automation. Za pomocą agenta można komunikować się z maszyną wirtualną i uzyskiwać informacje o stanie aktualizacji.

W oknie **włączanie Update Management** wybierz obszar roboczy log Analytics i konto usługi Automation, a następnie wybierz pozycję **Włącz**. Włączenie rozwiązania może potrwać do 15 minut.

Podczas dołączania automatycznie dodawane są następujące wymagania wstępne, które nie są spełnione:

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) obszar roboczy
* [Automatyzacja](../../automation/index.yml)
* [Hybrydowy proces roboczy elementu Runbook](../../automation/automation-hybrid-runbook-worker.md), który jest włączony na maszynie wirtualnej

Po włączeniu rozwiązania zostanie otwarte okno **Zarządzanie aktualizacjami** . Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie wybierz pozycję **Włącz**. Jeśli te opcje są wyszarzone, inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a w obszarze roboczym rozwiązania i koncie usługi Automation muszą być używane.

![Włącz Update Management rozwiązanie](./media/tutorial-monitoring/manageupdates-update-enable.png)

Aby można było włączyć rozwiązanie Update Management, może upłynąć do 15 minut. W tym czasie nie należy zamykać okna przeglądarki. Po włączeniu rozwiązania informacje dotyczące brakujących aktualizacji maszyny wirtualnej są przekazywane do dzienników usługi Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

### <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu Update Management zostanie wyświetlone okno **zarządzania aktualizacjami** . Po zakończeniu oceny aktualizacji zostanie wyświetlona lista brakujących aktualizacji na karcie **brakujące aktualizacje** .

 ![Wyświetlanie stanu aktualizacji](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi. Wybierasz, które typy aktualizacji mają być uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Aby zaplanować nowe wdrożenie aktualizacji dla maszyny wirtualnej, wybierz pozycję **Zaplanuj wdrażanie aktualizacji** w górnej części okna **zarządzania aktualizacjami** . W oknie **nowe wdrożenie aktualizacji** podaj następujące informacje:

| Opcja | Opis |
| --- | --- |
| **Nazwa** |Wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji. |
|**System operacyjny**| Wybierz system **Linux** lub **Windows**.|
| **Grupy do zaktualizowania** |W przypadku maszyn wirtualnych hostowanych na platformie Azure Zdefiniuj zapytanie na podstawie kombinacji subskrypcji, grup zasobów, lokalizacji i tagów. To zapytanie tworzy dynamiczną grupę maszyn wirtualnych hostowanych na platformie Azure do uwzględnienia we wdrożeniu. </br></br>W przypadku maszyn wirtualnych, które nie są hostowane na platformie Azure, wybierz istniejące zapisane wyszukiwanie. Korzystając z tego wyszukiwania, możesz wybrać grupę tych maszyn wirtualnych, które mają zostać uwzględnione we wdrożeniu. </br></br> Aby dowiedzieć się więcej, zobacz [grupy dynamiczne](../../automation/update-management/configure-groups.md).|
| **Maszyny do zaktualizowania** |Wybierz pozycję **zapisane wyszukiwanie**, **zaimportowana Grupa** lub **maszyny**.<br/><br/>W przypadku wybrania opcji **maszyny** można wybrać poszczególne komputery z listy rozwijanej. Gotowość poszczególnych maszyn jest pokazywana w kolumnie **Aktualizuj gotowość agenta** tabeli.</br></br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../../azure-monitor/logs/computer-groups.md) |
|**Klasyfikacje aktualizacji**|Wybierz wszystkie niezbędne klasyfikacje aktualizacji.|
|**Uwzględnij/Wyklucz aktualizacje**|Wybierz tę opcję, aby otworzyć okienko **Uwzględnij/wykluczania** . Aktualizacje do uwzględnienia, które mają zostać wykluczone, znajdują się na oddzielnych kartach. Aby uzyskać więcej informacji na temat obsługi dołączania, zobacz [Planowanie wdrożenia aktualizacji](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Ustawienia harmonogramu**|Wybierz godzinę do uruchomienia i wybierz **jeden raz** lub **cyklicznie**.|
| **Skrypty przed skryptami + po skrypcie**|Wybierz skrypty do uruchomienia przed i po wdrożeniu.|
| **Okno obsługi** | Wprowadź liczbę minut ustawioną dla aktualizacji. Prawidłowe wartości mieszczą się w zakresie od 30 do 360 minut. |
| **Kontrola ponownego uruchamiania**| Wybierz sposób obsługi ponownych uruchomień. Dostępne opcje to:<ul><li>**Uruchom ponownie, jeśli jest to wymagane**</li><li>**Zawsze uruchamiaj ponownie**</li><li>**Nigdy nie uruchamiaj ponownie**</li><li>**Tylko ponowny rozruch**</li></ul>**Ponowne uruchomienie, jeśli** jest to wymagane jest wybór domyślny. W przypadku wybrania opcji **tylko ponowny rozruch** aktualizacje nie zostaną zainstalowane.|

Po zakończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz** , aby powrócić do pulpitu nawigacyjnego stanu. W **planowanej** tabeli przedstawiono utworzony harmonogram wdrożenia.

Można również programowo tworzyć wdrożenia aktualizacji. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element Runbook, za pomocą którego można utworzyć cotygodniowe wdrożenie aktualizacji. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrożenia na karcie **wdrożenia aktualizacji** okna **Zarządzanie aktualizacjami** można zobaczyć stan wdrożenia.

Jeśli wdrożenie jest aktualnie uruchomione, jego stan jest wyświetlany jako "w toku". Po pomyślnym zakończeniu stan zmieni się na "powodzenie". Jeśli jednak aktualizacje we wdrożeniu zakończą się niepowodzeniem, stanem jest "częściowo niepowodzenie".

Wybierz ukończone wdrożenie aktualizacji, aby wyświetlić pulpit nawigacyjny dla tego wdrożenia.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/tutorial-monitoring/manageupdates-view-results.png)

Kafelek **Aktualizuj wyniki** zawiera podsumowanie łącznej liczby aktualizacji i wyników wdrożenia na maszynie wirtualnej. Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji. Każdy wynik ma jedną z następujących wartości:

* **Nie podjęto próby**: aktualizacja nie jest zainstalowana. Za mało czasu na podstawie zdefiniowanego czasu konserwacji.
* **Powodzenie**: aktualizacja powiodła się.
* **Niepowodzenie**: aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz opcję **Wszystkie dzienniki**.

Wybierz kafelek **dane wyjściowe** , aby wyświetlić strumień zadań elementu Runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Wybierz pozycję **Błędy** , aby wyświetlić szczegółowe informacje o błędach wdrożenia.

## <a name="monitor-changes-and-inventory"></a>Monitorowanie zmian i spisu

Można zbierać i przeglądać spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach. Śledzenie konfiguracji maszyn pomaga w wykorzystaniu problemów operacyjnych w środowisku i lepszym zrozumieniu stanu maszyn.

### <a name="enable-change-and-inventory-management"></a>Włączanie zarządzania zmianami i spisem

Aby włączyć zarządzanie zmianami i spisem dla maszyny wirtualnej:

1. Na lewej stronie okna wybierz pozycję **maszyny wirtualne**.
1. Z listy wybierz maszynę wirtualną.
1. W obszarze **operacje** w oknie maszyny wirtualnej wybierz pozycję **spis** lub **śledzenie zmian**.
1. Zostanie otwarte okienko **włączanie Change Tracking i spis** .

Skonfiguruj lokalizację, Log Analytics obszar roboczy i konto usługi Automation, a następnie wybierz pozycję **Włącz**. Jeśli opcje są wygaszone, rozwiązanie automatyzacji jest już włączone dla maszyny wirtualnej. W takim przypadku należy użyć już włączonego obszaru roboczego i konta usługi Automation.

Mimo że rozwiązania pojawiają się osobno w menu, są to te same rozwiązania. Włączenie jednego z nich oznacza włączenie obydwu na maszynie wirtualnej.

![Włączanie śledzenia zmian i spisu](./media/tutorial-monitoring/manage-inventory-enable.png)

Po włączeniu rozwiązania może upłynąć trochę czasu na zebranie spisu na maszynie wirtualnej przed wyświetleniem danych.

### <a name="track-changes"></a>Śledzenie zmian

Na maszynie wirtualnej w obszarze **operacje** wybierz pozycję **Change Tracking** a następnie wybierz pozycję **Edytuj ustawienia**. Zostanie otwarte okienko **Change Tracking** . Wybierz typ ustawienia do śledzenia, a następnie kliknij pozycję **+ Dodaj** w celu skonfigurowania ustawień.

Dostępne opcje ustawień dla systemu Windows to:

* Rejestr systemu Windows
* Pliki systemu Windows

Aby uzyskać szczegółowe informacje na temat Change Tracking, zobacz [Rozwiązywanie problemów ze zmianami w maszynie wirtualnej](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Wyświetlanie spisu

Na maszynie wirtualnej wybierz pozycję **spis** w obszarze **operacje**. Na karcie **oprogramowanie** znajduje się tabela przedstawiająca oprogramowanie, które zostało znalezione. Szczegóły wysokiego poziomu dla każdego rekordu oprogramowania są wyświetlane w tabeli. Te szczegóły obejmują nazwę oprogramowania, wersję, wydawcę i czas ostatniego odświeżenia.

![Wyświetlanie spisu](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorowanie dzienników aktywności i zmian

W oknie **śledzenie zmian** na maszynie wirtualnej wybierz pozycję **Zarządzaj połączeniem dziennika aktywności** , aby otworzyć okienko **Dziennik aktywności platformy Azure** . Wybierz pozycję **Połącz** , aby nawiązać połączenie Change Tracking z dziennikiem aktywności platformy Azure dla maszyny wirtualnej.

Po włączeniu Change Tracking przejdź do okienka **Przegląd** dla maszyny wirtualnej i wybierz pozycję **Zatrzymaj** , aby zatrzymać maszynę wirtualną. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. Po cofnięciu przydziału maszyny wirtualnej wybierz pozycję **Rozpocznij** , aby ponownie uruchomić maszynę wirtualną.

Zatrzymywanie i ponowne uruchamianie maszyny wirtualnej rejestruje zdarzenie w dzienniku aktywności. Wróć do okienka **śledzenie zmian** i wybierz kartę **zdarzenia** w dolnej części okienka. Po czasie zdarzenia są wyświetlane na wykresie i w tabeli. Możesz wybrać każde zdarzenie, aby wyświetlić szczegółowe informacje o tym zdarzeniu.

![Wyświetlanie zmian w dzienniku aktywności](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Poprzedni wykres pokazuje zmiany, które wystąpiły w czasie. Po dodaniu połączenia z dziennikiem aktywności platformy Azure wykres liniowy u góry Wyświetla zdarzenia dziennika aktywności platformy Azure.

Każdy wiersz wykresów słupkowych reprezentuje inny typ zmiany do śledzenia. Te typy to demony, pliki, klucze rejestru systemu Windows, oprogramowanie i usługi systemu Windows. Karta **zmiana** zawiera szczegóły zmiany. Zmiany są wyświetlane w kolejności, w której wystąpiły, wraz z ostatnią zmianą.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i sprawdzono Change Tracking i Update Management dla maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Utwórz grupę zasobów i maszynę wirtualną.
> * Zarządzaj aktualizacjami systemu Windows.
> * Monitoruj zmiany i spis.

Przejdź do następnego samouczka, aby dowiedzieć się więcej o monitorowaniu maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitor.md)

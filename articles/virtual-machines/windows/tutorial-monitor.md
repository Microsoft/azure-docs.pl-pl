---
title: Samouczek — monitorowanie maszyn wirtualnych z systemem Windows na platformie Azure
description: W tym samouczku dowiesz się, jak monitorować wydajność i wykryte składniki aplikacji uruchomione na maszynach wirtualnych z systemem Windows.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 8c4345d16efe296413ea9d995c2307bba62d33f8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560705"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Samouczek: monitorowanie maszyny wirtualnej z systemem Windows na platformie Azure

Usługa Azure Monitoring używa agentów do zbierania danych dotyczących rozruchu i wydajności z maszyn wirtualnych platformy Azure, przechowywania tych danych w usłudze Azure Storage i udostępniania ich za pośrednictwem portalu, modułu Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Zaawansowane monitorowanie jest dostarczane z Azure Monitor dla maszyn wirtualnych przez zbieranie metryk wydajności, odnajdywanie składników aplikacji zainstalowanych na maszynie wirtualnej i zawiera wykresy wydajności i mapę zależności.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta maszyny wirtualnej
> * Włącz Azure Monitor dla maszyn wirtualnych
> * Wyświetl metryki wydajności maszyny wirtualnej
> * Utworzenie alertu

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby w tym samouczku móc skonfigurować monitorowanie i zarządzanie aktualizacjami na platformie Azure, konieczne jest posiadanie maszyny wirtualnej z systemem Windows na platformie Azure. Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Grupa zasobów *myResourceGroupMonitorMonitor* i pomocnicze zasoby sieciowe zostaną utworzone, jeśli jeszcze nie istnieją:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.

## <a name="view-boot-diagnostics"></a>Wyświetlanie diagnostyki rozruchu

Podczas rozruchu maszyn wirtualnych z systemem Windows agent diagnostyki rozruchu przechwytuje dane wyjściowe z ekranu, których można używać do rozwiązywania problemów. Ta funkcja jest domyślnie włączona. Przechwycone zrzuty ekranu są przechowywane na koncie usługi Azure Storage, które jest również tworzone domyślnie.

Dane diagnostyczne rozruchu można pobrać za pomocą polecenia [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). W poniższym przykładzie dane diagnostyki rozruchu są pobierane do katalogu głównego na dysku *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Wyświetlanie metryki hosta

Maszyna wirtualna z systemem Windows ma na platformie Azure dedykowaną maszynę wirtualną hosta, z którą wchodzi w interakcję. Metryki są automatycznie zbierane dla hosta. Można je wyświetlić w witrynie Azure Portal.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.
2. Aby zobaczyć, jak działa maszyna wirtualna hosta, kliknij pozycję **Metryki** w bloku maszyny wirtualnej, a następnie wybierz dowolną metrykę Host w obszarze **Dostępne metryki**.

    ![Wyświetlanie metryki hosta](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Włącz monitorowanie zaawansowane

Aby włączyć monitorowanie maszyny wirtualnej platformy Azure przy użyciu Azure Monitor dla maszyn wirtualnych:

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)**.

3. Na stronie **Insights (wersja zapoznawcza)** wybierz pozycję **Wypróbuj teraz**.

    ![Włączanie Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Na stronie Dołączanie do usługi **Azure monitor Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji, wybierz go na liście rozwijanej.  

    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w której wdrożono maszynę wirtualną w ramach subskrypcji. 

    >[!NOTE]
    >Aby utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z maszyny wirtualnej, zobacz [tworzenie log Analytics obszaru roboczego](../../azure-monitor/logs/quick-create-workspace.md). Obszar roboczy musi należeć do jednego z [obsługiwanych regionów](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).

Po włączeniu monitorowania może być konieczne odczekanie kilku minut, zanim będzie można wyświetlić metryki wydajności dla maszyny wirtualnej.

![Włącz przetwarzanie wdrożenia monitorowania Azure Monitor dla maszyn wirtualnych](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Wyświetl metryki wydajności maszyny wirtualnej

Azure Monitor dla maszyn wirtualnych obejmuje zestaw wykresów wydajności przeznaczonych dla kilku kluczowych wskaźników wydajności (KPI), aby pomóc w określeniu, jak dobrze działa maszyna wirtualna. Aby uzyskać dostęp do maszyny wirtualnej, wykonaj następujące czynności.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję szczegółowe dane **(wersja zapoznawcza)**.

3. Wybierz kartę **Wydajność**.

Ta strona zawiera nie tylko wykresy wykorzystania wydajności, ale również tabelę zawierającą dla każdego wykrytego dysku logicznego, jego pojemność, wykorzystanie i łączną średnią wartość na podstawie każdej miary.

## <a name="create-alerts"></a>Tworzenie alertów

Możesz utworzyć alerty w oparciu o konkretne metryki wydajności. Przykładowo alertów można używać do wysyłania powiadomień w przypadku przekroczenia wybranego progu użycia procesora lub spadku dostępnego miejsca na dysku poniżej wybranej wartości. Alerty mogą być wyświetlane w witrynie Azure Portal lub wysyłane za pośrednictwem poczty e-mail. Możesz też wyzwolić elementy runbook usługi Azure Automation lub aplikacje usługi Azure Logic Apps w ramach reakcji na wygenerowane alerty.

Poniższy przykład tworzy alert dotyczący średniego użycia procesora.

1. W witrynie Azure Portal kliknij pozycję **Grupy zasobów**, wybierz grupę **myResourceGroupMonitor**, a następnie wybierz maszynę wirtualną **myVM** na liście zasobów.

2. Kliknij pozycję **Reguły alertów** w bloku maszyny wirtualnej, a następnie kliknij pozycję **Dodaj alert metryki** w górnej części bloku alertów.

3. Podaj **nazwę** alertu, np. *myAlertRule*

4. Aby wyzwolić alert, gdy procent użycia procesora przekracza 1,0 przez pięć minut, pozostaw wybrane wszystkie inne wartości domyślne.

5. Opcjonalnie zaznacz pole pozycji *Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników*, aby wysłać powiadomienie w wiadomości e-mail. Domyślne działanie to prezentowanie powiadomienia w portalu.

6. Kliknij przycisk **OK** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono Konfigurowanie i wyświetlanie wydajności maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i maszyny wirtualnej
> * Włączanie diagnostyki rozruchu na maszynie wirtualnej
> * Wyświetlanie diagnostyki rozruchu
> * Wyświetlanie metryki hosta
> * Włącz Azure Monitor dla maszyn wirtualnych
> * Wyświetlanie metryk maszyny wirtualnej
> * Utworzenie alertu

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat usługi Azure Security Center.

> [!div class="nextstepaction"]
> [Zarządzanie zabezpieczeniami maszyn wirtualnych](../../security/fundamentals/overview.md)

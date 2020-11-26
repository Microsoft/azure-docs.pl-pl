---
title: Monitoruj maszynę wirtualną platformy Azure przy użyciu Azure Monitor
description: Dowiedz się, jak zbierać i analizować dane dla maszyny wirtualnej platformy Azure w Azure Monitor.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: ef0e3a451a5687278acaf1e857012027a5e657a3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186766"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Szybki Start: monitorowanie maszyny wirtualnej platformy Azure za pomocą Azure Monitor
[Azure monitor](../overview.md) uruchamia zbieranie danych z maszyn wirtualnych platformy Azure w momencie ich tworzenia. W tym przewodniku szybki start zawarto krótkie wskazówki dotyczące danych, które są automatycznie zbierane dla maszyny wirtualnej platformy Azure oraz jak wyświetlać ją w Azure Portal. Następnie włączysz [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) dla maszyny wirtualnej, która umożliwi agentom na maszynie wirtualnej zbieranie i analizowanie danych z systemu operacyjnego gościa, w tym procesów i ich zależności.

W tym przewodniku Szybki start przyjęto założenie, że masz już maszynę wirtualną platformy Azure. W przeciwnym razie można utworzyć [maszynę wirtualną z systemem Windows](../../virtual-machines/windows/quick-create-portal.md) lub utworzyć [maszynę wirtualną](../../virtual-machines/linux/quick-create-cli.md) z systemem Linux przy użyciu samouczków szybki start dla maszyn wirtualnych.

Aby uzyskać bardziej szczegółowe opisy danych monitorowania zbieranych z zasobów platformy Azure, zobacz [monitorowanie maszyn wirtualnych platformy Azure przy użyciu Azure monitor](../insights/monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Ukończ monitorowanie usługi Azure Resource szybkiego startu.
Ukończ [monitorowanie zasobu platformy Azure za pomocą Azure monitor](quick-monitor-azure-resource.md) , aby wyświetlić stronę przeglądu, dziennik aktywności i metryki dla maszyny wirtualnej w ramach subskrypcji. Maszyny wirtualne platformy Azure zbierają te same dane monitorowania co inne zasoby platformy Azure, ale tylko dla maszyny wirtualnej hosta. Pozostała część tego przewodnika Szybki Start koncentruje się na monitorowaniu systemu operacyjnego gościa i jego obciążeń.


## <a name="enable-azure-monitor-for-vms"></a>Włącz Azure Monitor dla maszyn wirtualnych
W trakcie zbierania metryk i dzienników aktywności dla maszyny wirtualnej hosta potrzebna jest Agent i pewna konfiguracja służąca do zbierania i analizowania danych monitorowania z systemu operacyjnego gościa i jego obciążeń. Azure Monitor dla maszyn wirtualnych instaluje tych agentów i oferuje dodatkowe zaawansowane funkcje do monitorowania maszyn wirtualnych.

1. Przejdź do menu dla swojej maszyny wirtualnej.
2. Kliknij pozycję **Przejdź do szczegółowych** informacji na kafelku na stronie **Przegląd** lub kliknij pozycję **szczegółowe** dane w menu **monitorowanie** .

    ![Strona przeglądu](media/quick-monitor-azure-vm/overview-insights.png)

3. Jeśli Azure Monitor dla maszyn wirtualnych nie został jeszcze włączony dla maszyny wirtualnej, kliknij pozycję **Włącz**. 

    ![Włącz szczegółowe dane](media/quick-monitor-azure-vm/enable-insights.png)

4. Jeśli maszyna wirtualna nie jest już dołączona do obszaru roboczego Log Analytics, zostanie wyświetlony monit o wybranie istniejącego obszaru roboczego lub utworzenie nowego. Wybierz wartość domyślną, która jest obszarem roboczym z unikatową nazwą w tym samym regionie co maszyna wirtualna.

    ![Wybór obszaru roboczego](media/quick-monitor-azure-vm/select-workspace.png)

5. Dołączanie zajmie kilka minut, ponieważ rozszerzenia są włączone, a agenci są zainstalowani na maszynie wirtualnej. Po zakończeniu otrzymasz komunikat informujący o pomyślnym wdrożeniu szczegółowych informacji. Kliknij **Azure monitor** , aby otworzyć Azure monitor dla maszyn wirtualnych.

    ![Otwórz Azure Monitor](media/quick-monitor-azure-vm/azure-monitor.png)

6. Zostanie wyświetlona Twoja maszyna wirtualna z innymi maszynami wirtualnymi w ramach subskrypcji, które są dołączane. Wybierz kartę **Niemonitorowane** , jeśli chcesz wyświetlić maszyny wirtualne w subskrypcji, które nie zostały dołączone.

    ![Wprowadzenie](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego
Podczas tworzenia nowego obszaru roboczego Log Analytics należy go skonfigurować do zbierania dzienników. Tę konfigurację należy wykonać tylko raz, ponieważ konfiguracja jest wysyłana do wszystkich maszyn wirtualnych, które się z nią nawiązują.

1. Wybierz pozycję **Konfiguracja obszaru roboczego** , a następnie wybierz obszar roboczy.

2. Wybierz **Ustawienia zaawansowane**

    ![Ustawienia zaawansowane usługi Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Windows


2. Wybierz pozycję **Dane**, a następnie pozycję **Dzienniki zdarzeń systemu Windows**.

3. Dodaj dziennik zdarzeń, wpisując nazwę dziennika.  Wpisz **system** , a następnie wybierz znak plus **+** .

4. W tabeli zaznacz ważności **Błąd** i **Ostrzeżenie**.

5. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

### <a name="data-collection-from-linux-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Linux

1. Wybierz dziennik **Syslog**.  

2. Dodaj dziennik zdarzeń, wpisując nazwę dziennika.  Wpisz **Dziennik** systemowy, a następnie wybierz znak plus **+** .  

3. W tabeli Usuń zaznaczenie **informacji o informacjach** dotyczących serwerów, **powiadomienia** i **debugowania**. 

4. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

7. Kliknij maszynę wirtualną, a następnie wybierz kartę **wydajność** , która znajduje się na kafelku **monitorowanie** **szczegółowe** dane. Spowoduje to wyświetlenie grupy liczników wydajności zebranych z systemu operacyjnego gościa maszyny wirtualnej. Przewiń w dół, aby wyświetlić więcej liczników, i przesuń wskaźnik myszy na wykres, aby wyświetlić średnią i percentyly w różnych godzinach.

    ![Zrzut ekranu przedstawia okienko wydajności.](media/quick-monitor-azure-vm/performance.png)

9. Wybierz pozycję **Mapuj** , aby otworzyć funkcję Maps, która pokazuje procesy działające na maszynie wirtualnej i ich zależności. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości, jeśli nie jest jeszcze otwarte.

    ![Zrzut ekranu przedstawia okienko mapa.](media/quick-monitor-azure-vm/map.png)

11. Rozwiń procesy dla maszyny wirtualnej. Wybierz jeden z procesów, aby wyświetlić jego szczegóły i wyróżnić jego zależności.

    ![Zrzut ekranu przedstawia okienko mapa z rozwiniętymi procesami dla maszyny wirtualnej.](media/quick-monitor-azure-vm/processes.png)

12. Ponownie wybierz maszynę wirtualną, a następnie wybierz pozycję **zdarzenia dziennika**. 

    ![Zdarzenia dziennika](media/quick-monitor-azure-vm/log-events.png)

13. Zostanie wyświetlona lista tabel, które są przechowywane w obszarze roboczym Log Analytics dla maszyny wirtualnej. Ta lista będzie się różnić w zależności od tego, czy korzystasz z maszyny wirtualnej z systemem Windows lub Linux. Kliknij tabelę **zdarzeń** . Obejmuje to wszystkie zdarzenia z dziennika zdarzeń systemu Windows. Log Analytics otwiera z prostym zapytaniem do pobrania wpisów dziennika zdarzeń.

    ![Analiza dziennika](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start włączono Azure Monitor dla maszyn wirtualnych dla maszyny wirtualnej i skonfigurowano obszar roboczy Log Analytics do zbierania zdarzeń dla systemu operacyjnego gościa. Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../log-query/log-analytics-tutorial.md)
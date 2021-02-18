---
title: Sprawdź, jakie oprogramowanie jest zainstalowane na maszynach wirtualnych za pomocą Azure Automation | Microsoft Docs
description: W tym artykule opisano oprogramowanie zainstalowane na maszynach wirtualnych w danym środowisku.
services: automation
keywords: Spis, Automatyzacja, śledzenie zmian
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593781"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Odkryj, jakie oprogramowanie jest zainstalowane na maszynach wirtualnych

W ramach tego samouczka nauczysz się korzystać z funkcji Azure Automation Change Tracking i spisu, aby dowiedzieć się, jakie oprogramowanie jest zainstalowane w danym środowisku. Można zbierać i wyświetlać Spis oprogramowania, plików, demonów systemu Linux, usług Windows i kluczy rejestru systemu Windows na komputerach. Śledzenie konfiguracji maszyn ułatwia identyfikowanie problemów operacyjnych w środowisku oraz lepsze rozumienie stanu maszyn.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rozwiązania Change Tracking and Inventory
> * Włącz maszynę wirtualną platformy Azure
> * Włącz maszynę wirtualną spoza platformy Azure
> * Wyświetlanie zainstalowanego oprogramowania
> * Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) , która ma zostać włączona dla tej funkcji.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Najpierw należy włączyć śledzenie zmian i spisu na potrzeby tego samouczka. Jeśli funkcja została wcześniej włączona, ten krok nie jest konieczny.

>[!NOTE]
>Jeśli pola są wyszarzone, dla maszyny wirtualnej jest włączona inna funkcja automatyzacji i musisz użyć tego samego obszaru roboczego i konta usługi Automation.

1. Przejdź do konta usługi Automation i wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz obszar roboczy [log Analytics](../azure-monitor/logs/log-query-overview.md) . Ten obszar roboczy zbiera dane, które są generowane przez funkcje takie jak Change Tracking i spis. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Wybierz konto usługi Automation, które ma być używane.

4. Skonfiguruj lokalizację wdrożenia.

5. Kliknij pozycję **Włącz** , aby wdrożyć funkcję dla maszyny wirtualnej. 

    ![Transparent konfiguracji spisu](./media/automation-tutorial-installed-software/enableinventory.png)

Podczas instalacji maszyna wirtualna jest obsługiwana za pomocą agenta Log Analytics dla systemu Windows i [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md). Włączenie Change Tracking i spisu może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki.

Po włączeniu tej funkcji informacje o zainstalowanym oprogramowaniu i zmianach na maszynach wirtualnych są przesyłane do dzienników Azure Monitor. Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Dodawanie maszyny wirtualnej platformy Azure do Change Tracking i spisu

1. Na koncie usługi Automation przejdź do pozycji **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz pozycję **+ Dodaj maszynę wirtualną platformy Azure**.

3. Wybierz maszynę wirtualną z listy maszyn wirtualnych. 

4. Kliknij pozycję **Włącz** , aby włączyć Change Tracking i spis na maszynie wirtualnej. Agent Log Analytics dla systemu Windows jest wdrażany na maszynie wirtualnej i konfiguruje maszynę wirtualną w celu komunikowania się z obszarem roboczym Log Analytics. Operacja instalacji może potrwać kilka minut. 

5. W tym momencie, w razie potrzeby, można wybrać nową maszynę wirtualną z listy, aby włączyć tę funkcję.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Dodawanie maszyny spoza platformy Azure do Change Tracking i spisu

Aby włączyć maszyny spoza platformy Azure dla tej funkcji:

1. W zależności od używanego systemu operacyjnego zainstaluj [agenta log Analytics dla systemu Windows](../azure-monitor/agents/agent-windows.md) lub [agenta log Analytics w systemie Linux](automation-linux-hrw-install.md). 

2. Przejdź do konta usługi Automation i przejdź do pozycji **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 

3. Kliknij pozycję **Zarządzaj komputerami**. Zostanie wyświetlona lista maszyn zgłaszanych do obszaru roboczego Log Analytics, dla których nie włączono Change Tracking i spisu. Wybierz odpowiednią opcję dla danego środowiska:

    * **Włącz na wszystkich dostępnych maszynach** — ta opcja włącza funkcję na wszystkich maszynach, które są w tej chwili raportowane do obszaru roboczego log Analytics.
    * **Włącz na wszystkich dostępnych maszynach i w przyszłych maszynach** — ta opcja włącza funkcję na wszystkich komputerach, które są raportowane do obszaru roboczego log Analytics, a następnie na wszystkich przyszłych maszynach dodanych do obszaru roboczego.
    * **Włącz na wybranych maszynach** — ta opcja włącza funkcję tylko na wybranych maszynach.

    ![Zarządzanie maszynami](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Wyświetlanie zainstalowanego oprogramowania

Po włączeniu funkcji Change Tracking i spisu możesz wyświetlić wyniki na stronie spisu.

1. Na koncie usługi Automation wybierz pozycję **spis** w obszarze **Zarządzanie konfiguracją**.

2. Na stronie Spis kliknij kartę **Oprogramowanie**.

3. Zanotuj tabelę zawierającą listę znalezionego oprogramowania. Oprogramowanie jest grupowane według nazwy i wersji oprogramowania. Szczegółowe informacje wysokiego poziomu dotyczące każdego rekordu oprogramowania są wyświetlane w tabeli. Te szczegółowe informacje obejmują nazwę oprogramowania, wersję, wydawcę, czas ostatniego odświeżenia (ostatni czas odświeżania zgłoszony przez maszynę w grupie) i maszyny (liczba maszyn z tym oprogramowaniem).

    ![Zapasy oprogramowania](./media/automation-tutorial-installed-software/inventory-software.png)

4. Kliknij wiersz, aby wyświetlić właściwości rekordu oprogramowania i nazwy maszyn z tym oprogramowaniem.

5. Aby znaleźć określone oprogramowanie lub grupę oprogramowania, możesz je wyszukać w polu tekstowym bezpośrednio nad listą oprogramowania.
Filtr umożliwia wyszukiwanie na podstawie nazwy oprogramowania, wersji lub wydawcy. Na przykład wyszukiwanie **contoso** zwraca wszystkie oprogramowanie o nazwie, wydawcy lub wersji zawierającej **contoso**.

## <a name="search-inventory-logs-for-installed-software"></a>Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

Change Tracking i spis generuje dane dziennika, które są wysyłane do dzienników Azure Monitor. Aby wyszukać dzienniki przez uruchomienie zapytań, wybierz **log Analytics** w górnej części strony spis. Dane spisu są przechowywane pod typem `ConfigurationData` .

Poniższy przykład Log Analytics Query zwraca wyniki spisu dla wydawcy Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Aby dowiedzieć się więcej na temat uruchamiania i wyszukiwania plików dziennika usługi Azure Monitor, zobacz [Dzienniki usługi Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Zobacz Spis oprogramowania dla jednej maszyny

Aby wyświetlić spis oprogramowania dla pojedynczego komputera, możesz uzyskać dostęp do spisu ze strony zasobu maszyny wirtualnej platformy Azure lub użyć dzienników usługi Azure Monitor do odfiltrowania odpowiedniej maszyny. Poniższy przykład Log Analytics Query zwraca listę oprogramowania dla komputera o nazwie **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wyświetlania spisu oprogramowania:

> [!div class="checklist"]
> * Włączanie rozwiązania Change Tracking and Inventory
> * Włącz maszynę wirtualną platformy Azure
> * Włącz maszynę wirtualną spoza platformy Azure
> * Wyświetlanie zainstalowanego oprogramowania
> * Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

Przejdź do omówienia funkcji Change Tracking i spisu, aby dowiedzieć się więcej na jej temat.

> [!div class="nextstepaction"]
> [Przegląd Change Tracking i spisu](change-tracking/overview.md)

---
title: Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu | Microsoft Docs
description: Zarządzanie maszyną wirtualną z wykorzystaniem zbierania spisu
services: automation
ms.subservice: change-inventory-management
keywords: spis, automatyzacja, zmiana, śledzenie
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617368"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu

Istnieje możliwość włączenia śledzenia z użyciem spisu dla maszyny wirtualnej platformy Azure na stronie zasobów tej maszyny wirtualnej. Na komputerach można zbierać i przeglądać następujące informacje dotyczące spisu:

- Oprogramowanie systemu Windows (aplikacje systemu Windows i aktualizacje systemu Windows), usługi, pliki i klucze rejestru
- Demony oprogramowania (pakiety) systemu Linux i pliki

Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce przeznaczony do instalowania i konfigurowania zbierania spisu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

W tym artykule założono, że masz maszynę wirtualną, na której ma zostać skonfigurowane rozwiązanie. Jeśli nie masz maszyny wirtualnej platformy Azure, utwórz [maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Włączanie zbierania spisu na stronie zasobów maszyny wirtualnej

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Maszyny wirtualne**.
2. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
3. W menu **zasób** w obszarze **operacje**wybierz pozycję **spis**.
4. Wybierz obszar roboczy Log Analytics do przechowywania dzienników danych.
    Jeśli w tym regionie nie ma dostępnego dla Ciebie obszaru roboczego, pojawi się monit o utworzenie domyślnego obszaru roboczego i konta usługi Automation.
5. Aby rozpocząć dołączanie swojego komputera, kliknij pozycję **Włącz**.

   ![Widok opcji dołączania](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Pojawi się pasek stanu z informacją o tym, że trwa włączanie rozwiązania. Ten proces może potrwać do 15 minut. W tym czasie możesz zamknąć okno lub pozostawić je otwarte i powiadomić Cię, gdy rozwiązanie zostanie włączone. Stan wdrożenia możesz monitorować w okienku powiadomień.

   ![Widok rozwiązania spisu natychmiast po dołączeniu](./media/automation-vm-inventory/inventory-onboarded.png)

Po zakończeniu wdrażania pasek stanu zniknie. System nadal będzie zbierać dane spisu i dane mogą być jeszcze niewidoczne. Zebranie pełnych danych może zająć 24 godziny.

## <a name="configure-your-inventory-settings"></a>Konfigurowanie ustawień spisu

Domyślnie na potrzeby zbierania skonfigurowane są oprogramowanie, usługi systemu Windows oraz demony systemu Linux. Aby zbierać spis dla rejestru systemu Windows i plików, skonfiguruj ustawienia zbierania spisu.

1. Na stronie spis kliknij pozycję **Edytuj ustawienia** w górnej części strony.
2. Aby dodać nowe ustawienie kolekcji, przejdź do kategorii ustawień, która ma zostać dodana, wybierając kartę **Rejestr systemu Windows**, **pliki systemu Windows**lub system **Linux** .
3. Wybierz odpowiednią kategorię, a następnie kliknij przycisk **Dodaj** w górnej części strony.

Poniższe tabele zawierają informacje dotyczące poszczególnych właściwości, które można skonfigurować dla różnych kategorii.

### <a name="windows-registry"></a>Rejestr systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
|Grupa     | Nazwa grupy do logicznego grupowania plików        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzania pliku, na przykład: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Pliki systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.       |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Pliki systemu Linux

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **/etc/*. conf**.       |
|Typ ścieżki     | Typ elementu, który ma być śledzony. Wartości to plik i katalog.        |
|Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |
|Użyj polecenia Sudo     | Wartość true, jeśli sudo jest używany podczas sprawdzania elementu i w przeciwnym razie zwraca wartość false.         |
|Linki     | Wartość wskazująca, w jaki sposób są rozwiązywane linki symboliczne podczas przechodzenia z katalogów. Możliwe wartości: <br> Ignoruj — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołują<br>Śledź — śledzi linki symboliczne podczas rekursji i uwzględnia również pliki/katalogi, do których się odwołują<br>Zarządzaj — śledzi linki symboliczne i umożliwia obsługę zwracanej zawartości      |

## <a name="manage-machine-groups"></a>Zarządzanie grupami maszyn

Spis umożliwia tworzenie i przeglądanie grup maszyn w dziennikach Azure Monitor. Grupy maszyn to kolekcje maszyn zdefiniowane przez zapytanie w Azure Monitor dziennikach.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby wyświetlić grupy maszyn, wybierz kartę **grupy maszyn** na stronie spisu.

![Wyświetlanie grup maszyn na stronie spisu](./media/automation-vm-inventory/inventory-machine-groups.png)

Wybranie grupy maszyn z listy spowoduje otwarcie strony grupy maszyn. Na tej stronie są wyświetlane szczegółowe informacje o grupie maszyn. Te szczegóły obejmują zapytanie usługi log Analytics, które jest używane do definiowania grupy. W dolnej części strony jest stronicowaną listę maszyn, które są częścią tej grupy.

![Wyświetl stronę grupy maszyn](./media/automation-vm-inventory/machine-group-page.png)

Kliknij pozycję **+ Klonuj** , aby sklonować grupę maszyn. Należy nadać grupie nową nazwę i alias dla grupy. W tej chwili można zmienić definicję. Po zmianie zapytania kliknij polecenie **Weryfikuj zapytanie** , aby wyświetlić podgląd wybranych maszyn. Gdy jesteś zadowolony z grupy, kliknij przycisk **Utwórz** , aby utworzyć grupę maszyn.

Jeśli chcesz utworzyć nową grupę maszyn, kliknij pozycję **+ Utwórz grupę maszyn**. Ten przycisk otwiera stronę **Tworzenie grupy maszyn** , na której można zdefiniować nową grupę. Kliknij pozycję **Utwórz**, aby utworzyć grupę.

![Utwórz nową grupę maszyn](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odłączanie maszyny wirtualnej od zarządzania

Aby usunąć maszynę wirtualną z zarządzania spisem:

1. W okienku po lewej stronie w witrynie Azure Portal kliknij pozycję **Log Analytics**, a następnie wybierz kliknięciem obszar roboczy, który był używany podczas dołączania danej maszyny wirtualnej.
2. Na stronie Log Analytics Otwórz menu **zasobów** .
3. Wybierz **Virtual Machines** w obszarze **źródła danych obszaru roboczego**.
4. Z listy wybierz maszynę wirtualną, którą chcesz odłączyć. Maszyna wirtualna ma zielony znacznik wyboru obok pozycji **Ten obszar roboczy** w kolumnie **Połączenie OMS**.

   >[!NOTE]
   >Pakiet Operations Management Suite (OMS) jest teraz nazywany dziennikami Azure Monitor.
   
5. W górnej części następnej strony kliknij pozycję **Rozłącz**.
6. W oknie potwierdzenia kliknij przycisk **tak** , aby odłączyć maszynę od zarządzania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zarządzaniu zmianami w plikach i ustawieniach rejestru na maszynach wirtualnych, zobacz [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Śledzenie zmian oprogramowania w środowisku za pomocą rozwiązania do śledzenia zmian).
* Aby dowiedzieć się więcej na temat zarządzania aktualizacjami systemu Windows i pakietów na maszynach wirtualnych, zobacz [rozwiązanie Update Management na platformie Azure](../operations-management-suite/oms-solution-update-management.md).
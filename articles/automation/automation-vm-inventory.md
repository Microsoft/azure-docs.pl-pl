---
title: Zarządzanie kolekcją Azure Automation spisu z maszyn wirtualnych | Microsoft Docs
description: W tym artykule opisano sposób zarządzania kolekcją spisu z maszyn wirtualnych.
services: automation
ms.subservice: change-inventory-management
keywords: spis, automatyzacja, zmiana, śledzenie
ms.date: 06/30/2020
ms.topic: conceptual
ms.openlocfilehash: 32d3c17a5f3d152f32b19ffbfd5c9793a7a34b80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185725"
---
# <a name="manage-inventory-collection-from-vms"></a>Zarządzanie zbieraniem spisu z maszyn wirtualnych

Śledzenie spisu dla maszyny wirtualnej platformy Azure można włączyć na stronie zasobów maszyny. Na komputerach można zbierać i przeglądać następujące informacje dotyczące spisu:

- Aktualizacje systemu Windows, aplikacje systemu Windows, usługi, pliki i klucze rejestru
- Pakiety oprogramowania, demony i pliki systemu Linux

Azure Automation Change Tracking i spis udostępnia interfejs użytkownika oparty na przeglądarce służący do konfigurowania i konfigurowania kolekcji spisu.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

W tym artykule przyjęto założenie, że masz maszynę wirtualną do włączenia do Change Tracking i spisu. Jeśli nie masz maszyny wirtualnej platformy Azure, możesz [utworzyć maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Włącz zbieranie spisu na stronie zasobów maszyny wirtualnej

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Maszyny wirtualne**.
2. Z listy maszyn wirtualnych wybierz maszynę.
3. W menu **zasób** w obszarze **operacje**wybierz pozycję **spis**.
4. Wybierz obszar roboczy Log Analytics do przechowywania dzienników danych.
    Jeśli w tym regionie nie ma dostępnego dla Ciebie obszaru roboczego, pojawi się monit o utworzenie domyślnego obszaru roboczego i konta usługi Automation.
5. Aby rozpocząć włączanie komputera, wybierz pozycję **Włącz**.

   ![Widok opcji dołączania](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Pasek stanu informuje o tym, że funkcja Change Tracking i spis jest włączona. Ten proces może potrwać do 15 minut. W tym czasie możesz zamknąć okno lub pozostawić je otwarte i powiadomić Cię, gdy ta funkcja jest włączona. Stan wdrożenia możesz monitorować w okienku powiadomień.

   ![Wyświetlanie spisu](./media/automation-vm-inventory/inventory-onboarded.png)

Po zakończeniu wdrażania pasek stanu zniknie. System nadal będzie zbierać dane spisu i dane mogą być jeszcze niewidoczne. Zebranie pełnych danych może zająć 24 godziny.

## <a name="configure-your-inventory-settings"></a>Konfigurowanie ustawień spisu

Domyślnie na potrzeby zbierania skonfigurowane są oprogramowanie, usługi systemu Windows oraz demony systemu Linux. Aby zbierać spis dla rejestru systemu Windows i plików, skonfiguruj ustawienia zbierania spisu.

1. Na stronie spis kliknij pozycję **Edytuj ustawienia** w górnej części strony.
2. Aby dodać nowe ustawienie kolekcji, przejdź do kategorii ustawień, która ma zostać dodana, wybierając kartę **Rejestr systemu Windows**, **pliki systemu Windows**lub system **Linux** .
3. Wybierz odpowiednią kategorię, a następnie kliknij przycisk **Dodaj** w górnej części strony.

Poniższe sekcje zawierają informacje dotyczące poszczególnych właściwości, które można skonfigurować dla różnych kategorii.

### <a name="windows-registry"></a>Rejestr systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
|Group (Grupa)     | Nazwa grupy do logicznego grupowania plików        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzania pliku, na przykład: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Pliki systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Group (Grupa)     | Nazwa grupy do logicznego grupowania plików.       |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Pliki systemu Linux

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Group (Grupa)     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **/etc/*. conf**.       |
|Typ ścieżki     | Typ elementu, który ma być śledzony. Wartości to plik i katalog.        |
|Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |
|Użyj sudo     | Wartość true, jeśli sudo jest używany podczas sprawdzania elementu i w przeciwnym razie zwraca wartość false.         |
|Linki     | Wartość wskazująca, w jaki sposób są rozwiązywane linki symboliczne podczas przechodzenia z katalogów. Możliwe wartości: <br> Ignoruj — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołują<br>Śledź — śledzi linki symboliczne podczas rekursji i uwzględnia również pliki/katalogi, do których się odwołują<br>Zarządzaj — śledzi linki symboliczne i umożliwia obsługę zwracanej zawartości      |

## <a name="manage-machine-groups"></a>Zarządzanie grupami maszyn

Spis umożliwia tworzenie i przeglądanie grup maszyn w dziennikach Azure Monitor. Grupy maszyn to kolekcje maszyn zdefiniowane przez zapytanie w Azure Monitor dziennikach.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby wyświetlić grupy maszyn, wybierz kartę **grupy maszyn** na stronie spisu.

![Wyświetlanie grup maszyn na stronie spisu](./media/automation-vm-inventory/inventory-machine-groups.png)

Wybranie grupy maszyn z listy spowoduje otwarcie strony grupy maszyn. Na tej stronie są wyświetlane szczegółowe informacje o grupie maszyn. Te szczegóły obejmują zapytanie dziennika Azure Monitor, które jest używane do definiowania grupy. W dolnej części strony jest stronicowaną listę maszyn, które są częścią tej grupy.

![Wyświetl stronę grupy maszyn](./media/automation-vm-inventory/machine-group-page.png)

Kliknij pozycję **+ Klonuj** , aby sklonować grupę maszyn. Należy nadać grupie nową nazwę i alias dla grupy. W tej chwili można zmienić definicję. Po zmianie zapytania kliknij polecenie **Weryfikuj zapytanie** , aby wyświetlić podgląd wybranych maszyn. Gdy jesteś zadowolony z grupy, kliknij przycisk **Utwórz** , aby utworzyć grupę maszyn.

Jeśli chcesz utworzyć nową grupę maszyn, kliknij pozycję **+ Utwórz grupę maszyn**. Ten przycisk otwiera stronę **Tworzenie grupy maszyn** , na której można zdefiniować nową grupę. Kliknij pozycję **Utwórz**, aby utworzyć grupę.

![Utwórz nową grupę maszyn](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Odłączanie maszyny wirtualnej od zarządzania

Aby usunąć maszynę wirtualną z Change Tracking i zarządzania spisem:

1. W lewym okienku Azure Portal wybierz pozycję **log Analytics**, a następnie wybierz obszar roboczy, który był używany podczas włączania maszyny wirtualnej na potrzeby Change Tracking i spisu.
2. Na stronie **log Analytics** Otwórz menu **zasobów** .
3. Wybierz **Virtual Machines** w obszarze **źródła danych obszaru roboczego**.
4. Z listy wybierz maszynę wirtualną, którą chcesz odłączyć. Komputer ma zielony znacznik wyboru obok **tego obszaru roboczego** w kolumnie połączenie pakietu **OMS** .

   >[!NOTE]
   >Pakiet Operations Management Suite (OMS) jest teraz nazywany dziennikami Azure Monitor.

5. W górnej części następnej strony kliknij pozycję **Rozłącz**.
6. W oknie potwierdzenia kliknij przycisk **tak** , aby odłączyć maszynę od zarządzania.

>[!NOTE]
>Po wyrejestrowaniu maszyn nadal są one wyświetlane, ponieważ zgłaszamy wszystkie maszyny w spisie w ciągu ostatnich 24 godzin. Po rozłączeniu komputera należy odczekać 24 godziny, zanim nie będą już wyświetlane.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby dowiedzieć się więcej o śledzeniu zmian oprogramowania, zobacz [śledzenie zmian oprogramowania w środowisku przy użyciu Change Tracking](./change-tracking.md).
* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).

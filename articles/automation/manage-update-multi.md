---
title: Zarządzanie aktualizacjami dla wielu maszyn wirtualnych w Azure Automation
description: W tym artykule opisano sposób zarządzania aktualizacjami dla wielu maszyn wirtualnych.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: d08afc6e501fd76167e0939633442213958f0d49
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834632"
---
# <a name="manage-updates-for-multiple-vms"></a>Zarządzanie aktualizacjami dla wielu maszyn wirtualnych

Za pomocą Update Management Azure Automation można zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows i Linux. Korzystając z konta usługi [Azure Automation](automation-offering-get-started.md), można wykonywać następujące czynności:

- Włącz maszyny wirtualne na potrzeby zarządzania aktualizacjami.
- Ocenianie stanu dostępnych aktualizacji.
- Planowanie instalacji wymaganych aktualizacji.
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do wszystkich maszyn wirtualnych, dla których Update Management jest włączona.

Aby dowiedzieć się więcej o wymaganiach systemowych dla Update Management, zobacz [Update Management wymagania dotyczące klienta](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna wirtualna lub komputer z zainstalowanym jednym z obsługiwanych systemów operacyjnych.
* Dostęp do repozytorium aktualizacji dla maszyn wirtualnych z systemem Linux z włączoną obsługą Update Management.

## <a name="enable-update-management-for-azure-vms"></a>Włączanie Update Management dla maszyn wirtualnych platformy Azure

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **Update Management**.

2. Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure**.

    ![Karta Dodaj maszynę wirtualną Azure](./media/manage-update-multi/update-onboard-vm.png)

3. Wybierz maszynę wirtualną do włączenia i wybierz pozycję **Włącz** w obszarze **Włącz Update Management**.

    ![Okno dialogowe Włączanie rozwiązania Update Management](./media/manage-update-multi/update-enable.png)

    Po zakończeniu operacji Update Management jest włączona na maszynie wirtualnej.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Włącz Update Management dla maszyn wirtualnych i komputerów spoza platformy Azure

Log Analytics agenta dla systemów Windows i Linux należy zainstalować na maszynach wirtualnych działających w sieci firmowej lub w innym środowisku chmury, aby umożliwić ich korzystanie z Update Management. Aby poznać wymagania systemowe i obsługiwane metody wdrażania agenta na maszynach hostowanych poza platformą Azure, zobacz [Omówienie agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Wyświetl komputery dołączone do konta usługi Automation

Po włączeniu Update Management dla maszyn można wyświetlić informacje o maszynie, wybierając pozycję **komputery**. Można wyświetlić informacje na temat nazwy komputera, stanu zgodności, środowiska, typu systemu operacyjnego, zainstalowanych aktualizacji krytycznych i zabezpieczeń, innych zainstalowanych aktualizacji oraz aktualizacji gotowości agenta dla komputerów.

  ![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-computers-tab.png)

Komputery, na których ostatnio włączono obsługę Update Management, mogły nie zostać jeszcze ocenione. Stan zgodności tych komputerów to `Not assessed` . Poniżej znajduje się lista możliwych wartości stanu zgodności:

- `Compliant`: Komputery, na których nie brakuje aktualizacji krytycznych lub zabezpieczeń.
- `Non-compliant`: Komputery, na których brakuje co najmniej jednej aktualizacji krytycznej lub zabezpieczeń.
- `Not assessed`: Dane oceny aktualizacji nie zostały odebrane z komputera w oczekiwanym przedziale czasu. W przypadku komputerów z systemem Linux oczekiwany przedział czasu to Ostatnia godzina. W przypadku komputerów z systemem Windows oczekiwany przedział czasu wynosi ostatnich 12 godzin.

Aby wyświetlić stan agenta, wybierz łącze w kolumnie **Aktualizowanie gotowości agenta** . Wybranie tej opcji powoduje otwarcie okienka hybrydowego procesu roboczego i wyświetlenie stanu hybrydowego procesu roboczego. Na poniższej ilustracji przedstawiono przykład agenta, który nie został podłączony do Update Management przez dłuższy czas:

![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu Update Management zostanie otwarte okienko Update Management. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

## <a name="collect-data"></a>Zbieranie danych

Agenci zainstalowani na maszynach wirtualnych i komputerach zbierają dane dotyczące aktualizacji. Agenci wysyłają dane do usługi Azure Update Management.

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez Update Management:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Update Management zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Update Management zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Update Management zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania. |
| Konto usługi Azure Storage |Nie |Usługa Azure Storage nie zawiera informacji o aktualizacjach systemu. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Gdy komputer ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekaże informacje zbiorczo do dzienników Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od ponownego uruchomienia MMA, przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent MMA zostanie ponownie uruchomiony, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, Zaplanuj wdrożenie, które jest wyrównane z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Zaplanowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerach docelowych. W przypadku usunięcia zasobu harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia zostanie ono przerwane i zostanie wyświetlony komunikat o błędzie podczas próby ponownego skonfigurowania go z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.
>

Aby zaplanować nowe wdrożenie aktualizacji dla co najmniej jednej maszyny wirtualnej, w obszarze **Zarządzanie aktualizacjami**wybierz pozycję **Zaplanuj wdrożenie aktualizacji**.

W okienku **nowe wdrożenie aktualizacji** podaj następujące informacje:

- **Nazwa**: Wprowadź unikatową nazwę identyfikującą wdrożenie aktualizacji.
- **System operacyjny**: Wybierz **system Windows** lub **Linux**.
- **Grupy do zaktualizowania**: Zdefiniuj zapytanie w oparciu o kombinację subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. W przypadku maszyn wirtualnych spoza platformy Azure zapisane wyszukiwania są używane do tworzenia grupy dynamicznej do uwzględnienia w danym wdrożeniu. Aby dowiedzieć się więcej, zobacz [grupy dynamiczne](automation-update-management-groups.md).
- **Maszyny do zaktualizowania**: Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz maszyny, aby wybrać maszyny, które chcesz zaktualizować.

   >[!NOTE]
   >Wybranie zapisanej opcji wyszukiwania nie zwraca tożsamości komputera, tylko ich nazwy. Jeśli masz kilka maszyn wirtualnych o tej samej nazwie w wielu grupach zasobów, są one zwracane w wynikach. Zaleca się dołączenie unikatowych maszyn wirtualnych spełniających kryteria przy użyciu opcji **grupy do aktualizacji** .

   W przypadku wybrania opcji **maszyny**gotowość komputera zostanie wyświetlona w kolumnie **Aktualizowanie gotowości agenta** . Stan kondycji maszyny można sprawdzić przed zaplanowaniem wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md)

  ![Nowe okienko wdrożenia aktualizacji](./media/manage-update-multi/update-select-computers.png)

- **Klasyfikacja aktualizacji**: Wybierz typy oprogramowania, które mają zostać uwzględnione we wdrożeniu aktualizacji. Opis typów klasyfikacji zawiera temat [Aktualizowanie klasyfikacji](automation-view-update-assessments.md#work-with-update-classifications). Dostępne są następujące typy klasyfikacji:
  - Aktualizacje krytyczne
  - Aktualizacje zabezpieczeń
  - Pakiety zbiorcze aktualizacji
  - Pakiety funkcji
  - Dodatki Service Pack
  - Aktualizacje definicji
  - narzędzia
  - Aktualizacje

- **Aktualizacje do uwzględnienia/wykluczenia** — spowoduje to otwarcie strony Uwzględnij/Wyklucz. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać dodatkowe informacje na temat obsługi dołączania, zobacz [Planowanie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Ważne jest, aby wiedzieć, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania dla programu `*` , nie zostaną zainstalowane żadne poprawki ani pakiety, ponieważ są one wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące na komputerze. W przypadku maszyn z systemem Linux, jeśli pakiet został uwzględniony, ale jego pakiet zależny został wykluczony, pakiet nie jest zainstalowany.

> [!NOTE]
> Nie można określić aktualizacji, które zostały zastąpione w celu włączenia ich do wdrożenia aktualizacji.

- **Ustawienia harmonogramu**: możesz zaakceptować domyślną datę i godzinę, czyli 30 minut po bieżącej godzinie. Możesz również określić inny czas.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z harmonogramem cyklicznym. Aby skonfigurować harmonogram cykliczny, w obszarze **cykl**wybierz pozycję **cykliczne**.

   ![Okno dialogowe Ustawienia harmonogramu](./media/manage-update-multi/update-set-schedule.png)

- **Skrypty wstępne i końcowe**: wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).
- **Okno obsługi (minuty)**: Określ okres, w którym ma wystąpić wdrożenie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

- **Kontrola ponownego uruchamiania** — to ustawienie określa sposób obsługi ponownych uruchomień dla wdrożenia aktualizacji.

   |Opcja|Opis|
   |---|---|
   |Uruchom ponownie, jeśli jest to wymagane| **(Ustawienie domyślne)** W razie potrzeby zostanie zainicjowany ponowny rozruch, jeśli zezwoli na okno obsługi.|
   |Zawsze uruchamiaj ponownie|Inicjowany jest ponowny rozruch bez względu na to, czy jest to wymagane. |
   |Nigdy nie uruchamiaj ponownie|Niezależnie od tego, czy jest wymagany ponowny rozruch, ponowne uruchomienia są pomijane.|
   |Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|Ta opcja ignoruje Instalowanie aktualizacji i inicjuje ponowny rozruch.|

Po zakończeniu konfigurowania harmonogramu wybierz przycisk **Utwórz** , aby powrócić do pulpitu nawigacyjnego stanu. W **planowanej** tabeli przedstawiono utworzony harmonogram wdrożenia.

> [!NOTE]
> Rozwiązanie Update Management obsługuje wdrażanie aktualizacji tej samej firmy i wstępne pobieranie poprawek. Wymaga to zmian w systemach z poprawkami, zobacz [pierwsza firma i pomoc wstępna pobierania](automation-configure-windows-update.md#pre-download-updates) , aby dowiedzieć się, jak skonfigurować te ustawienia w systemach.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**.

Jeśli wdrożenie jest aktualnie uruchomione, wyświetlany jest stan **W toku**. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie**.

W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.

![Stan wdrożenia aktualizacji](./media/manage-update-multi/update-view-results.png)

Aby wyświetlić pulpit nawigacyjny wdrożenia aktualizacji, wybierz ukończone wdrożenie.

Okienko wyniki aktualizacji zawiera łączną liczbę aktualizacji i wyniki wdrożenia dla maszyny wirtualnej. Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyniki instalacji. Wyniki instalacji mogą mieć jedną z następujących wartości:

- `Not attempted`: Aktualizacja nie została zainstalowana, ponieważ na podstawie zdefiniowanego okna obsługi była dostępna niewystarczająca ilość czasu.
- `Succeeded`: Aktualizacja powiodła się.
- `Failed`: Aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz pozycję **Wszystkie dzienniki**.

Aby wyświetlić strumień zadań elementu Runbook, który zarządza wdrożeniem aktualizacji na docelowej maszynie wirtualnej, wybierz kafelek dane wyjściowe.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Następne kroki

* Jeśli potrzebujesz wyszukiwania dzienników aktualizacji, zobacz [dzienniki Update Management zapytań](automation-update-management-query-logs.md).

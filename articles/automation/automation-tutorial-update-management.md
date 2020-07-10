---
title: Zarządzaj aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure w Azure Automation
description: W tym artykule opisano, jak używać Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185793"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure

W tym artykule opisano, jak można użyć funkcji Azure Automation [Update Management](automation-update-management.md) do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure. Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> Update Management obsługuje wdrożenie aktualizacji pierwszej firmy oraz pobranie poprawek. Ta obsługa wymaga zmian w systemach z poprawkami. Aby dowiedzieć się, jak skonfigurować te ustawienia w systemach, zobacz [Konfigurowanie ustawień Windows Update dla Azure Automation Update Management](automation-configure-windows-update.md) .

Przed rozpoczęciem korzystania z procedur opisanych w tym artykule upewnij się, że Update Management na maszynach wirtualnych została włączona przy użyciu jednej z następujących metod:

* [Włączanie rozwiązania Update Management z poziomu konta usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Włącz Update Management, przeglądając Azure Portal](automation-onboard-solutions-from-browse.md)
* [Włączanie rozwiązania Update Management z poziomu elementu runbook](automation-onboard-solutions.md)
* [Włączanie rozwiązania Update Management z poziomu maszyny wirtualnej platformy Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Ograniczanie zakresu wdrożenia

Update Management używa konfiguracji zakresu w obszarze roboczym, aby określić komputery, na których mają zostać odebrane aktualizacje. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Aby wyświetlić ocenę aktualizacji:

1. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**. 

2. Aktualizacje środowiska są wyświetlane na stronie Update Management. Jeśli jakieś aktualizacje zostały zidentyfikowane jako brakujące, na karcie **brakujące aktualizacje** zostanie wyświetlona lista brakujących aktualizacji.

3. W obszarze **link do informacji**wybierz link do aktualizacji, aby otworzyć artykuł pomocy technicznej, który zawiera ważne informacje o aktualizacji.

    ![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Kliknij w dowolnym miejscu w obszarze aktualizacji, aby otworzyć okienko przeszukiwanie dzienników. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje.

    ![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurowanie alertów

Wykonaj poniższe kroki, aby skonfigurować alerty w celu poinformowania o stanie wdrożenia aktualizacji:

1. Na koncie usługi Automation przejdź do pozycji **alerty** w obszarze **monitorowanie**, a następnie kliknij pozycję **Nowa reguła alertu**.

2. Na stronie Tworzenie reguły alertu konto usługi Automation zostało już wybrane jako zasób. Jeśli chcesz go zmienić, kliknij pozycję **Edytuj zasób**. 

3. Na stronie wybierz zasób wybierz pozycję **konta usługi Automation** z menu rozwijanego **Filtruj według typu zasobu** . 

4. Wybierz konto usługi Automation, którego chcesz użyć, a następnie kliknij przycisk **gotowe**.

5. Kliknij przycisk **Dodaj warunek** , aby wybrać sygnał, który jest odpowiedni dla wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegóły dwóch dostępnych sygnałów.

    |Nazwa sygnału|Wymiary|Opis
    |---|---|---|
    |`Total Update Deployment Runs`|— Nazwa wdrażania aktualizacji<br>-Status    |Alerty dotyczące ogólnego stanu wdrożenia aktualizacji.|
    |`Total Update Deployment Machine Runs`|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>-Aktualizacja identyfikatora uruchomienia wdrożenia    |Alerty dotyczące stanu wdrożenia aktualizacji, które są określone na określonych maszynach.|

6. Dla wymiaru Wybierz prawidłową wartość z listy. Jeśli potrzebna wartość nie znajduje się na liście, kliknij przycisk **\+** obok wymiaru i wpisz nazwę niestandardową. Następnie wybierz wartość do wyszukania. Jeśli chcesz wybrać wszystkie wartości wymiaru, kliknij przycisk **Wybierz \* ** . Jeśli nie wybierzesz wartości wymiaru, Update Management zignoruje ten wymiar.

    ![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/signal-logic.png)

7. W obszarze **logika alertu**wprowadź wartości w polach **agregacja czasu** i **próg** , a następnie kliknij przycisk **gotowe**.

8. W następnym okienku wprowadź nazwę i opis alertu.

9. Ustaw wartość pola **ważność** na **informacyjną (ważność 2)** dla pomyślnego uruchomienia lub **informacyjnego (ważność 1)** dla nieudanego uruchomienia.

    ![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/define-alert-details.png)

10. Kliknij przycisk **tak** lub **nie**, w zależności od tego, jak chcesz włączyć regułę alertu.

11. Jeśli nie chcesz mieć alertów dla tej reguły, wybierz pozycję **Pomijaj alerty**.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurowanie grup akcji dla alertów

Po skonfigurowaniu alertów można skonfigurować grupę akcji, która jest grupą akcji do użycia w ramach wielu alertów. Akcje mogą obejmować powiadomienia e-mail, elementy Runbook, webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

1. Wybierz Alert, a następnie wybierz pozycję **Utwórz nowy** w obszarze **grupy akcji**. 

2. Wprowadź pełną nazwę i krótką nazwę grupy akcji. Update Management używa krótkiej nazwy podczas wysyłania powiadomień przy użyciu określonej grupy.

3. W obszarze **Akcje**wprowadź nazwę określającą akcję, na przykład **powiadomienie e-mail**. 

4. W **polu Typ akcji**wybierz odpowiedni typ, na przykład **e-mail/SMS/wypychanie/głos**. 

5. Kliknij przycisk **Edytuj szczegóły**.

6. Wypełnij okienko dla danego typu akcji. Na przykład jeśli używasz **poczty e-mail/SMS/wypychania/głosu**, wprowadź nazwę akcji, zaznacz pole wyboru adres **e-mail** , wprowadź prawidłowy adres e-mail, a następnie kliknij przycisk **OK**.

    ![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. W okienku Dodaj grupę akcji kliknij przycisk **OK**.

8. W przypadku wiadomości e-mail z alertami można dostosować temat wiadomości e-mail. Wybierz pozycję **Dostosuj akcje** w obszarze **Utwórz regułę**, a następnie wybierz pozycję **temat wiadomości e-mail**. 

9. Po zakończeniu kliknij przycisk **Utwórz regułę alertu**. 

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Planowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerach docelowych. Aby zainstalować aktualizacje, należy zaplanować wdrożenie zgodne z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Jeśli usuniesz zasób harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, to usunięcie spowoduje przerwanie zaplanowanego wdrożenia aktualizacji i wyświetli błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji:

1. Na koncie usługi Automation przejdź do pozycji **Update Management** w obszarze **Update Management**, a następnie wybierz pozycję **Planowanie wdrożenia aktualizacji**.

2. W obszarze **nowe wdrożenie aktualizacji**Użyj pola **Nazwa** , aby wprowadzić unikatową nazwę wdrożenia.

3. Wybierz system operacyjny, który ma być przeznaczony do wdrożenia aktualizacji.

4. W regionie **grupy do zaktualizowania (wersja zapoznawcza)** Zdefiniuj zapytanie łączące subskrypcję, grupy zasobów, lokalizacje i Tagi, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. Aby dowiedzieć się więcej, zobacz [Używanie grup dynamicznych z Update Management](automation-update-management-groups.md).

5. W obszarze **maszyny do zaktualizowania** Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz **maszyny** z menu rozwijanego i wybierz opcję poszczególne maszyny. Po wybraniu tej opcji można zobaczyć gotowość Log Analytics agenta dla każdej maszyny. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach Azure Monitor, zobacz [grupy komputerów w dziennikach Azure monitor](../azure-monitor/platform/computer-groups.md).

6. Region **klasyfikacje aktualizacji** umożliwia określenie [klasyfikacji aktualizacji](automation-view-update-assessments.md#work-with-update-classifications) produktów. Dla każdego produktu odznacz wszystkie obsługiwane klasyfikacje aktualizacji, ale te, które mają zostać uwzględnione we wdrożeniu aktualizacji.

7. Za pomocą regionu **Dołącz/Wyklucz aktualizacje** wybierz określone aktualizacje do wdrożenia. Na stronie dołączania/wykluczania są wyświetlane aktualizacje według numeru IDENTYFIKACYJNego artykułu bazy wiedzy, które mają zostać dołączone lub wykluczone. 
    
   > [!IMPORTANT]
   > Należy pamiętać, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*` , Update Management wyklucza wszystkie poprawki lub pakiety z instalacji. Wykluczone poprawki nadal są wyświetlane jako brakujące z maszyn. W przypadku komputerów z systemem Linux, jeśli zostanie uwzględniony pakiet z wyłączonym pakietem zależnym, program Update Management nie zainstaluje głównego pakietu.

   > [!NOTE]
   > Nie można określić aktualizacji, które zostały zastąpione w celu uwzględnienia w wdrożeniu aktualizacji.

8. Wybierz pozycję **ustawienia harmonogramu**. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

9. Użyj pola **cykl** , aby określić, czy wdrożenie występuje raz, czy ma cykliczny harmonogram, a następnie kliknij przycisk **OK**.

10. W regionie **pre-scripts + post-Scripts (wersja zapoznawcza)** wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz temat Zarządzanie skryptami [wstępnymi i po skryptach](pre-post-scripts.md).
    
11. Za pomocą pola **okna obsługi (minuty)** określ ilość czasu dozwoloną dla aktualizacji do zainstalowania. Podczas określania okna obsługi należy wziąć pod uwagę następujące informacje:

    * Okna obsługi kontrolują liczbę zainstalowanych aktualizacji.
    * Update Management nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna obsługi.
    * W przypadku przekroczenia okna obsługi Update Management nie przerywa aktualizacji w toku.
    * W przypadku przekroczenia okna obsługi w systemie Windows często trwa Instalowanie aktualizacji dodatku Service Pack.

    > [!NOTE]
    > Aby uniknąć stosowania aktualizacji poza oknem obsługi w programie Ubuntu, należy ponownie skonfigurować pakiet, `Unattended-Upgrade` Aby wyłączyć aktualizacje automatyczne. Informacje o sposobie konfigurowania pakietu znajdują się [w temacie Aktualizacje automatyczne w podręczniku serwera Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Użyj pola **Opcje ponownego rozruchu** , aby określić sposób obsługi ponownych uruchomień podczas wdrażania. Dostępne są następujące opcje: 
    * Uruchom ponownie w razie potrzeby (ustawienie domyślne)
    * Zawsze uruchamiaj ponownie
    * Nigdy nie uruchamiaj ponownie
    * Tylko Uruchom ponownie; Ta opcja nie powoduje instalacji aktualizacji

    > [!NOTE]
    > Klucze rejestru wymienione w obszarze [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie zdarzenia w przypadku, gdy **opcja ponownego** uruchamiania jest ustawiona na **nigdy nie uruchamiaj**ponownie.

13. Po zakończeniu konfigurowania harmonogramu wdrożenia kliknij pozycję **Utwórz**.

    ![Okienko ustawień harmonogramu aktualizacji](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **zaplanowane wdrożenia aktualizacji** , aby wyświetlić utworzony harmonogram wdrożenia.

## <a name="schedule-an-update-deployment-programmatically"></a>Programowe Planowanie wdrożenia aktualizacji

Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). 

Aby utworzyć cotygodniowe wdrożenie aktualizacji, można użyć przykładowego elementu Runbook. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Sprawdź stan wdrożenia

Po rozpoczęciu zaplanowanego wdrożenia można zobaczyć jego stan na karcie **wdrożenia aktualizacji** w obszarze **Update Management**. Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie**. Jeśli w danym wdrożeniu wystąpią błędy, stan jest **częściowo niepowodzenie**.

## <a name="view-results-of-a-completed-update-deployment"></a>Wyświetl wyniki ukończonego wdrożenia aktualizacji

Po zakończeniu wdrażania możesz wybrać go, aby wyświetlić jego pulpit nawigacyjny.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/automation-tutorial-update-management/manageupdates-view-results.png)

W obszarze **Aktualizuj wyniki**podsumowanie zawiera łączną liczbę aktualizacji i wyników wdrożenia na docelowych maszynach wirtualnych. Tabela po prawej stronie zawiera szczegółowy podział aktualizacji oraz wyniki instalacji dla każdego z nich.

Dostępne są następujące wartości:

* **Nie podjęto próby — nie** zainstalowano aktualizacji z powodu niewystarczającego czasu dostępności na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Nie wybrano** — aktualizacja nie została wybrana do wdrożenia.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Wybierz pozycję **wszystkie dzienniki** , aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz pozycję **dane wyjściowe** , aby wyświetlić strumień zadań elementu Runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowych maszynach wirtualnych.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="view-the-deployment-alert"></a>Wyświetl alert dotyczący wdrażania

Po zakończeniu wdrażania aktualizacji zostanie wyświetlony alert, który został określony podczas instalacji wdrożenia. Na przykład poniżej przedstawiono wiadomość e-mail z potwierdzeniem poprawki.

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat konfiguracji zakresu, zobacz [ograniczanie Update Management zakresu wdrożenia](automation-scope-configurations-update-management.md).
* Jeśli musisz przeszukać dzienniki przechowywane w obszarze roboczym Log Analytics, zobacz [Wyszukiwanie w dzienniku w](../azure-monitor/log-query/log-query-overview.md)dziennikach Azure monitor.
* Jeśli zakończono wdrożenia, zobacz [Odłącz obszar roboczy z konta usługi Automation dla Update Management](automation-unlink-workspace-update-management.md).
* Aby usunąć maszyny wirtualne z Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz [Rozwiązywanie problemów z agentem aktualizacji systemu Linux](troubleshoot/update-agent-issues-linux.md).

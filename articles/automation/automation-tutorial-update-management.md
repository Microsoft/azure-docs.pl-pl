---
title: Zarządzaj aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure w Azure Automation
description: W tym artykule opisano, jak używać Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 4b47fa873df88bf85c4c56c9f2ac94fce16c63be
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743650"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure

W tym artykule opisano, jak można użyć funkcji Azure Automation [Update Management](automation-update-management.md) do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure. 

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/).

## <a name="prerequisites"></a>Wymagania wstępne

* Funkcja [Update Management](automation-update-management.md) włączona dla co najmniej jednej maszyny wirtualnej. 
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) z włączoną Update Management.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu Update Management zostanie otwarta strona zarządzanie aktualizacjami. Jeśli jakieś aktualizacje zostały zidentyfikowane jako brakujące, na karcie **brakujące aktualizacje** zostanie wyświetlona lista brakujących aktualizacji.

W obszarze **link do informacji**wybierz łącze Aktualizuj, aby otworzyć artykuł pomocy technicznej dla aktualizacji. Możesz poznać ważne informacje o aktualizacji.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknij w dowolnym miejscu w obszarze aktualizacji, aby otworzyć okienko przeszukiwania dzienników dla wybranej aktualizacji. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje o aktualizacjach wdrożonych lub braku w środowisku.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurowanie alertów

W tym kroku dowiesz się, jak skonfigurować alert powiadamiający o stanie wdrożenia aktualizacji.

### <a name="alert-conditions"></a>Warunki alertu

Na koncie usługi Automation przejdź do pozycji **alerty** w obszarze **monitorowanie**, a następnie kliknij pozycję **Nowa reguła alertu**.

Twoje konto usługi Automation zostało już wybrane jako zasób. Jeśli chcesz ją zmienić, kliknij pozycję **Wybierz**. Na stronie wybierz zasób wybierz pozycję **konta usługi Automation** z menu rozwijanego **Filtruj według typu zasobu** . Wybierz konto usługi Automation, a następnie kliknij przycisk **gotowe**.

Kliknij pozycję **Dodaj warunek**, aby wybrać odpowiedni sygnał dla wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegóły dwóch dostępnych sygnałów.

|Nazwa sygnału|Wymiary|Opis|
|---|---|---|
|`Total Update Deployment Runs`|— Nazwa wdrażania aktualizacji<br>— Stan|Ten sygnał alarmuje o ogólnym stanie wdrożenia aktualizacji.|
|`Total Update Deployment Machine Runs`|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>-Aktualizacja identyfikatora uruchomienia wdrożenia|Ten sygnał alarmuje o stanie wdrożenia aktualizacji, który jest przeznaczony dla określonych maszyn.|

Dla wymiaru Wybierz prawidłową wartość z listy. Jeśli potrzebna wartość nie znajduje się na liście, kliknij **\+** znak obok wymiaru i wpisz nazwę niestandardową. Następnie wybierz wartość do wyszukania. Jeśli chcesz wybrać wszystkie wartości wymiaru, kliknij przycisk **Wybierz \* ** . Jeśli nie wybierzesz wartości wymiaru, Update Management zignoruje ten wymiar.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/signal-logic.png)

W obszarze **Logika alertu** w polu **Próg** wprowadź **1**. Po zakończeniu wybierz pozycję **Gotowe**.

### <a name="alert-details"></a>Szczegóły alertu

W obszarze **2. Zdefiniuj szczegóły alertu**, wprowadź nazwę i opis alertu. Ustaw opcję **Ważność** na **Informacyjny (ważność 2)** w przypadku pomyślnego przebiegu lub **Informacyjny (ważność 1)** w przypadku nieudanego przebiegu.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/define-alert-details.png)

W obszarze **grupy akcji**wybierz pozycję **Utwórz nową**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Akcje mogą obejmować powiadomienia e-mail, elementy Runbook, webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

W polu **Nazwa grupy akcji** wprowadź nazwę alertu i krótką nazwę. W przypadku wysyłania powiadomień przy użyciu określonej grupy Update Management używa nazwy krótkiej zamiast pełnej nazwy grupy akcji.

W obszarze **Akcje**wprowadź nazwę akcji, na przykład **powiadomienie e-mail**. W obszarze **Typ akcji**wybierz pozycję **poczta e-mail/SMS/wypychanie/głos**. Aby uzyskać **szczegółowe informacje**, wybierz pozycję **Edytuj szczegóły**.

W okienku E-mail/SMS/Push/Głos wprowadź nazwę. Zaznacz pole wyboru **adres e-mail** , a następnie wprowadź prawidłowy adres e-mail.

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

W okienku wiadomości E-mail/SMS/wypychanie/głos kliknij przycisk **OK**. W okienku Dodaj grupę akcji kliknij przycisk **OK**.

Aby dostosować temat wiadomości e-mail dotyczącej alertu, w obszarze **Tworzenie reguły**w obszarze **Dostosuj akcje**wybierz pozycję **temat wiadomości e-mail**. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**. Alert informuje o pomyślnym zakończeniu wdrażania aktualizacji oraz o tym, które komputery były częścią wdrożenia aktualizacji.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Zaplanuj wdrożenie zgodnie z harmonogramem wydania i oknem usługi, aby zainstalować aktualizacje. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Planowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerach docelowych. Jeśli usuniesz zasób harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, to usunięcie spowoduje przerwanie zaplanowanego wdrożenia aktualizacji i wyświetli błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji dla maszyny wirtualnej, przejdź do rozwiązania **Update Management**, a następnie wybierz pozycję **Zaplanuj wdrażanie aktualizacji**.

W obszarze **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa**: wprowadź unikatową nazwę wdrożenia aktualizacji.

* **System operacyjny**: wybierz docelowy system operacyjny do wdrażania aktualizacji.

* **Grupy do zaktualizowania (wersja zapoznawcza)**: Zdefiniuj zapytanie łączące subskrypcję, grupy zasobów, lokalizacje i Tagi, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. Aby dowiedzieć się więcej, zobacz [grupy dynamiczne](automation-update-management-groups.md).

* **Maszyny do zaktualizowania**: Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz **maszyny** z menu rozwijanego i wybierz opcję poszczególne maszyny. W przypadku wybrania opcji **komputery**gotowość poszczególnych maszyn zostanie wyświetlona w kolumnie **Aktualizuj gotowość agenta** . Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach Azure Monitor, zobacz [grupy komputerów w dziennikach Azure monitor](../azure-monitor/platform/computer-groups.md).

* **Klasyfikacja aktualizacji**: dla każdego produktu odznacz wszystkie obsługiwane klasyfikacje aktualizacji, ale te, które mają zostać uwzględnione we wdrożeniu aktualizacji. Opisy typów klasyfikacji znajdują się w sekcji [klasyfikacje aktualizacji](automation-view-update-assessments.md#work-with-update-classifications).

* **Aktualizacje do dołączenia/wykluczania** — otwiera stronę dołączania/wykluczania. Aktualizacje, które mają zostać dołączone lub wykluczone, znajdują się na oddzielnych kartach, określając numery IDENTYFIKACYJNe artykułu w bazie wiedzy. W przypadku określenia co najmniej jednego numeru IDENTYFIKACYJNego należy usunąć lub wyczyścić wszystkie klasyfikacje ze wdrożeniem aktualizacji. Dzięki temu podczas określania identyfikatorów aktualizacji nie są uwzględniane żadne inne aktualizacje pakietu aktualizacji.

> [!NOTE]
> Ważne jest, aby wiedzieć, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*` , Update Management nie zainstaluje żadnych poprawek ani pakietów, ponieważ są one wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące na komputerze. W przypadku komputerów z systemem Linux, jeśli zostanie uwzględniony pakiet z wyłączonym pakietem zależnym, program Update Management nie zainstaluje głównego pakietu.

> [!NOTE]
> Nie można określić aktualizacji, które zostały zastąpione w celu włączenia ich do wdrożenia aktualizacji.
>
* **Ustawienia harmonogramu**: spowoduje otwarcie okienka Ustawienia harmonogramu. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym. W obszarze **Cykl** wybierz pozycję **Raz**. Pozostaw wartość domyślną na 1 dzień, a następnie kliknij przycisk **OK**. Te wpisy powodują skonfigurowanie harmonogramu cyklicznego.

* **Skrypty wstępne i końcowe**: wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).

* **Okno konserwacji (w minutach)**: pozostaw wartość domyślną. Okna obsługi kontrolują ilość czasu dozwoloną dla aktualizacji do zainstalowania. Podczas określania okna obsługi należy wziąć pod uwagę następujące informacje:

  * Okna obsługi kontrolują liczbę zainstalowanych aktualizacji.
  * Update Management nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna obsługi.
  * W przypadku przekroczenia okna obsługi Update Management nie przerywa aktualizacji w toku.
  * W przypadku przekroczenia okna obsługi w systemie Windows często trwa Instalowanie aktualizacji dodatku Service Pack.

  > [!NOTE]
  > Aby uniknąć stosowania aktualizacji poza oknem obsługi w programie Ubuntu, należy ponownie skonfigurować pakiet nienadzorowany do uaktualnienia, aby wyłączyć aktualizacje automatyczne. Informacje o sposobie konfigurowania pakietu programu znajdują się [w temacie Aktualizacje automatyczne w przewodniku po serwerze Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opcje ponownego uruchamiania**: Użyj, aby określić opcje obsługi ponownych uruchomień. Dostępne są następujące opcje:
  * Uruchom ponownie w razie potrzeby (ustawienie domyślne)
  * Zawsze uruchamiaj ponownie
  * Nigdy nie uruchamiaj ponownie
  * Tylko ponowny rozruch — nie instaluje aktualizacji

> [!NOTE]
> Klucze rejestru wymienione w obszarze [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie zdarzenia w przypadku, gdy **opcja ponownego** uruchamiania jest ustawiona na **nigdy nie uruchamiaj**ponownie.

Po zakończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**.

![Okienko ustawień harmonogramu aktualizacji](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **zaplanowane wdrożenia aktualizacji** , aby wyświetlić właśnie utworzony harmonogram wdrożenia.

> [!NOTE]
> Update Management obsługuje wdrażanie aktualizacji pierwszej firmy i wstępne pobieranie poprawek. Ta obsługa wymaga zmian w systemach z poprawkami. Aby dowiedzieć się, jak skonfigurować te ustawienia w systemach, zobacz temat [Obsługa pierwszej strony i wstępnego pobierania](automation-configure-windows-update.md) .

Można również programowo tworzyć wdrożenia aktualizacji. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element Runbook, za pomocą którego można utworzyć cotygodniowe wdrożenie aktualizacji. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**. Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie**. Jeśli w danym wdrożeniu wystąpią błędy, stan jest **częściowo niepowodzenie**.

Wybierz ukończone wdrożenie aktualizacji, aby wyświetlić jego pulpit nawigacyjny.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Obszar **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej. Tabela po prawej stronie pokazuje szczegółowy podział każdej aktualizacji i wyniki instalacji.

Dostępne są następujące wartości:

* **Nie podjęto próby**: aktualizacja nie została zainstalowana z powodu niewystarczającego czasu dostępności na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Powodzenie**: aktualizacja powiodła się.
* **Niepowodzenie**: aktualizacja nie powiodła się.

Wybierz pozycję **wszystkie dzienniki** , aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

Po pomyślnym wdrożeniu aktualizacji zostanie wyświetlony komunikat potwierdzający, podobny do następującego:

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie rozwiązania Update Management](automation-update-management.md)
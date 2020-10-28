---
title: Jak utworzyć wdrożenia aktualizacji dla Azure Automation Update Management
description: W tym artykule opisano sposób planowania wdrożeń aktualizacji i przeglądania ich stanu.
services: automation
ms.subservice: update-management
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: 41ccecfb844f11a0d234271bcddc1851d3c02fda
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742286"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Jak wdrażać aktualizacje i przeglądać wyniki

W tym artykule opisano sposób planowania wdrożenia aktualizacji i przeglądania procesu po zakończeniu wdrażania. Wdrożenie aktualizacji można skonfigurować z wybranej maszyny wirtualnej platformy Azure, z wybranego serwera z obsługą łuku lub z konta usługi Automation we wszystkich skonfigurowanych maszynach i serwerach.

W każdym scenariuszu tworzone jest wdrożenie, które zostało wybrane dla wybranego komputera lub serwera, lub w przypadku tworzenia wdrożenia z konta usługi Automation można wybrać co najmniej jedną maszynę. Po zaplanowaniu wdrożenia aktualizacji z poziomu maszyny wirtualnej platformy Azure lub serwera z włączonym łukiem, kroki są takie same jak w przypadku wdrażania z konta usługi Automation z następującymi wyjątkami:

* System operacyjny jest automatycznie wybierany w oparciu o system operacyjny komputera
* Maszyna docelowa do zaktualizowania jest ustawiona na wartość docelową automatycznie
* Podczas konfigurowania harmonogramu można określić **aktualizację teraz** , występuje raz lub korzysta z harmonogramu cyklicznego.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Planowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](../shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerze docelowym lub na maszynach docelowych. Aby zainstalować aktualizacje, należy zaplanować wdrożenie zgodne z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Można na przykład uwzględnić aktualizacje krytyczne lub zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Jeśli usuniesz zasób harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, to usunięcie spowoduje przerwanie zaplanowanego wdrożenia aktualizacji i wyświetli błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji, wykonaj następujące czynności. W zależności od wybranego zasobu (czyli konta usługi Automation, serwera z obsługą łuku, maszyny wirtualnej platformy Azure) poniższe kroki dotyczą wszystkich z drobnymi różnicami podczas konfigurowania harmonogramu wdrażania.

1. W portalu w celu zaplanowania wdrożenia programu:

   * Co najmniej jedna maszyna przejdź do **konta usługi Automation** i wybierz konto usługi Automation z włączonym Update Management z listy.
   * W przypadku maszyny wirtualnej platformy Azure przejdź do opcji **maszyny wirtualne** i wybierz maszynę wirtualną z listy.
   * W przypadku serwera z włączonym Łukem przejdź do opcji **serwery — Azure Arc** i wybierz serwer z listy.

2. W zależności od wybranego zasobu, aby przejść do Update Management:

   * W przypadku wybrania konta usługi Automation przejdź do pozycji **Update Management** w obszarze **Update Management** , a następnie wybierz pozycję **Planowanie wdrożenia aktualizacji** .
   * W przypadku wybrania maszyny wirtualnej platformy Azure przejdź do pozycji **gość + aktualizacje hosta** , a następnie wybierz pozycję **Przejdź do Update Management** .
   * W przypadku wybrania serwera z włączonym Łukem przejdź do **Update Management** , a następnie wybierz pozycję **Planowanie wdrożenia aktualizacji** .

3. W obszarze **nowe wdrożenie aktualizacji** w polu **Nazwa** wprowadź unikatową nazwę wdrożenia.

4. Wybierz system operacyjny, który ma być przeznaczony do wdrożenia aktualizacji.

    > [!NOTE]
    > Ta opcja jest niedostępna w przypadku wybrania maszyny wirtualnej lub serwera z włączonym Łukem platformy Azure. System operacyjny jest identyfikowany automatycznie.

5. W obszarze **grupy do zaktualizowania** Zdefiniuj zapytanie łączące subskrypcję, grupy zasobów, lokalizacje i Tagi, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure do uwzględnienia w danym wdrożeniu. Aby dowiedzieć się więcej, zobacz [Używanie grup dynamicznych z Update Management](configure-groups.md).

    > [!NOTE]
    > Ta opcja jest niedostępna w przypadku wybrania maszyny wirtualnej lub serwera z włączonym Łukem platformy Azure. Komputer jest automatycznie kierowany do zaplanowanego wdrożenia.

6. W obszarze **maszyny do zaktualizowania** Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz **maszyny** z menu rozwijanego i wybierz opcję poszczególne maszyny. Po wybraniu tej opcji można zobaczyć gotowość Log Analytics agenta dla każdej maszyny. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach Azure Monitor, zobacz [grupy komputerów w dziennikach Azure monitor](../../azure-monitor/platform/computer-groups.md). W zaplanowanym wdrożeniu aktualizacji można uwzględnić maksymalnie 1000 maszyn.

    > [!NOTE]
    > Ta opcja jest niedostępna w przypadku wybrania maszyny wirtualnej lub serwera z włączonym Łukem platformy Azure. Komputer jest automatycznie kierowany do zaplanowanego wdrożenia.

7. Region **klasyfikacje aktualizacji** umożliwia określenie [klasyfikacji aktualizacji](view-update-assessments.md#work-with-update-classifications) produktów. Dla każdego produktu odznacz wszystkie obsługiwane klasyfikacje aktualizacji, ale te, które mają zostać uwzględnione we wdrożeniu aktualizacji.

    Jeśli wdrożenie ma zastosowanie tylko do wybranego zestawu aktualizacji, należy usunąć zaznaczenie wszystkich wstępnie wybranych klasyfikacji aktualizacji podczas konfigurowania opcji **Dołącz/Wyklucz aktualizacje** zgodnie z opisem w następnym kroku. Gwarantuje to, że tylko aktualizacje określone do *uwzględnienia* w tym wdrożeniu zostaną zainstalowane na komputerach docelowych.

8. Za pomocą regionu **Dołącz/Wyklucz aktualizacje** można dodawać lub wykluczać wybrane aktualizacje z wdrożenia. Na stronie **dołączanie/wykluczanie** wprowadzasz numery identyfikacyjne artykułu w bazie wiedzy, które mają zostać dołączone lub wykluczone dla aktualizacji systemu Windows. W przypadku obsługiwanego dystrybucje systemu Linux należy określić nazwę pakietu.

   > [!IMPORTANT]
   > Należy pamiętać, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*` , Update Management wyklucza wszystkie poprawki lub pakiety z instalacji. Wykluczone poprawki nadal są wyświetlane jako brakujące z maszyn. W przypadku komputerów z systemem Linux, jeśli zostanie uwzględniony pakiet z wyłączonym pakietem zależnym, program Update Management nie zainstaluje głównego pakietu.

   > [!NOTE]
   > Nie można określić aktualizacji, które zostały zastąpione w celu uwzględnienia w wdrożeniu aktualizacji.

9. Wybierz pozycję **ustawienia harmonogramu** . Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

    > [!NOTE]
    > Ta opcja jest inna, jeśli wybrano serwer z włączonym łukiem. W przyszłości możesz wybrać opcję **Aktualizuj teraz** lub godzinę rozpoczęcia 20 minut.

10. Użyj **cyklu** , aby określić, czy wdrożenie występuje raz, czy za pomocą harmonogramu cyklicznego, a następnie wybierz **przycisk OK** .

11. W regionie **pre-scripts + po skrypcie** wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz temat Zarządzanie skryptami [wstępnymi i po skryptach](pre-post-scripts.md).

12. Za pomocą pola **okna obsługi (minuty)** określ ilość czasu dozwoloną dla aktualizacji do zainstalowania. Podczas określania okna obsługi należy wziąć pod uwagę następujące informacje:

    * Okna obsługi kontrolują liczbę zainstalowanych aktualizacji.
    * Update Management nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna obsługi.
    * W przypadku przekroczenia okna obsługi Update Management nie przerywa aktualizacji w toku. Wszystkie pozostałe aktualizacje do zainstalowania nie są podejmowane. W takim przypadku należy przeprowadzić ponowną ocenę czasu trwania okna obsługi.
    * W przypadku przekroczenia okna obsługi w systemie Windows często trwa Instalowanie aktualizacji dodatku Service Pack.

    > [!NOTE]
    > Aby uniknąć stosowania aktualizacji poza oknem obsługi w programie Ubuntu, należy ponownie skonfigurować pakiet, `Unattended-Upgrade` Aby wyłączyć aktualizacje automatyczne. Informacje o sposobie konfigurowania pakietu znajdują się [w temacie Aktualizacje automatyczne w podręczniku serwera Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Użyj pola **Opcje ponownego rozruchu** , aby określić sposób obsługi ponownych uruchomień podczas wdrażania. Dostępne są następujące opcje: 
    * Uruchom ponownie w razie potrzeby (ustawienie domyślne)
    * Zawsze uruchamiaj ponownie
    * Nigdy nie uruchamiaj ponownie
    * Tylko Uruchom ponownie; Ta opcja nie powoduje instalacji aktualizacji

    > [!NOTE]
    > Klucze rejestru wymienione w obszarze [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie zdarzenia w przypadku, gdy **opcja ponownego** uruchamiania jest ustawiona na **nigdy nie uruchamiaj** ponownie.

14. Po zakończeniu konfigurowania harmonogramu wdrażania wybierz pozycję **Utwórz** .

    ![Okienko ustawień harmonogramu aktualizacji](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Po zakończeniu konfigurowania harmonogramu wdrażania dla wybranego serwera z włączonym Łukem wybierz pozycję **Przegląd + Utwórz** .

15. Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **harmonogramy wdrażania** , aby wyświetlić utworzony harmonogram wdrożenia. Wyświetlane są maksymalnie 500 harmonogramów. Jeśli masz więcej niż 500 harmonogramów i chcesz zapoznać się z pełną listą, zobacz metodę API REST [konfiguracje aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurations/list) . Określ interfejs API w wersji 2019-06-01 lub nowszej.

## <a name="schedule-an-update-deployment-programmatically"></a>Programowe Planowanie wdrożenia aktualizacji

Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create).

Aby utworzyć cotygodniowe wdrożenie aktualizacji, można użyć przykładowego elementu Runbook. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Sprawdź stan wdrożenia

Po rozpoczęciu zaplanowanego wdrożenia można zobaczyć jego stan na karcie **historia** w obszarze **Update Management** . Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie** . Jeśli w danym wdrożeniu wystąpią błędy, stan **nie zostanie zakończony niepowodzeniem** .

## <a name="view-results-of-a-completed-update-deployment"></a>Wyświetl wyniki ukończonego wdrożenia aktualizacji

Po zakończeniu wdrażania możesz wybrać je, aby wyświetlić jego wyniki.

[![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

W obszarze **Aktualizuj wyniki** podsumowanie zawiera łączną liczbę aktualizacji i wyników wdrożenia na docelowych maszynach wirtualnych. Tabela po prawej stronie zawiera szczegółowy podział aktualizacji oraz wyniki instalacji dla każdego z nich.

Dostępne są następujące wartości:

* **Nie podjęto próby — nie** zainstalowano aktualizacji z powodu niewystarczającego czasu dostępności na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Nie wybrano** — aktualizacja nie została wybrana do wdrożenia.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Wybierz pozycję **wszystkie dzienniki** , aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz pozycję **dane wyjściowe** , aby wyświetlić strumień zadań elementu Runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowych maszynach wirtualnych.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak utworzyć alerty w celu powiadomienia o wynikach wdrożenia aktualizacji, zobacz [tworzenie alertów dla Update Management](configure-alerts.md).
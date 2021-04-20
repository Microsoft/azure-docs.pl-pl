---
title: Jak tworzyć wdrożenia aktualizacji dla Azure Automation Update Management
description: W tym artykule opisano sposób planowania wdrożeń aktualizacji i przeglądania ich stanu.
services: automation
ms.subservice: update-management
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e410e5de529bde122fe42d21b593a6fc483dcbc0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726693"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Jak wdrażać aktualizacje i przeglądać wyniki

W tym artykule opisano sposób planowania wdrożenia aktualizacji i przeglądania procesu po zakończeniu wdrażania. Wdrożenie aktualizacji można skonfigurować z wybranej maszyny wirtualnej platformy Azure, z wybranego serwera z usługą Arc lub z konta usługi Automation na wszystkich skonfigurowanych maszynach i serwerach.

W każdym scenariuszu wdrożenie tworzy elementy docelowe dla wybranej maszyny lub serwera, a w przypadku tworzenia wdrożenia z konta usługi Automation można wybrać co najmniej jedną maszynę docelową. Podczas planowania wdrożenia aktualizacji z maszyny wirtualnej platformy Azure lub serwera z usługą Arc kroki są takie same jak w przypadku wdrażania z konta usługi Automation, z następującymi wyjątkami:

* System operacyjny jest automatycznie wstępnie wybierany na podstawie systemu operacyjnego maszyny
* Maszyna docelowa do zaktualizowania jest automatycznie ustawiana jako docelowa
* Podczas konfigurowania harmonogramu można określić zaktualizuj **teraz**, występuje raz lub używa harmonogramu cyklicznego.

> [!IMPORTANT]
> Tworząc wdrożenie aktualizacji, akceptujesz warunki postanowień licencyjnych oprogramowania (EULA) dostarczonych przez firmę oferujących aktualizacje systemu operacyjnego.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Planowanie wdrożenia aktualizacji powoduje [](../shared-resources/schedules.md) utworzenie zasobu harmonogramu połączonego z elementem runbook **Patch-MicrosoftOMSComputers,** który obsługuje wdrożenie aktualizacji na maszynie docelowej lub na maszynach docelowych. Aby zainstalować aktualizacje, należy zaplanować wdrożenie zgodnie z harmonogramem wydania i oknem obsługi. Możesz wybrać typy aktualizacji do dołączyć do wdrożenia. Na przykład można uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć zbiorczych aktualizacji.

>[!NOTE]
>Jeśli usuniesz zasób harmonogramu z witryny Azure Portal lub za pomocą programu PowerShell po utworzeniu wdrożenia, usunięcie spowoduje przerwę w zaplanowanym wdrożeniu aktualizacji i spowoduje błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji, wykonaj następujące kroki. W zależności od wybranego zasobu (czyli konta usługi Automation, serwera z obsługą usługi Arc, maszyny wirtualnej platformy Azure) poniższe kroki mają zastosowanie do wszystkich z niewielkimi różnicami podczas konfigurowania harmonogramu wdrażania.

1. Aby zaplanować wdrożenie dla:

   * Co najmniej jedna maszyna przejdź do kont **usługi Automation** i wybierz z listy Update Management usługi Automation z włączoną obsługą.
   * W przypadku maszyny wirtualnej platformy Azure przejdź do **opcji Maszyny wirtualne** i wybierz maszynę wirtualną z listy.
   * W przypadku serwera z obsługą usługi Arc przejdź **do** Azure Arc i wybierz serwer z listy.

2. W zależności od wybranego zasobu przejdź do Update Management:

   * Jeśli wybrano konto usługi Automation, przejdź do rozwiązania **Update Management** w obszarze **Update Management,** a następnie wybierz pozycję **Zaplanuj wdrożenie aktualizacji.**
   * Jeśli wybrano maszynę wirtualną platformy Azure, przejdź do opcji Aktualizacje gościa i **hosta,** a następnie wybierz pozycję **Przejdź do Update Management.**
   * Jeśli wybrano serwer z obsługą usługi Arc, przejdź do Update Management , **a** następnie wybierz pozycję **Zaplanuj wdrożenie aktualizacji.**

3. W **obszarze Nowe wdrożenie aktualizacji** w polu **Nazwa** wprowadź unikatową nazwę wdrożenia.

4. Wybierz docelowy system operacyjny dla wdrożenia aktualizacji.

    > [!NOTE]
    > Ta opcja nie jest dostępna, jeśli wybrano maszynę wirtualną platformy Azure lub serwer z obsługą usługi Arc. System operacyjny jest identyfikowany automatycznie.

5. W obszarze **Grupy** do zaktualizowania regionu zdefiniuj zapytanie, które łączy subskrypcję, grupy zasobów, lokalizacje i tagi w celu skompilowania dynamicznej grupy maszyn wirtualnych platformy Azure, które mają być dołączane do wdrożenia. Aby dowiedzieć się więcej, zobacz [Używanie grup dynamicznych z Update Management](configure-groups.md).

    > [!NOTE]
    > Ta opcja nie jest dostępna, jeśli wybrano maszynę wirtualną platformy Azure lub serwer z obsługą usługi Arc. Maszyna jest automatycznie kierowana do zaplanowanego wdrożenia.

6. W obszarze **Maszyny do zaktualizowania** regionu wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz pozycję Maszyny z menu rozwijanego i wybierz poszczególne maszyny.  Dzięki tej opcji można sprawdzić gotowość agenta usługi Log Analytics dla każdej maszyny. Aby dowiedzieć się więcej o różnych metodach tworzenia grup komputerów w dziennikach Azure Monitor, zobacz Grupy komputerów w [Azure Monitor dziennikach.](../../azure-monitor/logs/computer-groups.md) W zaplanowanym wdrożeniu aktualizacji można uwzględnić maksymalnie 1000 maszyn.

    > [!NOTE]
    > Ta opcja nie jest dostępna, jeśli wybrano maszynę wirtualną platformy Azure lub serwer z obsługą usługi Arc. Maszyna jest automatycznie kierowana do zaplanowanego wdrożenia.

7. Użyj **regionu Klasyfikacje aktualizacji,** aby [określić klasyfikacje aktualizacji](view-update-assessments.md#work-with-update-classifications) dla produktów. Dla każdego produktu usuń zaznaczenie wszystkich obsługiwanych klasyfikacji aktualizacji, ale tych, które mają być dołączane do wdrożenia aktualizacji.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Przykład przedstawiający wybór określonych klasyfikacji aktualizacji.":::

    Jeśli wdrożenie ma dotyczyć tylko wybranego zestawu aktualizacji, należy usunąć zaznaczenie wszystkich wstępnie wybranych klasyfikacji aktualizacji podczas konfigurowania opcji **Dołączanie/wykluczanie** aktualizacji zgodnie z opisem w następnym kroku. Dzięki temu na maszynach docelowych zostaną zainstalowane tylko aktualizacje określone do dołączyć do tego wdrożenia. 

   >[!NOTE]
   > Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w przypadku wersji RTM systemu CentOS. Aby prawidłowo wdrożyć aktualizacje systemu CentOS, wybierz wszystkie klasyfikacje, aby upewnić się, że aktualizacje zostały zastosowane. Obecnie nie jest obsługiwana metoda włączania natywnej dostępności danych klasyfikacji w systemie CentOS. Zobacz następujące informacje, aby uzyskać więcej informacji na temat [klasyfikacji aktualizacji.](overview.md#update-classifications)

8. Użyj **regionu Dołączanie/wykluczanie aktualizacji,** aby dodać lub wykluczyć wybrane aktualizacje z wdrożenia. Na stronie **Dołączanie/wykluczanie** należy wprowadzić numery identyfikatorów artykułów bazy wiedzy, które mają być dołączane lub wykluczane w przypadku aktualizacji systemu Windows. W przypadku obsługiwanych dystrybucji systemu Linux należy określić nazwę pakietu.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Przykład przedstawiający sposób dołączania określonych aktualizacji.":::

   > [!IMPORTANT]
   > Pamiętaj, że wykluczenia zastępują dołączenia. Jeśli na przykład zdefiniowano regułę wykluczania klasy , program Update Management wyklucza wszystkie poprawki `*` lub pakiety z instalacji. Wykluczone poprawki są nadal wyświetlane jako brakujące na maszynach. W przypadku maszyn z systemem Linux, jeśli dołączysz pakiet, który ma pakiet zależny, który został wykluczony, program Update Management nie zainstaluje pakietu głównego.

   > [!NOTE]
   > Nie można określić aktualizacji, które zostały wsadowe do dołączyć do wdrożenia aktualizacji.

   Oto kilka przykładowych scenariuszy, które ułatwiają zrozumienie sposobu jednoczesnego używania dołączania/wykluczania i klasyfikacji aktualizacji we wdrożeniach aktualizacji:

   * Jeśli chcesz zainstalować tylko określoną listę aktualizacji, nie  należy wybierać żadnych klasyfikacji aktualizacji i podać listę aktualizacji do zastosowania przy użyciu **opcji Uwzględnij.**

   * Jeśli chcesz zainstalować tylko aktualizacje zabezpieczeń i aktualizacje krytyczne wraz z co  najmniej jedną opcjonalną aktualizacją sterownika, wybierz zabezpieczenia i krytyczne w **obszarze** **Klasyfikacje aktualizacji.** Następnie dla **opcji Uwzględnij** określ aktualizacje sterowników.

   * Jeśli chcesz zainstalować tylko aktualizacje zabezpieczeń i aktualizacje krytyczne, ale pominąć co najmniej jedną  aktualizację  dla języka Python, aby uniknąć przerywania pracy starszej aplikacji, wybierz pozycję Zabezpieczenia i Krytyczne w obszarze **Klasyfikacje aktualizacji.** Następnie dla opcji **Wykluczanie** dodaj pakiety języka Python do pominięcia.

9. Wybierz **pozycję Ustawienia harmonogramu.** Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

    > [!NOTE]
    > Ta opcja różni się w przypadku wyboru serwera z włączoną usługą Arc. Możesz wybrać pozycję **Aktualizuj teraz** lub czas rozpoczęcia 20 minut w przyszłości.

10. Użyj opcji **Cykl,** aby określić, czy wdrożenie ma miejsce raz, czy korzysta z harmonogramu cyklicznego, a następnie wybierz przycisk **OK.**

11. W **regionie Pre-scripts + Post-scripts** (Skrypty wstępne i post-scripts) wybierz skrypty do uruchomienia przed wdrożeniem i po nim. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i skryptami po.](pre-post-scripts.md)

12. Użyj pola **Okno obsługi (minuty),** aby określić czas dozwolony na instalowanie aktualizacji. Podczas określania okna obsługi należy wziąć pod uwagę następujące szczegóły:

    * Okna obsługi kontrolują, ile aktualizacji jest zainstalowanych.
    * Update Management nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna obsługi.
    * Update Management nie przerywa aktualizacji w toku w przypadku przekroczenia okna obsługi. Nie jest podejmowana próba zainstalowania wszystkich pozostałych aktualizacji. Jeśli ten sposób jest stale powtarzany, należy ponownie sprawdzić czas trwania okna obsługi.
    * Przekroczenie okna obsługi w systemie Windows często jest spowodowane długim czasem instalacji aktualizacji dodatku Service Pack.

    > [!NOTE]
    > Aby uniknąć stosowania aktualizacji poza oknem obsługi w systemie Ubuntu, skonfiguruj ponownie `Unattended-Upgrade` pakiet, aby wyłączyć aktualizacje automatyczne. Aby uzyskać informacje na temat sposobu konfigurowania pakietu, zobacz temat [Aktualizacje automatyczne w Przewodniku po systemie Ubuntu Server.](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

13. Użyj pola **Opcje ponownego uruchamiania,** aby określić sposób obsługi ponownych uruchomień podczas wdrażania. Dostępne są następujące opcje: 
    * W razie potrzeby uruchom ponownie (ustawienie domyślne)
    * Zawsze uruchamiaj ponownie
    * Nigdy nie uruchamiaj ponownie
    * Tylko ponowne uruchomienie; Ta opcja nie powoduje zainstalowania aktualizacji

    > [!NOTE]
    > Klucze rejestru wymienione w obszarze [Klucze](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) rejestru używane do zarządzania  ponownym uruchomieniem mogą spowodować zdarzenie ponownego uruchomienia, jeśli dla opcji ponownego uruchamiania ustawiono wartość **Nigdy nie uruchamiaj ponownie.**

14. Po zakończeniu konfigurowania harmonogramu wdrażania wybierz pozycję **Utwórz**.

    ![Okienko ustawień harmonogramu aktualizacji](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Po zakończeniu konfigurowania harmonogramu wdrażania dla wybranego serwera z usługą Arc wybierz pozycję **Przejrzyj i utwórz.**

15. Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz **pozycję Harmonogramy** wdrażania, aby wyświetlić utworzony harmonogram wdrażania. Na liście znajduje się maksymalnie 500 harmonogramów. Jeśli masz więcej niż 500 harmonogramów i chcesz przejrzeć pełną listę, zobacz Software [Update Configurations - List](/rest/api/automation/softwareupdateconfigurations/list) REST API method (Konfiguracje aktualizacji oprogramowania — wyświetl listę metod interfejsu API REST). Określ wersję interfejsu API 2019-06-01 lub nowszą.

## <a name="schedule-an-update-deployment-programmatically"></a>Programowe planowanie wdrożenia aktualizacji

Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create).

Przykładowego podręcznika Runbook można użyć do utworzenia cotygodniowego wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego runbook, zobacz Tworzenie cotygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej [w grupie zasobów.](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group)

## <a name="check-deployment-status"></a>Sprawdzanie stanu wdrożenia

Po wdrożeniu zaplanowanym można wyświetlić jego stan na karcie **Historia** w obszarze **Update Management.** Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrażania stan zmieni się na **Powodzenie.** Jeśli w wdrożeniu występują błędy z co najmniej jedną aktualizacją, stan to **Niepowodzenie.**

## <a name="view-results-of-a-completed-update-deployment"></a>Wyświetlanie wyników ukończonego wdrożenia aktualizacji

Po zakończeniu wdrażania możesz wybrać je, aby wyświetlić jego wyniki.

[![Aktualizowanie pulpitu nawigacyjnego stanu wdrożenia dla określonego wdrożenia](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

W **obszarze Wyniki** aktualizacji podsumowanie zawiera łączną liczbę aktualizacji i wyników wdrożenia na docelowych maszyn wirtualnych. Tabela po prawej stronie zawiera szczegółowy podział aktualizacji i wyniki instalacji dla każdej z nich.

Dostępne wartości to:

* **Nie podjęto próby** — aktualizacja nie została zainstalowana, ponieważ nie ma wystarczającego czasu na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Nie wybrano** — aktualizacja nie została wybrana do wdrożenia.
* **Powodzenie —** aktualizacja zakończyła się pomyślnie.
* **Niepowodzenie —** aktualizacja nie powiodła się.

Wybierz **pozycję Wszystkie dzienniki,** aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz **pozycję Dane** wyjściowe, aby wyświetlić strumień zadań element runbook odpowiedzialny za zarządzanie wdrożeniem aktualizacji na docelowych maszyn wirtualnych.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć alerty w celu powiadamiania o wynikach wdrażania aktualizacji, zobacz [Tworzenie alertów](configure-alerts.md)dla Update Management .
* Aby rozwiązać ogólne problemy Update Management, zobacz [Rozwiązywanie Update Management problemów.](../troubleshoot/update-management.md)

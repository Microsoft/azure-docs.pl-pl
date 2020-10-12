---
title: Włącz Start/Stop VMs during off-hours Azure Automation
description: W tym artykule opisano, jak włączyć funkcję Start/Stop VMs during off-hours dla maszyn wirtualnych platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83743759"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Włączanie rozwiązania Start/Stop VMs during off-hours

Wykonaj kroki opisane w tym temacie w sekwencji, aby włączyć funkcję Start/Stop VMs during off-hours dla maszyn wirtualnych przy użyciu nowego lub istniejącego konta usługi Automation i połączonego Log Analytics obszaru roboczego. Po zakończeniu procesu instalacji skonfiguruj zmienne, aby dostosować funkcję.

>[!NOTE]
>Aby korzystać z tej funkcji z klasycznymi maszynami wirtualnymi, musisz mieć klasyczne konto Uruchom jako, które nie jest domyślnie tworzone. Zobacz [Tworzenie klasycznego konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Tworzenie zasobów dla funkcji

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com).
2. Wyszukaj i wybierz pozycję **konta usługi Automation**.
3. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.
4. Na koncie usługi Automation wybierz pozycję **Uruchom/Zatrzymaj maszynę wirtualną** w obszarze **powiązane zasoby**. W tym miejscu możesz kliknąć **więcej informacji na temat i włączyć rozwiązanie**. Jeśli masz już wdrożoną funkcję, możesz kliknąć przycisk **Zarządzaj rozwiązaniem** i znaleźć go na liście.

   ![Włącz z konta usługi Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Możesz również utworzyć zasób z dowolnego miejsca w Azure Portal, klikając pozycję **Utwórz zasób**. Na stronie Marketplace wpisz słowo kluczowe, takie jak **Start** lub **Start/Stop**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie możesz wpisać jedno lub więcej słów kluczowych z pełnej nazwy funkcji, a następnie nacisnąć klawisz **Enter**. Wybierz **Start/Stop VMS during off-hours** z wyników wyszukiwania.

5. Na stronie Start/Stop VMs during off-hours wybranego wdrożenia Przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Skonfiguruj funkcję

Po utworzeniu zasobu zostanie wyświetlona strona Dodawanie rozwiązania. Zostanie wyświetlony monit o skonfigurowanie funkcji przed zaimportowaniem jej do subskrypcji usługi Automation. Zobacz [konfigurowanie Start/Stop VMS during off-hours](automation-solution-vm-management-config.md).

   ![Strona dodawania rozwiązania do zarządzania maszyną wirtualną](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Wybierz obszar roboczy Log Analytics

1. Na stronie Dodawanie rozwiązania wybierz pozycję **obszar roboczy**. Wybierz obszar roboczy Log Analytics, który jest połączony z subskrypcją platformy Azure używaną przez konto usługi Automation. 

2. Jeśli nie masz obszaru roboczego, wybierz pozycję **Utwórz nowy obszar roboczy**. Na stronie obszar roboczy Log Analytics wykonaj następujące czynności:

   - Określ nazwę nowego obszaru roboczego Log Analytics, na przykład **ContosoLAWorkspace**.
   - Wybierz **subskrypcję** , z którą chcesz utworzyć łącze, wybierając z listy rozwijanej opcję, jeśli wybrana wartość domyślna nie jest odpowiednia.
   - W obszarze **Grupa zasobów**można utworzyć nową grupę zasobów lub wybrać istniejącą.
   - Wybierz **lokalizację**.
   - Wybierz **warstwę cenową**. Wybierz opcję **na GB (autonomiczne)** . W przypadku dzienników Azure Monitor Zaktualizowano [Cennik](https://azure.microsoft.com/pricing/details/log-analytics/) i jedyną opcją jest warstwa za GB.

   > [!NOTE]
   > Podczas włączania funkcji tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

3. Po podania wymaganych informacji na stronie obszaru roboczego Log Analytics kliknij przycisk **Utwórz**. Postęp można śledzić w obszarze **powiadomienia** z menu, co powoduje powrót do strony Dodawanie rozwiązania po zakończeniu.

## <a name="add-automation-account"></a>Dodawanie konta usługi Automation

Uzyskaj ponownie dostęp do strony Dodaj rozwiązanie i wybierz pozycję **konto usługi Automation**. Można wybrać istniejące konto usługi Automation, które nie zostało jeszcze połączone z obszarem roboczym Log Analytics. Jeśli tworzysz nowy obszar roboczy Log Analytics, możesz utworzyć nowe konto usługi Automation, które będzie z nim skojarzone. Wybierz istniejące konto usługi Automation lub kliknij pozycję **Utwórz konto usługi Automation**, a następnie na stronie Dodawanie konta usługi Automation Podaj nazwę konta usługi Automation w polu **Nazwa** .

Wszystkie inne opcje są wypełniane automatycznie zgodnie z wybranym obszarem roboczym Log Analytics. Tych opcji nie można modyfikować. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook dostępnych w ramach tej funkcji. 

Po kliknięciu przycisku **OK**opcje konfiguracji są weryfikowane i tworzone jest konto usługi Automation. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="define-feature-parameters"></a>Definiowanie parametrów funkcji

1. Na stronie Dodawanie rozwiązania wybierz pozycję **Konfiguracja**. Zostanie wyświetlona strona parametry.

    ![Strona parametrów rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Określ wartość dla docelowego pola **nazw zasobów** . Pole definiuje nazwy grup zawierające maszyny wirtualne, które mają być zarządzane przez funkcję. Można wprowadzić więcej niż jedną nazwę i oddzielić nazwy przy użyciu przecinków (wartości nie są rozróżniane wielkości liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Wartości są przechowywane w `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` zmiennych i.

    > [!IMPORTANT]
    > Wartość domyślna dla **docelowych nazw zasobów** jest **&ast;** . To ustawienie dotyczy wszystkich maszyn wirtualnych w ramach subskrypcji. Jeśli nie chcesz, aby funkcja mogła wskazywać wszystkie maszyny wirtualne w subskrypcji, musisz podać listę nazw grup zasobów przed wybraniem harmonogramu.
  
3. Określ wartość pola **listy wykluczania maszyny wirtualnej (ciąg)** . Ta wartość jest nazwą co najmniej jednej maszyny wirtualnej z docelowej grupy zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić nazwy przy użyciu przecinków (wartości nie są rozróżniane wielkości liter). Użycie symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w `External_ExcludeVMNames` zmiennej.
  
4. Użyj pola **harmonogram** , aby wybrać harmonogram zarządzania maszyną wirtualną przez funkcję. Wybierz datę i godzinę rozpoczęcia harmonogramu, aby utworzyć cykliczny dzienny harmonogram, zaczynając od wybranego czasu. Wybór innego regionu nie jest dostępny. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu tej funkcji, zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Aby otrzymywać powiadomienia e-mail z [grupy akcji](../azure-monitor/platform/action-groups.md), Zaakceptuj wartość domyślną **tak** w polu  **powiadomienia e-mail** i Podaj prawidłowy adres e-mail. Jeśli wybierzesz pozycję **nie** , ale zdecydujesz się na późniejszą datę otrzymywania powiadomień e-mail, możesz zaktualizować grupę akcji utworzoną przy użyciu prawidłowych adresów e-mail rozdzielonych przecinkami. 

6. Włącz następujące reguły alertów:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Tworzenie alertów

Start/Stop VMs during off-hours nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dziennika za pomocą Azure monitor](../azure-monitor/platform/alerts-log.md) , aby dowiedzieć się, jak utworzyć alerty dotyczące nieudanych zadań i procedur.

## <a name="deploy-the-feature"></a>Wdróż funkcję

1. Po skonfigurowaniu początkowych ustawień wymaganych dla tej funkcji kliknij przycisk **OK** , aby zamknąć stronę parametry.

2. Kliknij przycisk **Utwórz**. Po zweryfikowaniu wszystkich ustawień funkcja zostanie wdrożona w ramach subskrypcji. Ten proces może potrwać kilka sekund, a postęp można śledzić w obszarze **powiadomienia** z menu.

    > [!NOTE]
    > Jeśli masz subskrypcję dostawcy rozwiązań w chmurze platformy Azure (CSP), po zakończeniu wdrożenia na koncie usługi Automation przejdź do pozycji **zmienne** w obszarze **zasoby udostępnione** i ustaw dla zmiennej [External_EnableClassicVMs](automation-solution-vm-management.md#variables) **wartość false**. To uniemożliwia rozwiązanie wyszukiwanie klasycznych zasobów maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować tę funkcję, zobacz [Konfigurowanie zatrzymywania/uruchamiania maszyn wirtualnych poza godzinami pracy](automation-solution-vm-management-config.md).
* Aby rozwiązać błędy funkcji, zobacz [Rozwiązywanie problemów Start/Stop VMS during off-hours](troubleshoot/start-stop-vm.md).
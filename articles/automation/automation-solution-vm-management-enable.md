---
title: Włącz Azure Automation uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami
description: W tym artykule opisano sposób włączania Azure Automation uruchamiania/zatrzymywania maszyny wirtualnej na potrzeby maszyn wirtualnych platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096966"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Włącz rozwiązanie Azure Automation uruchamiania/zatrzymywania maszyn wirtualnych

Wykonaj następujące kroki, aby dodać rozwiązanie **Start/Stop VMS during off-hours** do nowego lub istniejącego konta usługi Automation i połączonego log Analytics obszaru roboczego. Po zakończeniu procesu dołączania Skonfiguruj zmienne, aby dostosować rozwiązanie.

>[!NOTE]
>Aby można było używać tego rozwiązania z klasycznymi maszynami wirtualnymi, musisz mieć klasyczne konto Uruchom jako, które nie jest domyślnie tworzone. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta Uruchom jako, zobacz [Tworzenie klasycznego konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Włączanie rozwiązania

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com).

2. Wyszukaj i wybierz pozycję **konta usługi Automation**.

3. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.

4. Na koncie usługi Automation wybierz pozycję **Uruchom/Zatrzymaj maszynę wirtualną** w obszarze **powiązane zasoby**. W tym miejscu możesz kliknąć **więcej informacji na temat i włączyć rozwiązanie**. Jeśli masz już wdrożone rozwiązanie do uruchamiania/zatrzymywania maszyny wirtualnej, możesz je wybrać, klikając pozycję **Zarządzaj rozwiązaniem** i wyszukując je na liście.

   ![Włącz z konta usługi Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Możesz również utworzyć go z dowolnego miejsca w Azure Portal, klikając pozycję **Utwórz zasób**. Na stronie Marketplace wpisz słowo kluczowe, takie jak **Start** lub **Start/Stop**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie możesz wpisać jedno lub więcej słów kluczowych z pełnej nazwy rozwiązania, a następnie nacisnąć klawisz ENTER. Wybierz **Start/Stop VMS during off-hours** z wyników wyszukiwania.

5. Na stronie **Start/Stop VMS during off-hours** wybranego rozwiązania Przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Zostanie wyświetlona strona Dodawanie rozwiązania. Zostanie wyświetlony monit o skonfigurowanie rozwiązania przed zaimportowaniem go do subskrypcji usługi Automation.

   ![Strona dodawania rozwiązania do zarządzania maszyną wirtualną](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na stronie Dodawanie rozwiązania wybierz pozycję **obszar roboczy**. Wybierz obszar roboczy Log Analytics, który jest połączony z tą samą subskrypcją platformy Azure, w której znajduje się konto usługi Automation. Jeśli nie masz obszaru roboczego, wybierz pozycję **Utwórz nowy obszar roboczy**. Na stronie obszar roboczy Log Analytics wykonaj następujące czynności:

   - Określ nazwę nowego obszaru roboczego Log Analytics, na przykład **ContosoLAWorkspace**.
   - Wybierz **subskrypcję** , z którą chcesz utworzyć łącze, wybierając z listy rozwijanej opcję, jeśli wybrana wartość domyślna nie jest odpowiednia.
   - W obszarze **Grupa zasobów**można utworzyć nową grupę zasobów lub wybrać istniejącą.
   - Wybierz **lokalizację**.
   - Wybierz **warstwę cenową**. Wybierz opcję **na GB (autonomiczne)** . W przypadku dzienników Azure Monitor Zaktualizowano [Cennik](https://azure.microsoft.com/pricing/details/log-analytics/) i jedyną opcją jest warstwa za GB.

   > [!NOTE]
   > Podczas włączania rozwiązań tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation.
   >
   > Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

8. Po podania wymaganych informacji na stronie obszaru roboczego Log Analytics kliknij przycisk **Utwórz**. Postęp można śledzić w obszarze **powiadomienia** z menu, co powoduje powrót do strony Dodawanie rozwiązania po zakończeniu.

9. Na stronie Dodawanie rozwiązania wybierz pozycję **konto usługi Automation**. Jeśli tworzysz nowy obszar roboczy Log Analytics, możesz utworzyć nowe konto usługi Automation, które ma być skojarzone z nim, lub wybrać istniejące konto usługi Automation, które nie jest już połączone z Log Analytics obszarem roboczym. Wybierz istniejące konto usługi Automation lub kliknij pozycję **Utwórz konto usługi Automation**, a następnie na stronie Dodawanie konta usługi Automation podaj następujące informacje:
 
   - W polu **Nazwa** wprowadź nazwę konta usługi Automation.

     Wszystkie inne opcje są wypełniane automatycznie na podstawie wybranego obszaru roboczego Log Analytics. Tych opcji nie można modyfikować. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu przycisku **OK**opcje konfiguracji są weryfikowane i tworzone jest konto usługi Automation. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

10. Na koniec na stronie Dodawanie rozwiązania wybierz pozycję **Konfiguracja**. Zostanie wyświetlona strona parametry.

    ![Strona parametrów rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit o:
  
   - Określ **nazwy docelowych nazw zasobów**. Te wartości to nazwy grup zasobów, które zawierają maszyny wirtualne, które mają być zarządzane przez to rozwiązanie. Można wprowadzić więcej niż jedną nazwę i oddzielić każdy z nich przy użyciu przecinka (w wartościach nie jest rozróżniana wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w zmiennych **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** .
  
   - Określ **listę wykluczeń maszyny wirtualnej (ciąg)**. Ta wartość jest nazwą co najmniej jednej maszyny wirtualnej z docelowej grupy zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić każdy z nich przy użyciu przecinka (w wartościach nie jest rozróżniana wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w zmiennej **External_ExcludeVMNames** .
  
   - Wybierz **harmonogram**. Wybierz datę i godzinę dla harmonogramu. Zostanie utworzony dzienny harmonogram, który rozpoczyna się od wybranego czasu. Wybór innego regionu nie jest dostępny. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [Modyfikowanie harmonogramu uruchamiania i zamykania](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Aby otrzymywać **powiadomienia e-mail** z grupy akcji, Zaakceptuj wartość domyślną **tak** i Podaj prawidłowy adres e-mail. Jeśli wybierzesz pozycję **nie** , ale zdecydujesz się na późniejszą datę otrzymywania powiadomień e-mail, możesz zaktualizować [grupę akcji](../azure-monitor/platform/action-groups.md) utworzoną przy użyciu prawidłowych adresów e-mail rozdzielonych przecinkami. Należy również włączyć następujące reguły alertów:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Wartość domyślna dla **docelowych nazw zasobów** jest **&ast;**. Dotyczy to wszystkich maszyn wirtualnych w subskrypcji. Jeśli nie chcesz, aby rozwiązanie przekierować wszystkie maszyny wirtualne w subskrypcji, ta wartość musi zostać zaktualizowana do listy nazw grup zasobów przed włączeniem harmonogramów.

11. Po skonfigurowaniu początkowych ustawień wymaganych dla rozwiązania kliknij przycisk **OK** , aby zamknąć stronę parametry, a następnie wybierz pozycję **Utwórz**. 

Po zweryfikowaniu wszystkich ustawień rozwiązanie zostanie wdrożone w ramach subskrypcji. Ten proces może potrwać kilka sekund, a postęp można śledzić w obszarze **powiadomienia** z menu.

> [!NOTE]
> Jeśli masz subskrypcję dostawcy rozwiązań w chmurze platformy Azure (CSP), po zakończeniu wdrożenia na koncie usługi Automation przejdź do pozycji **zmienne** w obszarze **zasoby udostępnione** i ustaw dla zmiennej [External_EnableClassicVMs](automation-solution-vm-management.md#variables) **wartość false**. To uniemożliwia rozwiązanie wyszukiwanie klasycznych zasobów maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już włączone rozwiązanie, możesz je [skonfigurować](automation-solution-vm-management-config.md) do obsługi wymagań dotyczących zarządzania maszyną wirtualną.
---
title: Włączanie rozwiązania Azure Automation Start/Stop VMs poza godzinami pracy
description: W tym artykule opisano, jak włączyć rozwiązanie azure automation start/stop maszyny wirtualnej dla maszyn wirtualnych platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2414567b74232d634fa0a34202691a8e43ae6135
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604748"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Włączanie rozwiązania Azure Automation Start/Stop VMs

Wykonaj następujące kroki, aby dodać **maszyny wirtualne Start/Stop podczas** poza godzinami pracy do nowego lub istniejącego konta automatyzacji i połączonego obszaru roboczego usługi Log Analytics. Po zakończeniu procesu dołączania skonfiguruj zmienne, aby dostosować rozwiązanie.

>[!NOTE]
>Aby użyć tego rozwiązania z klasycznymi maszynami wirtualnymi, potrzebujesz klasycznego konta Uruchom jako, które nie jest tworzone domyślnie. Aby uzyskać instrukcje dotyczące tworzenia konta Klasycznego uruchamiania jako, zobacz [Tworzenie konta klasycznego uruchamiania jako](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Włączanie rozwiązania

1. Zaloguj się do [witryny](https://portal.azure.com)Azure portal .

2. Wyszukaj i wybierz **pozycję Konta automatyzacji**.

3. Na stronie Konta automatyzacji wybierz konto automatyzacji z listy.

4. Na koncie Automatyzacja wybierz pozycję **Start/Stop VM** w obszarze **Zasoby pokrewne**. W tym miejscu możesz kliknąć dowiedz **się więcej o rozwiązaniu i włączyć je**. Jeśli masz już wdrożone rozwiązanie Start/Stop VM, możesz je zaznaczyć, klikając **pozycję Zarządzaj rozwiązaniem** i znajdując je na liście.

   ![Włącz z konta automatyzacji](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Można go również utworzyć z dowolnego miejsca w witrynie Azure portal, klikając pozycję **Utwórz zasób**. Na stronie Marketplace wpisz słowo kluczowe, takie jak **Start** lub **Start/Stop**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie można wpisać jedno lub więcej słów kluczowych z pełnej nazwy rozwiązania, a następnie nacisnąć klawisz Enter. Z wyników wyszukiwania **wybierz pozycję Start/Stop VMs poza godzinami** pracy.

5. Na stronie **Start/Stop VMs poza godzinami pracy** dla wybranego rozwiązania przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Zostanie wyświetlona strona Dodaj rozwiązanie. Zostanie wyświetlony monit o skonfigurowanie rozwiązania, zanim będzie można zaimportować je do subskrypcji automatyzacji.

   ![Strona Dodaj rozwiązanie dodawania do zarządzania maszynami wirtualnymi](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na stronie Dodawanie rozwiązania wybierz pozycję **Obszar roboczy**. Wybierz obszar roboczy usługi Log Analytics, który jest połączony z tą samą subskrypcją platformy Azure, w których znajduje się konto automatyzacji. Jeśli nie masz obszaru roboczego, wybierz pozycję **Utwórz nowy obszar roboczy**. Na stronie obszaru roboczego usługi Log Analytics wykonaj następujące czynności:

   - Określ nazwę nowego obszaru roboczego usługi Log Analytics, takiego jak **ContosoLAWorkspace**.
   - Wybierz **subskrypcję,** do z aby utworzyć łącze, wybierając z listy rozwijanej, jeśli wybrana wartość domyślna nie jest odpowiednia.
   - W przypadku **grupy zasobów**można utworzyć nową grupę zasobów lub wybrać istniejącą.
   - Wybierz **lokalizację**.
   - Wybierz **warstwę cenową**. Wybierz opcję **Na GB (autonomiczny).** Dzienniki usługi Azure Monitor mają [zaktualizowane ceny,](https://azure.microsoft.com/pricing/details/log-analytics/) a warstwa Na GB jest jedyną opcją.

   > [!NOTE]
   > Podczas włączania rozwiązań tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego usługi Log Analytics i konta automatyzacji.
   >
   > Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionu dla konta automatyzacji i obszaru roboczego Usługi Analizy dzienników](how-to/region-mappings.md).

8. Po podaniu wymaganych informacji na stronie obszaru roboczego usługi Log Analytics kliknij przycisk **Utwórz**. Jego postęp można śledzić w obszarze **Powiadomienia** z menu, które zwraca się do strony Dodaj rozwiązanie po zakończeniu.

9. Na stronie Dodawanie rozwiązania wybierz pozycję **Konto automatyzacji**. Jeśli tworzysz nowy obszar roboczy usługi Log Analytics, możesz utworzyć nowe konto automatyzacji, które ma być z nim skojarzone, lub wybrać istniejące konto automatyzacji, które nie jest jeszcze połączone z obszarem roboczym usługi Log Analytics. Wybierz istniejące konto automatyzacji lub kliknij pozycję **Utwórz konto automatyzacji,** a na stronie Dodaj konto automatyzacji podaj następujące informacje:
 
   - W polu **Nazwa** wprowadź nazwę konta usługi Automation.

     Wszystkie inne opcje są automatycznie wypełniane na podstawie wybranego obszaru roboczego usługi Log Analytics. Tych opcji nie można modyfikować. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu przycisku **OK**opcje konfiguracji zostaną zweryfikowane i zostanie utworzone konto Automatyzacja. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

10. Na koniec na stronie Dodawanie rozwiązania wybierz pozycję **Konfiguracja**. Zostanie wyświetlona strona Parametry.

    ![Strona Parametry dla rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit o:
  
   - Określ **docelowe nazwy grup zasobów**. Te wartości są nazwy grup zasobów, które zawierają maszyny wirtualne, które mają być zarządzane przez to rozwiązanie. Można wprowadzić więcej niż jedną nazwę i oddzielić każdą za pomocą przecinka (w wartościach nie rozróżnia się wielkość liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w **zmiennych External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames.**
  
   - Określ **listę wykluczeń maszyn wirtualnych (ciąg)**. Ta wartość jest nazwą co najmniej jednej maszyny wirtualnej z docelowej grupy zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić każdą za pomocą przecinka (w wartościach nie rozróżnia się wielkość liter). Korzystanie z symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w **zmiennej External_ExcludeVMNames.**
  
   - Wybierz **harmonogram**. Wybierz datę i godzinę dla swojego harmonogramu. Ponownie powtarzającym się harmonogramem dziennym zostanie utworzony, począwszy od wybranego czasu. Wybranie innego regionu jest niedostępne. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [Modyfikowanie harmonogramu uruchamiania i zamykania](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Aby otrzymywać **powiadomienia e-mail** od grupy akcji, zaakceptuj domyślną wartość **Tak** i podaj prawidłowy adres e-mail. Jeśli wybierzesz **opcję Nie,** ale w późniejszym terminie zdecydujesz, że chcesz otrzymywać powiadomienia e-mail, możesz zaktualizować [grupę akcji](../azure-monitor/platform/action-groups.md) utworzoną przy obliczu prawidłowych adresów e-mail oddzielonych przecinkiem. Należy również włączyć następujące reguły alertów:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Wartością domyślną dla nazw **&ast;** grup zasobów **docelowych** jest . Dotyczy to wszystkich maszyn wirtualnych w ramach subskrypcji. Jeśli nie chcesz, aby rozwiązanie docelowe wszystkie maszyny wirtualne w subskrypcji tej wartości musi zostać zaktualizowany do listy nazw grup zasobów przed włączeniem harmonogramów.

11. Po skonfigurowaniu ustawień początkowych wymaganych dla rozwiązania kliknij przycisk **OK,** aby zamknąć stronę Parametry, a następnie wybierz pozycję **Utwórz**. 

Po zatwierdzeniu wszystkich ustawień rozwiązanie jest wdrażane w ramach subskrypcji. Ten proces może potrwać kilka sekund, aby zakończyć i można śledzić jego **postępy** w obszarze Powiadomienia z menu.

> [!NOTE]
> Jeśli masz subskrypcję dostawcy rozwiązań w chmurze Azure (Azure CSP), po zakończeniu wdrażania, na **koncie** automatyzacji przejdź do zmiennych w obszarze **Zasoby udostępnione** i ustaw zmienną [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) na **False**. To zatrzymuje rozwiązanie od szukania zasobów klasycznej maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz włączone rozwiązanie, można [skonfigurować](automation-solution-vm-management-config.md) go do obsługi wymagań dotyczących zarządzania maszynami wirtualnymi.
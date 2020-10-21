---
title: Dostęp do maszyny wirtualnej just in Time w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób, w jaki dostęp just in Time (JIT) w Azure Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 6091ccbb64ec880224e861e1b8ee2bd39363385c
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342386"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpieczanie portów zarządzania przy użyciu dostępu just in time

Zablokuj ruch przychodzący do usługi Azure Virtual Machines przy użyciu funkcji dostępu do maszyny wirtualnej (JIT) Azure Security Center (just-in-Time). Pozwala to ograniczyć narażenie na ataki, zapewniając łatwy dostęp, gdy konieczne jest połączenie z maszyną wirtualną.

Aby zapoznać się z pełnym wyjaśnieniem działania JIT i logiki bazowej, zobacz [just-in-Time](just-in-time-explained.md).

Na tej stronie zawarto informacje na temat sposobu dołączania JIT do programu zabezpieczającego. Omawiane tematy: 

- **Włącz JIT na maszynach wirtualnych** — możesz włączyć JIT z własnymi opcjami niestandardowymi dla jednej lub wielu maszyn wirtualnych przy użyciu Security Center, programu PowerShell lub interfejsu API REST. Alternatywnie można włączyć JIT z użyciem domyślnych, trwale zakodowanych parametrów, z maszyn wirtualnych platformy Azure. Po włączeniu JIT blokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły w sieciowej grupie zabezpieczeń.
- **Zażądaj dostępu do maszyny wirtualnej z włączoną obsługą JIT** — celem JIT jest upewnienie się, że mimo że ruch przychodzący jest zablokowany, Security Center nadal zapewnia łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Możesz zażądać dostępu do maszyny wirtualnej z obsługą JIT z Security Center, Azure Virtual Machines, PowerShell lub interfejsu API REST.
- **Inspekcja działania** — aby upewnić się, że maszyny wirtualne są odpowiednio zabezpieczone, przejrzyj dostęp do maszyn wirtualnych z obsługą JIT w ramach zwykłych testów zabezpieczeń.   



## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Obsługiwane maszyny wirtualne:|![Tak ](./media/icons/yes-icon.png) w przypadku maszyn wirtualnych wdrożonych za pośrednictwem Azure Resource Manager.<br>![Nie ](./media/icons/no-icon.png) wdrożono żadnych maszyn wirtualnych z klasycznymi modelami wdrażania. [Dowiedz się więcej na temat tych modeli wdrażania](../azure-resource-manager/management/deployment-models.md).<br>![Brak ](./media/icons/no-icon.png) maszyn wirtualnych chronionych przez zapory platformy Azure kontrolowane przez [Menedżera zapory platformy Azure](../firewall-manager/overview.md)|
|Wymagane role i uprawnienia:|Role **Reader** i **SecurityReader** mogą wyświetlać stan i parametry JIT.<br>Aby utworzyć role niestandardowe, które mogą korzystać z JIT, zobacz [jakie uprawnienia są potrzebne do skonfigurowania i użycia JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Aby utworzyć rolę najniższych uprawnień dla użytkowników, którzy muszą zażądać dostępu JIT do maszyny wirtualnej i wykonać inne operacje JIT, użyj [skryptu Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) ze stron społeczności usługi GitHub Security Center.|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="enable-jit-vm-access"></a>Włącz dostęp JIT do maszyny wirtualnej <a name="jit-configure"></a>

Możesz włączyć dostęp JIT VM z własnymi opcjami niestandardowymi dla jednej lub wielu maszyn wirtualnych przy użyciu Security Center lub programowo. 

Alternatywnie można włączyć JIT z użyciem domyślnych, trwale zakodowanych parametrów, z maszyn wirtualnych platformy Azure.

Każda z tych opcji została omówiona na osobnej karcie poniżej.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Włącz JIT na maszynach wirtualnych za pomocą Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Konfigurowanie dostępu JIT VM w Azure Security Center":::

W Security Center można włączyć i skonfigurować dostęp JIT do maszyny wirtualnej.

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **dostęp just in Time do maszyny wirtualnej**.

    Zostanie otwarta strona **dostęp just in Time do maszyny wirtualnej** z maszynami wirtualnymi pogrupowanymi na następujące karty:

    - **Skonfigurowane** — maszyny wirtualne, które zostały już skonfigurowane do obsługi dostępu just in Time do maszyny wirtualnej. Dla każdej maszyny wirtualnej skonfigurowana karta pokazuje:
        - liczba zatwierdzonych żądań JIT w ciągu ostatnich siedmiu dni
        - Data i godzina ostatniego dostępu
        - skonfigurowane szczegóły połączenia
        - ostatni użytkownik
    - **Nie skonfigurowano** — maszyny wirtualne bez włączonego JIT, ale mogą obsługiwać JIT. Zalecamy włączenie JIT dla tych maszyn wirtualnych.
    - **Nieobsługiwane** — maszyny wirtualne bez włączonego JIT, które nie obsługują tej funkcji. Maszyna wirtualna może znajdować się na tej karcie z następujących powodów:
      - Brak sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) — JIT wymaga skonfigurowania sieciowej grupy zabezpieczeń
      - Klasyczna maszyna wirtualna — JIT obsługuje maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager, a nie "wdrożenie klasyczne". [Dowiedz się więcej na temat modeli wdrażania klasycznego vs Azure Resource Manager](../azure-resource-manager/management/deployment-models.md).
      - Inne — maszyna wirtualna może znajdować się na tej karcie, jeśli rozwiązanie JIT jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów.

1. Na karcie **Nieskonfigurowane** Oznacz maszyny wirtualne, które mają być chronione przy użyciu JIT, a następnie wybierz pozycję **Włącz JIT na maszynach wirtualnych**. 

    Zostanie wyświetlona strona dostępu JIT VM zawierający listę portów, które Security Center zalecaną ochronę:
    - 22 — SSH
    - 3389 — RDP
    - 5985 — WinRM 
    - 5986 — WinRM

    Aby zaakceptować ustawienia domyślne, wybierz pozycję **Zapisz**.

1. Aby dostosować opcje JIT:

    - Dodaj niestandardowe porty za pomocą przycisku **Dodaj** . 
    - Zmodyfikuj jeden z domyślnych portów, wybierając go z listy.

    Dla każdego portu (niestandardowego i domyślnego) okienka **Dodawanie konfiguracji portów** dostępne są następujące opcje:

    - **Protokół**— protokół, który jest dozwolony na tym porcie, gdy żądanie zostanie zatwierdzone
    - **Dozwolone źródła**IP — zakresy adresów IP, które są dozwolone na tym porcie, gdy żądanie zostanie zatwierdzone
    - **Maksymalny czas żądania**— maksymalny przedział czasu, w którym można otworzyć określony port

     1. Ustaw odpowiednie zabezpieczenia portów.

     1. Wybierz przycisk **OK**.

1. Wybierz pozycję **Zapisz**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Edytowanie konfiguracji JIT na maszynie wirtualnej z obsługą JIT przy użyciu Security Center <a name="jit-modify"></a>

Konfigurację just in Time maszyny wirtualnej można zmodyfikować przez dodanie i skonfigurowanie nowego portu do ochrony dla tej maszyny wirtualnej lub zmianę dowolnego ustawienia związanego z już chronionym portem.

Aby edytować istniejące reguły JIT dla maszyny wirtualnej:

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **adaptacyjne kontrolki aplikacji**.

1. Na **skonfigurowanej** karcie kliknij prawym przyciskiem myszy maszynę wirtualną, do której chcesz dodać port, a następnie wybierz polecenie Edytuj. 

    ![Edytowanie konfiguracji JIT dostępu do maszyny wirtualnej w Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. W obszarze **Konfiguracja dostępu JIT do maszyny wirtualnej**można edytować istniejące ustawienia już chronionego portu lub dodać nowy port niestandardowy.

1. Po zakończeniu edycji portów wybierz pozycję **Zapisz**.
 


### <a name="azure-virtual-machines"></a>[**Azure Virtual Machines**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Włączanie JIT na maszynach wirtualnych z poziomu usługi Azure Virtual Machines

Można włączyć JIT na maszynie wirtualnej ze stron usługi Azure Virtual Machines w Azure Portal.

![Konfigurowanie dostępu JIT VM na maszynach wirtualnych platformy Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Jeśli maszyna wirtualna ma już włączoną funkcję just-in-Time, po przejściu na stronę konfiguracji zobaczysz, że włączono just in Time i możesz użyć linku, aby otworzyć stronę dostępu just in Time do maszyny wirtualnej w Security Center, a następnie wyświetlić i zmienić ustawienia.

1. W [Azure Portal](https://ms.portal.azure.com)Wyszukaj i wybierz pozycję **maszyny wirtualne**. 

1. Wybierz maszynę wirtualną, która ma być chroniona za pomocą JIT.

1. W menu wybierz pozycję **Konfiguracja**.

1. W obszarze **dostęp just in Time**wybierz pozycję **Włącz just in Time**. 

    Pozwala to na dostęp just in Time do maszyny wirtualnej przy użyciu następujących ustawień domyślnych:

    - Maszyny z systemem Windows:
        - Port RDP 3389
        - Trzy godziny maksymalnego dozwolonego dostępu
        - Dozwolone źródłowe adresy IP są ustawione na wartość any
    - Maszyny z systemem Linux:
        - Port SSH 22
        - Trzy godziny maksymalnego dozwolonego dostępu
        - Dozwolone źródłowe adresy IP są ustawione na wartość any

1. Aby edytować dowolną z tych wartości lub dodać więcej portów do konfiguracji JIT, użyj strony just in Time do Azure Security Center:

    1. Z menu Security Center wybierz pozycję **dostęp just in Time do maszyny wirtualnej**.

    1. Na **skonfigurowanej** karcie kliknij prawym przyciskiem myszy maszynę wirtualną, do której chcesz dodać port, a następnie wybierz polecenie Edytuj. 

        ![Edytowanie konfiguracji JIT dostępu do maszyny wirtualnej w Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. W obszarze **Konfiguracja dostępu JIT do maszyny wirtualnej**można edytować istniejące ustawienia już chronionego portu lub dodać nowy port niestandardowy.

    1. Po zakończeniu edycji portów wybierz pozycję **Zapisz**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Włączanie funkcji JIT na maszynach wirtualnych za pomocą programu PowerShell

Aby włączyć dostęp just in Time do maszyny wirtualnej za pomocą programu PowerShell, użyj oficjalnego polecenia cmdlet programu PowerShell Azure Security Center `Set-AzJitNetworkAccessPolicy` .

**Przykład** : Włącz dostęp just in Time do maszyny wirtualnej na określonej maszynie wirtualnej z następującymi regułami.

* Zamknij porty 22 i 3389
* Ustawienie maksymalnego przedziału czasu (3 godziny) dla każdego z nich, aby można je było otworzyć na zatwierdzone żądanie
* Zezwalaj użytkownikowi, który żąda dostępu do kontrolowania źródłowych adresów IP
* Zezwalaj użytkownikowi, który żąda dostępu do ustanowienia pomyślnej sesji na podstawie zatwierdzonego żądania dostępu just in Time

Następujące polecenia programu PowerShell tworzą tę konfigurację JIT:

1. Przypisz zmienną, która zawiera reguły dostępu just in Time do maszyny wirtualnej:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Wstawianie reguł dostępu just in Time do maszyny wirtualnej do tablicy:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Skonfiguruj reguły dostępu just in Time do maszyny wirtualnej na wybranej maszynie wirtualnej:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Użyj parametru-name, aby określić maszynę wirtualną. Na przykład aby ustalić konfigurację JIT dla dwóch różnych maszyn wirtualnych, VM1 i VM2, należy użyć: ```Set-AzJitNetworkAccessPolicy -Name VM1``` i ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**Interfejs API REST**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Włączanie JIT na maszynach wirtualnych za pomocą interfejsu API REST

Funkcja dostępu just in Time do maszyny wirtualnej może być używana za pośrednictwem interfejsu API Azure Security Center. Użyj tego interfejsu API, aby uzyskać informacje o skonfigurowanych maszynach wirtualnych, dodać nowe, zażądać dostępu do maszyny wirtualnej i nie tylko. 

Dowiedz się więcej na temat [zasad dostępu do sieci JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Żądanie dostępu do maszyny wirtualnej z obsługą JIT

Możesz zażądać dostępu do maszyny wirtualnej z obsługą JIT z Azure Portal (w Security Center lub na maszynach wirtualnych platformy Azure) lub programowo.

Każda z tych opcji została omówiona na osobnej karcie poniżej.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Zażądaj dostępu do maszyny wirtualnej z obsługą JIT z Azure Security Center 

Jeśli maszyna wirtualna ma włączoną funkcję JIT, musisz poprosić o uzyskanie dostępu, aby się z nim połączyć. Możesz zażądać dostępu na dowolnym z obsługiwanych sposobów, niezależnie od tego, jak włączono JIT.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Konfigurowanie dostępu JIT VM w Azure Security Center":::

1. Na stronie **dostęp do maszyny wirtualnej just-in-Time** wybierz kartę **skonfigurowane** .

1. Oznacz maszyny wirtualne, do których chcesz uzyskać dostęp.

    - Ikona w kolumnie **szczegóły połączenia** wskazuje, czy JIT jest włączona w sieciowej grupie zabezpieczeń lub zaporze. Jeśli ta opcja jest włączona, zostanie wyświetlona tylko ikona zapory.

    - Kolumna **szczegóły połączenia** zawiera informacje wymagane do nawiązania połączenia z maszyną wirtualną oraz otwarte porty.

1. Wybierz pozycję **Żądaj dostępu**. Zostanie otwarte okno **żądania dostępu** .

1. W obszarze **Żądaj dostępu**dla każdej maszyny wirtualnej Skonfiguruj porty, które chcesz otworzyć, oraz źródłowe adresy IP, na których jest otwierany port, oraz przedział czasu, w którym port zostanie otwarty. Możliwe będzie tylko żądanie dostępu do skonfigurowanych portów. Każdy port ma maksymalny dozwolony czas pochodzący z utworzonej konfiguracji JIT.

1. Wybierz pozycję **Otwórz porty**.

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu, znajduje się za serwerem proxy, opcja **My IP** może nie zadziałać. Może być konieczne zdefiniowanie pełnego zakresu adresów IP organizacji.



### <a name="azure-virtual-machines"></a>[**Azure Virtual Machines**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Zażądaj dostępu do maszyny wirtualnej z obsługą JIT na stronie połączenia z maszyną wirtualną platformy Azure

Jeśli maszyna wirtualna ma włączoną funkcję JIT, musisz poprosić o uzyskanie dostępu, aby się z nim połączyć. Możesz zażądać dostępu na dowolnym z obsługiwanych sposobów, niezależnie od tego, jak włączono JIT.

  >![żądanie just-in-Time JIT](./media/security-center-just-in-time/jit-request-vm.png)


Aby zażądać dostępu z usługi Azure Virtual Machines:

1. W Azure Portal Otwórz strony maszyny wirtualne.

1. Wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie, i Otwórz stronę **Połącz** .

    Platforma Azure sprawdzi, czy dla tej maszyny wirtualnej jest włączona funkcja JIT.

    - Jeśli JIT nie jest włączona dla maszyny wirtualnej, zostanie wyświetlony monit o jej włączenie.

    - Jeśli włączono funkcję JIT, wybierz pozycję **Żądaj dostępu** , aby przekazać żądanie dostępu przy użyciu adresu IP żądania, zakresu czasu i portów skonfigurowanych dla tej maszyny wirtualnej.

> [!NOTE]
> Po zatwierdzeniu żądania dla maszyny wirtualnej chronionej przez zaporę platformy Azure Security Center udostępnia użytkownikowi odpowiednie szczegóły połączenia (mapowanie portów z tabeli DNAT), które ma być używane do nawiązywania połączenia z maszyną wirtualną.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Żądanie dostępu do maszyny wirtualnej z obsługą JIT za pomocą programu PowerShell

W poniższym przykładzie można zobaczyć żądanie dostępu just in Time do określonej maszyny wirtualnej, w której zażądano otwarcia portu 22 dla określonego adresu IP i przez określony czas:

Uruchom następujące polecenia w programie PowerShell:

1. Skonfiguruj właściwości dostępu żądania maszyny wirtualnej:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Wstawianie parametrów żądania dostępu do maszyny wirtualnej w tablicy:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Wyślij żądanie dostępu (Użyj identyfikatora zasobu z kroku 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Więcej informacji znajduje się w [dokumentacji poleceń cmdlet programu PowerShell](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**Interfejs API REST**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Zażądaj dostępu do maszyn wirtualnych z obsługą JIT przy użyciu interfejsu API REST

Funkcja dostępu just in Time do maszyny wirtualnej może być używana za pośrednictwem interfejsu API Azure Security Center. Użyj tego interfejsu API, aby uzyskać informacje o skonfigurowanych maszynach wirtualnych, dodać nowe, zażądać dostępu do maszyny wirtualnej i nie tylko. 

Dowiedz się więcej na temat [zasad dostępu do sieci JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Inspekcja aktywności dostępu JIT w Security Center

Szczegółowe informacje na temat działań maszyn wirtualnych można uzyskać przy użyciu funkcji przeszukiwania dzienników. Aby wyświetlić dzienniki:

1. Z **dostępu just in Time do maszyny wirtualnej**wybierz **skonfigurowaną** kartę.

1. Dla maszyny wirtualnej, którą chcesz poddać inspekcji, otwórz menu wielokropka na końcu wiersza.
 
1. Wybierz pozycję **Dziennik aktywności** z menu.

   ![Wybieranie dziennika aktywności JIT just in Time](./media/security-center-just-in-time/jit-select-activity-log.png)

   Dziennik aktywności zawiera filtrowany widok poprzednich operacji dla tej maszyny wirtualnej oraz godzinę, datę i subskrypcję.

1. Aby pobrać informacje dziennika, wybierz pozycję **Pobierz jako plik CSV**.








## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania i używania dostępu just in Time do maszyny wirtualnej. Aby dowiedzieć się, dlaczego należy użyć JIT, przeczytaj artykuł dotyczący koncepcji, w którym wyjaśniono, w jaki sposób są obronne:

> [!div class="nextstepaction"]
> [Wyjaśniono JIT](just-in-time-explained.md)
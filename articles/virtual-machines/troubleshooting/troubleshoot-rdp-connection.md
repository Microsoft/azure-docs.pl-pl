---
title: Nie można nawiązać połączenia RDP z maszyną wirtualną z systemem Windows na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów, gdy nie można nawiązać połączenia z maszyną wirtualną z systemem Windows na platformie Azure przy użyciu Pulpit zdalny
keywords: Błąd pulpitu zdalnego, błąd połączenia pulpitu zdalnego, nie można nawiązać połączenia z maszyną wirtualną, rozwiązywanie problemów z pulpitem zdalnym
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: 66e4ea622f546981d1f0651b20368590e59056d3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201624"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Rozwiązywanie problemów z połączeniami Pulpit zdalny z maszyną wirtualną platformy Azure
Połączenie RDP (Remote Desktop Protocol) z maszyną wirtualną platformy Azure bazującą na systemie Windows może ulec awarii z wielu powodów, uniemożliwiając uzyskanie dostępu do maszyny wirtualnej. Ten problem może być związany z usługą pulpitu zdalnego na maszynie wirtualnej, połączeniem sieciowym lub klientem pulpitu zdalnego na komputerze hosta. Ten artykuł przeprowadzi Cię przez niektóre z najbardziej popularnych metod rozwiązywania problemów z połączeniem RDP. 

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Szybkie rozwiązywanie problemów
Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną:

1. Zresetuj konfigurację Pulpit zdalny.
2. Sprawdź reguły grupy zabezpieczeń sieci/Cloud Services punkty końcowe.
3. Przejrzyj dzienniki konsoli maszyn wirtualnych.
4. Zresetuj kartę sieciową dla maszyny wirtualnej.
5. Sprawdź Resource Health maszyny wirtualnej.
6. Zresetuj hasło maszyny wirtualnej.
7. Uruchom ponownie maszynę wirtualną.
8. Wdróż ponownie maszynę wirtualną.

Kontynuuj odczytywanie, jeśli potrzebujesz bardziej szczegółowych kroków i wyjaśnień. Sprawdź, czy lokalny sprzęt sieciowy, taki jak routery i zapory, nie blokuje wychodzącego portu TCP 3389, zgodnie z [szczegółowymi scenariuszami rozwiązywania problemów RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Jeśli przycisk **Połącz** dla maszyny wirtualnej jest wyszarzony w portalu i nie nawiązano połączenia z platformą Azure za pośrednictwem usługi [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , przed użyciem protokołu RDP należy utworzyć i przypisać maszynę wirtualną jako publiczny adres IP. Więcej informacji o [publicznych adresach IP na platformie Azure](../../virtual-network/public-ip-addresses.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sposoby rozwiązywania problemów z protokołem RDP
Możesz rozwiązywać problemy z maszynami wirtualnymi utworzonymi przy użyciu Menedżer zasobów modelu wdrażania przy użyciu jednej z następujących metod:

* Azure Portal — doskonały, jeśli chcesz szybko zresetować konfigurację RDP lub poświadczenia użytkownika, a nie masz zainstalowanych narzędzi platformy Azure.
* Azure PowerShell — Jeśli masz doświadczenie z wierszem polecenia programu PowerShell, szybko Zresetuj konfigurację RDP lub poświadczenia użytkownika przy użyciu poleceń cmdlet Azure PowerShell.

Możesz również znaleźć kroki rozwiązywania problemów z maszynami wirtualnymi utworzonymi przy użyciu [klasycznego modelu wdrażania](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu Azure Portal
Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie można nawiązać połączenia, wypróbuj następny krok.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów Resetuje konfigurację RDP, gdy połączenia zdalne są wyłączone lub reguły zapory systemu Windows blokują protokół RDP.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **Resetuj hasło** . Ustaw **tryb** **resetowania tylko konfiguracji** , a następnie kliknij przycisk **Aktualizuj** :
   
    ![Zresetuj konfigurację R D P w Azure Portal.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Sprawdź reguły sieciowej grupy zabezpieczeń**. Użyj [weryfikacji przepływu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) , aby potwierdzić, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące zasady grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń "Zezwalaj" istnieje i ma priorytet dla portu RDP (domyślnie 3389). Aby uzyskać więcej informacji, zobacz Używanie obowiązujących [reguł zabezpieczeń w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Przejrzyj diagnostykę rozruchu maszyn wirtualnych**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny wirtualnej w celu ustalenia, czy maszyna wirtualna zgłasza problem. Nie wszystkie maszyny wirtualne mają włączoną diagnostykę rozruchu, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale mogą wskazywać szerszy problem wpływający na łączność z protokołem RDP. Aby uzyskać więcej informacji na temat przeglądania dzienników konsoli i zrzutu ekranu maszyny wirtualnej, zobacz [Diagnostyka rozruchu dla maszyn wirtualnych](boot-diagnostics.md).

4. **Zresetuj kartę sieciową dla maszyny wirtualnej**. Aby uzyskać więcej informacji, zobacz [jak zresetować kartę sieciową dla maszyny wirtualnej platformy Azure z systemem Windows](./reset-network-interface.md).
5. **Sprawdź Resource Health maszyny wirtualnej**. Ten krok rozwiązywania problemów sprawdza, czy nie występują żadne znane problemy z platformą Azure, które mogą mieć wpływ na łączność z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **kondycja zasobu** . Raporty maszyny wirtualnej w dobrej kondycji jako **dostępne**:
   
    ![Sprawdź kondycję zasobów V M w Azure Portal.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Zresetuj poświadczenia użytkownika**. Ten krok rozwiązywania problemów resetuje hasło na koncie administratora lokalnego, gdy nie masz pewności lub nie pamiętasz poświadczeń.  Po zalogowaniu się do maszyny wirtualnej należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **Resetuj hasło** . Upewnij się, że **tryb** jest ustawiony na **Resetowanie hasła** , a następnie wprowadź nazwę użytkownika i nowe hasło. Na koniec kliknij przycisk **Aktualizuj** :
   
    ![Zresetuj poświadczenia użytkownika w Azure Portal.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów może rozwiązać wszelkie podstawowe problemy związane z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w Azure Portal i kliknij kartę **Przegląd** . Kliknij przycisk **Uruchom ponownie** :
   
    ![Ponownie uruchom V M w Azure Portal.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Wdróż ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów polega na ponownym wdrożeniu maszyny wirtualnej na innym hoście na platformie Azure w celu poprawienia wszystkich podstawowych problemów z platformą lub siecią.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **Wdróż** ponownie, a następnie kliknij pozycję **ponowne wdrożenie**:
   
    ![Wdróż ponownie maszynę wirtualną w Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po zakończeniu tej operacji dane o dyskach tymczasowych zostaną utracone i zostaną zaktualizowane dynamiczne adresy IP skojarzone z maszyną wirtualną.

9. **Sprawdź Routing**. Użyj funkcji [następnego przeskoku](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher, aby upewnić się, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące trasy, aby zobaczyć wszystkie efektywne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z efektywnych tras w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-routing-problem.md).

10. Upewnij się, że Zapora lokalna lub Zapora na komputerze zezwala na ruch wychodzący TCP 3389 do platformy Azure.

Jeśli nadal występują problemy z protokołem RDP, możesz [otworzyć żądanie pomocy technicznej](https://azure.microsoft.com/support/options/) lub zapoznać się z [bardziej szczegółowymi pojęciami dotyczącymi rozwiązywania problemów z](detailed-troubleshoot-rdp.md)protokołem RDP.

## <a name="troubleshoot-using-azure-powershell"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
Jeśli jeszcze tego nie zrobiono, [Zainstaluj i skonfiguruj najnowszą Azure PowerShell](/powershell/azure/).

W poniższych przykładach użyto zmiennych, takich jak `myResourceGroup` , `myVM` , i `myVMAccessExtension` . Zastąp te nazwy zmiennych i lokalizacje własnymi wartościami.

> [!NOTE]
> Należy zresetować poświadczenia użytkownika i konfigurację protokołu RDP przy użyciu polecenia cmdlet [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) programu PowerShell. W poniższych przykładach `myVMAccessExtension` to nazwa określona jako część procesu. Jeśli wcześniej pracowałeś z VMAccessAgent, możesz uzyskać nazwę istniejącego rozszerzenia za pomocą polecenia, `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` Aby sprawdzić właściwości maszyny wirtualnej. Aby wyświetlić nazwę, zapoznaj się z sekcją "rozszerzenia" w danych wyjściowych.

Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie można nawiązać połączenia, wypróbuj następny krok.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów Resetuje konfigurację RDP, gdy połączenia zdalne są wyłączone lub reguły zapory systemu Windows blokują protokół RDP.
   
    W poniższym przykładzie zostanie zresetowane połączenie RDP na maszynie wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Sprawdź reguły sieciowej grupy zabezpieczeń**. Ten krok rozwiązywania problemów sprawdza, czy masz regułę w sieciowej grupie zabezpieczeń, aby zezwolić na ruch RDP. Domyślnym portem protokołu RDP jest port TCP 3389. Reguła zezwalająca na ruch RDP może nie zostać utworzona automatycznie podczas tworzenia maszyny wirtualnej.
   
    Najpierw Przypisz do zmiennej wszystkie dane konfiguracyjne dla sieciowej grupy zabezpieczeń `$rules` . Poniższy przykład pobiera informacje o sieciowej grupie zabezpieczeń o nazwie `myNetworkSecurityGroup` w grupie zasobów o nazwie `myResourceGroup` :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Teraz Przejrzyj reguły, które są skonfigurowane dla tej sieciowej grupy zabezpieczeń. Sprawdź, czy reguła istnieje, aby zezwolić na port TCP 3389 dla połączeń przychodzących w następujący sposób:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Poniższy przykład pokazuje prawidłową regułę zabezpieczeń, która zezwala na ruch RDP. Możesz zobaczyć `Protocol` ,, `DestinationPortRange` `Access` , i `Direction` są poprawnie skonfigurowane:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Jeśli nie masz reguły, która zezwala na ruch RDP, [Utwórz regułę sieciowej grupy zabezpieczeń](../windows/nsg-quickstart-powershell.md). Zezwalaj na port TCP 3389.
3. **Zresetuj poświadczenia użytkownika**. Ten krok rozwiązywania problemów resetuje hasło do konta administratora lokalnego określonego w przypadku braku pewności lub zapomniane poświadczenia.
   
    Najpierw określ nazwę użytkownika i nowe hasło, przypisując poświadczenia do `$cred` zmiennej w następujący sposób:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teraz zaktualizuj poświadczenia na maszynie wirtualnej. Poniższy przykład aktualizuje poświadczenia na maszynie wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów może rozwiązać wszelkie podstawowe problemy związane z maszyną wirtualną.
   
    Poniższy przykład powoduje ponowne uruchomienie maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Wdróż ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów polega na ponownym wdrożeniu maszyny wirtualnej na innym hoście na platformie Azure w celu poprawienia wszystkich podstawowych problemów z platformą lub siecią.
   
    Poniższy przykład ponownie wdraża maszynę wirtualną o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup` :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Sprawdź Routing**. Użyj funkcji [następnego przeskoku](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher, aby upewnić się, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące trasy, aby zobaczyć wszystkie efektywne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z efektywnych tras w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-routing-problem.md).

7. Upewnij się, że Zapora lokalna lub Zapora na komputerze zezwala na ruch wychodzący TCP 3389 do platformy Azure.

Jeśli nadal występują problemy z protokołem RDP, możesz [otworzyć żądanie pomocy technicznej](https://azure.microsoft.com/support/options/) lub zapoznać się z [bardziej szczegółowymi pojęciami dotyczącymi rozwiązywania problemów z](detailed-troubleshoot-rdp.md)protokołem RDP.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Rozwiązywanie problemów z maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów Resetuje konfigurację RDP, gdy połączenia zdalne są wyłączone lub reguły zapory systemu Windows blokują protokół RDP.
   
    Wybierz maszynę wirtualną w Azure Portal. Kliknij przycisk **... Przycisk więcej** , a następnie kliknij pozycję **Zresetuj dostęp zdalny**:
   
    ![Zresetuj konfigurację R D P w Azure Portal dla V M przy użyciu klasycznego modelu wdrażania.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Sprawdź Cloud Services punkty końcowe**. Ten krok rozwiązywania problemów sprawdza, czy masz punkty końcowe w Cloud Services, aby zezwolić na ruch RDP. Domyślnym portem protokołu RDP jest port TCP 3389. Reguła zezwalająca na ruch RDP może nie zostać utworzona automatycznie podczas tworzenia maszyny wirtualnej.
   
   Wybierz maszynę wirtualną w Azure Portal. Kliknij przycisk **punkty końcowe** , aby wyświetlić punkty końcowe aktualnie skonfigurowane dla maszyny wirtualnej. Sprawdź, czy istnieją punkty końcowe, które zezwalają na ruch RDP na porcie TCP 3389.
   
   W poniższym przykładzie przedstawiono prawidłowe punkty końcowe, które zezwalają na ruch RDP:
   
   ![Sprawdź, Cloud Services punkty końcowe w Azure Portal dla V M przy użyciu klasycznego modelu wdrażania.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Jeśli nie masz punktu końcowego, który zezwala na ruch RDP, [Utwórz punkt końcowy Cloud Services](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). Zezwalaj na TCP na port prywatny 3389.
3. **Przejrzyj diagnostykę rozruchu maszyn wirtualnych**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny wirtualnej w celu ustalenia, czy maszyna wirtualna zgłasza problem. Nie wszystkie maszyny wirtualne mają włączoną diagnostykę rozruchu, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale mogą wskazywać szerszy problem wpływający na łączność z protokołem RDP. Aby uzyskać więcej informacji na temat przeglądania dzienników konsoli i zrzutu ekranu maszyny wirtualnej, zobacz [Diagnostyka rozruchu dla maszyn wirtualnych](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Sprawdź Resource Health maszyny wirtualnej**. Ten krok rozwiązywania problemów sprawdza, czy nie występują żadne znane problemy z platformą Azure, które mogą mieć wpływ na łączność z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **Resource Health** . Raporty maszyny wirtualnej w dobrej kondycji jako **dostępne**:
   
    ![Sprawdź kondycję zasobów V M w Azure Portal dla V M przy użyciu klasycznego modelu wdrażania.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Zresetuj poświadczenia użytkownika**. Ten krok rozwiązywania problemów resetuje hasło na koncie administratora lokalnego określonego w przypadku braku pewności lub zapomniania poświadczeń.  Po zalogowaniu się do maszyny wirtualnej należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę wirtualną w Azure Portal. Przewiń w dół okienko ustawienia do sekcji **Pomoc techniczna i rozwiązywanie problemów** w dolnej części listy. Kliknij przycisk **Resetuj hasło** . Wprowadź nazwę użytkownika i nowe hasło. Na koniec kliknij przycisk **Zapisz** :
   
    ![Zresetuj poświadczenia użytkownika w Azure Portal dla V M przy użyciu klasycznego modelu wdrażania.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów może rozwiązać wszelkie podstawowe problemy związane z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w Azure Portal i kliknij kartę **Przegląd** . Kliknij przycisk **Uruchom ponownie** :
   
    ![Uruchom ponownie V M w Azure Portal dla V M przy użyciu klasycznego modelu wdrażania.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Upewnij się, że Zapora lokalna lub Zapora na komputerze zezwala na ruch wychodzący TCP 3389 do platformy Azure.

Jeśli nadal występują problemy z protokołem RDP, możesz [otworzyć żądanie pomocy technicznej](https://azure.microsoft.com/support/options/) lub zapoznać się z [bardziej szczegółowymi pojęciami dotyczącymi rozwiązywania problemów z](detailed-troubleshoot-rdp.md)protokołem RDP.

## <a name="troubleshoot-specific-rdp-errors"></a>Rozwiązywanie problemów dotyczących konkretnych błędów protokołu RDP
Podczas próby nawiązania połączenia z maszyną wirtualną za pośrednictwem protokołu RDP może wystąpić określony komunikat o błędzie. Poniżej przedstawiono najczęstsze komunikaty o błędach:

* [Sesja zdalna została rozłączona z powodu braku dostępnych serwerów licencji pulpit zdalny w celu udostępnienia licencji](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Pulpit zdalny nie może znaleźć komputera "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają](troubleshoot-specific-rdp-errors.md#wincred).
* [Ten komputer nie może nawiązać połączenia z komputerem zdalnym](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Dodatkowe zasoby
Jeśli żaden z tych błędów nie wystąpi i nadal nie można nawiązać połączenia z maszyną wirtualną za pośrednictwem Pulpit zdalny, przeczytaj szczegółowy [Przewodnik rozwiązywania problemów dotyczących pulpit zdalny](detailed-troubleshoot-rdp.md).
* Aby uzyskać informacje na temat rozwiązywania problemów z dostępem do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Jeśli masz problemy z używaniem Secure Shell (SSH) do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną z systemem Linux na platformie Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).

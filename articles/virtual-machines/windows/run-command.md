---
title: Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej platformy Azure z systemem Windows przy użyciu Uruchamianie polecenia wirtualnej
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 3271f5461447439772b656b8927a54057c8b0c7e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786409"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows przy użyciu Uruchamianie polecenia

Funkcja Uruchamianie polecenia używa agenta maszyny wirtualnej do uruchamiania skryptów programu PowerShell na maszynie wirtualnej z systemem Windows na platformie Azure. Za pomocą tych skryptów można ogólnie zarządzać maszynami lub aplikacjami. Mogą one pomóc w szybkim diagnozowaniu i rozwiązywaniu problemów z dostępem do maszyny wirtualnej i siecią oraz przywróceniu maszyny wirtualnej do dobrego stanu.



## <a name="benefits"></a>Korzyści

Dostęp do maszyn wirtualnych można uzyskać na wiele sposobów. Uruchamianie polecenia uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny wirtualnej. Usługi można Uruchamianie polecenia za pośrednictwem interfejsu Azure Portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub programu [PowerShell dla](/powershell/module/az.compute/invoke-azvmruncommand) maszyn wirtualnych z systemem Windows.

Ta funkcja jest przydatna we wszystkich scenariuszach, w których chcesz uruchomić skrypt na maszynie wirtualnej. Jest to jeden z jedynych sposobów rozwiązywania problemów i korygowania maszyny wirtualnej, która nie ma otwartego portu RDP lub SSH z powodu nieprawidłowej konfiguracji sieci lub użytkownika administracyjnego.

## <a name="restrictions"></a>Ograniczenia

W przypadku korzystania z usługi Uruchamianie polecenia obowiązują następujące Uruchamianie polecenia:

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów.
* Minimalny czas uruchamiania skryptu wynosi około 20 sekund.
* Skrypty są uruchamiane jako System w systemie Windows.
* W tym samym czasie można uruchomić jeden skrypt.
* Skrypty, które monitują o informacje (tryb interaktywny), nie są obsługiwane.
* Nie można anulować uruchomionego skryptu.
* Maksymalny czas uruchamiania skryptu to 90 minut. Po tym czasie przejdą.
* Łączność wychodząca z maszyny wirtualnej jest wymagana do zwrócenia wyników skryptu.
* Nie zaleca się uruchamiania skryptu, który spowoduje zatrzymanie lub aktualizację agenta maszyny wirtualnej. Może to pozwolić rozszerzeniu na przejście w stan Przejścia, co prowadzi do przeoczania limitu czasu.

> [!NOTE]
> Aby funkcja działała poprawnie, Uruchamianie polecenia połączenia (port 443) z publicznymi adresami IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą zostać uruchomione pomyślnie, ale nie będą zwracać wyników. Jeśli blokujesz ruch na maszynie wirtualnej, [](../../virtual-network/network-security-groups-overview.md#service-tags) możesz użyć tagów usługi, aby zezwolić na ruch do publicznych adresów IP platformy Azure przy użyciu `AzureCloud` tagu .

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Windows. Możesz użyć polecenia **RunPowerShellScript,** aby uruchomić dowolny skrypt niestandardowy. Jeśli używasz interfejsu wiersza polecenia platformy Azure lub programu PowerShell do uruchamiania polecenia, wartość podaną dla parametru lub musi być jedną z `--command-id` `-CommandId` następujących wartości na liście. Po określeniu wartości, która nie jest dostępnym poleceniem, zostanie wyświetlony ten błąd:

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**Uruchamianie kodu PowerShellScript**|Uruchamia skrypt programu PowerShell.|
|**EnableRemotePS**|Konfiguruje maszynę tak, aby włączyć zdalny program PowerShell.|
|**EnableAdminAccount**|Sprawdza, czy konto administratora lokalnego jest wyłączone, a jeśli tak, to umożliwia.|
|**Ipconfig**| Przedstawia szczegółowe informacje dotyczące adresu IP, maski podsieci i bramy domyślnej dla każdej karty powiązanej z protokołem TCP/IP.|
|**RDPSettings**|Sprawdza ustawienia rejestru i ustawienia zasad domeny. Sugeruje akcje zasad, jeśli maszyna jest częścią domeny lub modyfikuje ustawienia na wartości domyślne.|
|**ResetRDPCert**|Usuwa certyfikat protokołu TLS/SSL powiązany z odbiornikem protokołu RDP i przywraca domyślne zabezpieczenia odbiornika protokołu RDP. Użyj tego skryptu, jeśli widzisz problemy z certyfikatem.|
|**SetRDPPort**|Ustawia domyślny lub określony przez użytkownika numer portu dla Pulpit zdalny połączeń. Włącza reguły zapory dla dostępu przychodzącego do portu.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie użyto [polecenia az vm run-command,](/cli/azure/vm/run-command#az_vm_run_command_invoke) aby uruchomić skrypt powłoki na maszynie wirtualnej platformy Azure z systemem Windows.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com) i wybierz polecenie **Uruchom w** obszarze **OPERACJE.** Zostanie wyświetlona lista dostępnych poleceń do uruchomienia na maszynie wirtualnej.

![Lista poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą mieć opcjonalne lub wymagane parametry wejściowe. W przypadku tych poleceń parametry są prezentowane jako pola tekstowe w celu podania wartości wejściowych. Dla każdego polecenia można wyświetlić uruchamiany skrypt, rozwijając polecenie **Wyświetl skrypt**. **Skrypt RunPowerShellScript** różni się od innych poleceń, ponieważ umożliwia podanie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowanych poleceń nie można edytować.

Po wybraniu polecenia wybierz pozycję **Uruchom,** aby uruchomić skrypt. Po zakończeniu skrypt zwraca dane wyjściowe i wszelkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe uruchomienia **polecenia RDPSettings.**

![Uruchamianie danych wyjściowych skryptu polecenia](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

W poniższym przykładzie użyto polecenia cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) do uruchomienia skryptu programu PowerShell na maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje, że skrypt przywołyty w parametrze będzie lokalny dla miejsca, w którym polecenie `-ScriptPath` cmdlet jest uruchamiane.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do uruchamiania polecenia

Wyświetlanie listy poleceń uruchamiania lub wyświetlanie szczegółów polecenia wymaga `Microsoft.Compute/locations/runCommands/read` uprawnień na poziomie subskrypcji. Wbudowana rola [Czytelnik](../../role-based-access-control/built-in-roles.md#reader) i wyższe poziomy mają to uprawnienie.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnień. Rola [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i wyższe poziomy mają to uprawnienie.

Możesz użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub utworzyć rolę niestandardową, aby użyć Uruchamianie polecenia. [](../../role-based-access-control/custom-roles.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych sposobach zdalnego uruchamiania skryptów i poleceń na maszynie wirtualnej, zobacz Uruchamianie skryptów na maszynie [wirtualnej z systemem Windows.](run-scripts-in-vm.md)

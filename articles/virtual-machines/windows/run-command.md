---
title: Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej z systemem Windows Azure przy użyciu funkcji run polecenia
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: eac6201f45b11cae223e2293644bd9d0144e6e31
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203358"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom

Funkcja Run command używa agenta maszyny wirtualnej do uruchamiania skryptów programu PowerShell na maszynie wirtualnej z systemem Windows Azure. Za pomocą tych skryptów można zarządzać ogólnymi maszynami lub aplikacjami. Mogą one pomóc w szybkim zdiagnozowaniu i rozpoczęciu problemów z dostępem do maszyn wirtualnych i rozpoczęciu pracy z powrotem do dobrego stanu.



## <a name="benefits"></a>Korzyści

Dostęp do maszyn wirtualnych można uzyskać na wiele sposobów. Polecenie Uruchom może zdalnie uruchamiać skrypty na maszynach wirtualnych za pomocą agenta maszyny wirtualnej. Używasz polecenia Run za pośrednictwem Azure Portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub [programu PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) dla maszyn wirtualnych z systemem Windows.

Ta funkcja jest przydatna we wszystkich scenariuszach, w których chcesz uruchomić skrypt w ramach maszyny wirtualnej. Jest to jeden z jedynych sposobów rozwiązywania problemów i korygowania maszyny wirtualnej, która nie ma otwartego portu RDP lub protokołu SSH z powodu nieprawidłowej konfiguracji sieci lub użytkownika administracyjnego.

## <a name="restrictions"></a>Ograniczenia

W przypadku korzystania z polecenia Uruchom należy zastosować następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4 096 bajtów.
* Minimalny czas uruchomienia skryptu wynosi około 20 sekund.
* Skrypty są uruchamiane jako system w systemie Windows.
* Można uruchomić jeden skrypt jednocześnie.
* Skrypty, które monitują o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu.
* Maksymalny czas uruchomienia skryptu to 90 minut. Po tym czasie zostanie przekroczony limit czasu.
* Łączność wychodząca z maszyny wirtualnej jest wymagana do zwrócenia wyników skryptu.
* Nie zaleca się uruchamiania skryptu, który spowoduje zatrzymanie lub aktualizację agenta maszyny wirtualnej. Może to pozwolić na rozszerzenie stanu przejścia, co prowadzi do przekroczenia limitu czasu.

> [!NOTE]
> Aby działało prawidłowo, polecenie Run wymaga łączności (port 443) z publicznymi adresami IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać pomyślnie, ale nie zwracają wyników. W przypadku blokowania ruchu na maszynie wirtualnej można użyć [tagów usługi](../../virtual-network/network-security-groups-overview.md#service-tags) , aby zezwolić na ruch do publicznych adresów IP platformy Azure przy użyciu `AzureCloud` znacznika.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Windows. Możesz użyć polecenia **RunPowerShellScript** , aby uruchomić dowolny niestandardowy skrypt. Gdy korzystasz z interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby uruchomić polecenie, wartość podana dla `--command-id` parametru lub `-CommandId` musi być jedną z następujących wartości. Jeśli określisz wartość, która nie jest dostępnym poleceniem, zostanie wyświetlony następujący błąd:

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**RunPowerShellScript**|Uruchamia skrypt programu PowerShell.|
|**EnableRemotePS**|Konfiguruje maszynę w celu włączenia zdalnego programu PowerShell.|
|**EnableAdminAccount**|Sprawdza, czy konto administratora lokalnego jest wyłączone, a jeśli to możliwe, należy je włączyć.|
|**IPConfig**| Przedstawia szczegółowe informacje o adresie IP, masce podsieci i bramie domyślnej dla każdej karty powiązanej z protokołem TCP/IP.|
|**RDPSettings**|Sprawdza ustawienia rejestru i ustawienia zasad domeny. Sugeruje akcje zasad, jeśli komputer jest częścią domeny lub modyfikuje ustawienia do wartości domyślnych.|
|**ResetRDPCert**|Usuwa certyfikat TLS/SSL powiązany z odbiornikiem RDP i przywraca domyślne zabezpieczenia odbiornika RDP. Użyj tego skryptu, Jeśli zobaczysz problemy z certyfikatem.|
|**SetRDPPort**|Ustawia domyślny lub określony przez użytkownika numer portu dla połączeń Pulpit zdalny. Włącza reguły zapory dla przychodzącego dostępu do portu.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie za pomocą polecenia [AZ VM Run-command](/cli/azure/vm/run-command#az-vm-run-command-invoke) można uruchomić skrypt powłoki na maszynie wirtualnej z systemem Windows Azure.

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

Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Uruchom polecenie** w obszarze **operacje**. Zostanie wyświetlona lista dostępnych poleceń do uruchomienia na maszynie wirtualnej.

![Lista poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą zawierać opcjonalne lub wymagane parametry wejściowe. W przypadku tych poleceń parametry są prezentowane jako pola tekstowe umożliwiające podanie wartości wejściowych. Dla każdego polecenia można wyświetlić skrypt, który jest uruchamiany, rozszerzając **skrypt widoku**. **RunPowerShellScript** różni się od innych poleceń, ponieważ umożliwia udostępnianie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia wybierz pozycję **Uruchom** , aby uruchomić skrypt. Po zakończeniu działania skryptu zwraca dane wyjściowe i wszystkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe polecenia **RDPSettings** .

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

W poniższym przykładzie za pomocą polecenia cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) można uruchomić skrypt programu PowerShell na maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje skryptu, do którego istnieje odwołanie w `-ScriptPath` parametrze, do lokalizacji, w której jest uruchamiane polecenie cmdlet.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Run

Wyświetlanie listy poleceń uruchamiania lub wyświetlanie szczegółów polecenia wymaga `Microsoft.Compute/locations/runCommands/read` uprawnienia na poziomie subskrypcji. To uprawnienie ma wbudowaną rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższe poziomy.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia. Rola [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i wyższe poziomy mają to uprawnienie.

Możesz użyć jednej z [wbudowanych ról](../../role-based-access-control/built-in-roles.md) lub utworzyć [rolę niestandardową](../../role-based-access-control/custom-roles.md) , aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych metod zdalnego uruchamiania skryptów i poleceń na maszynie wirtualnej, zobacz [Uruchamianie skryptów na maszynie wirtualnej z systemem Windows](run-scripts-in-vm.md).

---
title: Uruchamianie skryptów powłoki na maszynie wirtualnej z systemem Linux na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów w maszynie wirtualnej platformy Azure z systemem Linux przy użyciu funkcji run polecenia
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 73dd15a5eed3e27d9b72bc0357e35901c04ba7a2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552936"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Uruchamianie skryptów powłoki na maszynie wirtualnej z systemem Linux przy użyciu polecenia Run

Funkcja Uruchamianie polecenia używa agenta maszyny wirtualnej do uruchamiania skryptów powłoki w ramach maszyny wirtualnej platformy Azure z systemem Linux. Za pomocą tych skryptów można ogólnie zarządzać maszynami lub aplikacjami. Mogą one pomóc w szybkim zdiagnozowaniu i rozpoczęciu problemów z dostępem do maszyn wirtualnych i rozpoczęciu pracy z powrotem do dobrego stanu.

## <a name="benefits"></a>Korzyści

Dostęp do maszyn wirtualnych można uzyskać na wiele sposobów. Polecenie Uruchom może zdalnie uruchamiać skrypty na maszynach wirtualnych za pomocą agenta maszyny wirtualnej. Używasz polecenia Run za pośrednictwem Azure Portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub [wiersza polecenia platformy Azure](/cli/azure/vm/run-command#az_vm_run_command_invoke) dla maszyn wirtualnych z systemem Linux.

Ta funkcja jest przydatna we wszystkich scenariuszach, w których chcesz uruchomić skrypt w ramach maszyny wirtualnej. Jest to jeden z jedynych sposobów rozwiązywania problemów i korygowania maszyny wirtualnej, która nie ma otwartego portu RDP lub protokołu SSH z powodu nieprawidłowej konfiguracji sieci lub użytkownika administracyjnego.

## <a name="restrictions"></a>Ograniczenia

W przypadku korzystania z polecenia Uruchom należy zastosować następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4 096 bajtów.
* Minimalny czas uruchomienia skryptu wynosi około 20 sekund.
* Skrypty są domyślnie uruchamiane jako użytkownicy z podwyższonym poziomem uprawnień w systemie Linux.
* Można uruchomić jeden skrypt jednocześnie.
* Skrypty, które monitują o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu.
* Maksymalny czas uruchomienia skryptu to 90 minut. Po tym czasie skrypt zostanie przekroczony.
* Łączność wychodząca z maszyny wirtualnej jest wymagana do zwrócenia wyników skryptu.

> [!NOTE]
> Aby działało prawidłowo, polecenie Run wymaga łączności (port 443) z publicznymi adresami IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać pomyślnie, ale nie zwracają wyników. W przypadku blokowania ruchu na maszynie wirtualnej można użyć [tagów usługi](../../virtual-network/network-security-groups-overview.md#service-tags) , aby zezwolić na ruch do publicznych adresów IP platformy Azure przy użyciu `AzureCloud` znacznika.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Linux. Możesz użyć polecenia **RunShellScript** , aby uruchomić dowolny niestandardowy skrypt. Gdy korzystasz z interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby uruchomić polecenie, wartość podana dla `--command-id` parametru lub `-CommandId` musi być jedną z następujących wartości. Jeśli określisz wartość, która nie jest dostępnym poleceniem, zostanie wyświetlony następujący błąd:

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**RunShellScript**|Uruchamia skrypt powłoki systemu Linux.|
|**ifconfig**| Pobiera konfigurację wszystkich interfejsów sieciowych.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie za pomocą polecenia [AZ VM Run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) można uruchomić skrypt powłoki na maszynie wirtualnej platformy Azure z systemem Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Aby uruchomić polecenia jako inny użytkownik, wprowadź, `sudo -u` Aby określić konto użytkownika.

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Uruchom polecenie** w obszarze **operacje**. Zostanie wyświetlona lista dostępnych poleceń do uruchomienia na maszynie wirtualnej.

![Lista poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą zawierać opcjonalne lub wymagane parametry wejściowe. W przypadku tych poleceń parametry są prezentowane jako pola tekstowe umożliwiające podanie wartości wejściowych. Dla każdego polecenia można wyświetlić skrypt, który jest uruchamiany, rozszerzając **skrypt widoku**. **RunShellScript** różni się od innych poleceń, ponieważ umożliwia udostępnianie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia wybierz pozycję **Uruchom** , aby uruchomić skrypt. Po zakończeniu działania skryptu zwraca dane wyjściowe i wszystkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe polecenia **ifconfig** .

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

W poniższym przykładzie za pomocą polecenia cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) można uruchomić skrypt programu PowerShell na maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje skryptu, do którego istnieje odwołanie w `-ScriptPath` parametrze, do lokalizacji, w której jest uruchamiane polecenie cmdlet.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do uruchamiania polecenia

Wyświetlanie listy poleceń uruchamiania lub wyświetlanie szczegółów polecenia wymaga `Microsoft.Compute/locations/runCommands/read` uprawnienia. To uprawnienie ma wbudowaną rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższe poziomy.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia. Rola [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i wyższe poziomy mają to uprawnienie.

Możesz użyć jednej z [wbudowanych ról](../../role-based-access-control/built-in-roles.md) lub utworzyć [rolę niestandardową](../../role-based-access-control/custom-roles.md) , aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych metod zdalnego uruchamiania skryptów i poleceń na maszynie wirtualnej, zobacz [Uruchamianie skryptów na maszynie wirtualnej z systemem Linux](run-scripts-in-vm.md).

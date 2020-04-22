---
title: Uruchamianie skryptów powłoki na maszynie Wirtualnej systemu Linux na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów na maszynie wirtualnej systemu Azure Linux przy użyciu funkcji Uruchom polecenie
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758606"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Uruchamianie skryptów powłoki na maszynie wirtualnej systemu Linux przy użyciu polecenia Uruchom

Funkcja Uruchom polecenie używa agenta maszyny wirtualnej (VM) do uruchamiania skryptów powłoki w ramach maszyny wirtualnej systemu Azure Linux. Tych skryptów można używać do ogólnego zarządzania maszynami lub aplikacjami. Mogą one pomóc szybko zdiagnozować i rozwiązać problemy z dostępem do maszyn wirtualnych i sieci i przywrócić maszynę wirtualną do dobrego stanu.

## <a name="benefits"></a>Zalety

Dostęp do maszyn wirtualnych można uzyskać na wiele sposobów. Uruchom polecenie można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny wirtualnej. Polecenia Uruchom można za pośrednictwem witryny Azure portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub platformy Azure [CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) dla maszyn wirtualnych z systemem Linux.

Ta funkcja jest przydatna we wszystkich scenariuszach, w których chcesz uruchomić skrypt w maszynie wirtualnej. Jest to jeden z niewielu sposobów rozwiązywania problemów i korygowanie maszyny wirtualnej, która nie ma otwartego portu RDP lub SSH z powodu niewłaściwej konfiguracji sieci lub użytkownika administracyjnego.

## <a name="restrictions"></a>Ograniczenia

Podczas korzystania z polecenia Uruchom polecenie obowiązują następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów.
* Minimalny czas uruchamiania skryptu wynosi około 20 sekund.
* Skrypty są domyślnie uruchamiane jako podwyższony poziom użytkownika w systemie Linux.
* Można uruchomić jeden skrypt naraz.
* Skrypty monitowane o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu.
* Maksymalny czas uruchomienia skryptu wynosi 90 minut. Po tym, skrypt będzie limit czasu.
* Łączność wychodząca z maszyny Wirtualnej jest wymagana do zwrócenia wyników skryptu.

> [!NOTE]
> Aby działać poprawnie, Uruchom polecenie wymaga łączności (port 443) do publicznych adresów IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać pomyślnie, ale nie zwracają wyników. Jeśli blokujesz ruch na maszynie wirtualnej, możesz użyć [tagów usługi,](../../virtual-network/security-overview.md#service-tags) aby `AzureCloud` zezwolić na ruch na publicznych adresach IP platformy Azure przy użyciu tagu.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Linux. Za pomocą polecenia **RunShellScript** można uruchomić dowolny skrypt niestandardowy. Podczas korzystania z interfejsu wiersza polecenia platformy Azure lub programu PowerShell `--command-id` `-CommandId` do uruchamiania polecenia, wartość, która jest podasz dla lub parametr musi być jedną z następujących wymienionych wartości. Po określeniu wartości, która nie jest dostępnym poleceniem, pojawia się ten błąd:

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**Kod RunShellScript**|Uruchamia skrypt powłoki systemu Linux.|
|**Ifconfig**| Pobiera konfigurację wszystkich interfejsów sieciowych.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie użyto polecenia [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) do uruchomienia skryptu powłoki na maszynie wirtualnej systemu Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Aby uruchomić polecenia jako inny `sudo -u` użytkownik, wprowadź, aby określić konto użytkownika.

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny Wirtualnej w [witrynie Azure portal](https://portal.azure.com) i wybierz **polecenie Uruchom w** obszarze **OPERACJE**. Widoczna jest lista dostępnych poleceń do uruchomienia na maszynie Wirtualnej.

![Lista poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą mieć opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są prezentowane jako pola tekstowe, aby zapewnić wartości wejściowe. Dla każdego polecenia można wyświetlić skrypt uruchamiany przez rozwinięcie **skryptu Widok**. **RunShellScript** różni się od innych poleceń, ponieważ umożliwia dostarczenie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia wybierz pozycję **Uruchom,** aby uruchomić skrypt. Po zakończeniu skryptu zwraca dane wyjściowe i wszelkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe z uruchomienia polecenia **ifconfig.**

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

W poniższym przykładzie użyto polecenia cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) do uruchomienia skryptu programu PowerShell na maszynie Wirtualnej platformy Azure. Polecenie cmdlet oczekuje, że skrypt, do którego odwołuje się `-ScriptPath` parametr, będzie lokalny, do którego jest uruchamiane polecenie cmdlet.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Wyświetlanie poleceń uruchamiania lub wyświetlanie szczegółów `Microsoft.Compute/locations/runCommands/read` polecenia wymaga uprawnienia na poziomie subskrypcji. Wbudowana rola [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższe poziomy mają to uprawnienie.

Uruchamianie polecenia `Microsoft.Compute/virtualMachines/runCommand/action` wymaga uprawnienia na poziomie subskrypcji. Rola [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i wyższe poziomy mają to uprawnienie.

Można użyć jednej z [wbudowanych ról](../../role-based-access-control/built-in-roles.md) lub utworzyć [rolę niestandardową,](../../role-based-access-control/custom-roles.md) aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych sposobach zdalnego uruchamiania skryptów i poleceń na maszynie wirtualnej, zobacz [Uruchamianie skryptów na maszynie Wirtualnej systemu Linux](run-scripts-in-vm.md).

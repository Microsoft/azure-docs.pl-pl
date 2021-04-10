---
title: Instalowanie Endpoint Protection firmy Symantec na maszynie wirtualnej z systemem Windows na platformie Azure
description: Dowiedz się, jak zainstalować i skonfigurować rozszerzenie zabezpieczeń firmy Symantec Endpoint Protection na nowej lub istniejącej maszynie wirtualnej platformy Azure utworzonej przy użyciu klasycznego modelu wdrażania.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/31/2017
ms.openlocfilehash: 9a25e9eb27111a450f787f4efb3e0d39456fc757
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559651"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak zainstalować i skonfigurować Endpoint Protection firmy Symantec na maszynie wirtualnej z systemem Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W tym artykule opisano sposób instalowania i konfigurowania klienta programu Symantec Endpoint Protection na istniejącej maszynie wirtualnej z systemem Windows Server. Ten pełny klient zawiera usługi, takie jak ochrona przed wirusami i programami szpiegującymi, Zapora i Zapobieganie włamaniom. Klient programu jest instalowany jako rozszerzenie zabezpieczeń przy użyciu agenta maszyny wirtualnej.

Jeśli masz istniejącą subskrypcję firmy Symantec dla rozwiązania lokalnego, możesz użyć jej do ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś jeszcze klientem, możesz zarejestrować się w celu uzyskania subskrypcji wersji próbnej. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz artykuł [Symantec Endpoint Protection na platformie Azure platformy Microsoft][Symantec]. Ta strona zawiera również linki do informacji licencyjnych i instrukcje dotyczące instalowania klienta programu, jeśli jesteś już klientem firmy Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalowanie Endpoint Protection firmy Symantec na istniejącej maszynie wirtualnej
Przed rozpoczęciem należy wykonać następujące czynności:

* Moduł Azure PowerShell w wersji 0.8.2 lub nowszej na komputerze służbowym. Możesz sprawdzić wersję Azure PowerShell, która została zainstalowana za pomocą polecenia **Get-module platformy Azure | format-Table Version** . Instrukcje i link do najnowszej wersji znajdują się w temacie [How to Install and Configure Azure PowerShell][PS]. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Add-AzureAccount` .
* Agent maszyny wirtualnej uruchomiony na maszynie wirtualnej platformy Azure.

Najpierw upewnij się, że Agent maszyny wirtualnej jest już zainstalowany na maszynie wirtualnej. Wypełnij pola Nazwa usługi w chmurze i nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia Azure PowerShell na poziomie administratora. Zamień wszystkie elementy w cudzysłowie, w tym znaki < i >.

> [!TIP]
> Jeśli nie znasz nazw usług w chmurze i maszyn wirtualnych, uruchom polecenie **Get-AzureVM** , aby wyświetlić listę nazw wszystkich maszyn wirtualnych w bieżącej subskrypcji.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli polecenie **write-host** wyświetli **wartość true**, Agent maszyny wirtualnej jest zainstalowany. Jeśli wartość jest **równa false**, zapoznaj się z instrukcjami i linkiem do pobrania w blogu Azure post [VM Agent i Extensions-część 2][Agent].

Jeśli Agent maszyny wirtualnej jest zainstalowany, uruchom następujące polecenia, aby zainstalować agenta Endpoint Protection firmy Symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Aby sprawdzić, czy rozszerzenie zabezpieczeń firmy Symantec zostało zainstalowane i jest aktualne:

1. Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [Jak zalogować się do maszyny wirtualnej z systemem Windows Server][Logon].
2. W przypadku systemu Windows Server 2008 R2 kliknij przycisk **Start > Symantec Endpoint Protection**. W przypadku systemu Windows Server 2012 lub Windows Server 2012 R2 na ekranie startowym wpisz **Symantec**, a następnie kliknij pozycję **Symantec Endpoint Protection**.
3. Na karcie **stan** okna **stanu — Symantec Endpoint Protection** Zastosuj aktualizacje lub Uruchom ponownie, jeśli jest to wymagane.

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server][Logon]

[Rozszerzenia i funkcje maszyny wirtualnej platformy Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md

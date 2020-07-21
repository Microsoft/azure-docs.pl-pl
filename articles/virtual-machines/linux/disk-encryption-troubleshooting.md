---
title: Azure Disk Encryption rozwiązywania problemów z maszynami wirtualnymi systemu Linux
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z Microsoft Azure szyfrowaniem dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: abd802f19917b048f6d006b8e3097b08efaf22e2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510484"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z maszynami wirtualnymi z systemem Linux Azure Disk Encryption

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji i administratorów chmury, których organizacje używają Azure Disk Encryption. Ten artykuł ma na celu pomoc w rozwiązywaniu problemów związanych z szyfrowaniem dysków.

Przed wykonaniem poniższych czynności należy najpierw upewnić się, że maszyny wirtualne, które mają zostać zaszyfrowane, znajdują się wśród [obsługiwanych rozmiarów maszyn wirtualnych i w systemach operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems)oraz że zostały spełnione wszystkie wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Rozwiązywanie problemów z szyfrowaniem dysków systemu operacyjnego Linux

Szyfrowanie dysków systemu operacyjnego Linux musi odinstalować dysk systemu operacyjnego przed uruchomieniem go przez pełny proces szyfrowania dysku. Jeśli nie można odinstalować dysku, komunikat o błędzie "nie można odinstalować po..." prawdopodobnie wystąpi.

Ten błąd może wystąpić, gdy podjęto próbę szyfrowania dysku systemu operacyjnego na maszynie wirtualnej ze środowiskiem, które zostało zmienione z obsługiwanego obrazu galerii zasobów. Odchylenia od obsługiwanego obrazu mogą zakłócać zdolność rozszerzenia do odinstalowania dysku systemu operacyjnego. Przykłady odchyleń mogą zawierać następujące elementy:
- Dostosowane obrazy nie są już zgodne z obsługiwanym systemem plików ani schematem partycjonowania.
- Duże aplikacje, takie jak SAP, MongoDB, Apache Cassandra i Docker nie są obsługiwane, gdy są instalowane i uruchamiane w systemie operacyjnym przed szyfrowaniem. Azure Disk Encryption nie jest w stanie bezpiecznie zamknąć tych procesów zgodnie z wymaganiami w przygotowaniu dysku systemu operacyjnego do szyfrowania dysków. Jeśli nadal istnieją aktywne procesy przechowujące uchwyty plików otwartych na dysku systemu operacyjnego, nie można odinstalować dysku systemu operacyjnego, co spowodowało uszkodzenie dysku systemu operacyjnego. 
- Niestandardowe skrypty, które są uruchamiane w czasie bliskości do włączonego szyfrowania, lub jeśli w trakcie procesu szyfrowania są wprowadzane inne zmiany na maszynie wirtualnej. Ten konflikt może wystąpić, gdy szablon Azure Resource Manager definiuje wiele rozszerzeń do wykonania jednocześnie lub gdy rozszerzenie niestandardowego skryptu lub inna akcja jest uruchamiany jednocześnie w celu szyfrowania dysku. Serializacja i izolowanie takich kroków może rozwiązać ten problem.
- Ulepszona ochrona systemu Linux (SELinux) nie została wyłączona przed włączeniem szyfrowania, więc krok dezinstalacji nie powiedzie się. SELinux można ponownie włączyć po zakończeniu szyfrowania.
- Dysk systemu operacyjnego używa schematu menedżera woluminów logicznych (LVM). Mimo że dostępna jest ograniczona Obsługa dysków danych LVM, dysk systemu operacyjnego LVM nie jest.
- Minimalne wymagania dotyczące pamięci nie są spełnione (w przypadku szyfrowania dysków systemu operacyjnego zaleca się 7 GB).
- Dyski danych są rekursywnie zainstalowane w katalogu/mnt/lub nawzajem (na przykład/mnt/data1,/mnt/DATA2,/DATA3 +/DATA3/DATA4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Zaktualizuj domyślne jądro dla Ubuntu 14,04 LTS

Obraz Ubuntu 14,04 LTS jest dostarczany z domyślną wersją jądra 4,4. W tej wersji jądra występuje znany problem polegający na tym, że zbyt mało pamięci, aby nie przerywać polecenia DD w trakcie procesu szyfrowania systemu operacyjnego. Ten błąd został rozwiązany w najnowszej jądrze systemu Linux z systemem Azure. Aby uniknąć tego błędu, przed włączeniem szyfrowania obrazu należy zaktualizować do [systemu Azure dostrojone jądro 4,15](https://packages.ubuntu.com/trusty/linux-azure) lub nowsze przy użyciu następujących poleceń:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po ponownym uruchomieniu maszyny wirtualnej w nowym jądrze można potwierdzić nową wersję jądra przy użyciu:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualizuj agenta maszyny wirtualnej platformy Azure i wersje rozszerzeń

Operacje Azure Disk Encryption mogą kończyć się niepowodzeniem w obrazach maszyn wirtualnych przy użyciu nieobsługiwanych wersji agenta maszyny wirtualnej platformy Azure. Obrazy systemu Linux z wersjami agenta wcześniejszą niż 2.2.38 należy zaktualizować przed włączeniem szyfrowania. Aby uzyskać więcej informacji, zobacz [jak zaktualizować agenta systemu Linux na maszynie wirtualnej](../extensions/update-linux-agent.md) i [minimalną wersję dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Wymagana jest również prawidłowa wersja rozszerzenia agenta gościa Microsoft. Azure. Security. AzureDiskEncryption lub Microsoft. Azure. Security. AzureDiskEncryptionForLinux. Wersje rozszerzeń są obsługiwane i aktualizowane automatycznie przez platformę, gdy spełnione są wymagania wstępne agenta maszyny wirtualnej platformy Azure i jest używana obsługiwana wersja agenta maszyny wirtualnej.

Rozszerzenie Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux jest przestarzałe i nie jest już obsługiwane.  

## <a name="unable-to-encrypt-linux-disks"></a>Nie można zaszyfrować dysków systemu Linux

W niektórych przypadkach szyfrowanie dysku z systemem Linux jest prawdopodobnie zablokowane z "szyfrowanie dysków systemu operacyjnego rozpoczęte", a protokół SSH jest wyłączony. Proces szyfrowania może zająć od 3-16 godzin do końca obrazu galerii giełdowej. W przypadku dodania dysków z danymi o rozmiarze wieloterabajtowym proces może zająć kilka dni.

Sekwencja szyfrowania dysków systemu operacyjnego Linux tymczasowo Odinstalowuje dysk systemu operacyjnego. Następnie wykonuje szyfrowanie całego dysku systemu operacyjnego przed jego ponownym zainstalowaniem w stanie zaszyfrowanego. Szyfrowanie dysków systemu Linux nie zezwala na współbieżne korzystanie z maszyny wirtualnej, gdy szyfrowanie jest w toku. Charakterystyka wydajności maszyny wirtualnej może spowodować znaczącą różnicę czasu wymaganego do ukończenia szyfrowania. Te cechy obejmują rozmiar dysku i czy konto magazynu jest magazynem standardowym lub Premium (SSD).

Aby sprawdzić stan szyfrowania, należy wykonać sondowanie pola **komunikat dotyczący postępu** zwróconego za pomocą polecenia [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Gdy dysk systemu operacyjnego jest szyfrowany, maszyna wirtualna przechodzi w stan obsługi i wyłącza protokół SSH, aby zapobiec ewentualnemu zakłóceniowi trwającemu procesowi. Komunikat **EncryptionInProgress** jest raportowany przez większość czasu, podczas gdy szyfrowanie jest w toku. Kilka godzin później komunikat **VMRestartPending** monit o ponowne uruchomienie maszyny wirtualnej. Na przykład:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Po wyświetleniu monitu o ponowne uruchomienie maszyny wirtualnej, gdy maszyna wirtualna zostanie ponownie uruchomiona, należy poczekać 2-3 minut na ponowny rozruch i w przypadku końcowych kroków, które mają zostać wykonane na elemencie docelowym. Komunikat o stanie zmienia się, gdy szyfrowanie jest ostatecznie zakończone. Po udostępnieniu tego komunikatu zaszyfrowaną dysk systemu operacyjnego będzie gotowy do użycia, a maszyna wirtualna będzie gotowa do użycia ponownie.

W następujących przypadkach zaleca się przywrócenie maszyny wirtualnej z powrotem do migawki lub kopii zapasowej wykonanej bezpośrednio przed szyfrowaniem:
   - Jeśli sekwencja ponownego uruchamiania opisana wcześniej, nie wystąpi.
   - Jeśli w trakcie tego procesu szyfrowanie systemu operacyjnego zakończyło się niepowodzeniem, w trakcie działania nie powiodło się, gdy wystąpił błąd w trakcie wykonywania tej operacji. Przykładem komunikatu jest błąd "nie można odinstalować" opisany w tym przewodniku.

Przed kolejną próbą należy ponownie oszacować charakterystykę maszyny wirtualnej i upewnić się, że spełnione są wszystkie wymagania wstępne.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów Azure Disk Encryption za zaporą

Zobacz [szyfrowanie dysków w sieci izolowanej](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

W portalu może być wyświetlany dysk zaszyfrowany nawet po zaszyfrowaniu go w ramach maszyny wirtualnej.  Taka sytuacja może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego wyszyfrowania dysku z poziomu maszyny wirtualnej, a nie przy użyciu Azure Disk Encryption polecenia zarządzania wyższego poziomu.  Polecenia wyższego poziomu nie tylko deszyfrują dysk z maszyny wirtualnej, ale poza maszyną wirtualną również aktualizują ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one zachowane, platforma nie będzie mogła zgłosić stanu szyfrowania ani zainicjować obsługi maszyny wirtualnej.   

Aby wyłączyć Azure Disk Encryption za pomocą programu PowerShell, użyj polecenia [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) , a następnie polecenie [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć Azure Disk Encryption przy użyciu interfejsu wiersza polecenia, użyj polecenie [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto więcej informacji o niektórych typowych problemach w Azure Disk Encryption i sposobach rozwiązywania tych problemów. Aby uzyskać więcej informacji o tej usłudze i jej możliwościach, zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w Azure Security Center](../../security-center/security-center-virtual-machine-protection.md)
- [Szyfrowanie danych magazynowanych platformy Azure](../../security/fundamentals/encryption-atrest.md)

---
title: Przykładowe skrypty usługi Azure Disk Encryption
description: Ten artykuł zawiera dodatek do Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: f11677d9ebc31f1c1f7cc6332b07b69f8e35ad52
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561181"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Azure Disk Encryption przykładowe skrypty dla maszyn wirtualnych z systemem Linux

Ten artykuł zawiera przykładowe skrypty do przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.  

> [!NOTE]
> Wszystkie skrypty odwołują się do najnowszej wersji programu ADE, która nie jest usługą AAD, z wyjątkiem sytuacji, w której zaznaczono.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Przykładowe skrypty programu PowerShell dla Azure Disk Encryption 

- **Wyświetl listę wszystkich szyfrowanych maszyn wirtualnych w ramach subskrypcji**
  
  Wszystkie maszyny wirtualne i wersje rozszerzeń można znaleźć we wszystkich grupach zasobów dostępnych w ramach subskrypcji przy użyciu [tego skryptu programu PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Alternatywnie te polecenia cmdlet będą wyświetlać wszystkie maszyny wirtualne zaszyfrowane przez ADE (ale nie wersję rozszerzenia):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Wyświetl wszystkie zaszyfrowane wystąpienia VMSS w ramach subskrypcji**
    
    Wszystkie wystąpienia VMSS i wersja rozszerzenia można znaleźć we wszystkich grupach zasobów dostępnych w ramach subskrypcji przy użyciu [tego skryptu programu PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).

- **Wyświetl listę wszystkich kluczy tajnych szyfrowania dysku używanych do szyfrowania maszyn wirtualnych w magazynie kluczy** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Korzystanie z skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych
Jeśli masz już doświadczenie z wymaganiami wstępnymi dotyczącymi Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Przykład korzystania z tego skryptu programu PowerShell można znaleźć w temacie [szyfrowanie maszyny wirtualnej — szybki start](disk-encryption-powershell-quickstart.md). Możesz usunąć komentarze z sekcji skryptu, zaczynając od wiersza 211, aby zaszyfrować wszystkie dyski dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono parametry, których można użyć w skrypcie programu PowerShell: 


|Parametr|Opis|Wypełnione?|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy Magazyn kluczy.  Jeśli jeszcze nie istnieje, zostanie utworzona nowa grupa zasobów o tej nazwie.| Prawda|
|$keyVaultName|Nazwa magazynu kluczy, w którym mają zostać umieszczone klucze szyfrowania. Nowy magazyn o tej nazwie zostanie utworzony, jeśli taki nie istnieje.| Prawda|
|$location|Lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które mają być szyfrowane, znajdują się w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|Prawda|
|$subscriptionId|Identyfikator subskrypcji platformy Azure, która ma zostać użyta.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|Prawda|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie używana do zapisywania wpisów tajnych w magazynie kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, Przekaż parametr aadClientSecret do skryptu.|Fałsz|
|$aadClientSecret|Wpis tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|Fałsz|
|$keyEncryptionKeyName|Nazwa opcjonalnego klucza szyfrowania klucza w magazynie kluczy. Nowy klucz o tej nazwie zostanie utworzony, jeśli taki nie istnieje.|Fałsz|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych bez aplikacji usługi Azure AD

- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzednia wersja)
 
- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux. 


- [Utwórz nowy zaszyfrowany dysk zarządzany na podstawie wstępnie zaszyfrowanego obiektu BLOB dysku VHD/magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowy zaszyfrowany dysk zarządzany przy użyciu wstępnie zaszyfrowanego wirtualnego dysku twardego i odpowiednich ustawień szyfrowania

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Szyfrowanie dysku systemu operacyjnego na działającej maszynie wirtualnej z systemem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysków systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji zgodnej z szyfrowaniem dysków systemu operacyjnego, która jest wymieniona w [Azure Disk Encryption obsługiwanych systemach operacyjnych](disk-encryption-overview.md#supported-vms) 
* Maszyna wirtualna musi zostać utworzona na podstawie obrazu z witryny Marketplace w Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączenie SELinux "w [podręczniku użytkownika SELinux i administratora](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie wirtualnej.
* Po wyłączeniu SELinux należy ponownie uruchomić maszynę wirtualną co najmniej raz.

### <a name="steps"></a>Kroki
1. Utwórz maszynę wirtualną przy użyciu jednej z określonych wcześniej dystrybucji.

   W przypadku CentOS 7,2 szyfrowanie dysków systemu operacyjnego jest obsługiwane za pośrednictwem obrazu specjalnego. Aby użyć tego obrazu, należy określić "7.2 n" jako jednostkę SKU podczas tworzenia maszyny wirtualnej:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Skonfiguruj maszynę wirtualną zgodnie z potrzebami. Jeśli chcesz zaszyfrować wszystkie dyski z systemem (OS + Data), dyski danych muszą być określone i instalowalne z katalogu/etc/fstab.

   > [!NOTE]
   > Użyj identyfikatora UUID =... Aby określić dyski danych w/etc/fstab zamiast określać nazwę urządzenia blokowego (na przykład/dev/sdb1). Podczas szyfrowania kolejność dysków zmienia się na maszynie wirtualnej. Jeśli maszyna wirtualna korzysta z określonej kolejności blokowania urządzeń, nie będzie można jej zainstalować po zaszyfrowaniu.

3. Wyloguj się z sesji SSH.

4. Aby zaszyfrować system operacyjny, określ wartość volumetype jako **All** lub **OS** po włączeniu szyfrowania.

   > [!NOTE]
   > Wszystkie procesy miejsca użytkownika, które nie są uruchomione jako `systemd` usługi, powinny być zabite z `SIGKILL` . Uruchom ponownie maszynę wirtualną. Po włączeniu szyfrowania dysków systemu operacyjnego na uruchomionej maszynie wirtualnej Zaplanuj przestoje maszyny wirtualnej.

5. Okresowo Monitoruj postęp szyfrowania przy użyciu instrukcji w [następnej sekcji](#monitoring-os-encryption-progress).

6. Po Get-AzVmDiskEncryptionStatus Pokaż "VMRestartPending", uruchom ponownie maszynę wirtualną, logując się do niej lub korzystając z portalu, programu PowerShell lub interfejsu wiersza polecenia.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Przed ponownym uruchomieniem zalecamy zapisanie [diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny wirtualnej.

## <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Postęp szyfrowania systemu operacyjnego można monitorować na trzy sposoby:

* Użyj `Get-AzVmDiskEncryptionStatus` polecenia cmdlet i sprawdź pole komunikat dotyczący postępu:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Po osiągnięciu przez maszynę wirtualną "rozpoczęto szyfrowanie dysku systemu operacyjnego, zajmie od 40 do 50 minut na maszynie wirtualnej z magazynem w warstwie Premium.

  Ze względu na [problem #388](https://github.com/Azure/WALinuxAgent/issues/388) w programie WALinuxAgent `OsVolumeEncrypted` i `DataVolumesEncrypted` przedstawiono je `Unknown` w niektórych dystrybucjach. W programie WALinuxAgent w wersji 2.1.5 lub nowszej ten problem został rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, możesz zweryfikować stan szyfrowania dysku przy użyciu Azure Resource Explorer.

  Przejdź do [Azure Resource Explorer](https://resources.azure.com/), a następnie rozwiń tę hierarchię w panelu wyboru po lewej stronie:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  W InstanceView przewiń w dół, aby wyświetlić stan szyfrowania dysków.

  ![Widok wystąpienia maszyny wirtualnej](./media/disk-encryption/vm-instanceview.png)

* Spójrz na [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzenia ADE powinny być poprzedzone prefiksem `[AzureDiskEncryption]` .

* Zaloguj się do maszyny wirtualnej za pośrednictwem protokołu SSH i Pobierz dziennik rozszerzeń z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Zalecamy, aby nie logować się do maszyny wirtualnej, podczas gdy szyfrowanie systemu operacyjnego jest w toku. Skopiuj dzienniki tylko wtedy, gdy inne dwie metody zakończyły się niepowodzeniem.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux
Przygotowanie wstępnie zaszyfrowanych dysków VHD może się różnić w zależności od rozkładu. Przykłady dotyczące przygotowywania Ubuntu 16, openSUSE 13,2 i CentOS 7 są dostępne. 

### <a name="ubuntu-16"></a>Ubuntu 16
Skonfiguruj szyfrowanie podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz opcję **Konfiguruj woluminy szyfrowane** podczas partycjonowania dysków.

   ![Konfiguracja Ubuntu 16,04 — Konfigurowanie szyfrowanych woluminów](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Utwórz oddzielny dysk rozruchowy, który nie może być zaszyfrowany. Zaszyfruj dysk główny.

   ![Konfiguracja programu Ubuntu 16,04 — wybierz urządzenia do zaszyfrowania](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło przekazane do magazynu kluczy.

   ![Instalator Ubuntu 16,04 — Podaj hasło](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończenie partycjonowania.

   ![Konfiguracja Ubuntu 16,04 — zakończenie partycjonowania](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Podczas uruchamiania maszyny wirtualnej i monitowania o podanie hasła należy użyć hasła podanego w kroku 3.

   ![Instalator Ubuntu 16,04 — Podaj hasło przy rozruchu](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotuj maszynę wirtualną do przekazania do platformy Azure, korzystając z [tych instrukcji](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

Skonfiguruj szyfrowanie do pracy z platformą Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze/usr/local/sbin/azure_crypt_key. sh z zawartością w poniższym skrypcie. Zwróć uwagę na nazwę pliku, ponieważ jest to nazwa pliku hasła używanego przez platformę Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Zmień konfigurację Crypt w */etc/crypttab*. Powinien on wyglądać następująco:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Dodaj uprawnienia do pliku wykonywalnego do skryptu:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edytuj */etc/initramfs-tools/modules* przez dołączenie wierszy:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Uruchom, `update-initramfs -u -k all` Aby zaktualizować initramfs, aby `keyscript` zastosować efekt.

7. Teraz można anulować obsługę administracyjną maszyny wirtualnej.

   ![Konfiguracja Ubuntu 16,04-Update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do następnego kroku i przekaż dysk VHD na platformę Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Podczas partycjonowania dysków wybierz pozycję **Szyfruj grupę woluminów**, a następnie wprowadź hasło. Jest to hasło, które zostanie przekazane do magazynu kluczy.

   ![Konfiguracja openSUSE 13,2 — Zaszyfruj grupę woluminów](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Wykonaj rozruch maszyny wirtualnej przy użyciu hasła.

   ![Instalator openSUSE 13,2 — Podaj hasło przy rozruchu](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotuj MASZYNę wirtualną do przekazania na platformę Azure, postępując zgodnie z instrukcjami w temacie [przygotowanie maszyny wirtualnej SLES lub openSUSE dla platformy Azure](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj/etc/Dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Dodaj komentarz do tych wierszy na końcu pliku/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Dołącz następujący wiersz na początku pliku/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zmień wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh i dołącz go do "# Open LUKS Device":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Uruchom, `/usr/sbin/dracut -f -v` Aby zaktualizować oryginalnych initrd.

6. Teraz możesz anulować obsługę administracyjną maszyny wirtualnej i przekazać dysk VHD na platformę Azure.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 i RHEL 7

Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz opcję **Szyfruj moje dane** podczas partycjonowania dysków.

   ![Instalacja CentOS 7 — miejsce docelowe instalacji](./media/disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że wybrano **szyfrowanie** dla partycji głównej.

   ![Instalator programu CentOS 7 — wybierz pozycję Szyfruj dla partycji głównej](./media/disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, które zostanie przekazane do magazynu kluczy.

   ![Instalator CentOS 7 — Podaj hasło](./media/disk-encryption/centos-encrypt-fig3.png)

4. Podczas uruchamiania maszyny wirtualnej i monitowania o podanie hasła należy użyć hasła podanego w kroku 3.

   ![Instalator CentOS 7 — Wprowadź hasło w rozruchu](./media/disk-encryption/centos-encrypt-fig4.png)

5. Przygotuj maszynę wirtualną do przekazania na platformę Azure, używając instrukcji "CentOS 7.0 +" w temacie [przygotowanie maszyny wirtualnej opartej na CentOS dla platformy Azure](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

6. Teraz możesz anulować obsługę administracyjną maszyny wirtualnej i przekazać dysk VHD na platformę Azure.

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj/etc/Dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Dodaj komentarz do tych wierszy na końcu pliku/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Dołącz następujący wiersz na początku pliku/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zmień wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na wartość
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh i Dołącz następujące elementy po "# Open LUKS Device":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Uruchom "/usr/sbin/Dracut-f-v", aby zaktualizować oryginalnych initrd.

    ![Konfiguracja programu CentOS 7 — Uruchamianie/usr/sbin/Dracut-f-v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego wirtualnego dysku twardego do konta usługi Azure Storage
Po włączeniu szyfrowania DM-Crypt lokalny zaszyfrowany dysk VHD musi zostać przekazany do konta magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekaż klucz tajny wstępnie zaszyfrowanej maszyny wirtualnej do magazynu kluczy
Podczas szyfrowania przy użyciu aplikacji usługi Azure AD (poprzednia wersja) klucz tajny szyfrowania dysku, który uzyskano wcześniej, musi zostać przekazany jako wpis tajny w magazynie kluczy. Magazyn kluczy musi mieć włączone szyfrowanie dysków i uprawnienia dla klienta usługi Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku nie został zaszyfrowany za pomocą elementu KEK
Aby skonfigurować wpis tajny w magazynie kluczy, użyj polecenie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Hasło jest kodowane jako ciąg Base64, a następnie przekazywane do magazynu kluczy. Ponadto należy się upewnić, że następujące znaczniki są ustawione podczas tworzenia wpisu tajnego w magazynie kluczy.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Użyj `$secretUrl` w następnym kroku w celu [dołączenia dysku systemu operacyjnego bez użycia KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku zaszyfrowany za pomocą KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy można opcjonalnie go zaszyfrować przy użyciu klucza szyfrowania klucza. Użyj [interfejsu API](/rest/api/keyvault/wrapkey) Otocz, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania to ciąg zakodowany przez adres URL w formacie Base64, który można następnie przekazać jako wpis tajny przy użyciu [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) polecenia cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego za pomocą KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określ tajny adres URL podczas dołączania dysku systemu operacyjnego

###  <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas dołączania dysku systemu operacyjnego należy przekazać `$secretUrl` . Adres URL został wygenerowany w sekcji "wpis tajny dysku bez szyfrowania za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Korzystanie z KEK
Po dołączeniu dysku systemu operacyjnego, należy przejść `$KeyEncryptionKey` i `$secretUrl` . Ten adres URL został wygenerowany w sekcji "klucz tajny szyfrowania dysku zaszyfrowany za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```

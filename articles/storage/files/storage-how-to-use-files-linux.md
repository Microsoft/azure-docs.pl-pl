---
title: Używanie Azure Files z systemem Linux | Microsoft Docs
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pośrednictwem protokołu SMB w systemie Linux. Zapoznaj się z listą wymagań wstępnych. Przejrzyj zagadnienia dotyczące zabezpieczeń protokołu SMB na klientach z systemem Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 957e827e621d07ed9b5533a1607f955f05985d9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004786"
---
# <a name="use-azure-files-with-linux"></a>Używanie usługi Azure Files z systemem Linux
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu [klienta jądra SMB](https://wiki.samba.org/index.php/LinuxCIFS). W tym artykule przedstawiono dwa sposoby instalowania udziału plików platformy Azure: na żądanie z `mount` poleceniem i przy rozruchu, tworząc wpis w `/etc/fstab` .

Zalecanym sposobem instalowania udziału plików platformy Azure w systemie Linux jest użycie protokołu SMB 3,0. Domyślnie Azure Files wymaga szyfrowania podczas przesyłania, które jest obsługiwane tylko przez protokół SMB 3,0. Azure Files obsługuje również protokół SMB 2,1, który nie obsługuje szyfrowania podczas przesyłania, ale nie może instalować udziałów plików platformy Azure z użyciem protokołu SMB 2,1 z innego regionu platformy Azure lub lokalnego ze względów bezpieczeństwa. O ile aplikacja nie wymaga protokołu SMB 2,1, z jakiegoś powodu większość popularnych, ostatnio wydanych dystrybucji systemu Linux obsługuje protokół SMB 3,0:  

| Dystrybucja systemu Linux | SMB 2.1 <br>(Instalacja na maszynach wirtualnych w ramach tego samego regionu platformy Azure) | SMB 3.0 <br>(Instalacje z poziomu lokalnego i obejmującego wiele regionów) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12+ | 12 Z DODATKIEM SP2 + |

Jeśli używasz dystrybucji systemu Linux, która nie jest wymieniona w powyższej tabeli, możesz sprawdzić, czy w systemie Linux jest obsługiwana obsługa protokołu SMB 3,0 z szyfrowaniem. Protokół SMB 3,0 z szyfrowaniem został dodany do jądra systemu Linux w wersji 4,11. `uname`Polecenie zwróci wersję jądra systemu Linux w użyciu:

```bash
uname -r
```

## <a name="prerequisites"></a>Wymagania wstępne
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Upewnij się, że pakiet CIFS-narzędzia jest zainstalowany.**  
    Pakiet CIFS-narzędzia można zainstalować przy użyciu Menedżera pakietów na wybranej dystrybucji systemu Linux. 

    W przypadku dystrybucji **opartych** na **Ubuntu** i Debian należy użyć `apt` Menedżera pakietów:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    W **Fedora**, **Red Hat Enterprise Linux 8 +** i **CentOS 8 +**, użyj `dnf` Menedżera pakietów:

    ```bash
    sudo dnf install cifs-utils
    ```

    W starszych wersjach **Red Hat Enterprise Linux** i **CentOS**Użyj `yum` Menedżera pakietów:

    ```bash
    sudo yum install cifs-utils 
    ```

    W systemie **openSUSE**Użyj `zypper` Menedżera pakietów:

    ```bash
    sudo zypper install cifs-utils
    ```

    W przypadku innych dystrybucji Użyj odpowiedniego Menedżera pakietów lub [Skompiluj ze źródła](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Najnowsza wersja interfejsu wiersza polecenia platformy Azure (CLI).** Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i wybieranie systemu operacyjnego. Jeśli wolisz używać modułu Azure PowerShell w programie PowerShell 6 lub nowszym, możesz jednak przedstawić poniższe instrukcje dla interfejsu wiersza polecenia platformy Azure.

* **Upewnij się, że port 445 jest otwarty**: protokół SMB komunikuje się za pośrednictwem portu TCP 445 — Sprawdź, czy Zapora nie blokuje portów TCP 445 z komputera klienckiego.  Zastąp `<your-resource-group>` , a `<your-storage-account>` następnie uruchom następujący skrypt:
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Jeśli połączenie zakończyło się pomyślnie, powinien zostać wyświetlony komunikat podobny do następującego:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Jeśli nie możesz otworzyć portu 445 w sieci firmowej lub jest on blokowany przez usługodawcę internetowego, możesz użyć połączenia sieci VPN lub ExpressRoute, aby obejść port 445. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące sieci w przypadku bezpośredniego dostępu do udziału plików platformy Azure](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Instalowanie udziału plików platformy Azure
Aby używać udziału plików platformy Azure z dystrybucją systemu Linux, należy utworzyć katalog, który będzie służyć jako punkt instalacji dla udziału plików platformy Azure. Punkt instalacji można utworzyć w dowolnym miejscu w systemie Linux, ale jest to typowa Konwencja do utworzenia tego elementu w ramach/mnt. Po punkcie instalacji należy użyć `mount` polecenia, aby uzyskać dostęp do udziału plików platformy Azure.

W razie potrzeby można zainstalować ten sam udział plików platformy Azure w wielu punktach instalacji.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Zainstaluj udział plików platformy Azure na żądanie z `mount`
1. **Utwórz folder dla punktu instalacji**: Zastąp `<your-resource-group>` , `<your-storage-account>` , i `<your-file-share>` z odpowiednimi informacjami dla środowiska:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Użyj polecenia mount, aby zainstalować udział plików platformy Azure**. W poniższym przykładzie domyślne uprawnienia do plików i folderów lokalnego systemu Linux 0755, czyli odczyt, zapis i wykonywanie dla właściciela (na podstawie właściciela pliku/katalogu systemu Linux), Odczytaj i wykonaj dla użytkowników w grupie właścicieli, a następnie odczytaj i wykonaj dla innych w systemie. Możesz użyć `uid` `gid` opcji instalacji i ustawić identyfikator użytkownika i identyfikator grupy dla instalacji. `dir_mode`W razie potrzeby można również użyć i `file_mode` skonfigurować uprawnienia niestandardowe. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz [notacja numeryczna systemu UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) w witrynie Wikipedia. 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Powyższe polecenie instalacji instaluje z protokołem SMB 3,0. Jeśli dystrybucja systemu Linux nie obsługuje protokołu SMB 3,0 z szyfrowaniem lub obsługuje protokół SMB 2,1, można zainstalować tylko z maszyny wirtualnej platformy Azure w tym samym regionie, w którym znajduje się konto magazynu. Aby zainstalować udział plików platformy Azure w dystrybucji systemu Linux, która nie obsługuje protokołu SMB 3,0 z szyfrowaniem, należy [wyłączyć szyfrowanie podczas przesyłania dla konta magazynu](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Po zakończeniu korzystania z udziału plików platformy Azure Możesz użyć `sudo umount $mntPath` programu do odinstalowania udziału.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Utwórz trwały punkt instalacji dla udziału plików platformy Azure `/etc/fstab`
1. **Utwórz folder dla punktu instalacji**: folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, ale jest to wspólna Konwencja do utworzenia tego elementu w obszarze/mnt. Na przykład następujące polecenie tworzy nowy katalog, zastępuje `<your-resource-group>` , `<your-storage-account>` i `<your-file-share>` z odpowiednimi informacjami dla środowiska:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Utwórz plik poświadczeń, aby zapisać nazwę użytkownika (nazwę konta magazynu) i hasło (klucz konta magazynu) dla udziału plików.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Zmień uprawnienia do pliku poświadczeń, aby tylko element główny mógł odczytać lub zmodyfikować plik hasła.** Ponieważ klucz konta magazynu jest zasadniczo hasłem administratora hasła dla konta magazynu, ustawienie uprawnień do pliku, tak aby tylko dostęp do katalogu głównego miało znaczenie, jest ważne, aby użytkownicy z niższymi uprawnieniami nie mogli pobrać klucza konta magazynu.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Użyj poniższego polecenia, aby dołączyć następujący wiersz do `/etc/fstab` **programu: w poniższym przykładzie domyślne uprawnienia do plików i folderów lokalnego systemu Linux 0755, które oznaczają odczyt, zapis i wykonywanie dla właściciela (na podstawie właściciela pliku/katalogu Linux), Odczytaj i wykonaj dla użytkowników w grupie właścicieli, a następnie odczytaj i wykonaj dla innych w systemie. Możesz użyć `uid` `gid` opcji instalacji i ustawić identyfikator użytkownika i identyfikator grupy dla instalacji. `dir_mode`W razie potrzeby można również użyć i `file_mode` skonfigurować uprawnienia niestandardowe. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz [notacja numeryczna systemu UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) w witrynie Wikipedia.

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Powyższe polecenie instalacji instaluje z protokołem SMB 3,0. Jeśli dystrybucja systemu Linux nie obsługuje protokołu SMB 3,0 z szyfrowaniem lub obsługuje protokół SMB 2,1, można zainstalować tylko z maszyny wirtualnej platformy Azure w tym samym regionie, w którym znajduje się konto magazynu. Aby zainstalować udział plików platformy Azure w dystrybucji systemu Linux, która nie obsługuje protokołu SMB 3,0 z szyfrowaniem, należy [wyłączyć szyfrowanie podczas przesyłania dla konta magazynu](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Automatyczne instalowanie udziałów plików platformy Azure przy użyciu programu AutoFS

1. **Upewnij się, że pakiet AutoFS został zainstalowany.**  

    Pakiet AutoFS można zainstalować przy użyciu Menedżera pakietów na wybranej dystrybucji systemu Linux. 

    W przypadku dystrybucji **opartych** na **Ubuntu** i Debian należy użyć `apt` Menedżera pakietów:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    W **Fedora**, **Red Hat Enterprise Linux 8 +** i **CentOS 8 +**, użyj `dnf` Menedżera pakietów:
    ```bash
    sudo dnf install autofs
    ```
    W starszych wersjach **Red Hat Enterprise Linux** i **CentOS**Użyj `yum` Menedżera pakietów:
    ```bash
    sudo yum install autofs 
    ```
    W systemie **openSUSE**Użyj `zypper` Menedżera pakietów:
    ```bash
    sudo zypper install autofs
    ```
2. **Utwórz punkt instalacji dla udziałów**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **Crete nowy niestandardowy plik konfiguracyjny AutoFS**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Dodaj następujące wpisy do/etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Dodaj następujący wpis do/etc/auto.Master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Uruchom ponownie AutoFS**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Dostęp do folderu wyznaczono dla udziału**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Zabezpieczanie systemu Linux
Aby można było zainstalować udział plików platformy Azure w systemie Linux, należy uzyskać dostęp do portu 445. Wiele organizacji blokuje port 445 z powodu zagrożeń bezpieczeństwa związanych z protokołem SMB 1. Protokół SMB 1, znany również jako CIFS (Common Internet File System), jest starszym protokołem systemu plików zawartym w wielu dystrybucjach Linux. Protokół SMB 1 jest nieaktualny, nieefektywny i, co najważniejsze, niezabezpieczony. Dobrym pomysłem jest to, że Azure Files nie obsługuje protokołu SMB 1 i począwszy od jądra systemu Linux w wersji 4,18, system Linux umożliwia wyłączenie protokołu SMB 1. Zawsze [zdecydowanie zalecamy](https://aka.ms/stopusingsmb1) wyłączenie protokołu SMB 1 na klientach z systemem Linux przed użyciem udziałów plików SMB w środowisku produkcyjnym.

Począwszy od jądra systemu Linux 4,18, moduł jądra protokołu SMB wywoływany ze `cifs` względu na starsze przyczyny ujawnia nowy parametr modułu (często określany jako *parametr* przez różne dokumenty zewnętrzne) o nazwie `disable_legacy_dialects` . Chociaż wprowadzono je w jądrze systemu Linux 4,18, niektórzy dostawcy przenoszą tę zmianę do starszych jądra, które są przez nie obsługiwane. Dla wygody Poniższa tabela zawiera szczegółowe informacje o dostępności tego parametru modułu w przypadku wspólnych dystrybucji systemu Linux.

| Dystrybucja | Może wyłączyć protokół SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nie |
| Ubuntu 18.04 | Tak |
| Ubuntu 19.04 + | Tak |
| Debian 8-9 | Nie |
| Debian 10 + | Tak |
| Fedora 29 + | Tak |
| CentOS 7 | Nie | 
| CentOS 8 + | Tak |
| Red Hat Enterprise Linux 6. x-7. x | Nie |
| Red Hat Enterprise Linux 8 + | Tak |
| openSUSE przestępnie 15,0 | Nie |
| openSUSE przestępny 15.1 + | Tak |
| openSUSE Tumbleweed | Tak |
| SUSE Linux Enterprise 11. x-12. x | Nie |
| SUSE Linux Enterprise 15 | Nie |
| SUSE Linux Enterprise 15,1 | Nie |

Możesz sprawdzić, czy dystrybucja systemu Linux obsługuje `disable_legacy_dialects` parametr modułu za pomocą następującego polecenia.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

To polecenie powinno wyprowadzić następujący komunikat:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Przed wyłączeniem protokołu SMB 1 należy upewnić się, że moduł SMB nie jest aktualnie załadowany w systemie (dzieje się to automatycznie, jeśli został zainstalowany udział SMB). Można to zrobić za pomocą następującego polecenia, które powinno wynikać nie tylko w przypadku niezaładowania protokołu SMB:

```bash
lsmod | grep cifs
```

Aby zwolnić moduł, najpierw Odinstaluj wszystkie udziały SMB (przy użyciu `umount` polecenia, jak opisano powyżej). Można zidentyfikować wszystkie zainstalowane udziały SMB w systemie przy użyciu następującego polecenia:

```bash
mount | grep cifs
```

Po odinstalowaniu wszystkich udziałów plików SMB bezpieczniej jest zwolnić moduł. W tym celu możesz użyć polecenia `modprobe`:

```bash
sudo modprobe -r cifs
```

Możesz ręcznie załadować moduł z opcją SMB 1 zwolnioną przy użyciu `modprobe` polecenia:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Na koniec można sprawdzić, czy moduł SMB został załadowany za pomocą parametru, przeglądając parametry załadowane w `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Aby trwale wyłączyć protokół SMB 1 w przypadku dystrybucji opartych na Ubuntu i Debian, należy utworzyć nowy plik (jeśli nie masz jeszcze opcji niestandardowych dla innych modułów) wywołanych `/etc/modprobe.d/local.conf` z ustawieniem. Można to zrobić za pomocą następującego polecenia:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Można sprawdzić, czy ten program działał przez załadowanie modułu SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Następne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:

* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów](storage-troubleshoot-linux-file-connection-problems.md)

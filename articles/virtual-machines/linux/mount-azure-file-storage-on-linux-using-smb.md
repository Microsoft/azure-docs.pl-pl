---
title: Instalowanie usługi Azure File Storage na maszynach wirtualnych z systemem Linux przy użyciu protokołu SMB
description: Jak zainstalować usługę Azure File Storage na maszynach wirtualnych z systemem Linux przy użyciu protokołu SMB i interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 6a5d4f388d9e5f47a13812687489525590c520c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016101"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Instalowanie usługi Azure File Storage na maszynach wirtualnych z systemem Linux przy użyciu protokołu SMB

W tym artykule pokazano, jak używać usługi Azure File Storage na maszynie wirtualnej z systemem Linux przy użyciu instalacji protokołu SMB z interfejsem wiersza polecenia platformy Azure. Usługa Azure File Storage oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. 

Usługa File Storage oferuje udziały plików w chmurze, które używają standardowego protokołu SMB. Udział plików można zainstalować z dowolnego systemu operacyjnego, który obsługuje protokół SMB 3,0. W przypadku korzystania z instalacji SMB w systemie Linux można łatwo tworzyć kopie zapasowe w niezawodnej, trwałej lokalizacji magazynu, która jest obsługiwana przez umowę SLA.

Przeniesienie plików z maszyny wirtualnej do instalacji SMB, która jest hostowana w usłudze File Storage, to doskonały sposób na Debugowanie dzienników. Ten sam udział SMB można zainstalować lokalnie na komputerze Mac, w systemie Linux lub na stacji roboczej z systemem Windows. Protokół SMB nie jest najlepszym rozwiązaniem do przesyłania strumieniowego dzienników systemu Linux lub aplikacji w czasie rzeczywistym, ponieważ nie jest on zbudowany w celu obsługi takich dużych obowiązków rejestrowania. Dedykowane, ujednolicone narzędzie do warstwy rejestrowania, takie jak Fluent, będzie lepszym rozwiązaniem niż SMB do zbierania danych wyjściowych systemu Linux i rejestrowania aplikacji.

Ten przewodnik wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów o nazwie Moja *resourceName* w lokalizacji *Wschodnie stany USA* .

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu w utworzonej grupie zasobów przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account). Ten przykład tworzy konto magazynu o nazwie *ciąg mystorageacct \<random number>* i umieszcza nazwę tego konta magazynu w zmiennej **STORAGEACCT**. Nazwy kont magazynu muszą być unikatowe, korzystając z `$RANDOM` dołączania liczby do końca, aby uczynić ją unikatową.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Pobieranie klucza magazynu

Podczas tworzenia konta magazynu klucze konta są tworzone w parach, aby można było je obrócić bez przerwy w działaniu usługi. Po przełączeniu do drugiego klucza w parze należy utworzyć nową parę kluczy. Nowe klucze konta magazynu są zawsze tworzone w parach, więc zawsze masz co najmniej jeden nieużywany klucz konta magazynu gotowy do przełączenia się do.

Wyświetl klucze konta magazynu za pomocą polecenia [AZ Storage account Keys list](/cli/azure/storage/account/keys). W tym przykładzie wartość klucza 1 jest przechowywana w zmiennej **STORAGEKEY** .

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Utwórz udział magazynu plików za pomocą polecenia [AZ Storage Share Create](/cli/azure/storage/share). 

Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą rozpoczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

W tym przykładzie tworzony jest udział *o nazwie GIB* z limitem przydziału 10. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Utwórz punkt instalacji

Aby zainstalować udział plików platformy Azure na komputerze z systemem Linux, należy upewnić się, że jest zainstalowany pakiet **CIFS-** narzędzia. Aby uzyskać instrukcje dotyczące instalacji, zobacz [Instalowanie pakietu CIFS-narzędzia dla dystrybucji systemu Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files używa protokołu SMB, który komunikuje się za pośrednictwem portu TCP 445.  Jeśli masz problemy z instalowaniem udziału plików platformy Azure, upewnij się, że Zapora nie blokuje portu TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Zainstaluj udział

Zainstaluj udział plików platformy Azure w katalogu lokalnym. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Powyższe polecenie używa polecenia [Mount](https://linux.die.net/man/8/mount) do zainstalowania udziału plików platformy Azure i opcji specyficznych dla protokołu [CIFS](https://linux.die.net/man/8/mount.cifs). W każdym przypadku opcje file_mode i dir_mode umożliwiają ustawianie plików i katalogów na uprawnienia `0777` . `0777`Uprawnienie przyznaje uprawnienia Odczyt, zapis i wykonywanie wszystkim użytkownikom. Te uprawnienia można zmienić, zastępując wartości innymi [uprawnieniami chmod](https://en.wikipedia.org/wiki/Chmod). Można również użyć innych opcji [CIFS](https://linux.die.net/man/8/mount.cifs) , takich jak gid lub UID. 


## <a name="persist-the-mount"></a>Utrwalanie instalacji

Po ponownym uruchomieniu maszyny wirtualnej z systemem Linux zainstalowany udział SMB zostanie odinstalowany podczas zamykania. Aby ponownie zainstalować udział SMB przy rozruchu, Dodaj wiersz do katalogu/etc/fstab. systemu Linux System Linux używa pliku fstab, aby wyświetlić listę systemów plików, które muszą zostać zainstalowane podczas procesu rozruchu. Dodanie udziału SMB gwarantuje, że udział magazynu plików jest trwale zainstalowanym systemem plików dla maszyny wirtualnej z systemem Linux. Dodawanie udziału SMB magazynu plików do nowej maszyny wirtualnej jest możliwe w przypadku korzystania z funkcji Cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

W celu zwiększenia bezpieczeństwa w środowiskach produkcyjnych należy przechowywać swoje poświadczenia poza programem fstab.

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie maszyny wirtualnej z systemem Linux podczas tworzenia przy użyciu funkcji Cloud-init](using-cloud-init.md)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md)
- [Usługa Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md)

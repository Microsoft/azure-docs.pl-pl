---
title: Jak zainstalować usługę Azure Blob Storage jako system plików w systemie Linux | Microsoft Docs
description: Dowiedz się, jak zainstalować kontener usługi Azure Blob Storage za pomocą blobfuse, wirtualnego sterownika systemu plików w systemie Linux.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 002e8650a5555b70caf09179e03ce1bad1acdef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737544"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak zainstalować magazyn obiektów BLOB jako system plików za pomocą blobfuse

## <a name="overview"></a>Omówienie
[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownikiem wirtualnego systemu plików usługi Azure Blob Storage. Blobfuse umożliwia dostęp do istniejących danych blokowych obiektów BLOB na koncie magazynu za pomocą systemu plików Linux. Blobfuse używa schematu katalogu wirtualnego z ukośnikiem "/" jako ogranicznika.  

W tym przewodniku przedstawiono sposób korzystania z programu blobfuse i instalowania kontenera magazynu obiektów BLOB w systemie Linux i uzyskiwania dostępu do danych. Aby dowiedzieć się więcej na temat blobfuse, zapoznaj się ze szczegółami w [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nie gwarantuje zgodności z 100% POSIX, ponieważ po prostu tłumaczy żądania na [interfejsy API REST obiektów BLOB](/rest/api/storageservices/blob-service-rest-api). Na przykład operacje zmiany nazwy są niepodzielne w modelu POSIX, ale nie w blobfuse.
> Aby zapoznać się z pełną listą różnic między natywnym systemem plików i blobfuse, odwiedź [repozytorium kodu źródłowego blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Zainstaluj program blobfuse w systemie Linux
Pliki binarne Blobfuse są dostępne w [repozytoriach oprogramowania firmy Microsoft dla systemu Linux](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) na potrzeby dystrybucji Ubuntu, Debian, SUSE, CentoOS, Oracle Linux i RHEL. Aby zainstalować blobfuse na tych dystrybucjach, należy skonfigurować jeden z repozytoriów z listy. Możesz również utworzyć pliki binarne z kodu źródłowego po [wykonaniu kroków instalacji usługi Azure Storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) , jeśli nie ma plików binarnych dostępnych dla dystrybucji.

Blobfuse obsługuje instalację w wersjach Ubuntu: 16,04, 18,04 i 20,04, RHELversions: 7,5, 7,8, 8,0, 8,1, 8,2, CentOS wersje: 7,0, 8,0, Debian wersje: 9,0, 10,0, wersja SUSE: 15, OracleLinux 8,1. Uruchom to polecenie, aby upewnić się, że masz wdrożoną jedną z tych wersji:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Skonfiguruj repozytorium pakietu Microsoft
Skonfiguruj [repozytorium pakietów systemu Linux dla produktów firmy Microsoft](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Przykładowo w dystrybucji Enterprise Linux 8:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
```

Analogicznie Zmień adres URL na tak, aby `.../rhel/7/...` wskazywał dystrybucję Enterprise Linux 7.

Inny przykład na Ubuntu 20,04:
```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Analogicznie, Zmień adres URL na `.../ubuntu/16.04/...` lub, `.../ubuntu/18.04/...` Aby odwołać się do innej wersji Ubuntu.

### <a name="install-blobfuse"></a>Zainstaluj blobfuse

W dystrybucji Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

W dystrybucji systemu Linux w przedsiębiorstwie:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Przygotuj do zainstalowania
Blobfuse zapewnia wydajność podobną do natywną, wymagając ścieżki tymczasowej w systemie plików do buforowania i buforowania wszelkich otwartych plików. W przypadku tej ścieżki tymczasowej wybierz najbardziej wydajny dysk lub użyj dysku Ramdisk w celu uzyskania najlepszej wydajności. 

> [!NOTE]
> Blobfuse przechowuje wszystkie otwarte pliki w tymczasowej ścieżce. Upewnij się, że masz wystarczająco dużo miejsca, aby pomieścić wszystkie otwarte pliki. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Obowiązkowe Użyj dysku Ramdisk dla ścieżki tymczasowej
Poniższy przykład tworzy dysk Ramdisk o 16 GB i katalog dla blobfuse. Wybierz rozmiar na podstawie Twoich potrzeb. Ten dysk Ramdisk umożliwia blobfuse otwieranie plików o rozmiarze do 16 GB. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Używanie dysku SSD jako ścieżki tymczasowej
Na platformie Azure możesz używać dysków tymczasowych (SSD) dostępnych na maszynach wirtualnych, aby zapewnić bufor o małym opóźnieniu dla blobfuse. W dystrybucjach Ubuntu ten dysk nieulotny jest instalowany na "/mnt". W przypadku dystrybucji Red Hat i CentOS dysk jest instalowany na "/mnt/Resource/".

Upewnij się, że użytkownik ma dostęp do ścieżki tymczasowej:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Skonfiguruj poświadczenia konta magazynu
Blobfuse wymaga, aby Twoje poświadczenia były przechowywane w pliku tekstowym w następującym formacie: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName`Jest to prefiks dla konta magazynu — a nie pełny adres URL.

Utwórz ten plik przy użyciu:

```
touch ~/fuse_connection.cfg
```

Po utworzeniu i edytowaniu tego pliku upewnij się, że nie można go odczytać.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Jeśli plik konfiguracji został utworzony w systemie Windows, upewnij się, że uruchomiono `dos2unix` oczyszczanie i konwertowanie pliku do formatu systemu UNIX. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Utwórz pusty katalog do zainstalowania
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Instalacja

> [!NOTE]
> Aby uzyskać pełną listę opcji instalacji, zapoznaj [się z repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Aby zainstalować blobfuse, uruchom następujące polecenie u użytkownika. To polecenie instaluje kontener określony w elemencie "/Path/to/fuse_connection. cfg" w lokalizacji "/mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teraz masz dostęp do blokowych obiektów BLOB za pomocą zwykłych interfejsów API systemu plików. Użytkownik instalujący katalog jest jedyną osobą, która ma do niej dostęp, domyślnie, która zabezpiecza dostęp. Aby zezwolić na dostęp wszystkim użytkownikom, można zainstalować za pomocą opcji ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Następne kroki

* [Strona główna Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Zgłoś problemy blobfuse](https://github.com/Azure/azure-storage-fuse/issues)

---
title: Utwórz wolumin serwera Ubuntu Linux NFS
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak ręcznie utworzyć wolumin serwera Ubuntu Linux NFS do użycia z identyfikatorami platformy Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251998"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ręczne tworzenie i używanie woluminu systemu plików NFS (sieciowy system plików) z systemem Linux za pomocą usługi Azure Kubernetes Service (AKS)
Udostępnianie danych między kontenerami jest często niezbędnym składnikiem usług i aplikacji opartych na kontenerach. Zwykle istnieją różne zasobniki, które wymagają dostępu do tych samych informacji na zewnętrznym woluminie trwałym.    
Podczas gdy usługa Azure Files jest opcją, Tworzenie serwera NFS na maszynie wirtualnej platformy Azure jest inną formą trwałego magazynu udostępnionego. 

W tym artykule przedstawiono sposób tworzenia serwera NFS na maszynie wirtualnej Ubuntu. Ponadto nadajemy kontenerom AKS dostęp do tego współużytkowanego systemu plików.

## <a name="before-you-begin"></a>Zanim rozpoczniesz
W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Klaster AKS będzie musiał być aktywny w tych samych lub równorzędnych sieciach wirtualnych co serwer NFS. Klaster musi zostać utworzony w istniejącej sieci wirtualnej, która może być tą samą siecią wirtualną co maszyna wirtualna.

Procedurę konfigurowania z istniejącą siecią wirtualną opisano w dokumentacji: [Tworzenie klastra AKS w istniejącej sieci wirtualnej][aks-virtual-network] i [łączenie sieci wirtualnych za pomocą komunikacji równorzędnej][peer-virtual-networks]

Przyjęto również założenie, że utworzono Ubuntu Linux maszynę wirtualną (na przykład 18,04 LTS). Ustawienia i rozmiar mogą być używane w Twoim wdrożeniu i mogą być wdrażane za pomocą platformy Azure. W przypadku szybkiego startu systemu Linux zobacz [zarządzanie maszyną wirtualną z systemem Linux][linux-create].

Jeśli klaster AKS jest wdrażany jako pierwszy, platforma Azure automatycznie wypełni pole sieci wirtualnej podczas wdrażania maszyny Ubuntu, udostępniając je na żywo w tej samej sieci wirtualnej. Jeśli jednak chcesz korzystać z sieci równorzędnej, zapoznaj się z powyższą dokumentacją.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Wdrażanie serwera NFS na maszynie wirtualnej
Oto skrypt służący do konfigurowania serwera NFS w ramach maszyny wirtualnej Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Serwer zostanie ponownie uruchomiony (ze względu na skrypt) i można zainstalować serwer NFS w AKS.

>[!IMPORTANT]  
>Pamiętaj, aby zastąpić **AKS_SUBNET** poprawną z nich w klastrze, lub inny "*" spowoduje otwarcie serwera NFS do wszystkich portów i połączeń.

Po utworzeniu maszyny wirtualnej Skopiuj skrypt powyżej do pliku. Następnie można przenieść ją z komputera lokalnego lub wszędzie tam, gdzie jest to skrypt, do maszyny wirtualnej przy użyciu: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Gdy skrypt znajduje się na maszynie wirtualnej, można przeprowadzić protokół SSH do maszyny wirtualnej i wykonać ją za pomocą polecenia:
```console
sudo ./nfs-server-setup.sh
```
Jeśli wykonanie nie powiedzie się z powodu błędu odmowy uprawnień, Ustaw uprawnienie do wykonywania za pomocą polecenia:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Łączenie klastra AKS z serwerem NFS
Serwer systemu plików NFS można połączyć z naszym klastrem, udostępniając wolumin trwały i liczbę trwałych roszczeń, która określa, jak uzyskać dostęp do woluminu.

Konieczne jest połączenie dwóch usług w tych samych lub równorzędnych sieciach wirtualnych. Instrukcje dotyczące konfigurowania klastra w tej samej sieci wirtualnej są następujące: [Tworzenie klastra AKS w istniejącej sieci wirtualnej][aks-virtual-network]

Gdy znajdują się w tej samej sieci wirtualnej (lub komunikacji równorzędnej), należy zainicjować obsługę woluminu trwałego i trwałego w klastrze AKS. Kontenery mogą następnie zainstalować dysk NFS w katalogu lokalnym.

Oto przykładowa definicja Kubernetes dla woluminu trwałego (w tej definicji przyjęto, że klaster i maszyna wirtualna znajdują się w tej samej sieci wirtualnej):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Zastąp **NFS_INTERNAL_IP**, **NFS_NAME** i **NFS_EXPORT_FILE_PATH** informacjami o serwerze NFS.

Wymagany jest również trwały plik żądania woluminu. Oto przykład, co należy uwzględnić:

>[!IMPORTANT]  
>**"storageClassName"** musi pozostać pustym ciągiem lub nie będzie działać.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli nie można nawiązać połączenia z serwerem z klastra, może to być problem z wyeksportowanym katalogiem lub jego elementem nadrzędnym, który nie ma wystarczających uprawnień dostępu do serwera.

Sprawdź, czy katalog eksportu i jego katalog nadrzędny mają uprawnienia 777.

Uprawnienia można sprawdzić, uruchamiając poniższe polecenie, a katalogi powinny mieć uprawnienia *"drwxrwxrwx"* :
```console
ls -l
```

## <a name="more-information"></a>Więcej informacji
Aby zapoznać się z pełnym przewodnikiem lub ułatwić debugowanie instalacji serwera NFS, Oto szczegółowy samouczek:
  - [Samouczek NFS][nfs-tutorial]

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md

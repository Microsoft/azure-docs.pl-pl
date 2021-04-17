---
title: Konfiguracja sterownika procesora GPU serii N platformy Azure dla systemu Linux
description: Jak skonfigurować sterowniki procesora GPU firmy NVIDIA dla maszyn wirtualnych serii N z systemem Linux na platformie Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565308"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalowanie sterowników procesora GPU firmy NVIDIA na komputerach wirtualnych z serii N z systemem Linux

Aby korzystać z możliwości procesora GPU maszyn wirtualnych z serii N platformy Azure, które są dostępne w procesorach GPU firmy NVIDIA, należy zainstalować sterowniki procesorów GPU firmy NVIDIA. Rozszerzenie [sterownika procesora GPU firmy NVIDIA](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej serii N. Zainstaluj rozszerzenie lub zarządzaj nimi przy użyciu Azure Portal narzędzi, takich jak interfejs wiersza polecenia platformy Azure lub Azure Resource Manager szablonów. Zobacz dokumentację [rozszerzenia sterownika procesora GPU firmy NVIDIA,](../extensions/hpccompute-gpu-linux.md) aby uzyskać informacje o obsługiwanych dystrybucjach i krokach wdrażania.

Jeśli zdecydujesz się zainstalować sterowniki procesora GPU firmy NVIDIA ręcznie, ten artykuł zawiera obsługiwane dystrybucje, sterowniki oraz kroki instalacji i weryfikacji. Informacje dotyczące ręcznej konfiguracji sterownika są również dostępne dla [maszyn wirtualnych z systemem Windows.](../windows/n-series-driver-setup.md)

Aby uzyskać informacje o specyfikacjach maszyn wirtualnych serii N, pojemnościach magazynu i szczegółach dysku, zobacz Gpu Linux VM sizes (Rozmiary maszyn [wirtualnych z systemem Linux z procesorem GPU).](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json) 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalowanie sterowników CUDA na maszyn wirtualnych z serii N

Poniżej znajdują się kroki instalowania sterowników CUDA z zestawu narzędzi NVIDIA CUDA Toolkit na maszyny wirtualne serii N. 

Deweloperzy języków C i C++ mogą opcjonalnie zainstalować pełny zestaw narzędzi, aby tworzyć aplikacje przyspieszane przez procesor GPU. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji cuda](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Aby zainstalować sterowniki CUDA, nawiązaniu połączenia SSH z każdą maszyną wirtualną. Aby sprawdzić, czy system ma procesor GPU z możliwością obsługi cuda, uruchom następujące polecenie:

```bash
lspci | grep -i NVIDIA
```
Zostaną wyświetlony dane wyjściowe podobne do następujących (z kartą NVIDIA Tesla K80):

![lspci dane wyjściowe polecenia](./media/n-series-driver-setup/lspci.png)

Lspci wyświetla listę urządzeń PCIe na maszynie wirtualnej, w tym Karty sieciowe InfiniBand i procesory GPU, jeśli są. Jeśli lspci nie zwróci pomyślnie, może być konieczne zainstalowanie LIS w CentOS/RHEL (instrukcje poniżej).
Następnie uruchom polecenia instalacji specyficzne dla danej dystrybucji.

### <a name="ubuntu"></a>Ubuntu 

1. Pobierz i zainstaluj sterowniki CUDA z witryny internetowej firmy NVIDIA. 
    > [!NOTE]
   >  W poniższym przykładzie przedstawiono ścieżkę pakietu CUDA dla systemu Ubuntu 16.04. Zastąp ścieżkę specyficzną dla wersji, która ma być używana. 
   >  
   >  Odwiedź [Centrum pobierania firmy Nvidia] ( https://developer.download.nvidia.com/compute/cuda/repos/) , aby uzyskać pełną ścieżkę specyficzną dla każdej wersji. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   Instalacja może potrwać kilka minut.
 

2. Aby opcjonalnie zainstalować kompletny zestaw narzędzi CUDA, wpisz:

   ```bash
   sudo apt-get install cuda
   ```

3. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.

#### <a name="cuda-driver-updates"></a>Aktualizacje sterowników CUDA

Zalecamy okresowe aktualizowanie sterowników CUDA po wdrożeniu.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS lub Red Hat Enterprise Linux

1. Zaktualizuj jądro (zalecane). Jeśli zdecydujesz się nie aktualizować jądra, upewnij się, że wersje i `kernel-devel` `dkms` są odpowiednie dla jądra.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Zainstaluj najnowsze usługi [integracji systemu Linux dla funkcji Hyper-V i platformy Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Sprawdź, czy LIS jest wymagany, weryfikując wyniki lspci. Jeśli wszystkie urządzenia procesora GPU są wymienione zgodnie z oczekiwaniami (i udokumentowane powyżej), instalowanie LIS nie jest wymagane.

   Należy pamiętać, że LIS ma zastosowanie do Red Hat Enterprise Linux, CentOS i Oracle Linux Red Hat zgodne jądra 5.2-5.11, 6.0-6.10 i 7.0-7.7. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją usług integracji systemu Linux] https://www.microsoft.com/en-us/download/details.aspx?id=55106) ( 
   Pomiń ten krok, jeśli zamierzasz używać systemu CentOS/RHEL 7.8 (lub jego wyższych wersji), ponieważ lis nie jest już wymagany dla tych wersji.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Połącz się ponownie z maszyną wirtualną i kontynuuj instalację za pomocą następujących poleceń:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Instalacja może potrwać kilka minut. 
   
    > [!NOTE]
   >  Odwiedź [witrynę Fedora](https://dl.fedoraproject.org/pub/epel/) and Nvidia CUDA repo (Fedora i [repo Nvidia CUDA),](https://developer.download.nvidia.com/compute/cuda/repos/) aby wybrać odpowiedni pakiet dla wersji CentOS lub RHEL, której chcesz użyć.
   >  

Na przykład centOS 8 i RHEL 8 będą potrzebować następujących kroków.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. Aby opcjonalnie zainstalować kompletny zestaw narzędzi CUDA, wpisz:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Jeśli zostanie wyświetlony komunikat o błędzie związany z brakującymi pakietami, na przykład vulkan-filesystem, może być konieczne edytowanie pliku /etc/yum.repos.d/rh-cloud, poszukaj optional-rpms i ustaw wartość 1
   >  

5. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.

### <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika

Aby odpytać o stan urządzenia procesora GPU, na maszynie wirtualnej za pomocą SSH uruchom narzędzie wiersza polecenia [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem. 

Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do następujących. Pamiętaj, że **narzędzie GPU-Util** pokazuje 0%, chyba że aktualnie na maszynie wirtualnej jest uruchomione obciążenie procesora GPU. Wersja sterownika i szczegóły procesora GPU mogą różnić się od pokazanych.

![Stan urządzenia firmy NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciową RDMA można włączyć na maszynach wirtualnych serii N z obsługą funkcji RDMA, takich jak NC24r, wdrożonych w tym samym zestawie dostępności lub w pojedynczej grupie umieszczania w zestawie skalowania maszyn wirtualnych. Sieć RDMA obsługuje ruch Message Passing Interface (MPI) dla aplikacji z procesorem Intel MPI 5.x lub nowszą wersją. Dodatkowe wymagania są następujące:

### <a name="distributions"></a>Dystrybucji

Wdrażanie maszyn wirtualnych serii N z możliwością RDMA z jednego z obrazów w Azure Marketplace, które obsługują łączność RDMA na maszyny wirtualne z serii N:
  
* **Ubuntu 16.04 LTS** — skonfiguruj sterowniki RDMA na maszynie wirtualnej i zarejestruj się w firmie Intel, aby pobrać procesor Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* Na maszynie wirtualnej są zainstalowane sterowniki RDMA i Intel MPI 5.1 oparte na systemie **CentOS 7.4 HPC.**

* **HpC oparty** na systemie CentOS — CentOS-HPC 7.6 i nowsze (dla jednostki SKU, w przypadku których infiniBand jest obsługiwany za pośrednictwem sr-IOV). Te obrazy mają wstępnie zainstalowane biblioteki Mellanox OFED i MPI.

> [!NOTE]
> CX3-Pro są obsługiwane tylko w wersjach LTS mellanox OFED. Użyj wersji LTS Mellanox OFED (4.9-0.1.7.0) na maszyny wirtualne serii N z ConnectX3-Pro kartami. Aby uzyskać więcej informacji, zobacz [Sterowniki systemu Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Ponadto niektóre z najnowszych obrazów hpc Azure Marketplace mają mellanox OFED 5.1 i nowsze, które nie obsługują ConnectX3-Pro kart. Sprawdź wersję Mellanox OFED w obrazie HPC przed użyciem go na maszyny wirtualne z ConnectX3-Pro kartami.
>
> Poniższe obrazy to najnowsze obrazy CentOS-HPC, które obsługują ConnectX3-Pro kart:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalowanie sterowników GRID na maszynach wirtualnych serii NV lub NVv3

Aby zainstalować sterowniki NVIDIA GRID na maszynach wirtualnych serii NV lub NVv3, nawiąź połączenie SSH z każdą maszyną wirtualną i wykonaj kroki dystrybucji systemu Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Uruchom polecenie `lspci`. Sprawdź, czy karta lub karty NVIDIA M60 są widoczne jako urządzenia PCI.

2. Instalowanie aktualizacji.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem firmy NVIDIA. (Sterownik NVIDIA należy używać tylko w przypadku maszyn wirtualnych NV lub NVv2). W tym celu utwórz plik o nazwie `/etc/modprobe.d` o `nouveau.conf` następującej zawartości:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Uruchom ponownie maszynę wirtualną i ponownie nawiąz połączenie. Wyjdź z serwera X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Pobierz i zainstaluj sterownik GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Gdy zostanie pytanie, czy chcesz uruchomić narzędzie nvidia-xconfig w celu zaktualizowania pliku konfiguracji X, wybierz pozycję **Tak.**

7. Po zakończeniu instalacji skopiuj plik /etc/nvidia/gridd.conf.template do nowego pliku gridd.conf w lokalizacji /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Dodaj następujący kod do : `/etc/nvidia/gridd.conf`
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Usuń następujące elementy z `/etc/nvidia/gridd.conf` , jeśli jest obecny:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS lub Red Hat Enterprise Linux 

1. Zaktualizuj jądro i usługi DKMS (zalecane). Jeśli nie chcesz aktualizować jądra, upewnij się, że wersje i `kernel-devel` `dkms` są odpowiednie dla jądra.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem firmy NVIDIA. (Sterownik NVIDIA należy używać tylko w przypadku maszyn wirtualnych NV lub NV3). W tym celu utwórz plik o nazwie `/etc/modprobe.d` o `nouveau.conf` następującej zawartości:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Uruchom ponownie maszynę wirtualną, ponownie nawiąż połączenie i zainstaluj najnowsze usługi [integracji systemu Linux dla funkcji Hyper-V i platformy Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Sprawdź, czy LIS jest wymagany, weryfikując wyniki lspci. Jeśli wszystkie urządzenia GPU są wymienione zgodnie z oczekiwaniami (i udokumentowane powyżej), instalowanie LIS nie jest wymagane. 

   Pomiń ten krok, jeśli zamierzasz używać centOS/RHEL 7.8 (lub wyższych wersji), ponieważ LIS nie jest już wymagane dla tych wersji.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Połącz się ponownie z maszyną wirtualną i uruchom `lspci` polecenie . Sprawdź, czy karta lub karty NVIDIA M60 są widoczne jako urządzenia PCI.
 
5. Pobierz i zainstaluj sterownik GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Gdy zostanie pytanie, czy chcesz uruchomić narzędzie nvidia-xconfig w celu zaktualizowania pliku konfiguracji X, wybierz pozycję **Tak.**

7. Po zakończeniu instalacji skopiuj plik /etc/nvidia/gridd.conf.template do nowego pliku gridd.conf w lokalizacji /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Dodaj następujący kod do : `/etc/nvidia/gridd.conf`
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Usuń następujące elementy z `/etc/nvidia/gridd.conf` , jeśli jest obecny:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.


### <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika


Aby odpytać o stan urządzenia procesora GPU, na maszynie wirtualnej za pomocą SSH uruchom narzędzie wiersza polecenia [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem. 

Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do następujących. Zwróć uwagę, **że narzędzie GPU-Util** pokazuje 0%, chyba że aktualnie na maszynie wirtualnej jest uruchomione obciążenie procesora GPU. Informacje o wersji sterownika i procesorze GPU mogą się różnić od wyświetlanych.

![Zrzut ekranu przedstawiający dane wyjściowe po zapytaniu o stan urządzenia procesora GPU.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Serwer X11
Jeśli serwer X11 jest potrzebny do połączeń zdalnych z maszyną wirtualną NV lub NVv2, zaleca się użycie [x11vnc,](http://www.karlrunge.com/x11vnc/) ponieważ umożliwia przyspieszanie sprzętowe grafiki. Wartość BusID urządzenia M60 należy ręcznie dodać do pliku konfiguracji X11 (zazwyczaj : `etc/X11/xorg.conf` ). Dodaj `"Device"` sekcję podobną do następującej:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ponadto zaktualizuj `"Screen"` sekcję, aby korzystać z tego urządzenia.
 
Dziesiętny busID można znaleźć, uruchamiając

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
Identyfikator BusID może ulec zmianie w przypadku ponownego alokacji lub ponownego uruchomienia maszyny wirtualnej. W związku z tym można utworzyć skrypt aktualizujący identyfikator BusID w konfiguracji X11 po ponownym uruchomieniu maszyny wirtualnej. Na przykład utwórz skrypt o nazwie `busidupdate.sh` (lub innej nazwie), który ma zawartość podobną do następującej:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Następnie utwórz wpis dla skryptu aktualizacji w programie `/etc/rc.d/rc3.d` , aby skrypt był wywoływany jako główny podczas rozruchu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Tryb trwałości można ustawić przy użyciu polecenia , dzięki czemu dane wyjściowe `nvidia-smi` polecenia będą szybsze, gdy trzeba będzie odpytywać karty. Aby ustawić tryb trwałości, wykonaj polecenie `nvidia-smi -pm 1` . Pamiętaj, że jeśli maszyna wirtualna zostanie ponownie uruchomiona, ustawienie trybu zostanie zmieszone. Zawsze można utworzyć skrypt ustawienia trybu do wykonania podczas uruchamiania.
* Jeśli zaktualizowano sterowniki CUDA firmy NVIDIA do najnowszej wersji i wiesz, że łączność RDMA nie działa, zainstaluj ponownie sterowniki [RDMA](#rdma-network-connectivity) w celu ponownego nainstalowania tej łączności. 
* Podczas instalacji LIS, jeśli niektóre CentOS/RHEL wersji systemu operacyjnego (lub jądra) nie jest obsługiwana dla LIS, zgłaszany jest błąd "Nieobsługiwana wersja jądra". Zgłoś ten błąd wraz z wersjami systemu operacyjnego i jądra.

## <a name="next-steps"></a>Następne kroki

* Aby przechwycić obraz maszyny wirtualnej z systemem Linux z zainstalowanymi sterownikami NVIDIA, zobacz Jak uogólnić i [przechwycić maszynę wirtualną z systemem Linux.](capture-image.md)

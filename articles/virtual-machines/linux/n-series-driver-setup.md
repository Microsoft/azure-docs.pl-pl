---
title: Konfiguracja sterowników procesora GPU platformy Azure dla systemu Linux
description: Jak skonfigurować sterowniki NVIDIA GPU dla maszyn wirtualnych serii N z systemem Linux na platformie Azure
services: virtual-machines-linux
author: vikancha-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: vikancha
ms.openlocfilehash: b80a09c82b1e932fb93b4c85ee250773aa7d3c38
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539757"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii N z systemem Linux

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N platformy Azure, które są obsługiwane przez procesory GPU firmy NVIDIA, należy zainstalować sterowniki procesora GPU NVIDIA. [Rozszerzenie sterownika NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim za pomocą Azure Portal lub narzędzi, takich jak szablony Azure Resource Manager interfejsu wiersza polecenia platformy Azure. Zapoznaj się z [dokumentacją rozszerzenia sterownika procesora GPU firmy NVIDIA](../extensions/hpccompute-gpu-linux.md) , aby poznać obsługiwane dystrybucje i kroki wdrażania.

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU w tym artykule przedstawiono obsługiwane dystrybucje, sterowniki oraz kroki instalacji i weryfikacji. Informacje o ręcznej instalacji sterownika są również dostępne dla [maszyn wirtualnych z systemem Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby poznać specyfikacje maszyn wirtualnych z serii N, pojemność magazynu i szczegóły dysku, zobacz [rozmiary maszyn wirtualnych GPU z systemem Linux](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalowanie sterowników CUDA na maszynach wirtualnych z serii N

Poniżej przedstawiono procedurę instalowania sterowników CUDA z zestawu narzędzi NVIDIA CUDA na maszynach wirtualnych z serii N. 


Deweloperzy C i C++ mogą opcjonalnie zainstalować pełny zestaw narzędzi, aby kompilować aplikacje przyspieszające procesor GPU. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji cuda](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Aby zainstalować sterowniki CUDA, należy nawiązać połączenie SSH z każdą maszyną wirtualną. Aby sprawdzić, czy system ma procesor GPU z obsługą CUDA, uruchom następujące polecenie:

```bash
lspci | grep -i NVIDIA
```
Zobaczysz dane wyjściowe podobne do poniższego przykładu (przedstawiamy kartę NVIDIA Tesla K80):

![dane wyjściowe polecenia lspci](./media/n-series-driver-setup/lspci.png)

Następnie uruchom polecenia instalacji specyficzne dla dystrybucji.

### <a name="ubuntu"></a>Ubuntu 

1. Pobierz i zainstaluj sterowniki CUDA z witryny internetowej firmy NVIDIA. Na przykład dla Ubuntu 16,04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

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

3. Uruchom ponownie maszynę wirtualną i sprawdź poprawność instalacji.

#### <a name="cuda-driver-updates"></a>Aktualizacje sterowników CUDA

Zalecamy okresowe Aktualizowanie sterowników CUDA po wdrożeniu.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS lub Red Hat Enterprise Linux

1. Zaktualizuj jądro (zalecane). Jeśli nie zdecydujesz się na aktualizowanie jądra, upewnij się, że wersje programu `kernel-devel` i `dkms` są odpowiednie dla danego jądra.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106). Check if LIS is required by verifying the results of lspci. If all GPU devices are listed as expected, installing LIS is not required.

Skip this step if you plan to use CentOS 7.8(or higher) as LIS is no longer required for these versions.

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Połącz się ponownie z maszyną wirtualną i Kontynuuj instalację przy użyciu następujących poleceń:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Instalacja może potrwać kilka minut. 

4. Aby opcjonalnie zainstalować kompletny zestaw narzędzi CUDA, wpisz:

   ```bash
   sudo yum install cuda
   ```

5. Uruchom ponownie maszynę wirtualną i sprawdź poprawność instalacji.

### <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Aby wykonać zapytanie o stan urządzenia GPU, SSH na maszynę wirtualną i uruchomić narzędzie wiersza polecenia [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane wraz z sterownikiem. 

Jeśli sterownik jest zainstalowany, zostaną wyświetlone dane wyjściowe podobne do poniższych. Należy pamiętać, że **GPU-util** pokazuje 0%, chyba że obecnie działa obciążenie procesora GPU na maszynie wirtualnej. Informacje o wersji sterownika i procesora GPU mogą różnić się od podanych elementów.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciową RDMA można włączyć na maszynach wirtualnych z serii N obsługujących funkcję RDMA, takich jak NC24r wdrożone w tym samym zestawie dostępności lub w pojedynczej grupie umieszczania w zestawie skalowania maszyn wirtualnych. Sieć RDMA obsługuje ruch MPI (Message Passing Interface) dla aplikacji uruchamianych z technologią Intel MPI 5. x lub nowszą. Dodatkowe wymagania są następujące:

### <a name="distributions"></a>Dystrybucji

Wdrażaj maszyny wirtualne z serii N obsługujące RDMA przy użyciu jednego z obrazów w portalu Azure Marketplace, które obsługuje łączność RDMA na maszynach wirtualnych z serii N:
  
* **Ubuntu 16,04 LTS** — Skonfiguruj sterowniki RDMA na maszynie wirtualnej i zarejestruj się w firmie Intel, aby pobrać firmę Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS 7,4 HPC** — sterowniki RDMA i Intel MPI 5,1 są zainstalowane na maszynie wirtualnej.

* **CentOS HPC** -CentOS-HPC 7,6 i nowsze (dla jednostek SKU, na których jest obsługiwana funkcja SR-IOV). Te obrazy mają wstępnie zainstalowane biblioteki Mellanox OFED i MPI.

> [!NOTE]
> Karty CX3-Pro są obsługiwane tylko przez LTS wersje Mellanox OFED. Użyj LTS Mellanox OFED (4.9-0.1.7.0) na maszynach wirtualnych serii N z kartami ConnectX3-Pro. Aby uzyskać więcej informacji, zobacz [sterowniki systemu Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Ponadto niektóre najnowsze obrazy HPC platformy Azure Marketplace mają Mellanox OFED 5,1 i nowsze, które nie obsługują kart ConnectX3-Pro. Sprawdź wersję Mellanox OFED w obrazie HPC przed użyciem jej na maszynach wirtualnych z kartami ConnectX3-Pro.
>
> Poniżej przedstawiono najnowsze obrazy CentOS-HPC, które obsługują karty ConnectX3-Pro:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalowanie sterowników siatki na maszynach wirtualnych z serii NV lub NVv3

Aby zainstalować sterowniki siatki NVIDIA na maszynach wirtualnych z serii NV lub NVv3, należy nawiązać połączenie SSH z każdą maszyną wirtualną i postępować zgodnie z instrukcjami dotyczącymi dystrybucji systemu Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Uruchom polecenie `lspci`. Sprawdź, czy karta NVIDIA M60 lub karty są widoczne jako urządzenia PCI.

2. Zainstaluj aktualizacje.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem NVIDIA. (Użyj sterownika NVIDIA tylko na maszynach wirtualnych NV lub NVv2). W tym celu Utwórz plik `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Uruchom ponownie maszynę wirtualną i ponownie nawiąż połączenie. Wyjdź z serwera X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Pobierz i zainstaluj sterownik siatki:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Gdy zostanie wyświetlony monit o uruchomienie narzędzia NVIDIA-Xconfig w celu zaktualizowania pliku konfiguracji X, wybierz opcję **tak**.

7. Po zakończeniu instalacji Skopiuj/etc/NVIDIA/gridd.conf.template do nowego pliku gridd. conf w lokalizacji/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Dodaj następujące elementy `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Usuń następujące z `/etc/nvidia/gridd.conf` nich, jeśli są obecne:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i sprawdź poprawność instalacji.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS lub Red Hat Enterprise Linux 

1. Zaktualizuj jądro i DKMS (zalecane). Jeśli nie zdecydujesz się na aktualizowanie jądra, upewnij się, że wersje programu `kernel-devel` i `dkms` są odpowiednie dla danego jądra.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem NVIDIA. (Użyj sterownika NVIDIA tylko na maszynach wirtualnych NV lub NV3). W tym celu Utwórz plik `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Uruchom ponownie maszynę wirtualną, Połącz się ponownie i zainstaluj najnowsze [usługi integracji systemu Linux dla funkcji Hyper-V i platformy Azure](https://www.microsoft.com/download/details.aspx?id=55106). Sprawdź, czy jest wymagana LIS, sprawdzając wyniki lspci. Jeśli wszystkie urządzenia GPU są wyświetlane zgodnie z oczekiwaniami, instalowanie LIS nie jest wymagane. 

Pomiń ten krok, jeśli używasz CentOS/RHEL 7,8 i nowszych.
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Ponownie nawiąż połączenie z maszyną wirtualną i uruchom `lspci` polecenie. Sprawdź, czy karta NVIDIA M60 lub karty są widoczne jako urządzenia PCI.
 
5. Pobierz i zainstaluj sterownik siatki:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Gdy zostanie wyświetlony monit o uruchomienie narzędzia NVIDIA-Xconfig w celu zaktualizowania pliku konfiguracji X, wybierz opcję **tak**.

7. Po zakończeniu instalacji Skopiuj/etc/NVIDIA/gridd.conf.template do nowego pliku gridd. conf w lokalizacji/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Dodaj następujące elementy `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Usuń następujące z `/etc/nvidia/gridd.conf` nich, jeśli są obecne:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i sprawdź poprawność instalacji.


### <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika


Aby wykonać zapytanie o stan urządzenia GPU, SSH na maszynę wirtualną i uruchomić narzędzie wiersza polecenia [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane wraz z sterownikiem. 

Jeśli sterownik jest zainstalowany, zostaną wyświetlone dane wyjściowe podobne do poniższych. Należy pamiętać, że **GPU-util** pokazuje 0%, chyba że obecnie działa obciążenie procesora GPU na maszynie wirtualnej. Informacje o wersji sterownika i procesora GPU mogą różnić się od podanych elementów.

![Zrzut ekranu pokazujący dane wyjściowe, gdy jest wysyłany zapytanie o stan urządzenia GPU.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Serwer X11
Jeśli potrzebujesz serwera X11 dla połączeń zdalnych z maszyną wirtualną NV lub NVv2, zaleca się, aby [x11vnc](http://www.karlrunge.com/x11vnc/) , ponieważ umożliwia przyspieszenie sprzętowe grafiki. BusID urządzenia M60 należy dodać ręcznie do pliku konfiguracji X11 (zazwyczaj `etc/X11/xorg.conf` ). Dodaj `"Device"` sekcję podobną do następującej:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ponadto zaktualizuj sekcję, `"Screen"` Aby korzystać z tego urządzenia.
 
BusID dziesiętną można znaleźć, uruchamiając

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID można zmienić, gdy maszyna wirtualna zostanie ponownie przypisana lub uruchomiona ponownie. W związku z tym możesz chcieć utworzyć skrypt, aby zaktualizować BusID w konfiguracji X11 podczas ponownego uruchamiania maszyny wirtualnej. Na przykład utwórz skrypt o nazwie `busidupdate.sh` (lub inną wybraną nazwę) z zawartością podobną do następującej:

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

Następnie utwórz wpis dla skryptu aktualizacji w programie, `/etc/rc.d/rc3.d` aby skrypt był wywoływany jako element główny przy rozruchu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Można ustawić tryb trwałości przy użyciu `nvidia-smi` , aby dane wyjściowe polecenia były szybsze, gdy zachodzi potrzeba zazapytania o karty. Aby ustawić tryb trwałości, wykonaj `nvidia-smi -pm 1` . Należy pamiętać, że jeśli maszyna wirtualna jest ponownie uruchamiana, ustawienie Tryb zostanie wysunięte. Zawsze możesz wykonać skrypty dla ustawienia Tryb, które ma być wykonywane po uruchomieniu.
* Jeśli sterowniki NVIDIA CUDA zostały zaktualizowane do najnowszej wersji i wyszukiwanie łączności RDMA nie działa dłużej, należy [ponownie zainstalować sterowniki RDMA](#rdma-network-connectivity) w celu ponownego nawiązania połączenia. 
* Jeśli określona wersja systemu operacyjnego CentOS/RHEL (lub jądro) nie jest obsługiwana w przypadku usługi LIS, zostanie zgłoszony błąd "nieobsługiwana wersja jądra". Zgłoś ten błąd wraz z wersjami systemu operacyjnego i jądra.

## <a name="next-steps"></a>Następne kroki

* Aby przechwycić obraz maszyny wirtualnej z systemem Linux przy użyciu zainstalowanych sterowników NVIDIA, zobacz [jak uogólniać i przechwytywać maszynę wirtualną z systemem Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

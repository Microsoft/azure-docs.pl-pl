---
title: Optymalizowanie przepływności sieci maszyn wirtualnych | Microsoft Docs
description: Dowiedz się, jak zoptymalizować przepływność sieci maszyn wirtualnych platformy Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 86785ada1d5b55a1eaa7c81243dd0b6c39087e1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695967"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure

Maszyny wirtualne platformy Azure mają domyślne ustawienia sieciowe, które mogą być bardziej zoptymalizowane pod kątem przepływności sieci. W tym artykule opisano, jak zoptymalizować przepływność sieci dla Microsoft Azure maszyn wirtualnych z systemami Windows i Linux, w tym dystrybucji głównych, takich jak Ubuntu, CentOS i Red Hat.

## <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows

Jeśli maszyna wirtualna z systemem Windows obsługuje [przyspieszone sieci](create-vm-accelerated-networking-powershell.md), włączenie tej funkcji będzie optymalną konfiguracją przepływności. W przypadku wszystkich innych maszyn wirtualnych z systemem Windows Korzystanie ze skalowania po stronie odbierającej (RSS) może osiągnąć wyższą maksymalną przepływność niż maszyna wirtualna bez funkcji RSS. Funkcja RSS może być domyślnie wyłączona na maszynie wirtualnej z systemem Windows. Aby określić, czy funkcja RSS jest włączona, i włączyć ją, jeśli jest ona aktualnie wyłączona, wykonaj następujące czynności:

1. Sprawdź, czy funkcja RSS jest włączona dla karty sieciowej za pomocą `Get-NetAdapterRss` polecenia programu PowerShell. W poniższych przykładowych danych wyjściowych zwróconych z `Get-NetAdapterRss` funkcji RSS nie jest włączona.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Aby włączyć funkcję RSS, wprowadź następujące polecenie:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Poprzednie polecenie nie ma danych wyjściowych. Polecenie zmieniło ustawienia karty sieciowej, powodując utratę łączności tymczasowej przez około minutę. Podczas utraty łączności pojawia się okno dialogowe Ponowne łączenie. Połączenie jest zwykle przywracane po trzeciej próbie.
3. Upewnij się, że funkcja RSS jest włączona na maszynie wirtualnej, wprowadzając `Get-NetAdapterRss` polecenie ponownie. Jeśli to się powiedzie, zwracane są następujące przykładowe dane wyjściowe:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Maszyna wirtualna z systemem Linux

Funkcja RSS jest zawsze włączona domyślnie na maszynie wirtualnej z systemem Linux na platformie Azure. Jądra systemu Linux wydane od października 2017 obejmują nowe opcje optymalizacji sieci, które umożliwiają maszynie wirtualnej z systemem Linux osiągnięcie większej przepływności sieci.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu dla nowych wdrożeń

Ubuntu jądro platformy Azure zapewnia najlepszą wydajność sieci na platformie Azure i jest domyślnym jądrem od 21 września 2017. Aby można było pobrać to jądro, należy najpierw zainstalować najnowszą obsługiwaną wersję 16,04-LTS w następujący sposób:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po zakończeniu tworzenia wprowadź następujące polecenia, aby pobrać najnowsze aktualizacje. Te kroki działają również w przypadku maszyn wirtualnych, na których obecnie działa jądro platformy Azure Ubuntu.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Następujący opcjonalny zestaw poleceń może być przydatny w przypadku istniejących wdrożeń Ubuntu, które mają już jądro platformy Azure, ale które nie powiodło się dalszej aktualizacji z błędami.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu uaktualnienie jądra platformy Azure dla istniejących maszyn wirtualnych

Znaczną wydajność przepływności można osiągnąć przez uaktualnienie do jądra systemu Linux platformy Azure. Aby sprawdzić, czy masz to jądro, sprawdź wersję jądra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Jeśli maszyna wirtualna nie ma jądra platformy Azure, numer wersji zwykle zaczyna się od "4,4". Jeśli maszyna wirtualna nie ma jądra platformy Azure, uruchom następujące polecenia jako główne:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

W celu uzyskania najnowszych optymalizacji najlepiej utworzyć maszynę wirtualną z najnowszą obsługiwaną wersją, określając następujące parametry:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowania najnowszych usług integracji systemu Linux (LIS). Optymalizacja przepływności jest w LIS, począwszy od 4.2.2-2, chociaż nowsze wersje zawierają dalsze ulepszenia. Wprowadź następujące polecenia, aby zainstalować najnowszą wersję systemu LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

W celu uzyskania optymalizacji najlepiej jest utworzyć maszynę wirtualną z najnowszą obsługiwaną wersją, określając następujące parametry:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowania najnowszych usług integracji systemu Linux (LIS). Optymalizacja przepływności jest w LIS, rozpoczynając od 4,2. Wprowadź następujące polecenia, aby pobrać i zainstalować funkcję LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Dowiedz się więcej o usługach integracji systemu Linux w wersji 4,2 dla funkcji Hyper-V, wyświetlając [stronę pobierania](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z zoptymalizowanym wynikiem [testowania przepustowości/przepływności maszyny wirtualnej platformy Azure](virtual-network-bandwidth-testing.md) dla Twojego scenariusza.
* Przeczytaj o sposobie [przydzielenia przepustowości do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej na temat [usługi Azure Virtual Network często zadawanych pytań](virtual-networks-faq.md)

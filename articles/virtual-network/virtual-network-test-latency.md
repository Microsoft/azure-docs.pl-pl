---
title: Testowanie opóźnienia sieci maszyn wirtualnych platformy Azure w sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak testować opóźnienie sieci między maszynami wirtualnymi platformy Azure w sieci wirtualnej
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226409"
---
# <a name="test-vm-network-latency"></a>Testowanie opóźnienia sieci maszyn wirtualnych

Aby uzyskać najbardziej dokładne wyniki, Zmierz opóźnienie sieci maszyn wirtualnych platformy Azure za pomocą narzędzia zaprojektowanego dla tego zadania. Publicznie dostępne narzędzia, takie jak SockPerf (for Linux) i latte.exe (dla systemu Windows) mogą izolować i mierzyć opóźnienia sieci przy jednoczesnym wykluczaniu innych typów opóźnienia, takich jak opóźnienie aplikacji. Te narzędzia koncentrują się na rodzaju ruchu sieciowego, który ma wpływ na wydajność aplikacji (czyli Transmission Control Protocol [TCP] i ruch protokołu UDP]). 

Inne popularne narzędzia do łączności, takie jak polecenie ping, mogą mierzyć opóźnienia, ale ich wyniki mogą nie reprezentować ruchu sieciowego używanego w rzeczywistych obciążeniach. Wynika to z tego, że większość z tych narzędzi korzysta z protokołu ICMP (Internet Control Message Protocol), który może być traktowany inaczej niż ruch aplikacji i którego wyniki mogą nie dotyczyć obciążeń, które korzystają z protokołów TCP i UDP. 

Aby uzyskać dokładne testy opóźnienia w sieci protokołów używanych przez większość aplikacji, SockPerf (dla systemu Linux) i latte.exe (w systemie Windows) generują najbardziej odpowiednie wyniki. W tym artykule omówiono oba te narzędzia.

## <a name="overview"></a>Omówienie

Korzystając z dwóch maszyn wirtualnych, jednego jako nadawcy i jednego jako odbiorcy, utworzysz kanał komunikacji dwukierunkowej. Korzystając z tej metody, można wysyłać i odbierać pakiety w obu kierunkach i mierzyć czas błądzenia (RTT).

Takie podejście służy do mierzenia opóźnień sieci między dwiema maszynami wirtualnymi lub nawet między dwoma komputerami fizycznymi. Pomiary opóźnień mogą być przydatne w następujących scenariuszach:

- Ustanów wzorzec dla opóźnień sieci między wdrożonymi maszynami wirtualnymi.
- Porównaj skutki zmian opóźnienia sieci po wprowadzeniu związanych z nimi zmian:
  - System operacyjny (OS) lub oprogramowanie stosu sieciowego, w tym zmiany konfiguracji.
  - Metoda wdrożenia maszyny wirtualnej, na przykład wdrożenie do strefy dostępności lub grupy położenia zbliżeniowe (PPG).
  - Właściwości maszyny wirtualnej, takie jak przyspieszona zmiana sieci lub rozmiar.
  - Sieć wirtualna, taka jak Routing lub zmiany filtrowania.

### <a name="tools-for-testing"></a>Narzędzia do testowania
Aby mierzyć opóźnienia, dostępne są dwa różne opcje narzędzi:

* W przypadku systemów z systemem Windows: [latte.exe (system Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* W przypadku systemów z systemem Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Za pomocą tych narzędzi można zagwarantować, że tylko czasy dostarczania protokołu TCP lub UDP są mierzone, a nie protokołu ICMP (ping) lub innych typów pakietów, które nie są używane przez aplikacje, i nie wpływają na ich wydajność.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Porady dotyczące tworzenia optymalnej konfiguracji maszyny wirtualnej

Podczas tworzenia konfiguracji maszyny wirtualnej należy wziąć pod uwagę następujące zalecenia:
- Użyj najnowszej wersji systemu Windows lub Linux.
- Włącz przyspieszoną sieć w celu uzyskania najlepszych wyników.
- Wdróż maszyny wirtualne przy użyciu [grupy umieszczania usługi Azure zbliżeniowe](../virtual-machines/co-location.md).
- Większe maszyny wirtualne zwykle działają lepiej niż w przypadku mniejszych maszyn wirtualnych.

### <a name="tips-for-analysis"></a>Wskazówki dotyczące analizy

Analizując wyniki testów, należy wziąć pod uwagę następujące zalecenia:

- Wczesne ustanawianie planu bazowego, gdy tylko wdrożenie, konfiguracja i optymalizacje zostaną ukończone.
- Zawsze porównuj nowe wyniki do linii bazowej lub, w przeciwnym razie, z jednego testu do innego ze kontrolowanymi zmianami.
- Powtarzaj testy po każdym zaobserwowaniu lub zaplanowaniu zmian.


## <a name="test-vms-that-are-running-windows"></a>Testowanie maszyn wirtualnych z systemem Windows

### <a name="get-latteexe-onto-the-vms"></a>Pobierz latte.exe na maszyny wirtualne

Pobierz [najnowszą wersję latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Rozważ umieszczenie latte.exe w osobnym folderze, na przykład *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Zezwalaj na latte.exe za poorednictwem zapory Windows Defender

Na *odbiorniku* Utwórz regułę zezwalania w zaporze Windows Defender, aby zezwolić na odbieranie latte.exe ruchu. Najłatwiej jest zezwolić na cały program latte.exe według nazwy, a nie zezwalać na ruch przychodzący określonych portów TCP.

Zezwól na latte.exe za pomocą zapory Windows Defender, uruchamiając następujące polecenie:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Na przykład, jeśli skopiowano latte.exe do folderu *c:\Tools* , będzie to polecenie:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Testy opóźnienia uruchamiania

* Na *odbiorniku* Uruchom latte.exe (Uruchom go z okna cmd, a nie z programu PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Około 65 000 iteracji jest wystarczająco dużo, aby zwracały reprezentatywne wyniki.

    Wszystkie dostępne numery portów są dokładne.

    Jeśli maszyna wirtualna ma adres IP 10.0.0.4, polecenie będzie wyglądać następująco:

    `latte -a 10.0.0.4:5005 -i 65100`

* Na stronie *nadawca* Uruchom latte.exe (Uruchom ją z okna cmd, a nie z programu PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Wyniki polecenia są takie same jak w odniesieniu do odbiornika, z tą różnicą, &nbsp;  że jest to *Klient* lub *nadawca*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Poczekaj na wyniki. W zależności od tego, jak daleko są maszyny wirtualne, test może potrwać kilka minut. Rozważ rozpoczęcie od mniejszej liczby iteracji do przetestowania pod kątem sukcesu przed uruchomieniem dłuższych testów.

## <a name="test-vms-that-are-running-linux"></a>Testowanie maszyn wirtualnych z systemem Linux

Aby przetestować maszyny wirtualne z systemem Linux, użyj [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalowanie SockPerf na maszynach wirtualnych

Na maszynach wirtualnych systemu Linux, *nadawcy* i *odbiorniku* Uruchom następujące polecenia, aby przygotować SockPerf na maszynach wirtualnych. Polecenia są dostępne dla głównych dystrybucje.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Dla Red Hat Enterprise Linux (RHEL)/CentOS

Uruchom następujące polecenia:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Dla Ubuntu

Uruchom następujące polecenia:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Dla wszystkich dystrybucje

Skopiuj, Kompiluj i zainstaluj SockPerf zgodnie z poniższymi krokami:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Uruchom SockPerf na maszynach wirtualnych

Po zakończeniu instalacji SockPerf maszyny wirtualne są gotowe do uruchomienia testów opóźnienia. 

Najpierw Zacznij SockPerf na *odbiorniku*.

Wszystkie dostępne numery portów są dokładne. W tym przykładzie używamy portu 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Teraz, gdy serwer nasłuchuje, klient może rozpocząć wysyłanie pakietów do serwera na porcie, na którym nasłuchuje (w tym przypadku 12345).

Około 100 sekund jest wystarczająca do zwrócenia reprezentatywnych wyników, jak pokazano w następującym przykładzie:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Poczekaj na wyniki. W zależności od tego, jak daleko są maszyny wirtualne, liczba iteracji będzie się różnić. Aby przetestować się pod kątem sukcesu przed uruchomieniem dłuższych testów, należy rozważyć rozpoczęcie krótszych testów wynoszących około 5 sekund.

Ten SockPerf przykład używa rozmiaru komunikatu 350-bajtowego, który jest typowy dla średniego pakietu. Można dostosować rozmiar większy lub niższy, aby osiągnąć wyniki, które bardziej precyzyjnie reprezentują obciążenie uruchomione na maszynach wirtualnych.


## <a name="next-steps"></a>Następne kroki
* Zwiększ opóźnienie przy użyciu [grupy rozmieszczenia usługi Azure zbliżeniowe](../virtual-machines/co-location.md).
* Dowiedz się, jak [zoptymalizować sieci dla maszyn wirtualnych](../virtual-network/virtual-network-optimize-network-bandwidth.md) w danym scenariuszu.
* Przeczytaj o tym [, jak przepustowość jest przypisana do maszyn wirtualnych](../virtual-network/virtual-machine-network-throughput.md).
* Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące usługi Azure Virtual Network](../virtual-network/virtual-networks-faq.md).
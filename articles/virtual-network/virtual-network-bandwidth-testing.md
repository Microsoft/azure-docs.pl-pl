---
title: Testowanie przepływności sieci maszyn wirtualnych platformy Azure
titlesuffix: Azure Virtual Network
description: Użyj NTTTCP, aby docelowa sieć do testowania i zminimalizować użycie innych zasobów, które mogą mieć wpływ na wydajność.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: abcd2f6e23ea0c7c26b2ee26b2f6ca4ad502b769
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177185"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testowanie przepustowości/przepływności (NTTTCP)

Podczas testowania wydajności przepływności sieci na platformie Azure najlepiej jest używać narzędzia, które jest przeznaczone dla sieci do testowania i minimalizuje użycie innych zasobów, które mogą mieć wpływ na wydajność. NTTTCP jest zalecane.

Skopiuj narzędzie do dwóch maszyn wirtualnych platformy Azure o takim samym rozmiarze. Jedna maszyna wirtualna działa jako NADAWCa i inna jako odbiornik.

#### <a name="deploying-vms-for-testing"></a>Wdrażanie maszyn wirtualnych do testowania
Na potrzeby tego testu dwie maszyny wirtualne powinny znajdować się w tej samej [grupie położenia sąsiedztwa](../virtual-machines/co-location.md) lub w tym samym zestawie dostępności, aby można było używać wewnętrznych adresów IP i wykluczać usługi równoważenia obciążenia z testu. Możliwe jest przetestowanie przy użyciu adresu VIP, ale tego rodzaju testowanie wykracza poza zakres tego dokumentu.

Zanotuj adres IP odbiorcy. Wywołajmy ten adres IP "a. b. c. r"

Zanotuj liczbę rdzeni na maszynie wirtualnej. Wywołajmy to " \# liczba \_ rdzeni"

Uruchom test NTTTCP przez 300 sekund (lub 5 minut) na maszynie wirtualnej nadawcy i na maszynie wirtualnej odbiornika.

Porada: w przypadku konfigurowania tego testu po raz pierwszy możesz próbować uzyskać opinię krótszy okres próbny. Gdy narzędzie działa zgodnie z oczekiwaniami, należy zwiększyć okres testu do 300 sekund, aby uzyskać najbardziej dokładne wyniki.

> [!NOTE]
> Nadawca **i** odbiorca muszą określać ten **sam** parametr czasu trwania testu (-t).

Aby przetestować pojedynczy strumień TCP przez 10 sekund:

Parametry odbiornika: NTttcp-r-t 10-P 1

Parametry nadawcy: NTttcp-s 10.27.33.7-t 10-n 1-P 1

> [!NOTE]
> Poprzedni przykład powinien być używany tylko do potwierdzenia konfiguracji. Prawidłowe przykłady testowania zostały omówione w dalszej części tego dokumentu.

## <a name="testing-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Uzyskaj NTTTCP na maszynach wirtualnych.

Pobierz najnowszą wersję: https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

Lub Wyświetl stronę usługi GitHub najwyższego poziomu: <https://github.com/microsoft/ntttcp>\

Rozważ umieszczenie NTTTCP w osobnym folderze, na przykład c: \\ Tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Zezwalaj na NTTTCP za pomocą zapory systemu Windows
Na ODBIORNIKu Utwórz regułę zezwalania w zaporze systemu Windows, aby zezwolić na odbieranie ruchu NTTTCP. Najłatwiej zezwolić na cały program NTTTCP o nazwę zamiast zezwalać na ruch przychodzący do określonych portów TCP.

Zezwalaj na NTttcp za pomocą zapory systemu Windows w następujący sposób:

netsh advfirewall firewall add Rule program = \<PATH\> \\ntttcp.exe Name = "NTttcp" Protocol = any dir = in Action = Allow Enable = Yes profil = any

Na przykład, jeśli skopiowano ntttcp.exe do folderu "c: \\ Tools", będzie to polecenie: 

netsh advfirewall firewall add Rule program = c: \\ tools \\ntttcp.exe Name = "NTttcp" Protocol = any dir = in Action = Allow Enable = Yes profil = any

#### <a name="running-ntttcp-tests"></a>Uruchamianie testów NTTTCP

Uruchom NTTTCP na ODBIORNIKu (**Uruchom z polecenia cmd**, a nie z programu PowerShell):

NTttcp-r – m [2 \* \# liczba \_ rdzeni], \* , a. b. c. r-t 300

Jeśli maszyna wirtualna ma cztery rdzenie i adres IP 10.0.0.4, będzie wyglądać następująco:

NTttcp-r – m 8, \* , 10.0.0.4-t 300


Uruchom NTTTCP na NADAWCy (**Uruchom z polecenia cmd**, a nie z programu PowerShell):

NTttcp-s – m 8, \* , 10.0.0.4-t 300 

Poczekaj na wyniki.


## <a name="testing-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem LINUX:

Użyj nttcp dla systemu Linux. Jest ona dostępna z <https://github.com/Microsoft/ntttcp-for-linux>

Na maszynach wirtualnych z systemem Linux (NADAWCy i odbiornik) Uruchom następujące polecenia, aby przygotować NTttcp dla systemu Linux na maszynach wirtualnych:

CentOS — Zainstaluj program w zatoce i git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu — Zainstaluj kompilację — podstawowe i git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
SUSE — Instalowanie narzędzia Git-core, w zatoce i udostępnianie:
``` bash
  zypper in -y git-core gcc make
```
Utwórz i zainstaluj na obu:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Podobnie jak w przypadku systemu Windows Załóżmy, że adres IP odbiorcy Linux to 10.0.0.4

Uruchom NTTTCP dla systemu Linux na ODBIORNIKu:

``` bash
ntttcp -r -t 300
```

Na stronie NADAWCa Uruchom polecenie:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Długość testu jest równa 60 sekund, jeśli nie podano parametru czasu

## <a name="testing-between-vms-running-windows-and-linux"></a>Testowanie między maszynami wirtualnymi z systemem Windows i LINUX:

W tym scenariuszu należy włączyć tryb bez synchronizacji, aby można było uruchomić test. Jest to realizowane przy użyciu **flagi-N** dla systemu Linux i **flagi-NS** w systemie Windows.

#### <a name="from-linux-to-windows"></a>W systemie Linux do systemu Windows:

Odbiornik \<Windows> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Nadawca \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>W systemie Windows do systemu Linux:

Odbiornik \<Linux> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Nadawca \<Windows> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testowanie wystąpień usługi w chmurze:
Musisz dodać następującą sekcję do swojej ServiceDefinition. csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Następne kroki
* W zależności od wyników może być dostępne pomieszczenie do [optymalizowania wydajności sieci](virtual-network-optimize-network-bandwidth.md) w scenariuszu.
* Przeczytaj o sposobie [przydzielenia przepustowości do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej na temat [usługi Azure Virtual Network często zadawanych pytań](virtual-networks-faq.md)

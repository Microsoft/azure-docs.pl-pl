---
title: Zalecane testy porównawcze wydajności — Azure NetApp Files
description: Zapoznaj się z zaleceniami dotyczącymi testowania testów porównawczych dotyczących wydajności woluminu i metryk przy użyciu Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593399"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Zalecenia dotyczące testu wydajności dla usługi Azure NetApp Files

Ten artykuł zawiera zalecenia dotyczące testowania testów porównawczych dotyczących wydajności woluminu i metryk przy użyciu Azure NetApp Files.

## <a name="overview"></a>Omówienie

Aby poznać charakterystykę wydajności woluminu Azure NetApp Files, można użyć narzędzia typu "open source" [FIO](https://github.com/axboe/fio) , aby uruchomić serię testów porównawczych w celu symulowania różnorodnych obciążeń. FIO można zainstalować zarówno w systemach operacyjnych Linux, jak i Windows.  Jest to doskonałe narzędzie do uzyskania szybkiej migawki zarówno operacji we/wy, jak i przepływności dla woluminu.

### <a name="vm-instance-sizing"></a>Rozmiar wystąpienia maszyny wirtualnej

Aby uzyskać najlepsze wyniki, należy się upewnić, że używane jest wystąpienie maszyny wirtualnej o odpowiednim rozmiarze do przeprowadzenia testów. W poniższych przykładach użyto Standard_D32s_v3 wystąpienia. Aby uzyskać więcej informacji o rozmiarach wystąpień maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dla maszyn wirtualnych z systemem Windows oraz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla maszyn wirtualnych opartych na systemie Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files rozmiar woluminu

Upewnij się, że wybrano prawidłowy poziom usług i rozmiar przydziału woluminu dla oczekiwanego poziomu wydajności. Aby uzyskać więcej informacji, zobacz [poziomy usług dla Azure NetApp Files](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Zalecenia dotyczące sieci wirtualnej (VNet)

Testy porównawcze należy wykonywać w tej samej sieci wirtualnej co Azure NetApp Files. W poniższym przykładzie przedstawiono zalecenia:

![Zalecenia dotyczące sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalacja programu FIO

FIO jest dostępny w formacie binarnym dla systemów Linux i Windows. Postępuj zgodnie z sekcją pakietów binarnych w [FIO](https://github.com/axboe/fio) , aby zainstalować wybraną platformę.

## <a name="fio-examples-for-iops"></a>Przykłady FIO dla operacji we/wy na sekundę 

Przykłady FIO w tej sekcji korzystają z następującej konfiguracji:
* Rozmiar wystąpienia maszyny wirtualnej: D32s_v3
* Poziom i rozmiar usługi puli pojemności: Premium/50 TiB
* Rozmiar przydziału woluminu: 48 TiB

W poniższych przykładach pokazano losowe operacje odczytu i zapisu FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k blokowy rozmiar 100% odczytów losowych

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Dane wyjściowe: wyświetlona liczba IOPS odczytu 68k

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blokowy rozmiar 100% losowe zapisy

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Dane wyjściowe: wyświetlona liczba IOPS zapisu 73k

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO przykłady przepustowości

W przykładach w tej sekcji przedstawiono FIO sekwencyjne operacje odczytu i zapisu.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 KB rozmiar bloku 100% sekwencyjne odczyty

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Wynik: wyświetlona przepływność GB/s 11,8

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 KB rozmiar bloku 100% sekwencyjne zapisy

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Wynik: wyświetlona przepływność GB/s 12,2

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metryki woluminów

Dane dotyczące wydajności Azure NetApp Files są dostępne za poorednictwem Azure Monitor liczników. Liczniki są dostępne za pośrednictwem żądań GET interfejsu API usługi Azure Portal i REST. 

Można wyświetlić dane historyczne dla następujących informacji:
* Średnie opóźnienie odczytu 
* Średnie opóźnienie zapisu 
* Odczyt IOPS (średnia)
* Operacje zapisu IOPS (średnia)
* Rozmiar logiczny woluminu (średnia)
* Rozmiar migawki woluminu (średnia)

### <a name="using-azure-monitor"></a>Korzystanie z usługi Azure Monitor 

Dostęp do liczników Azure NetApp Files można uzyskać na poszczególnych woluminach na stronie metryk, jak pokazano poniżej:

![Metryki Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Możesz również utworzyć pulpit nawigacyjny w Azure Monitor dla Azure NetApp Files, przechodząc do strony metryki, filtrując NetApp i określając potrzebne liczniki woluminów: 

![Pulpit nawigacyjny usługi Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor dostęp do interfejsu API

Dostęp do liczników Azure NetApp Files można uzyskać przy użyciu wywołań interfejsu API REST. Zobacz [obsługiwane metryki z Azure Monitor: Microsoft. NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) for Counters i Volumes.

W poniższym przykładzie przedstawiono adres URL pobierania na potrzeby wyświetlania rozmiaru woluminu logicznego:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Następne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla systemu Linux](performance-benchmarks-linux.md)
---
title: Wyniki testu porównawczego wydajności dla Azure NetApp Files | Microsoft Docs
description: Opisuje wyniki testów porównawczych wydajności dla Azure NetApp Files na poziomie woluminu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881755"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Wyniki testu wydajności dla usługi Azure NetApp Files

W tym artykule opisano wyniki testów porównawczych wydajności dla Azure NetApp Files na poziomie woluminu. 

## <a name="sample-application-used-for-the-tests"></a>Przykładowa aplikacja używana do testów

Testy wydajności zostały uruchomione z przykładową aplikacją przy użyciu Azure NetApp Files. Aplikacja ma następujące cechy: 

* Aplikacja oparta na systemie Linux skompilowana dla chmury
* Może skalować liniowo przy użyciu dodanych maszyn wirtualnych, aby zwiększyć moc obliczeniową w razie potrzeby
* Wymaga szybkiego ułatwienia dostępu w usłudze Data Lake
* Ma wzorce we/wy, które czasami losowo i czasami sekwencyjne 
    * Wzorzec losowy wymaga małych opóźnień dla dużych ilości operacji we/wy. 
    * Wzorzec sekwencyjny wymaga dużej ilości przepustowości. 

## <a name="about-the-workload-generator"></a>Informacje o generatorze obciążenia

Wyniki pochodzą z plików podsumowania Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) to narzędzie wiersza polecenia, które generuje obciążenia we/wy dysku umożliwiające sprawdzenie wydajności magazynu. Używana konfiguracja klienta programu jest skalowalna.  Obejmuje pojedynczy wzorzec mieszany/klienta oraz 14 dedykowanych maszyn wirtualnych klienta.

## <a name="about-the-tests"></a>Informacje o testach

Testy zostały zaprojektowane tak, aby identyfikować limity, które mogą mieć przykładową aplikację, oraz czas odpowiedzi, który jest Krzywy do limitów.  

Uruchomiono następujące testy: 

* 100% 8 — KiB losowy odczyt
* 100% 8 — KiB losowy zapis
* 100% 64-KiB sekwencyjny odczyt
* 100% 64-KiB sekwencyjny zapis
* 50% 64-KiB sekwencyjny odczyt, 50% 64-KiB sekwencyjny zapis
* 50% 8 — KiB losowy odczyt, 50% 8 — KiB losowy zapis

## <a name="bandwidth"></a>Przepustowość

Azure NetApp Files oferuje wiele [poziomów usług](azure-netapp-files-service-levels.md). Każdy poziom usług oferuje różną przepustowość na TiB dyspozycyjności (przydział woluminu). Limit przepustowości dla woluminu jest inicjowany na podstawie kombinacji poziomu usługi i limitu przydziału woluminu. Limit przepustowości jest tylko jednym czynnikiem określającym rzeczywistą ilość przepływności, która zostanie zrealizowana.  

Obecnie 4 500 MiB jest największą przepływność osiągniętą przez obciążenie dla pojedynczego woluminu w ramach testowania.  W przypadku użycia poziomu usługi Premium przydział woluminu 70,31 TiB będzie zapewniać wystarczającą przepustowość do realizacji tej przepływności zgodnie z poniższym obliczeniem: 

![Formuła przepustowości](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Przydział i poziom usług](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Obciążenia intensywnie korzystające z przepływności

Używany test przepływności Vdbench i kombinacja maszyn wirtualnych magazynu 12xD32s v3. Przykładowy wolumin w teście osiągnął następujące numery przepływności:

![Test przepływności](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Duże obciążenia we/wy

Test we/wy użył Vdbench oraz kombinacji maszyn wirtualnych magazynu 12xD32s v3. Przykładowy wolumin w teście osiągnął następujące numery we/wy:

![Test we/wy](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Opóźnienie

Odległość między testowymi maszynami wirtualnymi a woluminem Azure NetApp Files ma wpływ na wydajność operacji we/wy.  Na poniższym wykresie porównano krzywe IOPS i odpowiedzi na opóźnienia dla dwóch różnych zestawów maszyn wirtualnych.  Jeden zestaw maszyn wirtualnych jest bliski Azure NetApp Files, a drugi jeszcze więcej.  Zwiększone opóźnienie dla dalszych zestawów maszyn wirtualnych ma wpływ na liczbę operacji we/wy osiągniętą na danym poziomie równoległości.  Niezależnie od tego, odczyty na woluminie mogą przekraczać 300 000 operacji we/wy, jak pokazano poniżej: 

![Badanie opóźnienia](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Podsumowanie

Obciążenia zależne od opóźnienia (bazy danych) mogą mieć jeden milisekund czasu odpowiedzi. Wydajność transakcyjna może przekraczać 300 tysięcy pozycji IOPS dla pojedynczego woluminu.

Aplikacje zależne od przepływności (do przesyłania strumieniowego i tworzenia obrazów) mogą mieć przepływność GiB/s.

## <a name="example-scripts"></a>Przykładowe skrypty

Poniższe przykładowe skrypty są przeznaczone tylko do celów demonstracyjnych.  Nie są one używane do celów produkcyjnych.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done

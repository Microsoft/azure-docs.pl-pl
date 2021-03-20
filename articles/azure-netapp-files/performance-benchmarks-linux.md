---
title: Azure NetApp Files wzorców wydajności dla systemu Linux | Microsoft Docs
description: Opisuje testy wydajnościowe Azure NetApp Files dostarcza dla systemu Linux.
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
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82614592"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files wzorców wydajności dla systemu Linux

W tym artykule opisano testy porównawcze wydajności Azure NetApp Files dla systemu Linux.

## <a name="linux-scale-out"></a>Skalowanie w poziomie systemu Linux

W tej sekcji opisano testy wydajnościowe dotyczące przepływności obciążeń i liczby operacji we/wy obciążenia systemu Linux.

### <a name="linux-workload-throughput"></a>Przepływność obciążeń systemu Linux  

Wykres poniżej przedstawia obciążenie sekwencyjne 64-kibibyte (KiB) i 1-TiB zestaw roboczy. Pokazuje, że pojedynczy wolumin Azure NetApp Files może obsłużyć między ~ 1 600 MiB/s czystych zapisów sekwencyjnych i ~ 4 500 MiB/s czysto sekwencyjne odczyty.  

Wykres ilustruje spadek w 10% w czasie, od odczytu czystego do czystego zapisu. Ilustruje to, czego można oczekiwać przy użyciu różnych współczynników odczytu/zapisu (100%: 0%, 90%: 10%, 80%: 20% itd.).

![Przepływność obciążeń systemu Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Liczba operacji we/wy obciążenia systemu Linux  

Poniższy wykres przedstawia obciążenie losowe 4-kibibyte (KiB) i 1-TiB zestaw roboczy. Wykres pokazuje, że wolumin Azure NetApp Files może obsłużyć między ~ 130 000 czystych losowych zapisów i ~ 460 000 czystych odczytów losowych.  

Ten wykres ilustruje spadek w 10% w czasie od odczytu czystego do czystego zapisu. Ilustruje to, czego można oczekiwać przy użyciu różnych współczynników odczytu/zapisu (100%: 0%, 90%: 10%, 80%: 20% itd.).

![Liczba operacji we/wy obciążenia systemu Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Skalowanie w systemie Linux  

Jądro Linux 5,3 umożliwia skalowanie w poziomie pojedynczego klienta dla systemu plików NFS `nconnect` . Na wykresach w tej sekcji przedstawiono wyniki testów sprawdzania poprawności dla opcji instalacji po stronie klienta z NFSv3. Ta funkcja jest dostępna w systemie SUSE (Zaczynając od SLES12SP4) i Ubuntu (począwszy od wersji 19,10). Jest to podobne w koncepcji zarówno dla wielokanałowego protokołu SMB, jak i dla systemu plików NFS Oracle Direct.

Wykresy porównują zalety programu z `nconnect` niepołączonym woluminem zainstalowanym. Na wykresach FIO wygenerowało obciążenie z jednego wystąpienia D32s_v3 w regionie US-west2 platformy Azure.

### <a name="linux-read-throughput"></a>Przepływność odczytu systemu Linux  

Poniższe wykresy pokazują sekwencyjne odczyty ~ 3 500 MiB/s odczytuje z `nconnect` , około 2.3 x niebędących `nconnect` .

![Przepływność odczytu systemu Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Przepływność zapisu systemu Linux  

Poniższe wykresy pokazują sekwencyjne zapisy. Wskazują one, że `nconnect` nie ma zauważalnej korzyści dla sekwencyjnego zapisu. 1 500 MiB/s ma przybliżony górny limit woluminu zapisu sekwencyjnego i limit wychodzący wystąpienia D32s_v3.

![Przepływność zapisu systemu Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Operacje we/wy odczytu dla systemu Linux  

Poniższe wykresy przedstawiają losowe odczyty ~ 200 000 operacji we/wy odczytu z `nconnect` , około 3 `nconnect` .

![Operacje we/wy odczytu dla systemu Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Operacje we/wy zapisu w systemie Linux  

Poniższe wykresy przedstawiają losowe zapisy ~ 135 000 liczby IOPS zapisu z `nconnect` , w przybliżeniu trzykrotnie 3 `nconnect` .

![Operacje we/wy zapisu w systemie Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Następne kroki

- [Azure NetApp Files: maksymalne wykorzystanie magazynu w chmurze](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)

---
title: Avere vFXT dla projektów demonstracyjnych na platformie Azure
description: 'W tych przykładach przedstawiono najważniejsze funkcje i przypadki użycia programu avere vFXT for Azure: renderowanie wideo, obliczenia o wysokiej wydajności, wydajność vFXT i konfigurację klienta.'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 31e32bfc0a2c3279375148bdf3da7d4a4829af1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271077"
---
# <a name="avere-vfxt-demo-projects"></a>Projekty demonstracyjne avere vFXT

Przykładowe samouczki są dostępne w serwisie [GitHub](https://github.com/Azure/Avere). Te małe projekty przedstawiają kluczowe funkcje i przypadki użycia dla avere vFXT dla platformy Azure.

## <a name="video-rendering"></a>Renderowanie wideo

* [Renderowanie przy użyciu Azure Batch i avere vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) — projekt 60-minutowy, który demonstruje sposób użycia Autodesk Maya z Azure Batch i avere klastra vFXT do generowania animowanego filmu

* [Dlaczego warto używać avere vFXT do renderowania?](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) -Demonstracja porównująca czasy renderowania z magazynu dołączonego do sieci z klastrem usługi avere vFXT i bez niego

## <a name="high-performance-computing"></a>Obliczenia o wysokiej wydajności

* [Najlepsze rozwiązania dotyczące ulepszania czasu rozruchu maszyn wirtualnych platformy Azure](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) — powtarzalny test, który korzysta z avere VFXT dla platformy Azure w celu skrócenia czasu uruchamiania podczas szybkiego nanoszenia tysięcy klientów obliczeniowych

## <a name="vfxt-performance"></a>vFXT wydajność

* [Mierzenie wydajności vFXT za pomocą vdbench](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) -a podstawowego Instalatora testów do generowania małych i średnich obciążeń w celu przetestowania pamięci vFXT i podsystemów dysków

## <a name="client-setup"></a>Konfiguracja klienta

* [Windows 10 Workstation for avere vFXT](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) — pokazuje, jak skonfigurować stację roboczą z systemem Windows i zainstalować ją w klastrze avere vFXT

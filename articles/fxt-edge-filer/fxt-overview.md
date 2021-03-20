---
title: Przegląd Microsoft Azure FXT Edge
description: Opisuje pamięć podręczną magazynu hybrydowego usługi Azure FXT Edge, aktywny plik archiwum i akcelerator dostępu do plików dla obliczeń o wysokiej wydajności
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218786"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co to jest pamięć podręczna magazynu hybrydowego usługi Azure FXT Edge?

Usługa Azure FXT Edge to hybrydowe urządzenie magazynujące magazynu, które zapewnia szybki dostęp do plików i aktywne Archiwum dla zadań obliczeniowych o wysokiej wydajności (HPC).

Współpracuje z wieloma źródłami danych, niezależnie od tego, czy są przechowywane w lokalnym centrum danych, zdalnie czy w chmurze. Usługa Azure FXT Edge może zapewnić ujednoliconą przestrzeń nazw dla danych w różnych systemach magazynowania.

Trzy lub więcej urządzeń sprzętowych plików FXT Edge współpracują ze sobą jako klastrowany system plików, aby zapewnić pamięć podręczną. Aby uzyskać szczegółowe informacje na temat kupowania wymaganego sprzętu, skontaktuj się z przedstawicielem firmy Microsoft.

Aby dowiedzieć się więcej, zapoznaj się z informacjami o produkcie i arkuszem danych w [usłudze Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Przypadki zastosowań

Plik usługi Azure FXT Edge zwiększa produktywność w zakresie przepływów pracy takich jak:

* Przepływ pracy dostępu do pliku z dużym dostępem do odczytu
* Protokoły NFSv3 lub SMB2
* Farmy obliczeniowe z 1000 do 100 000 rdzeni procesora CPU

### <a name="nas-optimization-and-scaling"></a>Optymalizacja i skalowanie serwera NAS

Pamięć podręczna plików usługi Azure FXT Edge umożliwia bezproblemowe dostęp do istniejących systemów NetApp i Dell EMC Isilon NAS. Możesz również dodać obiekt blob platformy Azure lub inny magazyn w chmurze, aby zapewnić skalowalność bez konieczności ponownego wykonywania procesów dostępu do danych po stronie klienta.

### <a name="wan-caching"></a>Buforowanie sieci WAN

Plik usługi Azure FXT Edge może służyć do obsługi szybkiego dostępu do plików od użytkowników zaawansowanych, gdy potrzebne dane są przechowywane w innym miejscu. Zapewnianie dostępu podczas utrzymywania kopii zapasowych i innych systemów zarządzania danymi w scentralizowanym centrum danych.

### <a name="active-archive-in-azure-blob"></a>Aktywne Archiwum w obiekcie blob platformy Azure

Rozszerz swoje centrum danych do magazynu w chmurze za pomocą usługi Azure FXT Edge jako punktu dostępu.

## <a name="features"></a>Funkcje

Dostępne są dwa modele sprzętu.

| Model | PAMIĘCI | Dysk SSD interfejsu NVMe | Porty sieciowe |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10Gb + 2 x 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10Gb + 2 x 1 GB |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z samouczkiem dotyczącym usługi Azure FXT Edge, odczytując [specyfikacje](fxt-specs.md) lub [Samouczek instalacji](fxt-install.md).
* Dowiedz się, jak kupić usługę Azure FXT Edge na [stronie produktu usługi Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).

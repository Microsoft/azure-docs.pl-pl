---
title: Rozwiązywanie nieoczekiwanych ponownych uruchomień maszyn wirtualnych z dołączonymi dyskami VHD na maszynach wirtualnych Azure | Microsoft Docs
description: Jak rozwiązywać problemy z nieoczekiwanym ponownym uruchomieniem maszyn wirtualnych.
keywords: odmówiono połączenia SSH, błąd SSH, usługa Azure SSH, Niepowodzenie połączenia SSH
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75358530"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie nieoczekiwanych ponownych uruchomień maszyn wirtualnych z dołączonymi dyskami VHD

Jeśli maszyna wirtualna platformy Azure ma dużą liczbę dołączonych wirtualnych dysków twardych, które znajdują się na tym samym koncie magazynu, może przekroczyć elementy docelowe skalowalności dla poszczególnych kont magazynu, co spowoduje nieoczekiwane ponowne uruchomienie maszyny wirtualnej. Sprawdź metryki minut dla konta magazynu (**TotalRequests** / **TotalIngress** / **TotalEgress**), które przekraczają elementy docelowe skalowalności dla konta magazynu. Zobacz [metryki pokazują wzrost wzrost percentthrottlingerror,](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) Aby uzyskać pomoc w ustaleniu, czy na koncie magazynu nastąpiło ograniczenie przepustowości.

Ogólnie rzecz biorąc każda operacja wejścia lub wyjścia na wirtualnym dysku twardym z maszyny wirtualnej tłumaczy się, aby **pobrać** operacje na stronie lub **umieścić stronę** na bazowym obiekcie blob strony. W związku z tym możesz użyć szacowanej liczby IOPS dla danego środowiska, aby dostroić, ile wirtualnych dysków twardych może znajdować się na jednym koncie magazynu na podstawie określonego zachowania aplikacji. Firma Microsoft zaleca korzystanie z 40 lub mniej dysków na jednym koncie magazynu. Aby uzyskać więcej informacji o skalowalności dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji na temat celów skalowalności dla kont usługi BLOB Storage na stronie Premium, zobacz [elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

W przypadku przekroczenia celów skalowalności dla konta magazynu należy umieścić dyski VHD na wielu kontach magazynu, aby zmniejszyć aktywność poszczególnych kont.

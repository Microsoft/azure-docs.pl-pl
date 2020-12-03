---
title: Preferencja routingu na platformie Azure
description: Dowiedz się więcej na temat sposobu wybierania tras ruchu między platformą Azure i Internetem z preferencją routingu.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: c1c2c5f27143be7fc10269c948bee9d5465d565a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530446"
---
# <a name="what-is-routing-preference-preview"></a>Co to jest preferencja routingu (wersja zapoznawcza)?

Preferencja routingu platformy Azure umożliwia wybór sposobu tras ruchu między platformą Azure i Internetem. Możesz kierować ruchem za pośrednictwem sieci firmy Microsoft lub za pośrednictwem sieci usługodawcy internetowego (publicznego Internetu). Te opcje są również określane jako " *routing z zimną ziemniakami* " i " *aktywne* ". Cena transferu danych wychodzących jest różna w zależności od wyboru routingu. Możesz wybrać opcję routingu podczas tworzenia publicznego adresu IP. Publiczny adres IP można skojarzyć z zasobami, takimi jak maszyna wirtualna, zestawy skalowania maszyn wirtualnych, moduł równoważenia obciążenia dostępny z Internetu itp. Możesz również ustawić preferencję routingu dla zasobów usługi Azure Storage, takich jak obiekty blob, pliki, sieci Web i usługa Azure datalake. Domyślnie ruch jest kierowany za pośrednictwem sieci globalnej firmy Microsoft dla wszystkich usług platformy Azure.

> [!IMPORTANT]
> Preferencje routingu są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="routing-via-microsoft-global-network"></a>Routing za pośrednictwem sieci globalnej firmy Microsoft

Gdy kierujesz ruchem za pośrednictwem *sieci globalnej firmy Microsoft*, ruch jest dostarczany przez jedną z największych sieci na całym świecie, obejmującą ponad 160 000 kilometrów z włókna, z ponad 165 punktem obecności (pop). Sieć jest dobrze obsługiwana z wieloma nadmiarowymi ścieżkami światłowodowymi w celu zapewnienia wyjątkowo wysokiej niezawodności i dostępności. Inżynieria ruchu jest zarządzana przez kontroler sieci WAN zdefiniowany przez oprogramowanie, który zapewnia małą ilość opóźnienia dla ruchu sieciowego i oferuje wydajność sieci w warstwie Premium.

![Routing za pośrednictwem sieci globalnej firmy Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Ruch przychodzący:** Anons dotyczący protokołu BGP dla ruchu przychodzącego zapewnia dostęp do sieci firmy Microsoft najbliżej użytkownika. Na przykład, jeśli użytkownik Singapur uzyskuje dostęp do zasobów platformy Azure hostowanych w Chicago, stan USA jest wprowadzany do sieci globalnej firmy Microsoft w Singapurze, a podróże w sieci firmy Microsoft do usługi hostowanej w Chicago.

**Ruch wychodzący:** Ruch wychodzący jest zgodny z tą samą zasadą. Ruch porusza się przede wszystkim z podróży w sieci globalnej firmy Microsoft i opuszcza użytkownika. Na przykład, jeśli ruch z usługi Azure Chicago jest przeznaczony dla użytkownika z Singapuru, ruch w sieci firmy Microsoft z Chicago do Singapuru i wyjście z sieci firmy Microsoft w Singapurze.

Ruch przychodzący i wyjściowy odbywa się w całości z podróży w sieci globalnej firmy Microsoft. Jest to również nazywane *routingiem z zimną ziemniakami*.


## <a name="routing-over-public-internet-isp-network"></a>Routing przez publiczny Internet (sieć usługodawcy internetowego)

Nowe wybór routingu *Internet Routing* minimalizuje podróż w sieci globalnej firmy Microsoft i używa sieci TRANZYTowej usługodawcy internetowego do kierowania ruchu sieciowego. Ta opcja routingu zoptymalizowanego pod kątem kosztów zapewnia wydajność sieci, która jest porównywalna z innymi dostawcami chmury.

![Routing przez publiczny Internet](media/routing-preference-overview/route-via-isp-network.png)

**Ruch przychodzący:** Ścieżka transferu danych przychodzących używa *routingu gorąca* , co oznacza, że ruch przechodzi do sieci firmy Microsoft znajdującej się najbliżej regionu hostowanej usługi. Na przykład jeśli użytkownik Singapur uzyskuje dostęp do zasobów platformy Azure hostowanych w Chicago, ruch jest przesyłany przez publiczny Internet i przechodzi do sieci globalnej firmy Microsoft w Chicago.

**Ruch wychodzący:** Ruch wychodzący jest zgodny z tą samą zasadą. Ruch powoduje wyjście z sieci firmy Microsoft w tym samym regionie, w którym jest hostowana usługa. Na przykład jeśli ruch z usługi w Chicago platformy Azure jest przeznaczony dla użytkownika z Singapuru, ruch wychodzący z sieci firmy Microsoft w Chicago i podróżuje przez publiczny Internet do użytkownika w Singapurze.

## <a name="supported-services"></a>Obsługiwane usługi

Publiczny adres IP z wyborem preferencji routingu "Microsoft Global Network" można skojarzyć z dowolnymi usługami platformy Azure. Publiczny adres IP **z wyborem preferencji** routingu można jednak skojarzyć z następującymi zasobami platformy Azure:

* Maszyna wirtualna
* Zestaw skalowania maszyn wirtualnych
* Azure Kubernetes Service (AKS)
* Moduł równoważenia obciążenia dostępny z Internetu
* Application Gateway
* Azure Firewall

W przypadku usługi Storage podstawowe punkty końcowe zawsze używają **sieci globalnej firmy Microsoft**. Pomocnicze punkty końcowe można włączyć w **Internecie** jako wybór dla routingu ruchu. Obsługiwane usługi magazynu:

* Obiekty blob
* Pliki
* Sieć Web
* Azure DataLake

## <a name="pricing"></a>Ceny
Różnica między cenami w obu opcjach jest uwzględniana w cenach transferu danych wychodzących z Internetu. Funkcja routingu za pośrednictwem usługi **Microsoft Global Network** Data Transfer jest taka sama jak bieżąca cena wychodząca z Internetu. Aby uzyskać najnowsze informacje o cenach, odwiedź [stronę z cennikiem usługi Azure przepustowość](https://azure.microsoft.com/pricing/details/bandwidth/) . 

## <a name="availability"></a>Dostępność

Obsługa preferencji routingu jest dostępna w następujących regionach dla usług takich jak maszyna wirtualna i moduł równoważenia obciążenia dostępnego z Internetu, który korzysta z publicznego adresu IP dla ruchu wychodzącego z Internetu — Europa Północna, Europa Zachodnia, Francja Południowa, Południowe Zjednoczone Królestwo, Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA, Szwajcaria Zachodnia Niemcy Środkowo-Zachodnie Azja Południowo-Wschodnia,

Obsługa preferencji routingu dla konta magazynu jest dostępna w następujących regionach platformy Azure — Północno-środkowe stany USA, zachodnio-środkowe stany USA, Południowo-środkowe stany USA, wschód USA, zachodnie Stany Zjednoczone, Europa Północna, Francja Południowa, Niemcy Środkowo-Zachodnie, Szwajcaria Zachodnia, Południowe Azja Wschodnia, Japonia Wschodnia i Japonia Zachodnia.
## <a name="limitations"></a>Ograniczenia

* Preferencja routingu jest zgodna tylko z standardową jednostką SKU publicznego adresu IP. Podstawowa jednostka SKU publicznego adresu IP nie jest obsługiwana.
* Preferencje routingu obsługują obecnie tylko publiczne adresy IP IPv4. Publiczne adresy IP protokołu IPv6 nie są obsługiwane.
* Maszyny wirtualne z wieloma kartami sieciowymi mogą mieć tylko jeden typ preferencji routingu.


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](configure-routing-preference-virtual-machine-cli.md)

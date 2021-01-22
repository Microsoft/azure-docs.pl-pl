---
title: Dostępność zasobów według regionów
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Azure Container Instances w różnych regionach świadczenia usługi Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 63ec7c9027f957714108263dcbb4300a3cc68388
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661378"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostępność zasobów dla Azure Container Instances w regionach świadczenia usługi Azure

Ten artykuł zawiera szczegółowe informacje o dostępności zasobów obliczeniowych, pamięci i magazynu Azure Container Instances w regionach platformy Azure oraz w docelowym systemie operacyjnym. Aby uzyskać listę ogólnych dostępnych regionów dla Azure Container Instances, zobacz [dostępne regiony](https://azure.microsoft.com/regions/services/).

Podane wartości to maksymalne zasoby dostępne na wdrożenie [grupy kontenerów](container-instances-container-groups.md). Wartości są aktualne w momencie publikacji.

> [!NOTE]
> Grupy kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obszarze wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby wyeliminować takie niepowodzenie wdrożenia, spróbuj wdrożyć wystąpienia z niższymi ustawieniami zasobów lub spróbuj przeprowadzić wdrożenie w późniejszym czasie lub w innym regionie z dostępnymi zasobami.

Aby uzyskać informacje na temat przydziałów i innych limitów wdrożenia, zobacz [limity przydziału i limity dla Azure Container Instances](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Grupy kontenerów systemu Linux

Poniższe regiony i maksymalne zasoby są dostępne dla grup kontenerów z kontenerami systemu Linux w obszarze Ogólne wdrożenia, wdrożenia [usługi Azure Virtual Network](container-instances-vnet.md) i wdrożenia z [zasobami procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

> [!IMPORTANT]
> Maksymalne zasoby w regionie są różne w zależności od wdrożenia. Na przykład region może mieć inny maksymalny rozmiar procesora i pamięci w ramach wdrożenia sieci wirtualnej platformy Azure niż w przypadku ogólnego wdrożenia. Ten sam region może również mieć inny zestaw wartości maksymalnych dla wdrożenia z zasobami procesora GPU. Sprawdź typ wdrożenia przed sprawdzeniem w poniższych tabelach wartości maksymalnych w Twoim regionie.

| Region (Region) | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Maksymalny procesor sieci wirtualnej | Maksymalna pamięć sieci wirtualnej (GB) | Magazyn (GB) | Jednostki SKU procesora GPU (wersja zapoznawcza) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australia Wschodnia | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Brazylia Południowa | 4 | 16 | 2 | 8 | 50 | Nie dotyczy |
| Kanada Środkowa | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Indie Środkowe | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | V100 |
| Central US | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Azja Wschodnia | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| East US | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Wschodnie stany USA 2 | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Francja Środkowa | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Niemcy Środkowo-Zachodnie | 3 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Japonia Wschodnia | 2 | 8 | 4 | 16 | 50 | Nie dotyczy |
| Korea Środkowa | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Północno-środkowe stany USA | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 |
| Europa Północna | 4 | 16 | 4 | 16 | 50 | K80 |
| South Central US | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Southeast Asia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Indie Południowe | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Południowe Zjednoczone Królestwo | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Północne Zjednoczone Emiraty Arabskie | 3 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Zachodnio-środkowe stany USA| 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| West Europe | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Zachodnie stany USA | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Zachodnie stany USA 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Następujące maksymalne zasoby są dostępne dla grupy kontenerów wdrożonej z [zasobami procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

> [!IMPORTANT]
> W tej chwili wdrożenia z zasobami procesora GPU nie są obsługiwane w ramach wdrożenia sieci wirtualnej platformy Azure i są dostępne tylko w grupach kontenerów systemu Linux.

| Jednostki SKU procesora GPU | Liczba procesorów GPU | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Magazyn (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Grupy kontenerów systemu Windows

Następujące regiony i maksymalne zasoby są dostępne dla grup kontenerów z [obsługiwanymi i podglądami](container-instances-faq.md#what-windows-base-os-images-are-supported) kontenerów systemu Windows Server.

> [!IMPORTANT]
> W tej chwili wdrożenia z grupami kontenerów systemu Windows nie są obsługiwane w ramach wdrożenia sieci wirtualnej platformy Azure.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Aby uzyskać więcej informacji na temat hostów 1B, 2B i 3B, zobacz [zgodność hostów i wersji kontenerów](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Region (Region) | 1B/2B maksymalny procesor CPU | 1B/2B Maksymalna pamięć (GB) |3B maksymalny procesor CPU | b Maksymalna pamięć (GB) | Magazyn (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australia Wschodnia | 2 | 8 | 2 | 8 | 20 |
| Brazylia Południowa | 4 | 16 | 4 | 16 | 20 |
| Kanada Środkowa | 2 | 8 | 2 | 3,5 | 20 |
| Indie Środkowe | 2 | 3,5 | 2 | 3,5 | 20 |
| Central US | 2 | 3,5 | 2 | 3,5 | 20 |
| Azja Wschodnia | 2 | 3,5 | 2 | 3,5 | 20 |
| East US | 4 | 16 | 2 | 8 | 20 |
| Wschodnie stany USA 2 | 2 | 3,5 | 4 | 16 | 20 |
| Japonia Wschodnia | 4 | 16 | 4 | 16 | 20 |
| Korea Środkowa | 4 | 16 | 4 | 16 | 20 |
| Północno-środkowe stany USA | 4 | 16 | 4 | 16 | 20 |
| Europa Północna | 2 | 8 | 2 | 8 | 20 |
| South Central US | 2 | 3,5 | 2 | 8 | 20 |
| Southeast Asia | NIE DOTYCZY | NIE DOTYCZY | 2 | 3,5 | 20 |
| Indie Południowe | 2 | 3,5 | 2 | 3,5 | 20 |
| Południowe Zjednoczone Królestwo | 2 | 8 | 2 | 3,5 | 20 |
| Zachodnio-środkowe stany USA | 4 | 16 | 2 | 8 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| Zachodnie stany USA | 4 | 16 | 2 | 8 | 20 |
| Zachodnie stany USA 2 | 2 | 8 | 2 | 3,5 | 20 |


### <a name="windows-server-2019-ltsc"></a>LTSC systemu Windows Server 2019

> [!NOTE]
> Aby uzyskać więcej informacji na temat hostów 1B, 2B i 3B, zobacz [zgodność hostów i wersji kontenerów](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Region (Region) | 1B/2B maksymalny procesor CPU | 1B/2B Maksymalna pamięć (GB) |3B maksymalny procesor CPU | b Maksymalna pamięć (GB) | Magazyn (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australia Wschodnia | 4 | 16 | 4 | 16 | 20 |
| Brazylia Południowa | 4 | 16 | 4 | 16 | 20 |
| Kanada Środkowa | 4 | 16 | 4 | 16 | 20 |
| Indie Środkowe | 4 | 16 | 4 | 16 | 20 |
| Central US | 4 | 16 | 4 | 16 | 20 |
| Azja Wschodnia | 4 | 16 | 4 | 16 | 20 |
| East US | 4 | 16 | 4 | 16 | 20 |
| Wschodnie stany USA 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Francja Środkowa | 4 | 16 | 4 | 16 | 20 |
| Japonia Wschodnia | NIE DOTYCZY | Nie dotyczy | 4 | 16 | 20 |
| Korea Środkowa | 4 | 16 | 4 | 16 | 20 |
| Północno-środkowe stany USA | 4 | 16 | 4 | 16 | 20 |
| Europa Północna | 4 | 16 | 4 | 16 | 20 |
| South Central US | 4 | 16 | 4 | 16 | 20 |
| Southeast Asia | 4 | 16 | 4 | 16 | 20 |
| Indie Południowe | 4 | 16 | 4 | 16 | 20 |
| Południowe Zjednoczone Królestwo | 4 | 16 | 4 | 16 | 20 |
| Zachodnio-środkowe stany USA | 4 | 16 | 4 | 16 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| Zachodnie stany USA | 4 | 16 | 4 | 16 | 20 |
| Zachodnie stany USA 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z zespołem, jeśli chcesz widzieć dodatkowe regiony lub zwiększyć dostępność zasobów w [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Informacje o rozwiązywaniu problemów z wdrażaniem wystąpienia kontenera znajdują się w temacie [Rozwiązywanie problemów z wdrażaniem w Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

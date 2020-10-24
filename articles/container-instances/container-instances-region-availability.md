---
title: Dostępność zasobów według regionów
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Azure Container Instances w różnych regionach świadczenia usługi Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521462"
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

| Region | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Maksymalny procesor sieci wirtualnej | Maksymalna pamięć sieci wirtualnej (GB) | Magazyn (GB) | Jednostki SKU procesora GPU (wersja zapoznawcza) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australia Wschodnia | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Brazil South | 4 | 16 | 2 | 8 | 50 | Nie dotyczy |
| Kanada Środkowa | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Indie Środkowe | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | V100 |
| Central US | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Azja Wschodnia | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| East US | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Wschodnie stany USA 2 | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Japonia Wschodnia | 2 | 8 | 4 | 16 | 50 | Nie dotyczy |
| Korea Środkowa | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Północno-środkowe stany USA | 2 | 3,5 | 4 | 16 | 50 | Nie dotyczy |
| Europa Północna | 4 | 16 | 4 | 16 | 50 | K80 |
| South Central US | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Southeast Asia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Indie Południowe | 4 | 16 | NIE DOTYCZY | NIE DOTYCZY | 50 | Nie dotyczy |
| Południowe Zjednoczone Królestwo | 4 | 16 | 4 | 16 | 50 | Nie dotyczy |
| Zachodnio-środkowe stany USA| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| West Europe | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Zachodnie stany USA | 4 | 16 | 2 | 4 | 16| Nie dotyczy |
| Zachodnie stany USA 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Następujące maksymalne zasoby są dostępne dla grupy kontenerów wdrożonej z [zasobami procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

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

| Region | Maksymalny procesor systemu Windows Server 2016 | Maksymalna pamięć w systemie Windows Server 2016 (GB) | Windows Server 2019 LTSC Max CPU | LTSC Maksymalna pamięć (GB) dla systemu Windows Server 2019 | Magazyn (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australia Wschodnia | 2 | 3,5 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Kanada Środkowa | 2 | 3,5 | 4 | 16 | 20 |
| Indie Środkowe | 2 | 3,5 | 4 | 16 | 20 |
| Central US | 2 | 3,5 | 4 | 16 | 20 |
| Azja Wschodnia | 2 | 3,5 | 4 | 16 | 20 |
| East US | 2 | 8 | 4 | 16 | 20 |
| Wschodnie stany USA 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Francja Środkowa | 4 | 16 | 4 | 16 | 20 |
| Japonia Wschodnia | 4 | 16 | 4 | 16 | 20 |
| Korea Środkowa | 4 | 16 | 4 | 16 | 20 |
| Północno-środkowe stany USA | 2 | 3,5 | 4 | 16 | 20 |
| Europa Północna | 2 | 3,5 | 4 | 16 | 20 |
| South Central US | 2 | 3,5 | 4 | 16 | 20 |
| Indie Południowe | 2 | 3,5 | 4 | 16 | 20 |
| Southeast Asia | 2 | 3,5 | 4 | 16 | 20 |
| Południowe Zjednoczone Królestwo | 2 | 3,5 | 4 | 16 | 20 |
| Zachodnio-środkowe stany USA | 4 | 16 | 4 | 16 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| Zachodnie stany USA | 4 | 14 | NIE DOTYCZY | NIE DOTYCZY | 20 |
| Zachodnie stany USA 2 | 2 | 3,5 | 2 | 3,5 | 20 |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z zespołem, jeśli chcesz widzieć dodatkowe regiony lub zwiększyć dostępność zasobów w [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Informacje o rozwiązywaniu problemów z wdrażaniem wystąpienia kontenera znajdują się w temacie [Rozwiązywanie problemów z wdrażaniem w Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

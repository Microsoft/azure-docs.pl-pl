---
title: Dostępność zasobów według regionów
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Azure Container Instances w różnych regionach świadczenia usługi Azure.
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: 97baa5199a1803bd967c0b55c846908ea5a2ddcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565433"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostępność zasobów dla Azure Container Instances w regionach świadczenia usługi Azure

Ten artykuł zawiera szczegółowe informacje o dostępności zasobów obliczeniowych, pamięci i magazynu Azure Container Instances w regionach platformy Azure oraz w docelowym systemie operacyjnym. 

Podane wartości to maksymalne zasoby dostępne na wdrożenie [grupy kontenerów](container-instances-container-groups.md). Wartości są aktualne w momencie publikacji. 

> [!NOTE]
> Grupy kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obszarze wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby wyeliminować takie niepowodzenie wdrożenia, spróbuj wdrożyć wystąpienia z niższymi ustawieniami zasobów lub spróbuj przeprowadzić wdrożenie w późniejszym czasie lub w innym regionie z dostępnymi zasobami.

Aby uzyskać informacje na temat przydziałów i innych limitów wdrożenia, zobacz [limity przydziału i limity dla Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostępność — ogólne

Następujące regiony i maksymalne zasoby są dostępne dla grup kontenerów z systemem Linux i [obsługiwanymi](container-instances-faq.md#what-windows-base-os-images-are-supported) kontenerami systemu Windows Server 2016.

| Regiony | System operacyjny | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Magazyn (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazylia Południowa, Kanada środkowa, Indie Środkowe, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Kanada, Południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA, zachodnie stany USA 2 | Linux | 4 | 16 | 50 |
| Australia Wschodnia, Japonia Wschodnia | Linux | 2 | 8 | 50 |
| Północno-środkowe stany USA | Linux | 2 | 3,5 | 50 |
| Brazylia Południowa, Japonia Wschodnia, Europa Zachodnia | Windows | 4 | 16 | 20 |
| Wschodnie stany USA, zachodnie stany USA | Windows | 4 | 14 | 20 |
| Australia Wschodnia, Kanada środkowa, Indie Środkowe, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA 2, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Kanada, Południowe Zjednoczone Królestwo, zachodnie stany USA 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostępność — wdrożenia systemu Windows Server 2019 LTSC, 1809 (wersja zapoznawcza)

Następujące regiony i maksymalne zasoby są dostępne dla grup kontenerów z kontenerami opartymi na systemie Windows Server 2019 (wersja zapoznawcza).

| Regiony | System operacyjny | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Magazyn (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Australia Wschodnia, Brazylia Południowa, Kanada środkowa, Indie Środkowe, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Japonia Wschodnia, Północna środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Południowe Zjednoczone Królestwo, Europa Zachodnia | Windows | 4 | 16 | 20 |
| Wschodnie stany USA 2, zachodnie stany USA 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Dostępność — wdrażanie sieci wirtualnej

Następujące regiony i maksymalne zasoby są dostępne dla grupy kontenerów wdrożonej w [sieci wirtualnej platformy Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostępność — zasoby procesora GPU (wersja zapoznawcza)

Następujące regiony i maksymalne zasoby są dostępne dla grupy kontenerów wdrożonej z [zasobami procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z zespołem, jeśli chcesz widzieć dodatkowe regiony lub zwiększyć dostępność zasobów w [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Informacje o rozwiązywaniu problemów z wdrażaniem wystąpienia kontenera znajdują się w temacie [Rozwiązywanie problemów z wdrażaniem w Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

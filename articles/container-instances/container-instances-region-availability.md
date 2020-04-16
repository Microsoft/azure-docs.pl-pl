---
title: Dostępność zasobów według regionów
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Wystąpienia kontenerów platformy Azure w różnych regionach platformy Azure.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: c84ab7833f7c90d5d4b3c340c268fd9f75e20da9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399399"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostępność zasobów dla wystąpień kontenerów platformy Azure w regionach platformy Azure

W tym artykule opisano dostępność zasobów obliczeniowych, pamięci i magazynu wystąpień kontenerów platformy Azure w regionach platformy Azure i według docelowego systemu operacyjnego. 

Przedstawione wartości to maksymalne zasoby dostępne na wdrożenie [grupy kontenerów](container-instances-container-groups.md). Wartości są aktualne w momencie publikacji. 

> [!NOTE]
> Grupy kontenerów utworzone w ramach tych limitów zasobów są zależne od dostępności w regionie wdrażania. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby ograniczyć taki błąd wdrażania, spróbuj wdrożyć wystąpienia z niższymi ustawieniami zasobów lub spróbuj wdrożyć w późniejszym czasie lub w innym regionie z dostępnymi zasobami.

Aby uzyskać informacje o przydziałach i innych limitach we wdrożeniach, zobacz [Przydziały i limity dla wystąpień kontenerów platformy Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Dostępność - Ogólne

Następujące regiony i maksymalne zasoby są dostępne dla grup kontenerów z systemem Linux i [obsługiwanymi kontenerami](container-instances-faq.md#what-windows-base-os-images-are-supported) opartymi na systemie Windows Server 2016.

| Regiony | System operacyjny | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Pamięć masowa (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazylia Południowa, Kanada Środkowa, Indie Środkowe, Środkowe Stany Zjednoczone, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Indie Południowe, Europa Zachodnia, Zachodnie Stany Usa, Zachodnie stany USA 2 | Linux | 4 | 16 | 50 |
| Australia Wschodnia, Japonia Wschodnia | Linux | 2 | 8 | 50 |
| Północno-środkowe stany USA | Linux | 2 | 3,5 | 50 |
| Brazylia Południowa, Japonia Wschodnia, Europa Zachodnia | Windows | 4 | 16 | 20 |
| Wschodnie stany USA, zachodnie stany USA | Windows | 4 | 14 | 20 |
| Australia Wschodnia, Kanada Środkowa, Indie Środkowe, Środkowe Stany Zjednoczone, Azja Wschodnia, Wschodnie STANY USA 2, Północno-środkowe stany USA, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Indie Południowe, Zachodnie Stany Usa 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostępność — wdrożenia LTSC dla systemu Windows Server 2019, 1809 (wersja zapoznawcza)

Następujące regiony i maksymalne zasoby są dostępne dla grup kontenerów z kontenerami opartymi na systemie Windows Server 2019 (wersja zapoznawcza).

| Regiony | System operacyjny | CPU (maksymalnie) | Maksymalna ilość pamięci (GB) | Pamięć masowa (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Australia Wschodnia, Brazylia Południowa, Kanada Środkowa, Indie Środkowe, Środkowe Stany Zjednoczone, Azja Wschodnia, Wschodnie STANY USA, Japonia Wschodnia, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Zjednoczone Ameryki Południowej, Europa Zachodnia | Windows | 4 | 16 | 20 |
| Wschodnie stany USA 2, zachodnie stany USA 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Dostępność — wdrażanie sieci wirtualnej

Następujące regiony i maksymalne zasoby są dostępne dla grupy kontenerów wdrożonych w [sieci wirtualnej platformy Azure.](container-instances-vnet.md)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostępność — zasoby GPU (wersja zapoznawcza)

Następujące regiony i maksymalne zasoby są dostępne dla grupy kontenerów wdrożonych z [zasobami GPU](container-instances-gpu.md) (wersja zapoznawcza).

> [!IMPORTANT]
> Zasoby GPU są dostępne tylko na życzenie. Aby zażądać dostępu do zasobów gpu, prześlij [żądanie pomocy technicznej platformy Azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Następne kroki

Poinformuj zespół, czy chcesz zobaczyć dodatkowe regiony lub zwiększoną dostępność zasobów w [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Aby uzyskać informacje na temat rozwiązywania problemów z wdrażaniem wystąpienia [kontenera, zobacz Rozwiązywanie problemów z wdrażaniem za pomocą wystąpień kontenerów platformy Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

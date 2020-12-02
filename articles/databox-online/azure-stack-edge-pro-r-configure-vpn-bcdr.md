---
title: Skonfiguruj ciągłość biznesową i odzyskiwanie po awarii (BCDR) na Azure Stack krawędzi wirtualnej sieci prywatnej (VPN)
description: Opisuje sposób konfigurowania BCDR na Azure Stack Edge sieci VPN.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467277"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Skonfiguruj ciągłość biznesową i odzyskiwanie po awarii dla Azure Stack Edge sieci VPN

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

W tym artykule opisano sposób konfigurowania ciągłości działania i odzyskiwania po awarii (BCDR) w wirtualnej sieci prywatnej (VPN) skonfigurowanej na urządzeniu brzegowym Azure Stack.

Ten artykuł ma zastosowanie w przypadku urządzeń Azure Stack EDGE Pro R i Azure Stack Edge mini R.

## <a name="configure-failover-to-a-paired-region"></a>Konfigurowanie trybu failover w sparowanym regionie

Urządzenie brzegowe Azure Stack używa innych usług platformy Azure, na przykład Azure Storage. Usługę BCDR można skonfigurować w dowolnej usłudze platformy Azure, która jest używana przez Azure Stack Urządzenie brzegowe. Jeśli usługa platformy Azure używana przez Azure Stack Edge zostanie przełączona w tryb failover do jego sparowanego regionu, urządzenie Azure Stack Edge będzie teraz łączyło się z nowymi adresami IP, a komunikacja nie zostanie zaszyfrowana podwójnie. 

Urządzenie brzegowe Azure Stack używa tunelowania podzielonego oraz wszystkich danych i usług skonfigurowanych w regionie głównym (region skojarzony z Azure Stack urządzeniem brzegowym) przechodzą przez tunel VPN. Jeśli usługi platformy Azure są przełączane w tryb failover do sparowanego regionu, który znajduje się poza regionem głównym, dane przestaną być przełączane przez sieć VPN i dlatego nie są szyfrowane podwójnie. 

W tym scenariuszu mają wpływ tylko kilku usługi platformy Azure. Aby rozwiązać ten problem, należy wprowadzić następujące zmiany w konfiguracji Azure Stack Edge sieci VPN:

1. Dodaj zakresy adresów IP usługi platformy Azure w trybie failover w ramach tras włącznie dla sieci VPN na Azure Stack Edge. Usługi będą następnie uruchamiane przez sieć VPN.

    Aby dodać trasy włączne, należy pobrać plik JSON, który ma trasy specyficzne dla usługi. Upewnij się, że plik został zaktualizowany przy użyciu nowych tras.
2. Dodaj odpowiednie zakresy adresów IP usług platformy Azure w tabeli tras platformy Azure.
3. Dodaj trasy do zapory.

> [!NOTE]
>
> 1. Przełączenie w tryb failover bramy sieci VPN platformy Azure i usługi Azure Virtual Network (VNET) zostało rozkierowane w sekcji [odzyskiwanie z regionu platformy Azure, który nie powiódł się z powodu awarii](#recover-from-a-failed-azure-region).
> 2. Zakresy adresów IP dodane w tabeli tras platformy Azure mogą przekraczać limit 400. W takim przypadku należy postępować zgodnie z instrukcjami w sekcji, przełączając się [z jednego regionu świadczenia usługi Azure do innego regionu platformy Azure](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Odzyskaj sprawność z niezakończonego regionu platformy Azure

W przypadku przełączenia całego regionu platformy Azure w tryb failover z powodu katastrofalnego zdarzenia, takiego jak trzęsienie ziemi, wszystkie usługi platformy Azure w tym regionie, w tym usługa Azure Stack Edge, przejdzie w tryb failover. Ze względu na to, że istnieje wiele usług, trasy włączne mogą łatwo zakresać do kilku setek. Platforma Azure ma ograniczenie 400 tras. 

Gdy region przejdzie w tryb failover, Sieć wirtualna (VNET) jest również przełączona w tryb failover do nowego regionu i dlatego Brama sieci wirtualnej (VPN Gateway). Aby rozwiązać tę zmianę, wprowadź następujące zmiany w konfiguracji Azure Stack Edge sieci VPN:

1. Przenieś sieć wirtualną do regionu docelowego. Aby uzyskać więcej informacji, zobacz: [przenoszenie sieci wirtualnej platformy Azure do innego regionu za pośrednictwem Azure Portal](../virtual-network/move-across-regions-vnet-portal.md).
2. Wdróż nową bramę sieci VPN platformy Azure w regionie docelowym, w której została przeniesiona Sieć wirtualna. Aby uzyskać więcej informacji, zobacz [Tworzenie bramy sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Zaktualizuj konfigurację Azure Stack Edge sieci VPN, aby użyć powyższej bramy sieci VPN w ramach połączenia sieci VPN, a następnie wybierz region docelowy, aby dodać trasy korzystające z bramy sieci VPN.
4. Aktualizacja przychodzącej tabeli tras platformy Azure w przypadku zmiany puli adresów klienta. 

## <a name="move-from-an-azure-region-to-another"></a>Przenoszenie z regionu platformy Azure do innego

Urządzenie Azure Stack Edge można przenieść z jednej lokalizacji do innej lokalizacji. Aby użyć regionu znajdującego się najbliżej miejsca wdrożenia urządzenia, należy skonfigurować urządzenie pod kątem nowego regionu macierzystego. Wprowadź następujące zmiany:

1. Konfigurację Azure Stack Edge sieci VPN można zaktualizować, aby użyć bramy sieci VPN nowego regionu i wybrać nowy region, aby dodać trasy korzystające z bramy sieci VPN.

## <a name="next-steps"></a>Następne kroki

[Utwórz kopię zapasową urządzenia Azure Stack Edge](azure-stack-edge-gpu-prepare-device-failure.md).
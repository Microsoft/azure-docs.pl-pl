---
title: Omówienie wdrażania Menedżera zapory platformy Azure
description: Informacje o krokach wdrożenia wysokiego poziomu wymaganych przez Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079102"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Omówienie wdrażania Menedżera zapory platformy Azure

Istnieje więcej niż jeden sposób wdrożenia Menedżera zapory platformy Azure, ale zalecany jest następujący proces ogólny.

## <a name="general-deployment-process"></a>Ogólny proces wdrażania

### <a name="hub-virtual-networks"></a>Centra sieci wirtualnych

1.  Tworzenie zasad zapory

    - Tworzenie nowych zasad
<br>*oraz*<br>
    - Tworzenie podstawowych zasad i dostosowywanie zasad lokalnych
<br>*oraz*<br>
    - Importuj reguły z istniejącej zapory platformy Azure. Pamiętaj o usunięciu reguł translatora adresów sieciowych z zasad, które powinny być stosowane przez wiele zapór
1. Tworzenie architektury gwiazdy i szprych
   - Tworzenie centrum Virtual Network przy użyciu Menedżera zapory platformy Azure i sieci wirtualnych równorzędnych z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych
<br>*oraz*<br>
    - Tworzenie sieci wirtualnej i Dodawanie połączeń sieci wirtualnych i sieci wirtualnych równorzędnych usługi peer Network przy użyciu komunikacji równorzędnej sieci wirtualnej

3. Wybierz pozycję dostawcy zabezpieczeń i skojarz zasady zapory. Obecnie obsługiwane są tylko zapory platformy Azure.

   - Jest to wykonywane podczas tworzenia Virtual Network centrum
<br>*oraz*<br>
    - Przekonwertuj istniejącą sieć wirtualną na Virtual Network centrum. Istnieje również możliwość konwersji wielu sieci wirtualnych.

4. Skonfiguruj trasy definiowane przez użytkownika w celu kierowania ruchu do centrum Virtual Network zapory.


### <a name="secured-virtual-hubs"></a>Zabezpieczone centra wirtualne

1. Tworzenie architektury gwiazdy i szprych

   - Utwórz zabezpieczone centrum wirtualne przy użyciu Menedżera zapory platformy Azure i Dodaj połączenia sieci wirtualnej.<br>*oraz*<br>
   - Tworzenie wirtualnego centrum sieci WAN i Dodawanie połączeń sieci wirtualnej.
2. Wybierz dostawców zabezpieczeń

   - Gotowe podczas tworzenia bezpiecznego koncentratora wirtualnego.<br>*oraz*<br>
   - Przekonwertuj istniejące wirtualne koncentrator sieci WAN na zabezpieczenie koncentratora wirtualnego.
3. Tworzenie zasad zapory i kojarzenie ich z centrum

   - Ma zastosowanie tylko w przypadku korzystania z zapory platformy Azure.
   - Zasady zabezpieczeń jako usługi (SECaaS) innych firm są konfigurowane przez środowisko zarządzania partnerami.
4. Konfigurowanie ustawień trasy w celu kierowania ruchu do bezpiecznego centrum

   - Łatwo kieruj ruch do bezpiecznego centrum w celu filtrowania i rejestrowania bez tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych przy użyciu strony ustawień bezpiecznej trasy wirtualnego centrum.

> [!NOTE]
> - Na każdym regionie nie może być więcej niż jeden koncentrator na wirtualną sieć WAN. Można jednak dodać wiele wirtualnych sieci WAN w regionie, aby to osiągnąć.
> - Nie ma nakładających się przestrzeni adresowych IP dla centrów w vWAN.
> - Połączenia sieci wirtualnej koncentratora muszą znajdować się w tym samym regionie co centrum.
>
> Aby poznać więcej znanych problemów, zobacz [co to jest Menedżer zapory platformy Azure?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Konwertowanie sieci wirtualnych

Poniższe informacje dotyczą konwersji istniejącej sieci wirtualnej do sieci wirtualnej centrum:

- Jeśli w sieci wirtualnej znajduje się istniejąca Zapora platformy Azure, należy wybrać zasady zapory, które mają zostać skojarzone z istniejącą zaporą. Stan aprowizacji zapory będzie aktualizowany, gdy zasady zapory zastąpią reguły zapory. W stanie aprowizacji Zapora kontynuuje przetwarzanie ruchu i nie ma przestojów. Istniejące reguły można zaimportować do zasad zapory za pomocą Menedżera zapory lub Azure PowerShell.
- Jeśli sieć wirtualna nie ma skojarzonej zapory platformy Azure, zostanie wdrożona Zapora, a zasady zapory są skojarzone z nową zaporą.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpieczanie sieci w chmurze za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal](secure-cloud-network.md)
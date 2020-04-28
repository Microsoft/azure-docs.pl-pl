---
title: Co to jest bezpieczny koncentrator wirtualny?
description: Informacje o bezpiecznych koncentratorach wirtualnych
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518439"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co to jest bezpieczny koncentrator wirtualny?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Koncentrator wirtualny to zarządzana przez firmę Microsoft Sieć wirtualna, która umożliwia łączność z innych zasobów. Gdy koncentrator wirtualny jest tworzony na podstawie wirtualnej sieci WAN w Azure Portal, jako jej składniki są tworzone wirtualne i bramy koncentratora wirtualnego (opcjonalnie).

*Bezpiecznym* koncentratorem wirtualnym jest [Wirtualne Centrum sieci WAN platformy Azure](../virtual-wan/virtual-wan-about.md#resources) ze skojarzonymi zasadami zabezpieczeń i routingu skonfigurowanymi przez Menedżera zapory platformy Azure. Bezpieczne centra wirtualne umożliwiają łatwe tworzenie architektur gwiazdy i satelitów z natywnymi usługami zabezpieczeń dla zarządzania ruchem i ochrony. 

Możesz użyć bezpiecznego koncentratora wirtualnego jako zarządzanej centralnej sieci wirtualnej bez łączności Premium. Zastępuje ona centralną sieć wirtualną, która była wcześniej wymagana do wdrożenia zapory platformy Azure. Ponieważ zabezpieczone centrum wirtualne zapewnia automatyczne Routing, nie ma potrzeby konfigurowania własnych UDR (tras zdefiniowanych przez użytkownika) w celu kierowania ruchu przez zaporę.

Istnieje również możliwość użycia zabezpieczonych koncentratorów wirtualnych w ramach pełnej architektury wirtualnej sieci WAN. Ta architektura zapewnia bezpieczną, zoptymalizowaną i zautomatyzowaną łączność z gałęzią z usługą i za pośrednictwem platformy Azure. Można wybrać usługi do ochrony i zarządzania ruchem sieciowym, w tym zaporą platformy Azure i dostawcami innych firm jako usługi (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Tworzenie bezpiecznego centrum wirtualnego

Korzystając z Menedżera zapory w Azure Portal, można utworzyć nowe bezpieczne lub przekonwertować istniejące centrum wirtualne utworzone wcześniej przy użyciu wirtualnej sieci WAN platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zabezpieczone centrum wirtualne i użyć go do zabezpieczenia i zarządzania siecią gwiazdy i satelity, zobacz [Samouczek: Zabezpieczanie sieci w chmurze za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal](secure-cloud-network.md).
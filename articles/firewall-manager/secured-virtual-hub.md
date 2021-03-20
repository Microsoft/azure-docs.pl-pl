---
title: Co to jest bezpieczny koncentrator wirtualny?
description: Informacje o bezpiecznych koncentratorach wirtualnych
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948072"
---
# <a name="what-is-a-secured-virtual-hub"></a>Co to jest bezpieczny koncentrator wirtualny?

Koncentrator wirtualny to zarządzana przez firmę Microsoft Sieć wirtualna, która umożliwia łączność z innych zasobów. Gdy koncentrator wirtualny jest tworzony na podstawie wirtualnej sieci WAN w Azure Portal, jako jej składniki są tworzone wirtualne i bramy koncentratora wirtualnego (opcjonalnie).

*Bezpiecznym* koncentratorem wirtualnym jest [Wirtualne Centrum sieci WAN platformy Azure](../virtual-wan/virtual-wan-about.md#resources) ze skojarzonymi zasadami zabezpieczeń i routingu skonfigurowanymi przez Menedżera zapory platformy Azure. Bezpieczne centra wirtualne umożliwiają łatwe tworzenie architektur gwiazdy i satelitów z natywnymi usługami zabezpieczeń dla zarządzania ruchem i ochrony. 

Możesz użyć bezpiecznego koncentratora wirtualnego do filtrowania ruchu między sieciami wirtualnymi (V2V), sieciami wirtualnymi i biurami oddziałów (B2V) i ruchem do Internetu (B2I/V2I). Bezpieczny koncentrator wirtualny zapewnia automatyczne Routing. Nie ma potrzeby konfigurowania własnych UDR (tras zdefiniowanych przez użytkownika) w celu kierowania ruchu przez zaporę.

Możesz wybrać wymaganych dostawców zabezpieczeń, aby chronić ruch sieciowy i zarządzać nim, w tym zaporą platformy Azure, dostawcami zabezpieczeń (SECaaS) innych firm. Obecnie zabezpieczone centrum nie obsługuje filtrowania gałęzi do gałęzi (B2B) i filtrowania w wielu centrach. Aby dowiedzieć się więcej, zobacz [co to jest Menedżer zapory platformy Azure?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Tworzenie bezpiecznego centrum wirtualnego

Korzystając z Menedżera zapory w Azure Portal, można utworzyć nowe bezpieczne lub przekonwertować istniejące centrum wirtualne utworzone wcześniej przy użyciu wirtualnej sieci WAN platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zabezpieczone centrum wirtualne i użyć go do zabezpieczenia i zarządzania siecią gwiazdy i satelity, zobacz [Samouczek: Zabezpieczanie sieci w chmurze za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal](secure-cloud-network.md).
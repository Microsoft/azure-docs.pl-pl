---
title: Zdalna pomoc techniczna dla zapory platformy Azure
description: W tym artykule pokazano, jak Zapora platformy Azure może obsługiwać wymagania dotyczące zdalnego wymuszenia pracy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289643"
---
# <a name="azure-firewall-remote-work-support"></a>Zdalna pomoc techniczna dla zapory platformy Azure

Zapora systemu Azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. 

## <a name="firewall-rules"></a>Reguły zapory

Za pomocą zapory platformy Azure można zabezpieczyć dostęp do infrastruktury pulpitów wirtualnych (VDI) do sieci wirtualnej platformy Azure przy użyciu [reguł DNAT](rule-processing.md)zapory platformy Azure. Pulpit wirtualny systemu Windows (WVD) nie wymaga otwierania dowolnego dostępu przychodzącego do sieci wirtualnej. Należy jednak zezwolić na zestaw wychodzących połączeń sieciowych dla maszyn wirtualnych WVD, które działają w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [co to jest pulpit wirtualny systemu Windows?](../virtual-desktop/overview.md#requirements)

Można skonfigurować ten dostęp wychodzący przy użyciu reguł aplikacji zapory platformy Azure. Aby uzyskać więcej informacji, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/azure/virtual-desktop/).
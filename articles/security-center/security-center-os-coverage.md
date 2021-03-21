---
title: Platformy obsługiwane przez Azure Security Center | Microsoft Docs
description: Ten dokument zawiera listę platform obsługiwanych przez Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 88dc0760f320a99b0cbc99b7637dc34dd11dfecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465450"
---
# <a name="supported-platforms"></a>Obsługiwane platformy 

Na tej stronie są wyświetlane platformy i środowiska obsługiwane przez Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinacje środowisk <a name="vm-server"></a>

Azure Security Center obsługuje maszyny wirtualne i serwery w różnych typach środowisk hybrydowych:

* Tylko platforma Azure
* Azure i lokalnie
* Platforma Azure i inne chmury
* Azure, inne chmury i lokalne

W przypadku środowiska platformy Azure aktywowanego w ramach subskrypcji platformy Azure usługa Azure Security Center automatycznie odnajdzie zasoby IaaS wdrożone w ramach subskrypcji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Security Center zależy od [agenta log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Upewnij się, że na komputerach jest uruchomiony jeden z obsługiwanych systemów operacyjnych dla tego agenta, zgodnie z opisem na następujących stronach:

* [Agent Log Analytics obsługujący systemy operacyjne Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Agent Log Analytics dla systemów operacyjnych obsługiwanych przez system Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Upewnij się również, że Agent Log Analytics jest [prawidłowo skonfigurowany do wysyłania danych do Security Center](security-center-enable-data-collection.md#manual-agent)

Aby dowiedzieć się więcej na temat określonych funkcji Security Center dostępnych w systemach Windows i Linux, zobacz temat [pokrycie funkcji dla maszyn](security-center-services.md).

> [!NOTE]
> Mimo że usługa Azure Defender została zaprojektowana w celu ochrony serwerów, większość możliwości **usługi Azure Defender dla serwerów** jest obsługiwana na komputerach z systemem Windows 10. Jedną z funkcji, która nie jest obecnie obsługiwana, jest [Security Center zintegrowane rozwiązanie EDR: Microsoft Defender for Endpoint](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Zarządzane usługi maszyny wirtualnej <a name="virtual-machine"></a>

Maszyny wirtualne są również tworzone w ramach subskrypcji klienta jako część niektórych usług zarządzanych przez platformę Azure, takich jak Azure Kubernetes (AKS), Azure Databricks i wiele innych. Security Center odnajduje te maszyny wirtualne, a agent Log Analytics można zainstalować i skonfigurować w przypadku dostępności obsługiwanego systemu operacyjnego.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Obsługiwane są również maszyny wirtualne działające w usłudze w chmurze. Monitorowane są tylko role sieć Web i proces roboczy usług Cloud Services, które działają w gniazdach produkcyjnych. Aby dowiedzieć się więcej o usługach w chmurze, zobacz [Omówienie usługi Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Obsługiwana jest również ochrona maszyn wirtualnych znajdujących się w centrum Azure Stack. Aby uzyskać więcej informacji na temat integracji Security Center z centrum Azure Stack, zobacz Dołączanie [maszyn wirtualnych z centrum Azure Stack do Security Center](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane przy użyciu agenta log Analytics](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
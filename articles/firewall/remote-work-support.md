---
title: Zdalna pomoc techniczna dla zapory platformy Azure
description: W tym artykule pokazano, jak Zapora platformy Azure może obsługiwać wymagania dotyczące zdalnego wymuszenia pracy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658319"
---
# <a name="azure-firewall-remote-work-support"></a>Zdalna pomoc techniczna dla zapory platformy Azure

Zapora systemu Azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Obsługa wdrażania infrastruktury pulpitów wirtualnych (VDI)

Korzystanie z zasad domowych wymaga wielu organizacji IT, aby zająć się fundamentalnymi zmianami wydajności, sieci, zabezpieczeń i zarządzania. Pracownicy nie są chronieni przez zasady zabezpieczeń warstwowych skojarzone z usługami lokalnymi podczas pracy z domu. Wdrożenia infrastruktury pulpitów wirtualnych (VDI) na platformie Azure mogą pomóc organizacjom szybko reagować na to zmieniające się środowisko. Jednak konieczna jest ochrona przychodzącego/wychodzącego dostępu do Internetu do i z tych wdrożeń infrastruktury VDI. Aby chronić wdrożenia infrastruktury VDI, można użyć [reguł DNAT](rule-processing.md) zapory platformy Azure wraz z funkcjami filtrowania opartymi na [analizie zagrożeń](threat-intel.md) .

## <a name="azure-windows-virtual-desktop-support"></a>Obsługa pulpitu wirtualnego systemu Windows Azure

Windows Virtual Desktop to kompleksowa usługa wirtualizacji pulpitu i aplikacji działająca na platformie Azure. Jest to jedyna Infrastruktura pulpitu wirtualnego (VDI), która oferuje uproszczone zarządzanie, wielosesyjne Windows 10, optymalizacje dla Microsoft 365 aplikacji dla przedsiębiorstw i obsługę środowisk Usługi pulpitu zdalnego (RDS). Możesz wdrażać i skalować komputery stacjonarne i aplikacje z systemem Windows na platformie Azure w ciągu kilku minut oraz korzystać z wbudowanych funkcji zabezpieczeń i zgodności. Pulpit wirtualny systemu Windows nie wymaga otwierania żadnego dostępu przychodzącego do sieci wirtualnej. Należy jednak zezwolić na zestaw wychodzących połączeń sieciowych dla maszyn wirtualnych z systemem Windows, które działają w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Korzystanie z zapory platformy Azure do ochrony wdrożeń pulpitów wirtualnych systemu Windows](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [pulpitu wirtualnego systemu Windows](../virtual-desktop/index.yml).
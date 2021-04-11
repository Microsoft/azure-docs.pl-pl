---
title: Omówienie NACISKania usługi Azure Virtual Network | Microsoft Docs
description: Dowiedz się więcej o NACIŚNIĘCIu sieci wirtualnej. NACIŚNIĘCIe sieci wirtualnej umożliwia głębokie kopiowanie ruchu w sieci maszyn wirtualnych, które mogą być przesyłane strumieniowo do modułu zbierającego pakiety.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617186"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP
> [!IMPORTANT]
> Wersja zapoznawcza usługi Virtual Network jest obecnie wstrzymana we wszystkich regionach platformy Azure. Możesz wysłać wiadomość e-mail na adres <azurevnettap@microsoft.com> przy użyciu identyfikatora subskrypcji, aby powiadomić Cię o przyszłych aktualizacjach dotyczących wersji zapoznawczej. W tymczasowym systemie można używać rozwiązań opartych na agentach lub urządzenie WUS, które udostępniają funkcję widoczności dla [rozwiązań](#virtual-network-tap-partner-solutions) firmy Microsoft w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) . Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane pod kątem współpracy z siecią wirtualną, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).
Na poniższej ilustracji przedstawiono sposób działania narzędzia Virtual Network TAP. Możesz dodać konfigurację TAP w [interfejsie sieciowym](virtual-network-network-interface.md) , który jest dołączony do maszyny wirtualnej wdrożonej w sieci wirtualnej. Lokalizacją docelową jest adres IP sieci wirtualnej w tej samej sieci wirtualnej co monitorowany interfejs sieciowy lub [równorzędna Sieć wirtualna](virtual-network-peering-overview.md) . Rozwiązanie modułu zbierającego dla programu Virtual Network TAP można wdrożyć za pomocą wewnętrznego modułu równoważenia obciążenia platformy Azure w celu zapewnienia wysokiej dostępności.
![Jak działa NACIŚNIĘCIe sieci wirtualnej](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem sieci wirtualnej należy otrzymać wiadomość potwierdzającą, która została zarejestrowana w wersji zapoznawczej, i utworzyć co najmniej jedną maszynę wirtualną, która została utworzona przy użyciu [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) model wdrażania i rozwiązanie partnerskie do agregowania ruchu w ramach tego samego regionu platformy Azure. Jeśli nie masz rozwiązania partnerskiego w sieci wirtualnej, zapoznaj się z tematem [rozwiązania partnerskie](#virtual-network-tap-partner-solutions) , aby wdrożyć aplikację. Możesz użyć tego samego zasobu sieci wirtualnej, aby agregować ruch z wielu interfejsów sieciowych w ramach tej samej lub różnych subskrypcji. Jeśli monitorowane interfejsy sieciowe znajdują się w różnych subskrypcjach, subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory. Ponadto monitorowane interfejsy sieciowe i docelowy punkt końcowy do agregowania ruchu dla programu TAP mogą znajdować się w równorzędnych sieciach wirtualnych w tym samym regionie. Jeśli używasz tego modelu wdrażania, upewnij się, że [Komunikacja równorzędna sieci wirtualnej](virtual-network-peering-overview.md) jest włączona przed SKONFIGUROWANIEm naciskania sieci wirtualnej.

## <a name="permissions"></a>Uprawnienia

Konta używane do zastosowania konfiguracji TAP w interfejsach sieciowych muszą być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano wymagane akcje z poniższej tabeli:

| Akcja | Nazwa |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Wymagane do utworzenia, zaktualizowania, odczytania i usunięcia sieci wirtualnej naciśnij zasób |
| Microsoft. Network/networkInterfaces/odczyt | Wymagane do odczytania zasobu interfejsu sieciowego, na którym zostanie skonfigurowany wybór |
| Microsoft. Network/tapConfigurations/* | Wymagane do utworzenia, zaktualizowania, odczytania i usunięcia konfiguracji TAP w interfejsie sieciowym |


## <a name="virtual-network-tap-partner-solutions"></a>Rozwiązania partnerskie TAP sieci wirtualnej

### <a name="network-packet-brokers"></a>Brokerzy pakietów sieciowych

- [GigaVUE Cloud Suite for Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Sieć szkieletowa Big monitoring](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Analiza zabezpieczeń, zarządzanie wydajnością sieci i aplikacji

- [Zabezpieczenia w stanie aktywności](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Chmura Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis cyberbezpieczeństwa](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [VSTREAM]( https://www.netscout.com/marketplace-azure)
- [Zabezpieczenia NoName](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platformę monitora RSA](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć sieć wirtualną TAP](tutorial-tap-virtual-network-cli.md).

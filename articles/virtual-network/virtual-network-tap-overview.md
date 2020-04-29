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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279613"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) . Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane pod kątem współpracy z siecią wirtualną, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Usługa Virtual Network TAP jest obecnie dostępna w wersji zapoznawczej we wszystkich regionach świadczenia usługi Azure. Aby skorzystać z usługi Virtual Network, musisz zarejestrować się w wersji zapoznawczej, wysyłając <azurevnettap@microsoft.com> wiadomość e-mail na adres z identyfikatorem subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie możesz korzystać z tej możliwości, dopóki nie otrzymasz wiadomości e-mail z potwierdzeniem. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie powinna być używana na potrzeby obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure. 

## <a name="virtual-network-tap-partner-solutions"></a>Rozwiązania partnerskie TAP sieci wirtualnej

### <a name="network-packet-brokers"></a>Brokerzy pakietów sieciowych

- [Sieć szkieletowa Big monitoring](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analiza zabezpieczeń, zarządzanie wydajnością sieci i aplikacji

- [Zabezpieczenia w stanie aktywności](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Chmura Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis cyberbezpieczeństwa](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [VSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platformę monitora RSA](https://www.rsa.com/azure)
- [Vectra usługi cognito](https://vectra.ai/microsoftazure)

Na poniższej ilustracji przedstawiono sposób działania narzędzia Virtual Network TAP. Możesz dodać konfigurację TAP w [interfejsie sieciowym](virtual-network-network-interface.md) , który jest dołączony do maszyny wirtualnej wdrożonej w sieci wirtualnej. Lokalizacją docelową jest adres IP sieci wirtualnej w tej samej sieci wirtualnej co monitorowany interfejs sieciowy lub [równorzędna Sieć wirtualna](virtual-network-peering-overview.md) . Rozwiązanie modułu zbierającego dla programu Virtual Network TAP można wdrożyć za pomocą wewnętrznego modułu równoważenia obciążenia platformy Azure w celu zapewnienia wysokiej dostępności. Aby oszacować opcje wdrażania dla poszczególnych rozwiązań, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć sieć wirtualną TAP](tutorial-tap-virtual-network-cli.md).

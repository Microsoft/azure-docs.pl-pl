---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
description: Informacje o rozszerzeniach maszyn wirtualnych platformy Azure i sposobach ich używania z maszynami wirtualnymi platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74072973"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które udostępniają zadania związane z konfiguracją i automatyzacją po wdrożeniu na maszynach wirtualnych platformy Azure, możesz używać istniejących obrazów, a następnie dostosowywać je w ramach wdrożeń, co pozwala uzyskać możliwość tworzenia obrazów niestandardowych.

Platforma Azure obsługuje wiele rozszerzeń, które przedziały od konfiguracji maszyny wirtualnej, monitorowania, zabezpieczeń i narzędzi. Wydawcy pobierają aplikację, zaznaczają ją w rozszerzeniu i upraszczają instalację, dlatego należy podać obowiązkowe parametry. 

 Jeśli aplikacja w repozytorium rozszerzeń nie istnieje, można użyć rozszerzenia niestandardowego skryptu i skonfigurować maszynę wirtualną przy użyciu własnych skryptów i poleceń, jeśli nie istnieją.

Przykłady kluczowych scenariuszy, w których rozszerzenia są używane dla:
* Konfiguracja maszyny wirtualnej pozwala zainstalować agentów konfiguracji maszyny wirtualnej i skonfigurować maszynę wirtualną przy użyciu programu PowerShell DSC (Konfiguracja żądanego stanu), Chef, Puppet i niestandardowych rozszerzeń skryptów. 
* Produkty AV, takie jak Symantec, ESET.
* Narzędzie do luk w zabezpieczeniach maszyny wirtualnej, takie jak Qualys, Rapid7, HPE.
* Narzędzia do monitorowania maszyn wirtualnych i aplikacji, takie jak DynaTrace, Azure Network Watcher, Site24x7 i rozwiązania Stackify.

Rozszerzenia można powiązać z nowym wdrożeniem maszyny wirtualnej. Mogą na przykład być częścią większego wdrożenia, konfigurowania aplikacji na maszynach wirtualnych lub uruchamiania ich na wszystkich obsługiwanych systemach obsługujących rozszerzenia po wdrożeniu.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak mogę znaleźć dostępne rozszerzenia?
Dostępne rozszerzenia można wyświetlić w bloku maszyny wirtualnej w portalu, w obszarze rozszerzenia reprezentuje ona tylko niewielką ilość, aby uzyskać pełną listę, można użyć narzędzi interfejsu wiersza polecenia, zobacz [odnajdywanie rozszerzeń maszyn wirtualnych dla systemu Linux](features-linux.md) i [odnajdywanie rozszerzeń maszyn wirtualnych w systemie Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak mogę zainstalować rozszerzenie?
Rozszerzenia maszyny wirtualnej platformy Azure można zarządzać przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell, szablonów Azure Resource Manager i Azure Portal. Aby wypróbować rozszerzenie, możesz przejść do Azure Portal, wybrać rozszerzenie niestandardowego skryptu, a następnie przekazać polecenie/skrypt i uruchomić rozszerzenia.

Jeśli chcesz mieć to samo rozszerzenie dodane w portalu według interfejsu wiersza polecenia lub szablonu Menedżer zasobów, zobacz inną dokumentację rozszerzenia, taką jak rozszerzenie [niestandardowego skryptu systemu Windows](custom-script-windows.md) i [rozszerzenia niestandardowego skryptu](custom-script-linux.md)w systemie Linux.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak mogę zarządzać cyklem życia aplikacji rozszerzenia?
Nie musisz łączyć się z maszyną wirtualną bezpośrednio, aby zainstalować lub usunąć rozszerzenie. Gdy cykl życia aplikacji rozszerzenia platformy Azure jest zarządzany poza maszyną wirtualną i jest zintegrowany z platformą Azure, można również uzyskać zintegrowany stan rozszerzenia.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Coś innego, co muszę zrobić w przypadku rozszerzeń?
Rozszerzenia instalują aplikacje, podobnie jak w przypadku niektórych aplikacji, istnieją pewne wymagania, w przypadku rozszerzenia istnieje lista obsługiwanych systemów operacyjnych systemów Windows i Linux oraz wymaganych jest zainstalowanie agentów maszyny wirtualnej platformy Azure. Niektóre pojedyncze aplikacje rozszerzenia maszyny wirtualnej mogą mieć własne wymagania wstępne dotyczące środowiska, takie jak dostęp do punktu końcowego.

## <a name="troubleshoot-extensions"></a>Rozwiązywanie problemów z rozszerzeniami

Informacje dotyczące rozwiązywania problemów dla każdego rozszerzenia można znaleźć w sekcji **Rozwiązywanie problemów i pomoc techniczna** w temacie Omówienie rozszerzenia. Poniżej znajduje się lista dostępnych informacji dotyczących rozwiązywania problemów:

| Przestrzeń nazw | Rozwiązywanie problemów |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Zależność Azure Monitor dla systemu Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor zależność dla systemu Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption dla systemu Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Azure Disk Encryption dla systemu Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. COMPUTE. customscriptextension | [Skrypt niestandardowy dla systemu Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Konfiguracja żądanego stanu dla systemu Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Konfiguracja żądanego stanu dla systemu Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [Rozszerzenie sterownika GPU NVIDIA dla systemu Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [Rozszerzenie sterownika GPU NVIDIA dla systemu Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Rozszerzenie chroniące przed złośliwym kodem dla systemu Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Azure Monitor dla systemu Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Azure Monitor dla systemu Windows](oms-windows.md#troubleshoot-and-support) |
| rozwiązania Stackify. linuxagent. Extension. stackifylinuxagentextension | [Rozwiązania Stackify retrace dla systemu Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Resetowanie hasła (VMAccess) dla systemu Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Migawka dla systemu Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Migawka dla systemu Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń systemu Linux, zobacz [Azure VM Extensions and features for Linux](features-linux.md).
* Aby uzyskać więcej informacji o działaniu agenta i rozszerzeń gościa systemu Windows, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure dla systemu Windows](features-windows.md).  
* Aby zainstalować agenta gościa systemu Windows, zobacz [Omówienie agenta maszyny wirtualnej systemu Windows Azure](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [Omówienie agenta maszyny wirtualnej z systemem Linux](agent-linux.md).  


---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
description: Dowiedz się więcej o rozszerzeniach maszyn wirtualnych platformy Azure
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: b1dd26fce2e0a761ceed211933cb79ce518905e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965888"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
Rozszerzenia to małe aplikacje, które zapewniają konfigurację i automatyzację po wdrożeniu na maszynach wirtualnych platformy Azure. Platforma Azure obsługuje wiele rozszerzeń obejmujących konfiguracje maszyn wirtualnych, monitorowanie, zabezpieczenia i narzędzia. Wydawcy pobierają aplikację, zawijają ją do rozszerzenia i upraszczają instalację. Wszystko, co musisz zrobić, zapewnia parametry obowiązkowe. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak mogę znaleźć dostępne rozszerzenia?
Dostępne rozszerzenia można wyświetlić, zaznaczając maszynę wirtualną, wybierając **rozszerzenia** w menu po lewej stronie. Aby uzyskać pełną listę rozszerzeń, zobacz [odnajdywanie rozszerzeń maszyn wirtualnych dla systemu Linux](features-linux.md) i [odnajdywanie rozszerzeń maszyn wirtualnych dla Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak mogę zainstalować rozszerzenie?
Rozszerzenia maszyny wirtualnej platformy Azure można zarządzać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów Menedżer zasobów i Azure Portal. Aby wypróbować rozszerzenie, przejdź do Azure Portal, wybierz rozszerzenie niestandardowego skryptu, a następnie Przekaż polecenie lub skrypt, aby uruchomić rozszerzenie.

Aby uzyskać więcej informacji, zobacz rozszerzenia [niestandardowego skryptu systemu Windows](custom-script-windows.md) i [rozszerzenie niestandardowego skryptu](custom-script-linux.md)w systemie Linux.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak mogę zarządzać cyklem życia aplikacji rozszerzenia?
Nie musisz łączyć się z maszyną wirtualną bezpośrednio, aby zainstalować lub usunąć rozszerzenie. Cykl życia rozszerzenia platformy Azure jest zarządzany poza maszyną wirtualną i zintegrowany z platformą Azure.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Coś innego, co muszę zrobić w przypadku rozszerzeń?
Niektóre pojedyncze aplikacje rozszerzenia maszyny wirtualnej mogą mieć własne wymagania wstępne dotyczące środowiska, takie jak dostęp do punktu końcowego. Każde rozszerzenie zawiera artykuł objaśniający wymagania wstępne, w tym systemy operacyjne, które są obsługiwane.

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
| vmaccessforlinux. Microsoft. ostcextensions | [Resetowanie hasła dla systemu Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Migawka dla systemu Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Migawka dla systemu Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń systemu Linux, zobacz [Azure VM Extensions and features for Linux](features-linux.md).
* Aby uzyskać więcej informacji o działaniu agenta i rozszerzeń gościa systemu Windows, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure dla systemu Windows](features-windows.md).  
* Aby zainstalować agenta gościa systemu Windows, zobacz [Omówienie agenta maszyny wirtualnej systemu Windows Azure](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [Omówienie agenta maszyny wirtualnej z systemem Linux](agent-linux.md).  


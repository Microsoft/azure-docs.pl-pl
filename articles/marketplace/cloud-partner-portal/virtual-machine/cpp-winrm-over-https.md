---
title: Windows Remote Management za pośrednictwem protokołu HTTPS dla platformy Azure | Portal Azure Marketplace
description: Wyjaśniono, jak skonfigurować maszynę wirtualną z systemem Windows hostowaną na platformie Azure, aby można było zarządzać nią zdalnie przy użyciu programu PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 88941b334a9c218365bd2d97046dbb6c32d50f35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142765"
---
# <a name="windows-remote-management-over-https"></a>Zdalne zarządzanie systemem Windows przez protokół HTTPS

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

W tej sekcji opisano sposób konfigurowania maszyny wirtualnej opartej na platformie Azure, która umożliwia zdalne zarządzanie i wdrażanie za pomocą programu PowerShell.  Aby włączyć obsługę zdalną programu PowerShell, docelowa maszyna wirtualna musi uwidaczniać punkt końcowy HTTPS Windows Remote Management (WinRM).  Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, zobacz [Uruchamianie poleceń zdalnych](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Aby uzyskać więcej informacji na temat usługi WinRM, zobacz [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Jeśli maszyna wirtualna została utworzona przy użyciu jednego z "klasycznych" rozwiązań platformy Azure — portalu Azure Service Manager lub przestarzałych [interfejs API zarządzania usługami platformy Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))— jest ona automatycznie konfigurowana za pomocą punktu końcowego usługi WinRM.  Jeśli jednak utworzysz maszynę wirtualną przy użyciu dowolnego z następujących podejść "nowoczesne" platformy Azure, maszyna wirtualna *nie* zostanie skonfigurowana na potrzeby usługi WinRM za pośrednictwem protokołu HTTPS.

- Korzystając z [Azure Portal](https://portal.azure.com/), zazwyczaj z zatwierdzonej bazy, zgodnie z opisem w sekcji [Tworzenie wirtualnego dysku twardego zgodnego z platformą Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Korzystanie z szablonów Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Za pomocą Azure PowerShell lub powłoki poleceń interfejsu wiersza polecenia platformy Azure.  Aby zapoznać się z przykładami, zobacz [Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) i [Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)

Ten punkt końcowy usługi WinRM jest również wymagany do uruchomienia zestawu narzędzi certyfikacji na potrzeby dołączania maszyny wirtualnej, zgodnie z opisem w temacie [certyfikowanie obrazu maszyny wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Z kolei zazwyczaj maszyny wirtualne z systemem Linux są zarządzane zdalnie za pomocą poleceń [interfejsu wiersza polecenia platformy Azure lub systemu](https://docs.microsoft.com/cli/azure) Linux z poziomu konsoli SSH.  Platforma Azure udostępnia również kilka alternatywnych metod [uruchamiania skryptów na maszynie wirtualnej z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  W przypadku bardziej złożonych scenariuszy istnieje wiele rozwiązań do automatyzacji i integracji dostępnych dla maszyn wirtualnych opartych na systemie Windows lub Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurowanie i wdrażanie za pomocą usługi WinRM

Punkt końcowy usługi WinRM dla maszyny wirtualnej opartej na systemie Windows można skonfigurować na dwa różne etapy jego rozwoju:

- Podczas tworzenia — podczas wdrażania maszyny wirtualnej na istniejącym dysku VHD.  Jest to preferowane podejście dla nowych ofert.  Takie podejście wymaga utworzenia certyfikatu platformy Azure przy użyciu dostarczonych szablonów Azure Resource Manager i uruchomienia niestandardowych skryptów programu PowerShell.
- Po wdrożeniu — na istniejącej maszynie wirtualnej hostowanej na platformie Azure.  Użyj tej metody, jeśli masz już rozwiązanie maszyny wirtualnej wdrożone na platformie Azure i musisz włączyć zdalne zarządzanie systemem Windows.  To podejście wymaga ręcznej zmiany w Azure Portal i wykonania skryptu na docelowej maszynie wirtualnej.


## <a name="next-steps"></a>Następne kroki
Jeśli tworzysz nową maszynę wirtualną, możesz włączyć usługę WinRM podczas [wdrażania maszyny wirtualnej ze swoich wirtualnych dysków twardych](./cpp-deploy-vm-vhd.md).  W przeciwnym razie usługa WinRM może być włączona na istniejącej maszynie wirtualnej

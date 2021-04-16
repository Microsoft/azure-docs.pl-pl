---
title: Azure Confidential Computing — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące poufnego przetwarzania na platformie Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 05c98102109d1925eb78d4558faf62b366801e77
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538990"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Często zadawane pytania dotyczące poufnego przetwarzania na platformie Azure

Ten artykuł zawiera odpowiedzi na niektóre najczęściej zadawane pytania dotyczące uruchamiania poufnych obciążeń obliczeniowych [na maszynach wirtualnych platformy Azure.](overview.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w witrynie [MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Swój problem możesz opublikować na tych forach lub opublikować w [ @AzureSupport serwisie Twitter.](https://twitter.com/AzureSupport) Możesz również przesłać wniosek o pomoc techniczną platformy Azure. Aby przesłać wniosek o pomoc techniczną, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)wybierz pozycję Uzyskaj pomoc techniczną.

## <a name="confidential-computing-virtual-machines"></a>Poufne przetwarzanie Virtual Machines <a id="vm-faq"></a>

**Jak wdrożyć maszyny wirtualne serii DCsv2 na platformie Azure?**

Oto kilka sposobów wdrażania maszyny wirtualnej DCsv2:
   - Korzystanie z [Azure Resource Manager szablonu](../virtual-machines/windows/template-description.md)
   - Z [Azure Portal](https://portal.azure.com/#create/hub)
   - W [szablonie rozwiązania Azure Confidential Computing (Virtual Machine)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) w witrynie Marketplace. Szablon rozwiązania z platformy handlowej pomoże ograniczyć klienta do obsługiwanych scenariuszy (regiony, obrazy, dostępność, szyfrowanie dysków). 

**Czy wszystkie obrazy systemu operacyjnego będą działać z poufnym przetwarzaniem na platformie Azure?**

Nie. Maszyny wirtualne można wdrożyć tylko na maszynach operacyjnych 2. generacji z systemami Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter i Windows Server 2016 Datacenter. Przeczytaj więcej na temat maszyn wirtualnych 2. generacji w [systemach Linux i](../virtual-machines/generation-2.md) [Windows](../virtual-machines/generation-2.md)

**Maszyny wirtualne DCsv2 są wyszarowane w portalu i nie mogę wybrać jednej z nich**

Na podstawie bąbelka informacyjnego obok maszyny wirtualnej należy podjąć różne akcje:
   -    **UnsupportedGeneration:** zmień generację obrazu maszyny wirtualnej na "Gen2".
   -    **NotAvailableForSubscription:** region nie jest jeszcze dostępny dla Twojej subskrypcji. Wybierz dostępny region.
   -    **InsufficientQuota:** [Utwórz wniosek o pomoc techniczną w celu zwiększenia limitu przydziału](../azure-portal/supportability/per-vm-quota-requests.md). Subskrypcje bezpłatnej wersji próbnej nie mają limitu przydziału dla maszyn wirtualnych do poufnego przetwarzania. 

**Maszyny wirtualne DCsv2 nie są wyświetlane podczas próby wyszukania ich w selektorze rozmiaru portalu**

Upewnij się, że wybrano [dostępny region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines). Upewnij się również, że w selektorze rozmiaru jest wybierana "wyczyść wszystkie filtry". 

**Czy mogę włączyć przyspieszoną sieć przy użyciu poufnego przetwarzania na platformie Azure?**

 Nie. Przyspieszona sieć nie jest obsługiwana na DC-Series ani DCsv2-Series wirtualnych. Przyspieszonej sieci nie można włączyć dla dowolnego wdrożenia maszyny wirtualnej poufnego przetwarzania ani Azure Kubernetes Service klastra działającego na poufnym przetwarzaniu.

**Czy można używać Azure Dedicated Host z tymi maszynami?**

Tak. Azure Dedicated Host obsługują maszyny wirtualne serii DCsv2. Azure Dedicated Host udostępnia serwer fizyczny z jedną dzierżawą, na którym można uruchamiać maszyny wirtualne. Użytkownicy zwykle używają Azure Dedicated Host, aby spełnić wymagania dotyczące zgodności dotyczące zabezpieczeń fizycznych, integralności danych i monitorowania. 

**Otrzymuję błąd Azure Resource Manager wdrażania szablonu: "Nie można ukończyć operacji, ponieważ powoduje przekroczenie zatwierdzonego standardowego limitu przydziału rdzeni rodziny DcsV2"**

[Utwórz wniosek o pomoc techniczną, aby zwiększyć limit przydziału.](../azure-portal/supportability/per-vm-quota-requests.md) Subskrypcje bezpłatnej wersji próbnej nie mają limitu przydziału dla maszyn wirtualnych do poufnego przetwarzania. 

**Jaka jest różnica między maszynami DCsv2-Series i DC-Series wirtualnych?**

DC-Series wirtualne działają na starszych 6-rdzeniowych procesorach Intel z technologią Intel SGX i mają mniej całkowitej pamięci, mniej pamięci eklawy stronicowanej pamięci podręcznej (EPC) i są dostępne tylko w dwóch regionach (Wschodnie stany USA i Europa Zachodnia w rozmiarach Standard_DC2s i Standard_DC4s). Nie ma planów, aby te maszyny wirtualne były ogólnie dostępne i nie są zalecane do użycia w środowisku produkcyjnym. Aby wdrożyć te maszyny wirtualne, użyj wystąpienia poufnego obliczenia DC-Series  [wirtualnej [wersja zapoznawcza] w witrynie](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace.

**Czy maszyny wirtualne DCsv2 są dostępne globalnie?**

Nie. Obecnie te maszyny wirtualne są dostępne tylko w wybranych regionach. Sprawdź stronę [products by regions (Produkty według regionów),](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) aby uzyskać najnowsze dostępne regiony. 

**Czy hiperwątkność jest wyłączona na tych maszynach?**

Hiperwątkność jest wyłączona dla wszystkich klastrów poufnego przetwarzania na platformie Azure.

**Jak mogę zainstalować zestaw SDK Open Enclave na maszynach wirtualnych DCsv2?**
   
Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu SDK OE na platformie Azure lub komputerze lokalnym, postępuj zgodnie z instrukcjami w witrynie GitHub zestawu SDK open [enclave.](https://github.com/openenclave/openenclave)
     
Instrukcje dotyczące instalacji specyficzne dla systemu operacyjnego można również znaleźć w witrynie GitHub zestawu SDK Open Enclave SDK:
   - [Instalowanie zestawu SDK OE w systemie Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Instalowanie zestawu OE SDK w systemie Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
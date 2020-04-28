---
title: Dane poufne dotyczące platformy Azure — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure poufnego przetwarzania.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189449"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Często zadawane pytania dotyczące usługi Azure poufnego przetwarzania

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [poufnych obciążeń obliczeniowych na platformie Azure](overview.md).

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go [ @AzureSupport w](https://twitter.com/AzureSupport)serwisie Twitter. Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na [stronie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)wybierz pozycję Uzyskaj pomoc techniczną.

## <a name="confidential-computing-virtual-machines"></a>Virtual Machines danych poufnych<a id="vm-faq"></a>

1. **Jak można rozpocząć wdrażanie maszyn wirtualnych z serii DCsv2?**

   Oto kilka sposobów wdrożenia maszyny wirtualnej DCsv2:
   - Korzystanie z [szablonu Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Z [Azure Portal](https://portal.azure.com/#create/hub)
   - W szablonie rozwiązania Marketplace dotyczącego zasobów [obliczeniowych (maszyna wirtualna) na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) . Szablon rozwiązania Marketplace pomaga ograniczyć klienta do obsługiwanych scenariuszy (regionów, obrazów, dostępności, szyfrowania dysków). 

1. **Czy wszystkie obrazy systemu operacyjnego będą współpracują z usługą Azure poufne dane?**

   Nie. Maszyny wirtualne można wdrażać tylko na maszynach wirtualnych generacji 2. Oferujemy obsługę generacji 2 dla serwera Ubuntu Server 18,04, Ubuntu Server 16,04 i systemu Windows Server 2016 centrum danych. Przeczytaj więcej na temat maszyn wirtualnych generacji 2 w systemach [Linux](../virtual-machines/linux/generation-2.md) i [Windows](../virtual-machines/windows/generation-2.md)

1. **Maszyny wirtualne DCsv2 są wyszarzone w portalu i nie można ich wybrać**

   W oparciu o dymek informacyjny obok maszyny wirtualnej istnieją różne akcje do wykonania:
    -   **UnsupportedGeneration**: Zmień generację obrazu maszyny wirtualnej na "Gen2".
    -   **NotAvailableForSubscription** : region nie jest jeszcze dostępny dla Twojej subskrypcji. Wybierz dostępny region.
    -   **InsufficientQuota**: [Utwórz żądanie pomocy technicznej w celu zwiększenia limitu przydziału](../azure-portal/supportability/per-vm-quota-requests.md). Subskrypcje bezpłatnej wersji próbnej nie mają przydziału dla maszyn wirtualnych służących do przetwarzania poufnego. 

1. **DCsv2 maszyny wirtualne nie są wyświetlane podczas próby wyszukania ich w selektorze rozmiaru portalu**

   Upewnij się, że wybrano dostępny region. Upewnij się również, że wybrano opcję "Wyczyść wszystkie filtry" w selektorze rozmiarów. 

1. **Jaka jest różnica między maszynami wirtualnymi serii DCsv2 i kontrolerów domeny?**

   Maszyny wirtualne z serii DC działają na starszym 6-rdzeniowych procesorach Intel z technologią Intel SGX. Mają one mniej całkowitą pamięć, mniej pamięci sygnatury EPC (enklawy strony) i są dostępne w mniej regionów. Te maszyny wirtualne są dostępne tylko w regionach Wschodnie stany USA i Europa Zachodnia są dostępne w dwóch rozmiarach: Standard_DC2s i Standard_DC4s. Nie będą one znajdować się w stanie GA i mogą być wdrażane tylko w ramach wystąpienia portalu Marketplace w przypadku [maszyn wirtualnych z serii DC [wersja zapoznawcza]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

1. **Czy maszyny wirtualne DCsv2 są dostępne globalnie?**

   Nie. te maszyny wirtualne są dostępne tylko w wybranych regionach. Zapoznaj się ze [stroną produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) , aby uzyskać najnowsze dostępne regiony. 

1. **Jak mogę zainstalować zestaw SDK open enklawy?**
   
   Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu OE SDK na komputerze, na którym znajduje się na platformie Azure lub lokalnie, postępuj zgodnie z instrukcjami w temacie [Open ENKLAWY SDK GitHub](https://github.com/openenclave/openenclave).
     
   Możesz również przejść do sekcji Open enklawy SDK GitHub, aby uzyskać instrukcje dotyczące instalacji specyficzne dla systemu operacyjnego:
     - [Instalowanie zestawu OE SDK w systemie Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Instalowanie zestawu OE SDK w systemie Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Instalowanie zestawu OE SDK w systemie Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)

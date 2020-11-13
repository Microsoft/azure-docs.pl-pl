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
ms.openlocfilehash: 9df3d9771029e6d72e9d0092a129cddc27be6cd7
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564110"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Często zadawane pytania dotyczące usługi Azure poufnego przetwarzania

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [poufnych obciążeń obliczeniowych na maszynach wirtualnych platformy Azure](overview.md).

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go w serwisie [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na [stronie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)wybierz pozycję Uzyskaj pomoc techniczną.

## <a name="confidential-computing-virtual-machines"></a>Virtual Machines danych poufnych <a id="vm-faq"></a>

**Jak można wdrożyć maszyny wirtualne z serii DCsv2 na platformie Azure?**

Oto kilka sposobów wdrożenia maszyny wirtualnej DCsv2:
   - Korzystanie z [szablonu Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Z [Azure Portal](https://portal.azure.com/#create/hub)
   - W szablonie rozwiązania Marketplace dotyczącego [usługi Azure CONFIDENTIAL (maszyna wirtualna)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) . Szablon rozwiązania Marketplace pomaga ograniczyć klienta do obsługiwanych scenariuszy (regionów, obrazów, dostępności, szyfrowania dysków). 

**Czy wszystkie obrazy systemu operacyjnego będą współpracują z usługą Azure poufne dane?**

Nie. Maszyny wirtualne można wdrażać tylko na maszynach operacyjnych generacji 2 z Ubuntu Server 18,04, Ubuntu Server 16,04, Windows Server 2019 Datacenter i Windows Server 2016 Datacenter. Przeczytaj więcej na temat maszyn wirtualnych generacji 2 w systemach [Linux](../virtual-machines/generation-2.md) i [Windows](../virtual-machines/generation-2.md)

**Maszyny wirtualne DCsv2 są wyszarzone w portalu i nie można ich wybrać**

W oparciu o dymek informacyjny obok maszyny wirtualnej istnieją różne akcje do wykonania:
   -    **UnsupportedGeneration** : Zmień generację obrazu maszyny wirtualnej na "Gen2".
   -    **NotAvailableForSubscription** : region nie jest jeszcze dostępny dla Twojej subskrypcji. Wybierz dostępny region.
   -    **InsufficientQuota** : [Utwórz żądanie pomocy technicznej w celu zwiększenia limitu przydziału](../azure-portal/supportability/per-vm-quota-requests.md). Subskrypcje bezpłatnej wersji próbnej nie mają przydziału dla maszyn wirtualnych służących do przetwarzania poufnego. 

**DCsv2 maszyny wirtualne nie są wyświetlane podczas próby wyszukania ich w selektorze rozmiaru portalu**

Upewnij się, że wybrano [dostępny region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines). Upewnij się również, że wybrano opcję "Wyczyść wszystkie filtry" w selektorze rozmiarów. 

**Otrzymuję błąd wdrażania szablonu Azure Resource Manager: "nie można ukończyć operacji, ponieważ spowoduje to przekroczenie zatwierdzonego limitu przydziału rdzeni rodziny DcsV2**

[Utwórz żądanie obsługi, aby zwiększyć limit przydziału](../azure-portal/supportability/per-vm-quota-requests.md). Subskrypcje bezpłatnej wersji próbnej nie mają przydziału dla maszyn wirtualnych służących do przetwarzania poufnego. 

**Jaka jest różnica między maszynami wirtualnymi DCsv2-Series i DC-Series?**

DC-Series maszyny wirtualne działają na starszym 6-rdzeniowych procesorach Intel z technologią Intel SGX i mają mniej łączną ilość pamięci, mniej enklawy pamięć podręczną strony (EPC) i są dostępne tylko w dwóch regionach (Wschodnie stany USA i Europa Zachodnia w systemach Standard_DC2s i Standard_DC4s). Nie ma żadnych planów, aby te maszyny wirtualne były ogólnie dostępne i nie są zalecane do użycia w środowisku produkcyjnym. Aby wdrożyć te maszyny wirtualne, użyj wystąpienia portalu Marketplace dotyczącego  [przetwarzania poufnego DC-Series maszyny wirtualnej [wersja zapoznawcza]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) .

**Czy maszyny wirtualne DCsv2 są dostępne globalnie?**

Nie. W tej chwili te maszyny wirtualne są dostępne tylko w wybranych regionach. Zapoznaj się ze [stroną produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) , aby uzyskać najnowsze dostępne regiony. 

**Czy funkcja Hyper-Threading jest wyłączona na tych maszynach?**

Funkcja Hyper-Threading jest wyłączona dla wszystkich poufnych klastrów obliczeniowych platformy Azure.

**Jak mogę zainstalować zestaw SDK open enklawy na maszynach wirtualnych DCsv2?**
   
Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu OE SDK na komputerze z systemem Azure lub lokalnym, postępuj zgodnie z instrukcjami w witrynie [Open ENKLAWY SDK GitHub](https://github.com/openenclave/openenclave).
     
Możesz również zapoznać się z instrukcją instalacji enklawy SDK usługi GitHub dla systemu operacyjnego:
   - [Instalowanie zestawu OE SDK w systemie Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Instalowanie zestawu OE SDK w systemie Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Instalowanie zestawu OE SDK w systemie Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
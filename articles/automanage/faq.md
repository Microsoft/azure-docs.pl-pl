---
title: Azure automanage dla maszyn wirtualnych — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure automanage dla maszyn wirtualnych.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 003f97c99de7dd4be79e820e822b6071f45ed146
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714965"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Często zadawane pytania dotyczące usługi Azure automanage dla maszyn wirtualnych

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące [usługi Azure automanage dla maszyn wirtualnych](automanage-virtual-machines.md).

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go w serwisie [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na [stronie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)wybierz pozycję **Uzyskaj pomoc techniczną**.


## <a name="azure-automanage-for-virtual-machines"></a>Usługa Azure automanage dla maszyn wirtualnych

**Jakie są wszystkie wymagania wstępne wymagane do włączenia usługi Azure automanage?**

Poniżej przedstawiono wymagania wstępne dotyczące włączania usługi Azure automanage:
- Tylko maszyny wirtualne z systemem Windows Server
- Maszyny wirtualne muszą być uruchomione
- Maszyny wirtualne muszą znajdować się w obsługiwanym regionie
- Użytkownik musi mieć odpowiednie uprawnienia
- Tylko maszyny wirtualne z zestawami nieskalowanymi
- Maszyny wirtualne nie mogą łączyć się z obszarem roboczym usługi log Analytics w innej subskrypcji
- W tej chwili usługa autozarządzania nie obsługuje subskrypcji piaskownicy

**Jakie uprawnienia RBAC platformy Azure są potrzebne do włączenia autozarządzania?**

Jeśli włączysz Autozarządzanie na maszynie wirtualnej z istniejącym kontem autozarządzania, musisz mieć rolę współautora dla grupy zasobów, w której znajduje się maszyna wirtualna.

Jeśli podczas włączania korzystasz z nowego konta Autozarządzanie, musisz mieć rolę właściciela lub mieć rolę administratora dostępu współautor i użytkownika do subskrypcji.


**Jakie regiony są obsługiwane?**

Maszyny wirtualne w następujących regionach są obsługiwane: Europa Zachodnia, Wschodnie stany USA, zachodnie stany USA 2, Kanada środkowa, zachodnio-środkowe stany USA.


**Jakie możliwości usługa Azure automanage jest zautomatyzowana?**

Autozarządzanie rejestracjami, konfigurowaniem i monitorowaniem w całym cyklu życia maszyny wirtualnej usługi wymienione w [tym miejscu](virtual-machines-best-practices.md).

**Czy usługa Azure automanage współpracuje z maszynami wirtualnymi z obsługą usługi Azure Arc?**

Autozarządzanie obecnie nie obsługuje maszyn wirtualnych z włączoną funkcją Arc.

**Czy mogę dostosować konfiguracje na platformie Azure Autozarządzanie?**

Klienci mogą dostosowywać ustawienia dla określonych usług, takich jak Azure Backup przechowywanie, za poorednictwem preferencji konfiguracji. Aby uzyskać pełną listę ustawień, które można zmienić, zobacz [naszą dokumentację.](virtual-machines-best-practices.md)


**Czy usługa Azure automanage działa z maszynami wirtualnymi z systemem Linux i Windows?**

Obecnie Autozarządzanie obsługuje maszyny wirtualne platformy Azure z systemem Windows Server.


**Czy mogę selektywnie zastosować Autozarządzanie tylko na zestawie maszyn wirtualnych?**

Autozarządzanie można włączyć za pomocą kliknięcia i punktu prostoty dla wybranych nowych i istniejących maszyn wirtualnych. Autozarządzanie można także wyłączyć w dowolnym momencie.


**Czy usługa Azure automanage obsługuje maszyny wirtualne w zestawie skalowania maszyn wirtualnych?**

Nie, usługa Azure automanage nie obsługuje obecnie maszyn wirtualnych w zestawie skalowania maszyn wirtualnych.


**Ile kosztuje usługa Azure automanage?**

Usługa Azure automanage jest dostępna bez dodatkowych kosztów w publicznej wersji zapoznawczej. Dołączenie zasobów platformy Azure, takich jak Azure Backup, spowoduje naliczenie kosztu.


**Czy mogę zastosować Autozarządzanie za pomocą usługi Azure Policy?**

Tak, mamy wbudowane zasady, które będą automatycznie stosowały Autozarządzanie do wszystkich maszyn wirtualnych w określonym zakresie. Zostanie również określony profil konfiguracji (DevTest lub produkcyjny) wraz z kontem Autozarządzanie. Dowiedz się więcej na temat włączania autozarządzania za pomocą usługi Azure Policy [tutaj](virtual-machines-policy-enable.md).


**Co to jest konto Autozarządzanie?**

Konto automanage to plik MSI (tożsamość usługi zarządzanej), który zapewnia kontekst zabezpieczeń lub tożsamość, w ramach której występują operacje zautomatyzowane.


**Czy włączenie autozarządzania ma wpływ na wszystkie dodatkowe maszyny wirtualne oprócz wybranych maszyn wirtualnych?**

Jeśli maszyna wirtualna jest połączona z istniejącym obszarem roboczym Log Analytics, użyjemy tego obszaru roboczego do zastosowania następujących rozwiązań: Change Tracking, spisu i Update Management. Dla wszystkich maszyn wirtualnych połączonych z tym obszarem roboczym będą dostępne te rozwiązania.


**Czy mogę zmienić profil konfiguracji mojej maszyny wirtualnej?**

W tej chwili należy wyłączyć Autozarządzanie dla tej maszyny wirtualnej, a następnie włączyć ją ponownie przy użyciu odpowiedniego profilu konfiguracji i preferencji.


**Jeśli moja maszyna wirtualna została już skonfigurowana dla usługi, na przykład Update Management, program automatycznie ponownie skonfiguruje ją.**
Nie, Autozarządzanie nie spowoduje jego ponownego skonfigurowania. Rozpocznie się monitorowanie zasobów skojarzonych z tą usługą na potrzeby dryfowania.


**Dlaczego moja maszyna wirtualna ma stan niepowodzenia w portalu autozarządzania?**

Jeśli zobaczysz stan jako *Niepowodzenie*, możesz rozwiązać problemy ze wdrożeniem za pomocą grupy zasobów, w której znajduje się maszyna wirtualna. Przejdź do pozycji **grupy zasobów**, wybierz grupę zasobów, kliknij pozycję **wdrożenia** , a następnie *Failed* Zobacz szczegóły błędu.

**Jak mogę uzyskać pomoc techniczną dotyczącą rozwiązywania problemów z autozarządzaniem?**

Możesz zaplikować [bilet przypadku pomocy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)technicznej. Dla opcji **Usługa** Wyszukaj i wybierz pozycję *Autozarządzanie* w sekcji *monitorowanie i zarządzanie* .


## <a name="next-steps"></a>Następne kroki

Spróbuj włączyć Autozarządzanie dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)
---
title: Azure Automanage dla maszyn wirtualnych — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące Azure Automanage maszyn wirtualnych.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363363"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Często zadawane pytania dotyczące Azure Automanage maszyn wirtualnych

Ten artykuł zawiera odpowiedzi na niektóre najczęściej zadawane pytania dotyczące Azure Automanage [maszyn wirtualnych.](automanage-virtual-machines.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w witrynie [MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Swój problem możesz opublikować na tych forach lub opublikować w [ @AzureSupport serwisie Twitter.](https://twitter.com/AzureSupport) Możesz również przesłać wniosek o pomoc techniczną platformy Azure. Aby przesłać wniosek o pomoc techniczną, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)wybierz pozycję Uzyskaj pomoc **techniczną.**


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage maszyn wirtualnych

**Jakie są wszystkie wymagania wstępne wymagane do włączenia Azure Automanage?**

Poniżej przedstawiono wymagania wstępne dotyczące włączania Azure Automanage:
- Obsługiwane [wersje systemu Windows Server](automanage-windows-server.md#supported-windows-server-versions) i [dystrybucje systemu Linux](automanage-linux.md#supported-linux-distributions-and-versions)
- Maszyny wirtualne muszą znajdować się w obsługiwanym regionie
- Użytkownik musi mieć poprawne uprawnienia
- Tylko maszyny wirtualne w zestawie nieskalowania
- Automatycznemanage nie obsługuje obecnie subskrypcji piaskownicy

**Jakie uprawnienia RBAC platformy Azure są potrzebne do włączenia funkcji automatycznego zarządzania?**

W przypadku włączania funkcji Automatycznego zarządzania na maszynie wirtualnej przy użyciu istniejącego konta automatycznego zarządzania potrzebna jest rola Współautor w grupie zasobów, w której znajduje się maszyna wirtualna.

Jeśli podczas włączania używasz nowego konta automatycznego, musisz mieć rolę właściciel lub rolę Współautor i administrator dostępu użytkowników do subskrypcji.


**Jakie regiony są obsługiwane?**

Pełna lista obsługiwanych regionów jest dostępna [tutaj.](./automanage-virtual-machines.md#supported-regions)


**Jakie możliwości Azure Automanage zautomatyzować?**

Automatyczne zarządzanie rejestruje, konfiguruje i monitoruje w całym cyklu życia maszyny wirtualnej usługi wymienione [tutaj.](automanage-virtual-machines.md)

**Czy Azure Automanage z maszynami Azure Arc z włączoną obsługą sieci?**

Automanage obecnie nie obsługuje maszyn wirtualnych z obsługą usługi Arc.

**Czy można dostosować konfiguracje Azure Automanage?**

Klienci mogą dostosowywać ustawienia określonych usług, takich jak Azure Backup przechowywania, za pośrednictwem preferencji konfiguracji. Pełną listę ustawień, które można zmienić, można znaleźć w naszej dokumentacji [tutaj.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Czy Azure Automanage z maszynami wirtualnych z systemami Linux i Windows?**

Tak, zobacz obsługiwane wersje [systemu Windows Server](automanage-windows-server.md#supported-windows-server-versions) i [dystrybucje systemu Linux.](automanage-linux.md#supported-linux-distributions-and-versions)


**Czy mogę selektywnie zastosować funkcję Automatycznegomanage tylko do zestawu maszyn wirtualnych?**

Funkcję automatycznego zarządzania można włączyć za pomocą kliknięcia i wskazać prostotę wybranych nowych i istniejących maszyn wirtualnych. Automanage można również wyłączyć w dowolnym momencie.


**Czy Azure Automanage maszyny wirtualne w zestawie skalowania maszyn wirtualnych?**

Nie, Azure Automanage nie obsługuje obecnie maszyn wirtualnych w zestawie skalowania maszyn wirtualnych.


**Ile kosztuje Azure Automanage?**

Azure Automanage jest dostępna bez dodatkowych kosztów w publicznej wersji zapoznawczej. Dołączone zasoby platformy Azure, takie Azure Backup, będą wiązać się z kosztami.


**Czy mogę zastosować funkcję Automatycznego zarządzania za pomocą usługi Azure Policy?**

Tak, mamy wbudowane zasady, które automatycznie zastosują funkcję Automanage do wszystkich maszyn wirtualnych w zdefiniowanym zakresie. Określisz również konfigurację środowiska (DevTest lub Production) wraz z kontem automatycznego zarządzania. Dowiedz się więcej o włączaniu automatycznego zarządzania za pomocą zasad platformy Azure [tutaj.](virtual-machines-policy-enable.md)


**Co to jest konto automatycznegomanage?**

Konto automatycznego zarządzania to tożsamość usługi zarządzanej (MSI, Managed Service Identity), która zapewnia kontekst zabezpieczeń lub tożsamość, w ramach której są wykonywane zautomatyzowane operacje.


**Czy włączenie funkcji Automanage ma wpływ na dodatkowe maszyny wirtualne oprócz wybranych przeze mnie maszyn wirtualnych?**

Jeśli maszyna wirtualna jest połączona z istniejącym obszarem roboczym usługi Log Analytics, ponownie użyjemy tego obszaru roboczego do zastosowania tych rozwiązań: Change Tracking, Spis i Update Management. Wszystkie maszyny wirtualne połączone z tym obszarem roboczym będą mieć włączone te rozwiązania.


**Czy mogę zmienić środowisko mojej maszyny wirtualnej?**

W tej chwili należy wyłączyć funkcję Automatycznemanage dla tej maszyny wirtualnej, a następnie ponownie włączyć funkcję Automanage z żądanym środowiskiem i preferencjami.


**Jeśli moja maszyna wirtualna jest już skonfigurowana dla usługi, na przykład Update Management, czy funkcja automatycznego konfigurowania skonfiguruje ją ponownie?**
Nie, automatyczne konfigurowanie nie spowoduje jego ponownego skonfigurowania. Rozpoczniemy monitorowanie zasobów skojarzonych z tą usługą pod względu na dryf.


**Dlaczego moja maszyna wirtualna ma stan Niepowodzenie w portalu automatycznej ochrony?**

Jeśli stan to *Niepowodzenie,* możesz rozwiązać problemy z wdrożeniem na kilka różnych sposobów:
* Przejdź do **grupy zasobów,** wybierz grupę zasobów,  kliknij pozycję **Wdrożenia** i zobacz tam stan Niepowodzenie wraz ze szczegółami błędu.
* Przejdź do **subskrypcji,** wybierz grupę zasobów, kliknij  pozycję **Wdrożenia** i zobacz tam stan Niepowodzenie wraz ze szczegółami błędu.
* Możesz również odwiedzić dziennik aktywności maszyny wirtualnej, który będzie zawierać wpis "Tworzenie lub aktualizowanie przypisań profilów konfiguracji". Może to również zawierać więcej szczegółów dotyczących wdrożenia.

**Jak uzyskać pomoc techniczną w zakresie rozwiązywania problemów z automatycznymmanage?**

Możesz utworzyć bilet [zgłoszenia do pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). W polu **Usługa** wyszukaj i wybierz pozycję *Automatyczne zarządzanie* w sekcji *Monitorowanie i* zarządzanie.


## <a name="next-steps"></a>Następne kroki

Spróbuj w celu włączenia funkcji Automanage dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)
---
title: Rozwiązywanie typowych Azure Automanage błędów dołączania
description: Typowe błędy automatycznego dołączania i sposoby ich rozwiązywania
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862979"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Rozwiązywanie typowych problemów z automatycznym dołączaniem
Automanage może nie zostać w trybie failboard maszyny do usługi. W tym dokumencie wyjaśniono, jak rozwiązywać problemy z błędami wdrażania, przedstawiono niektóre typowe przyczyny niepowodzenia wdrożeń i opisano potencjalne następne kroki dotyczące ograniczania ryzyka.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrażania
Dołączanie maszyny do funkcji automatycznego Azure Resource Manager wdrożenia. Aby uzyskać więcej informacji, zobacz wdrożenie, aby uzyskać szczegółowe informacje o tym, dlaczego zakończyło się niepowodzeniem. W wysuwanych szczegółach niepowodzenia znajdują się linki do wdrożeń, które przedstawiono poniżej.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Wysuwają się szczegóły niepowodzenia automatycznego.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Sprawdzanie wdrożeń dla grupy zasobów zawierającej nieudaną maszynę wirtualną
Wysuwające awarii będzie zawierać link do wdrożeń w grupie zasobów zawierającej maszynę, na którym nie powiodło się dołączanie. Wysuwu będzie również zawierać nazwę prefiksu, za pomocą których można filtrować wdrożenia. Kliknięcie linku wdrażania spowoduje kliknięcie bloku wdrożeń, w którym można następnie filtrować wdrożenia, aby wyświetlić wdrożenia automatyczne na maszynie. Jeśli wdrażasz w wielu regionach, upewnij się, że klikniesz wdrożenie w odpowiednim regionie.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Sprawdź wdrożenia subskrypcji zawierającej nieudaną maszynę wirtualną
Jeśli nie widzisz żadnych błędów we wdrożeniu grupy zasobów, następnym krokiem będzie przyjrzenie się wdrożeniom w subskrypcji zawierającym maszynę wirtualną, która nie powiodła się podczas dołączania. Kliknij link **Wdrożenia dla subskrypcji w** wysuwanych awarii i filtruj wdrożenia przy użyciu **filtru Automanage-DefaultResourceGroup.** Użyj nazwy grupy zasobów z bloku awarii, aby filtrować wdrożenia. Nazwa wdrożenia będzie mieć sufiks z nazwą regionu. Jeśli wdrażasz w wielu regionach, upewnij się, że klikniesz wdrożenie w odpowiednim regionie.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Sprawdzanie wdrożeń w subskrypcji połączonej z obszarem roboczym usługi Log Analytics
Jeśli nie widzisz żadnych nieudanych wdrożeń w grupie zasobów lub subskrypcji zawierającej nieudaną maszynę wirtualną i jeśli nieudana maszyna wirtualna jest połączona z obszarem roboczym usługi Log Analytics w innej subskrypcji, przejdź do subskrypcji połączonej z obszarem roboczym usługi Log Analytics i sprawdź, czy wdrożenia zakończyły się niepowodzeniem.

## <a name="common-deployment-errors"></a>Typowe błędy związane z wdrażaniem

Błąd |  Ograniczanie ryzyka
:-----|:-------------|
Błąd niewystarczających uprawnień konta automatycznego | Ten błąd może wystąpić, jeśli niedawno przeniesiono subskrypcję zawierającą nowe konto automatycznego konta do nowej dzierżawy. Kroki rozwiązywania tego błędu znajdują się [tutaj.](./repair-automanage-account.md)
Region obszaru roboczego nie jest zgodny z wymaganiami mapowania regionów | Funkcja automatycznego sterowania nie mogła do dołączać maszyny, ponieważ obszar roboczy usługi Log Analytics, z który jest aktualnie połączony, nie jest mapowany na obsługiwany region usługi Automation. Upewnij się, że istniejący obszar roboczy usługi Log Analytics i konto usługi Automation znajdują się w [obsługiwanym mapowaniu regionów.](../automation/how-to/region-mappings.md)
"Odmowa dostępu z powodu przypisania odmowy o nazwie 'Przypisanie odmowy systemowej utworzonej przez aplikację zarządzaną'" | Dla zasobu utworzono przypisanie [denyAssignment,](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) co uniemożliwia automatyczne zarządzanie dostępem do zasobu. To przypisanie denyAssignment może zostać utworzone przez [usługę Blueprint lub](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) [aplikację zarządzaną.](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview)
"Informacje o systemach operacyjnych: Name='(null)', ver='(null)', agent status='Not Ready'"." | Upewnij się, że używasz minimalnej obsługiwanej wersji agenta [,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)agent jest uruchomiony[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) i [Windows)](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)oraz że agent jest aktualny[(Systemy Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) i [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Nie można określić systemu operacyjnego dla nazwy systemu operacyjnego maszyny wirtualnej:, ver . Sprawdź, czy agent maszyny wirtualnej jest uruchomiony. Bieżący stan to Gotowe." | Upewnij się, że używasz minimalnej obsługiwanej wersji agenta [,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)agent jest uruchomiony[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) i [Windows)](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)oraz że agent jest aktualny[(Systemy Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) i [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia IaaSAntimalware" | Upewnij się, że na maszynie wirtualnej nie zainstalowano jeszcze innej oferty ochrony przed złośliwym oprogramowaniem/oprogramowaniem antywirusowym. Jeśli to się nie powiedzie, skontaktuj się z pomocą techniczną.
Obszar roboczy usługi ASC: Funkcja automatycznegomanage nie obsługuje obecnie usługi Log Analytics w _lokalizacji_. | Sprawdź, czy maszyna wirtualna znajduje się w [obsługiwanym regionie](./automanage-virtual-machines.md#supported-regions).
Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad. Aby uzyskać więcej informacji, zobacz szczegóły. | Istnieją zasady uniemożliwiające automatyczne dołączanie maszyny wirtualnej. Sprawdź zasady, które są stosowane do subskrypcji lub grupy zasobów zawierającej maszynę wirtualną, którą chcesz dodać do funkcji Automatycznego zarządzania.
"Przypisanie nie powiodło się; nie ma dostępnych dodatkowych informacji" | Otwórz przypadek z Microsoft Azure pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)
---
title: Włącz Update Management Azure Automation w Azure Portal
description: W tym artykule opisano sposób włączania Update Management z Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 136a5da8d9956e034532c14b6fce011e4e7ce415
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563894"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Włączanie rozwiązania Update Management w witrynie Azure Portal

W tym artykule opisano, jak włączyć funkcję [Update Management](update-mgmt-overview.md) dla maszyn wirtualnych, przeglądając Azure Portal. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management.

Liczba grup zasobów, których można użyć do zarządzania maszynami wirtualnymi, jest ograniczona przez [Menedżer zasobów limity wdrożenia](../../azure-resource-manager/templates/cross-scope-deployment.md). Menedżer zasobów wdrożeń nie należy mylić z wdrożeniami aktualizacji, są ograniczone do pięciu grup zasobów na wdrożenie. Dwie z tych grup zasobów są zastrzeżone do konfigurowania obszaru roboczego Log Analytics, konta usługi Automation i powiązanych zasobów. Spowoduje to pozostawienie trzech grup zasobów do wyboru dla zarządzania przez Update Management. Ten limit dotyczy tylko równoczesnej konfiguracji, a nie liczby grup zasobów, które mogą być zarządzane przez funkcję automatyzacji.

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do zarządzania maszynami.
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. W Azure Portal przejdź do **maszyn wirtualnych**.

2. Użyj pól wyboru, aby wybrać maszyny wirtualne, które mają zostać dodane do Update Management. W danym momencie można dodać maszyny do maksymalnie trzech różnych grup zasobów. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

    ![Lista maszyn wirtualnych](media/update-mgmt-enable-portal/vmlist.png)

    > [!TIP]
    > Użyj kontrolek filtr, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów. Możesz kliknąć górne pole wyboru, aby wybrać wszystkie maszyny wirtualne znajdujące się na liście.

    [![Włącz Update Management](./media/update-mgmt-enable-portal/onboard-feature.png)](./media/update-mgmt-enable-portal/onboard-feature-expanded.png#lightbox)

3. Wybierz pozycję **usługi** , a następnie wybierz pozycję **Update Management** dla funkcji Update Management.

4. Lista maszyn wirtualnych jest filtrowana w celu wyświetlania tylko maszyn wirtualnych znajdujących się w tej samej subskrypcji i lokalizacji. Jeśli maszyny wirtualne znajdują się w więcej niż trzech grupach zasobów, wybierane są pierwsze trzy grupy zasobów.

5. Istniejący obszar roboczy Log Analytics i konto usługi Automation są domyślnie zaznaczone. Jeśli chcesz użyć innego obszaru roboczego Log Analytics i konta usługi Automation, wybierz pozycję **niestandardowa** , aby wybrać je ze strony Konfiguracja niestandardowa. Po wybraniu obszaru roboczego Log Analytics należy sprawdzić, czy jest on połączony z kontem usługi Automation. Jeśli zostanie znalezione połączone konto usługi Automation, zobaczysz następujący ekran. Po zakończeniu wybierz polecenie **Zamknij**.

    [![Wybieranie obszaru roboczego i konta](./media/update-mgmt-enable-portal/select-workspace-and-account.png)](./media/update-mgmt-enable-portal/select-workspace-and-account-expanded.png#lightbox)

6. Jeśli wybrany obszar roboczy nie jest połączony z kontem usługi Automation, zobaczysz następujący ekran. Wybierz konto usługi Automation i po zakończeniu wybierz **przycisk OK** .

    ![Brak obszaru roboczego](media/update-mgmt-enable-portal/no-workspace.png)

7. Usuń zaznaczenie każdej maszyny wirtualnej, która nie ma być włączona. Maszyny wirtualne, które nie mogą być włączone, są już odwybrane.

8. Wybierz pozycję **Włącz** , aby włączyć tę funkcję. Po włączeniu Update Management może upłynąć około 15 minut, zanim będzie możliwe wyświetlenie z nich oceny aktualizacji.

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](update-mgmt-manage-updates-for-vm.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](../troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz [Rozwiązywanie problemów z agentem aktualizacji systemu Linux](../troubleshoot/update-agent-issues-linux.md).

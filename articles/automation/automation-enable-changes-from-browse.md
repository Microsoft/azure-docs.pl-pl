---
title: Włącz Azure Automation Change Tracking i spisu w Azure Portal
description: W tym artykule opisano, jak włączyć funkcję Change Tracking i spisu z Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 11ae873ae4700dc4f9cb3d02a898a3ded9f6db59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87367422"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Włącz Change Tracking i spis z Azure Portal

W tym artykule opisano, jak włączyć funkcję [Change Tracking i spisu](change-tracking.md) dla maszyn wirtualnych, przeglądając Azure Portal. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu. 

Liczba grup zasobów, których można użyć do zarządzania maszynami wirtualnymi, jest ograniczona przez [Menedżer zasobów limity wdrożenia](../azure-resource-manager/templates/cross-scope-deployment.md). Menedżer zasobów wdrożeń nie należy mylić z wdrożeniami aktualizacji, są ograniczone do pięciu grup zasobów na wdrożenie. Dwie z tych grup zasobów są zastrzeżone do konfigurowania obszaru roboczego Log Analytics, konta usługi Automation i powiązanych zasobów. Spowoduje to pozostawienie trzech grup zasobów do wyboru w celu zarządzania przez Change Tracking i spis. Ten limit dotyczy tylko równoczesnej konfiguracji, a nie liczby grup zasobów, które mogą być zarządzane przez funkcję automatyzacji.

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. W Azure Portal przejdź do **maszyn wirtualnych**.

2. Użyj pól wyboru, aby wybrać maszyny wirtualne, które mają zostać dodane do Change Tracking i spisu. W danym momencie można dodać maszyny do maksymalnie trzech różnych grup zasobów. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

    ![Lista maszyn wirtualnych](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > Użyj kontrolek filtr, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów. Możesz kliknąć górne pole wyboru, aby wybrać wszystkie maszyny wirtualne znajdujące się na liście.

3. Wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

4. Lista maszyn wirtualnych jest filtrowana w celu wyświetlania tylko maszyn wirtualnych znajdujących się w tej samej subskrypcji i lokalizacji. Jeśli maszyny wirtualne znajdują się w więcej niż trzech grupach zasobów, wybierane są pierwsze trzy grupy zasobów.

5. Istniejący obszar roboczy Log Analytics i konto usługi Automation są domyślnie zaznaczone. Jeśli chcesz użyć innego obszaru roboczego Log Analytics i konta usługi Automation, kliknij pozycję **niestandardowa** , aby wybrać je ze strony Konfiguracja niestandardowa. Po wybraniu obszaru roboczego Log Analytics należy sprawdzić, czy jest on połączony z kontem usługi Automation. Jeśli zostanie znalezione połączone konto usługi Automation, zobaczysz następujący ekran. Po zakończeniu kliknij przycisk **OK**.

    ![Wybieranie obszaru roboczego i konta](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Jeśli wybrany obszar roboczy nie jest połączony z kontem usługi Automation, zobaczysz następujący ekran. Wybierz konto usługi Automation, a następnie kliknij przycisk **OK** po zakończeniu.

    ![Brak obszaru roboczego](media/automation-enable-changes-from-browse/no-workspace.png)

7. Usuń zaznaczenie pola wyboru obok każdej maszyny wirtualnej, która nie ma być włączona. Maszyny wirtualne, które nie mogą być włączone, są już odwybrane.

8. Kliknij pozycję **Włącz** , aby włączyć wybraną funkcję. Ukończenie instalacji może potrwać do 15 minut.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).

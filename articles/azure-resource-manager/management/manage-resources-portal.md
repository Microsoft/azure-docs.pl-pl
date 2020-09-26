---
title: Zarządzanie zasobami — Azure Portal
description: Użyj Azure Portal i Azure Resource Manager do zarządzania zasobami. Pokazuje, jak wdrażać i usuwać zasoby.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 266ad613a4966b26fc4a6ddffbb7b4196aa937b1
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372106"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Zarządzanie zasobami platformy Azure przy użyciu Azure Portal

Dowiedz się, jak używać [Azure Portal](https://portal.azure.com) z [Azure Resource Manager](overview.md) do zarządzania zasobami platformy Azure. Aby zarządzać grupami zasobów, zobacz [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure Portal](manage-resource-groups-portal.md).

Inne artykuły dotyczące zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)
- [Zarządzanie zasobami platformy Azure przy użyciu Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu Menedżer zasobów można użyć Azure Portal do wdrożenia zasobów platformy Azure. Aby utworzyć szablon, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Aby wdrożyć szablon przy użyciu portalu, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure Portal](../templates/deploy-portal.md).

## <a name="open-resources"></a>Otwarte zasoby

Zasoby platformy Azure są zorganizowane według usług platformy Azure i grup zasobów. Poniższe procedury pokazują, jak otworzyć konto magazynu o nazwie **mystorage0207**. Maszyna wirtualna znajduje się w grupie zasobów o nazwie **mystorage0207rg**.

Aby otworzyć zasób według typu usługi:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz usługę platformy Azure. W tym przypadku **konta magazynu**.  Jeśli nie widzisz usługi na liście, wybierz pozycję **wszystkie usługi**, a następnie wybierz typ usługi.

    ![Otwórz zasób platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Wybierz zasób, który chcesz otworzyć.

    ![Otwórz zasób platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Konto magazynu wygląda następująco:

    ![Otwórz zasób platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Aby otworzyć zasób według grupy zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz pozycję **grupy zasobów** , aby wyświetlić listę zasobów w grupie.
3. Wybierz zasób, który chcesz otworzyć. 

## <a name="manage-resources"></a>Zarządzanie zasobami

Podczas wyświetlania zasobu w portalu są wyświetlane opcje zarządzania tym konkretnym zasobem.

![Zarządzanie zasobami platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej platformy Azure. Można wykonywać operacje, takie jak uruchamianie, ponowne uruchamianie i zatrzymywanie maszyny wirtualnej.

## <a name="delete-resources"></a>Usuwanie zasobów

1. Otwórz zasób w portalu. Aby zapoznać się z procedurami, zobacz [Open Resources](#open-resources).
2. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej.

    ![Usuń zasób platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Wpisz nazwę zasobu, aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń**.

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](delete-resource-group.md).

## <a name="move-resources"></a>Przenoszenie zasobów

1. Otwórz zasób w portalu. Aby zapoznać się z procedurami, zobacz [Open Resources](#open-resources).
2. Wybierz pozycję **Przenieś**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![Przenoszenie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Wybierz pozycję **Przenieś do innej grupy zasobów** lub **Przenieś do innej subskrypcji** w zależności od potrzeb.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

1. Otwórz zasób w portalu. Aby zapoznać się z procedurami, zobacz [Open Resources](#open-resources).
2. Wybierz pozycję **blokady**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![Zablokuj zasób platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Wybierz pozycję **Dodaj**, a następnie określ właściwości blokady.

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Tagowanie pomaga w logicznym organizowaniu grupy zasobów i zasobów. 

1. Otwórz zasób w portalu. Aby zapoznać się z procedurami, zobacz [Open Resources](#open-resources).
2. Wybierz pozycję **Tagi**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![Oznacz zasób platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Określ właściwości tagu, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md#portal).

## <a name="monitor-resources"></a>Monitorowanie zasobów

Po otwarciu zasobu Portal przedstawia domyślne wykresy i tabele umożliwiające monitorowanie tego typu zasobu. Poniższy zrzut ekranu przedstawia wykresy dla maszyny wirtualnej:

![monitorowanie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Możesz wybrać ikonę pinezki w prawym górnym rogu wykresów, aby przypiąć wykres do pulpitu nawigacyjnego. Aby dowiedzieć się więcej o pracy z pulpitami nawigacyjnymi, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](../templates/template-syntax.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](../index.yml).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).

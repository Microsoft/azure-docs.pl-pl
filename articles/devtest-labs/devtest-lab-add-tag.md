---
title: Dodawanie tagów do laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak tworzyć niestandardowe znaczniki w Azure DevTest Labs i używać tagów do kategoryzowania zasobów. Możesz wyświetlić wszystkie zasoby w ramach subskrypcji, które mają tag.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270737"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Dodawanie tagów do laboratorium w Azure DevTest Labs

Możesz utworzyć niestandardowe znaczniki i zastosować je do zasobów DevTest Labs, aby logicznie klasyfikować zasoby. Później można szybko i łatwo zobaczyć wszystkie zasoby w subskrypcji, które mają ten tag. Tagi są przydatne, gdy trzeba organizować zasoby na potrzeby rozliczeń lub zarządzania.

Zasoby obsługiwane przez Tagi obejmują

* Maszyny wirtualne obliczeń
* Karty interfejsów sieciowych
* Adresy IP
* Moduły równoważenia obciążenia
* Konta magazynu
* Dyski zarządzane

Możesz zastosować Tagi podczas [tworzenia laboratorium](devtest-lab-create-lab.md) i później zarządzać nimi za pomocą bloku Tagi w obszarze Konfiguracja i ustawienia.

Każdy tag składa się z pary **Nazwa** / **wartość** . Na przykład można utworzyć tag o nazwie *costcenter* , który ma wartość *34543*. Ten tag może ułatwić identyfikację zasobów laboratorium, które są rozliczane do określonego obszaru organizacji. Możesz wybrać nazwy i wartości, które mają sens, w jaki sposób chcesz zorganizować subskrypcję.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Procedura zarządzania tagami w istniejącym laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** . Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**.
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz dodać Tagi lub zarządzać nimi.
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **Tagi**.
1. Aby utworzyć nowy tag dla tego laboratorium, wprowadź pary **Nazwa** / **wartości** i wybierz pozycję **Zapisz**. Możesz również wybrać istniejący tag z listy, aby wyświetlić i zarządzać zasobami skojarzonymi z tym tagiem.

    ![Zarządzaj tagami](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Tagi utworzone w przepływie na poziomie laboratorium przez wszystkie zasoby, które są rozliczane w ramach subskrypcji. Na przykład Tagi poziomu laboratorium przepływają do podstawowych maszyn wirtualnych obliczeniowych maszyn wirtualnych laboratorium. można używać tagów w kontekście zarządzania kosztami. Tagi poziomu laboratorium są wyświetlane w filtrze tagów w celu zarządzania kosztami.

## <a name="understanding-limitations-to-tags"></a>Informacje o ograniczeniach dotyczących tagów

Tagi mają następujące ograniczenia:

* Każdy zasób lub grupa zasobów może mieć co najwyżej 15 par nazwa/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, z których każdy może mieć 15 par nazwa/wartość tagu.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

Za [pomocą tagów można organizować zasoby platformy Azure, aby](../azure-resource-manager/management/tag-resources.md) uzyskać więcej informacji o używaniu tagów na platformie Azure, w tym o sposobach zarządzania tagami przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Ograniczenia i konwencje w ramach subskrypcji można stosować przy użyciu zasad niestandardowych. Zdefiniowane zasady mogą wymagać, aby wszystkie zasoby miały wartość dla określonego tagu. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md).
* Zapoznaj się z [galerią szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).

---
title: Tworzenie lub modyfikowanie laboratoriów przy użyciu szablonów Azure Resource Manager
description: Dowiedz się, jak korzystać z szablonów Azure Resource Manager za pomocą programu PowerShell, aby automatycznie tworzyć lub modyfikować laboratoria w laboratorium DevTest
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272644"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatyczne tworzenie lub modyfikowanie laboratoriów przy użyciu szablonów Azure Resource Manager i programu PowerShell

DevTest Labs udostępnia wiele szablonów Azure Resource Manager i skryptów programu PowerShell, które mogą pomóc szybko i automatycznie tworzyć nowe laboratoria oraz modyfikować istniejące laboratoria, a następnie wdrażać te zasoby.

Ten artykuł ułatwia przeprowadzenie procesu korzystania z tych szablonów i skryptów w celu zautomatyzowania tworzenia, modyfikowania i wdrażania laboratoriów. W tym artykule pokazano również, gdzie można znaleźć więcej informacji na temat sposobu korzystania z programu PowerShell w celu wykonywania niektórych typowych zadań w DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1. zbieranie szablonów i skryptów
Wstępnie utworzone [szablony Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) i [skrypty programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) można znaleźć w naszym publicznym [repozytorium GitHub](https://github.com/Azure/azure-devtestlab). Użyj ich jako-lub dostosuj je do swoich potrzeb i Zapisz je w swoim [prywatnym repozytorium git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2. modyfikowanie szablonu Azure Resource Manager
Możesz wykonać kroki opisane w sekcji [Tworzenie pierwszego szablonu Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , jeśli wcześniej nie utworzono szablonu.

Ponadto [najlepsze rozwiązania dotyczące tworzenia szablonów Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) oferują wiele wytycznych i sugestii ułatwiających tworzenie Azure Resource Manager szablonów, które są niezawodne i łatwe w użyciu. Zazwyczaj należy użyć odmiany jednego z podejść lub przykładów, a także zmodyfikować szablon do własnych potrzeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3. wdrażanie zasobów przy użyciu programu PowerShell
Po dostosowaniu szablonów i skryptów wykonaj kroki niezbędne do [wdrożenia zasobów przy użyciu szablonów Menedżer zasobów i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Artykuł zawiera ogólne informacje dotyczące używania Azure PowerShell z szablonami Azure Resource Manager do wdrażania zasobów na platformie Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Typowe zadania, które można wykonywać w DevTest Labs przy użyciu programu PowerShell
Istnieje wiele innych typowych zadań, które można zautomatyzować za pomocą programu PowerShell. W poniższych sekcjach dokumentacji przedstawiono kroki wymagane do wykonania tych zadań.

* [Tworzenie obrazu niestandardowego z pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu programu PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Tworzenie niestandardowej roli laboratorium przy użyciu programu PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć [prywatne repozytorium git](devtest-lab-add-artifact-repo.md) , w którym będą przechowywane dostosowane szablony lub skrypty.
* Poznaj [szablony Azure Resource Manager z galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).

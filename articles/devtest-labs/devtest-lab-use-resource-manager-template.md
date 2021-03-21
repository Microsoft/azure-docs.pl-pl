---
title: Wyświetlanie i używanie szablonu Azure Resource Manager maszyny wirtualnej
description: Dowiedz się, jak utworzyć inne maszyny wirtualne przy użyciu szablonu Azure Resource Manager z maszyny wirtualnej
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696003"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Tworzenie maszyn wirtualnych przy użyciu szablonu Azure Resource Manager 

Podczas tworzenia maszyny wirtualnej (VM) w DevTest Labs za pomocą [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)można wyświetlić szablon Azure Resource Manager przed zapisaniem maszyny wirtualnej. Szablonu można następnie użyć jako podstawy, aby utworzyć więcej maszyn wirtualnych laboratorium z tymi samymi ustawieniami.

W tym artykule opisano szablony Menedżer zasobów z obsługą jednej maszyny wirtualnej, a w tym przedstawiono sposób wyświetlania i zapisywania szablonu podczas tworzenia maszyny wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Szablony Menedżer zasobów z obsługą jednej maszyny wirtualnej a maszyną wirtualną
Istnieją dwa sposoby tworzenia maszyn wirtualnych w DevTest Labs przy użyciu szablonu Menedżer zasobów: zainicjuj zasób Microsoft. wspólny/Labs/virtualmachines lub zainicjuj zasób Microsoft. COMPUTE/virtualmachines. Każdy z nich jest używany w różnych scenariuszach i wymaga innych uprawnień.

- Menedżer zasobów szablonów używających typu zasobu Microsoft. wspólny/Labs/virtualmachines (zgodnie z deklaracją w właściwości "Resource" w szablonie) mogą udostępniać poszczególne maszyny wirtualne laboratorium. Każda maszyna wirtualna jest następnie wyświetlana jako pojedynczy element na liście maszyn wirtualnych DevTest Labs:

   ![Zrzut ekranu przedstawiający listę maszyn wirtualnych jako pojedyncze elementy na liście maszyn wirtualnych DevTest Labs.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ten typ szablonu Menedżer zasobów można zainicjować za pomocą polecenia Azure PowerShell **New-AzResourceGroupDeployment** lub przy użyciu interfejsu wiersza polecenia platformy Azure **AZ Deployment Group Create**. Wymaga uprawnień administratora, więc użytkownicy, którzy są przypisani przy użyciu roli użytkownika DevTest Labs, nie mogą wykonać wdrożenia. 

- Menedżer zasobów szablony korzystające z typu zasobu Microsoft. COMPUTE/virtualmachines mogą obsługiwać wiele maszyn wirtualnych jako pojedyncze środowisko na liście maszyn wirtualnych DevTest Labs:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Maszyny wirtualne w tym samym środowisku mogą być zarządzane razem i współużytkować ten sam cykl życia. Użytkownicy, którzy są przypisani przy użyciu roli użytkownika DevTest Labs, mogą tworzyć środowiska przy użyciu tych szablonów, o ile administrator skonfigurował laboratorium w taki sposób.

W pozostałej części tego artykułu omówiono Menedżer zasobów szablonów korzystających z programu Microsoft. wspólny/Labs/virtualmachines. Są one używane przez administratorów laboratorium do automatyzowania tworzenia maszyn wirtualnych laboratorium (na przykład z maszynami wirtualnymi) lub do generowania obrazów (na przykład fabryki obrazów).

[Najlepsze rozwiązania dotyczące tworzenia szablonów Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) oferują wiele wytycznych i sugestii ułatwiających tworzenie Azure Resource Manager szablonów, które są niezawodne i łatwe w użyciu.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Wyświetlanie i zapisywanie szablonu Menedżer zasobów maszyny wirtualnej
1. Wykonaj kroki opisane w temacie [Tworzenie pierwszej maszyny wirtualnej w laboratorium,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) aby rozpocząć tworzenie maszyny wirtualnej.
1. Wprowadź wymagane informacje dotyczące maszyny wirtualnej i Dodaj dowolne artefakty dla tej maszyny wirtualnej.
1. Przejdź do karty **Ustawienia zaawansowane** . 
1. W dolnej części okna Konfigurowanie ustawień wybierz pozycję **Wyświetl szablon ARM**.
1. Skopiuj i Zapisz szablon Menedżer zasobów, aby później utworzyć inną maszynę wirtualną.

   ![Menedżer zasobów szablon do zapisania do późniejszego użycia](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po zapisaniu szablonu Menedżer zasobów należy zaktualizować sekcję Parametry szablonu przed jego użyciem. Można utworzyć parameter.js, który dostosowuje tylko parametry, poza rzeczywistym szablonem Menedżer zasobów. 

![Dostosowywanie parametrów przy użyciu pliku JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Szablon Menedżer zasobów jest teraz gotowy do użycia w celu [utworzenia maszyny wirtualnej](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Ustaw datę wygaśnięcia
W scenariuszach takich jak szkolenia, pokazy i wersje próbne można tworzyć maszyny wirtualne i usuwać je automatycznie po upływie ustalonego czasu, aby nie ponosić niepotrzebnych kosztów. Można utworzyć maszynę wirtualną laboratorium z datą wygaśnięcia, określając właściwość **expirationDate** maszyny wirtualnej. Zapoznaj się z tym samym szablonem Menedżer zasobów w [naszym repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzyć środowiska z obsługą wielu maszyn wirtualnych za pomocą szablonów Menedżer zasobów](devtest-lab-create-environment-from-arm.md).
* [Wdróż szablon Menedżer zasobów, aby utworzyć maszynę wirtualną](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Zapoznaj się z wieloma przewodnikami szybki Menedżer zasobów Start dla usługi DevTest Labs, które są dostępne w [repozytorium GitHub DevTest Labs](https://github.com/Azure/azure-quickstart-templates).

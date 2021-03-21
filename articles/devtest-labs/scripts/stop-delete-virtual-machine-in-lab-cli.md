---
title: Interfejs wiersza polecenia platformy Azure — zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium
description: Ten artykuł zawiera skrypt interfejsu wiersza polecenia platformy Azure, który umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium w Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198204"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure DevTest Labs

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Zatrzymuje maszynę wirtualną w laboratorium. Wykonanie tej operacji może chwilę potrwać. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Usuwa maszynę wirtualną w laboratorium. Wykonanie tej operacji może chwilę potrwać. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).

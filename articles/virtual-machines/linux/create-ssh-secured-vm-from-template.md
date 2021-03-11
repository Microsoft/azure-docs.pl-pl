---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure na podstawie szablonu
description: Jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonu Menedżer zasobów za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554687"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonów Azure Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonu Azure Resource Manager i interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell. Aby utworzyć maszynę wirtualną z systemem Windows, zobacz [Tworzenie maszyny wirtualnej z systemem Windows na podstawie szablonu Menedżer zasobów](../windows/ps-template.md).

Alternatywą jest wdrożenie szablonu na podstawie Azure Portal. Aby otworzyć szablon w portalu, wybierz przycisk **Wdróż na platformie Azure** .

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Przegląd szablonów

Szablony Azure Resource Manager są plikami JSON, które definiują infrastrukturę i konfigurację rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej o formacie szablonu i sposobie jego konstruowania, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure zwykle obejmuje dwa kroki:

1. Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
1. Tworzy maszynę wirtualną.

Poniższy przykład tworzy maszynę wirtualną na podstawie [szablonu szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Dla tego wdrożenia można używać tylko uwierzytelniania SSH. Po wyświetleniu monitu podaj wartość własnego klucza publicznego SSH, na przykład zawartość *~/.ssh/id_rsa. pub*. Jeśli musisz utworzyć parę kluczy SSH, zobacz [jak utworzyć i używać pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md). Oto kopia szablonu:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Aby uruchomić skrypt interfejsu wiersza polecenia, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Ostatnie polecenie interfejsu wiersza polecenia platformy Azure pokazuje publiczny adres IP nowo utworzonej maszyny wirtualnej. Do nawiązania połączenia z maszyną wirtualną potrzebny jest publiczny adres IP. Zobacz następną sekcję tego artykułu.

W poprzednim przykładzie określono szablon przechowywany w serwisie GitHub. Możesz również pobrać lub utworzyć szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

Oto kilka dodatkowych zasobów:

- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](../../azure-resource-manager/index.yml).
- Aby wyświetlić schematy maszyn wirtualnych platformy Azure, zobacz [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.compute/allversions).
- Aby zobaczyć więcej przykładów szablonów maszyn wirtualnych, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Następnie można obsłużyć SSH na potrzeby maszyny wirtualnej. Podaj własny publiczny adres IP z poprzedniego polecenia:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Następne kroki

W tym przykładzie utworzono podstawową maszynę wirtualną z systemem Linux. Aby uzyskać więcej Menedżer zasobów szablonów obejmujących struktury aplikacji lub tworzyć bardziej złożone środowiska, przejrzyj [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Aby dowiedzieć się więcej na temat tworzenia szablonów, Wyświetl składnię i właściwości JSON dla wdrożonych typów zasobów:

- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

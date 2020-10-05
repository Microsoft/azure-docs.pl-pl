---
title: Zautomatyzowane wdrażanie maszyn wirtualnych przy użyciu konfiguracji aplikacji platformy Azure — Szybki Start
description: W tym przewodniku szybki start pokazano, jak używać modułu Azure PowerShell i szablonów Azure Resource Manager do wdrażania magazynu konfiguracji aplikacji platformy Azure. Następnie użyj wartości ze sklepu, aby wdrożyć maszynę wirtualną.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88661473"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Szybki Start: Automatyczne wdrażanie maszyn wirtualnych z konfiguracją aplikacji i szablonem Menedżer zasobów (szablon ARM)

Dowiedz się, jak za pomocą szablonów Azure Resource Manager i Azure PowerShell wdrożyć magazyn konfiguracji aplikacji platformy Azure, jak dodać wartości klucza do magazynu oraz jak używać wartości klucza w sklepie do wdrażania zasobów platformy Azure, takich jak maszyna wirtualna platformy Azure, w tym przykładzie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-templates"></a>Przejrzyj szablony

Szablony używane w tym przewodniku szybki start pochodzą z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/). [Pierwszy szablon](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) tworzy magazyn konfiguracji aplikacji:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): Tworzenie magazynu konfiguracji aplikacji.

[Drugi szablon](https://azure.microsoft.com/resources/templates/101-app-configuration/) tworzy maszynę wirtualną przy użyciu wartości kluczy w sklepie. Przed wykonaniem tego kroku należy dodać kluczowe wartości przy użyciu portalu lub interfejsu wiersza polecenia platformy Azure.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

### <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy magazyn konfiguracji aplikacji.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

    - **subskrypcja**: wybierz subskrypcję platformy Azure używaną do utworzenia magazynu konfiguracji aplikacji.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, chyba że chcesz użyć istniejącej grupy zasobów.
    - **Region**: Wybierz lokalizację dla grupy zasobów.  Na przykład **Wschodnie stany USA**.
    - **Nazwa magazynu konfiguracji**: wprowadź nową nazwę magazynu konfiguracji aplikacji.
    - **Lokalizacja**: Określ lokalizację magazynu konfiguracji aplikacji.  Użyj wartości domyślnej.
    - **Nazwa jednostki SKU**: Określ nazwę jednostki SKU magazynu konfiguracji aplikacji. Użyj wartości domyślnej.

1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Sprawdź, czy strona zawiera **Zakończono walidację**, a następnie wybierz pozycję **Utwórz**.

Zanotuj nazwę grupy zasobów i nazwę magazynu konfiguracji aplikacji.  Te wartości są wymagane podczas wdrażania maszyny wirtualnej
### <a name="add-vm-configuration-key-values"></a>Dodawanie klucza konfiguracji maszyny wirtualnej — wartości

Po utworzeniu magazynu konfiguracji aplikacji możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby dodać kluczowe wartości do magazynu.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do nowo utworzonego magazynu konfiguracji aplikacji.
1. Wybierz opcję **Eksplorator konfiguracji** z menu po lewej stronie.
1. Wybierz pozycję **Utwórz** , aby dodać następujące pary klucz-wartość:

   |Klucz|Wartość|Etykieta|
   |-|-|-|
   |windowsOsVersion|2019 — centrum danych|szablon|
   |diskSizeGB|1023|szablon|

   Pozostaw pusty **Typ zawartości** .

Aby korzystać z interfejsu wiersza polecenia platformy Azure, zobacz temat [współpraca z kluczowymi wartościami w magazynie konfiguracji aplikacji platformy Azure](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Wdróż maszynę wirtualną przy użyciu przechowywanych wartości klucza

Teraz, po dodaniu klucza — wartości do magazynu, możesz przystąpić do wdrożenia maszyny wirtualnej przy użyciu szablonu Azure Resource Manager. Szablon odwołuje się do utworzonych kluczy **windowsOsVersion** i **diskSizeGB** .

> [!WARNING]
> Szablony ARM nie mogą odwoływać się do kluczy w magazynie konfiguracji aplikacji z włączonym linkiem prywatnym.

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy maszynę wirtualną przy użyciu przechowywanych wartości klucza w magazynie konfiguracji aplikacji.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

    - **subskrypcja**: wybierz subskrypcję platformy Azure użytą do utworzenia maszyny wirtualnej.
    - **Grupa zasobów**: Określ tę samą grupę zasobów co magazyn konfiguracji aplikacji lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów.
    - **Region**: Wybierz lokalizację dla grupy zasobów.  Na przykład **Wschodnie stany USA**.
    - **Lokalizacja**: Określ lokalizację maszyny wirtualnej. Użyj wartości domyślnej.
    - **Nazwa użytkownika administratora**: Określ nazwę użytkownika administratora dla maszyny wirtualnej.
    - **Hasło administratora**: Określ hasło administratora dla maszyny wirtualnej.
    - **Etykieta nazwy domeny**: Określ unikatową nazwę domeny.
    - **Nazwa konta magazynu**: Określ unikatową nazwę konta magazynu skojarzonego z maszyną wirtualną.
    - **Grupa zasobów magazynu konfiguracji aplikacji**: Określ grupę zasobów zawierającą magazyn konfiguracji aplikacji.
    - **Nazwa magazynu konfiguracji aplikacji**: Określ nazwę magazynu konfiguracji aplikacji platformy Azure.
    - **Klucz jednostki SKU maszyny wirtualnej**: Określ **windowsOsVersion**.  Jest to nazwa wartości klucza dodanej do sklepu.
    - **Klucz rozmiaru dysku**: Określ **diskSizeGB**. Jest to nazwa wartości klucza dodanej do sklepu.

1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Sprawdź, czy strona zawiera **Zakończono walidację**, a następnie wybierz pozycję **Utwórz**.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do nowo utworzonej maszyny wirtualnej.
1. Z menu po lewej stronie wybierz pozycję **Przegląd** i sprawdź, czy **jednostka SKU** to **2019 — centrum**danych.
1. Z menu po lewej stronie wybierz pozycję **dyski** , a następnie sprawdź, czy rozmiar dysku danych to **2013**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, magazyn konfiguracji aplikacji, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Jeśli planujesz używać magazynu konfiguracji aplikacji lub maszyny wirtualnej w przyszłości, możesz pominąć usuwanie. Jeśli nie planujesz używania tego zadania w przyszłości, usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki start, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono maszynę wirtualną przy użyciu szablonu Azure Resource Manager i wartości kluczy z konfiguracji aplikacji platformy Azure.

Aby dowiedzieć się więcej o tworzeniu innych aplikacji za pomocą usługi Azure App Configuration, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure](quickstart-aspnet-core-app.md)

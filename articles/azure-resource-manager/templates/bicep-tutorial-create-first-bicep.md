---
title: Samouczek — Tworzenie & wdrażanie Azure Resource Manager plików Bicep
description: Utwórz pierwszy plik Bicep do wdrażania zasobów platformy Azure. Samouczek zawiera informacje na temat składni pliku Bicep oraz sposobu wdrażania konta magazynu.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594278"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Samouczek: Tworzenie i wdrażanie pierwszego Azure Resource Manager pliku Bicep

Ten samouczek przedstawia [Bicep](./bicep-overview.md). Pokazuje, jak utworzyć plik startowy Bicep i wdrożyć go na platformie Azure. Dowiesz się więcej na temat struktury pliku Bicep i narzędzi potrzebnych do pracy z plikami Bicep. Ukończenie tego samouczka zajmuje około **12 minut** , ale rzeczywisty czas będzie różny w zależności od liczby narzędzi, które należy zainstalować.

Ten samouczek jest pierwszą częścią serii. W miarę postępów przez serię należy zmodyfikować początkowy plik Bicep krok po kroku, dopóki nie zostaną zbadane wszystkie podstawowe części pliku Bicep. Elementy te są blokami konstrukcyjnymi dla znacznie bardziej złożonych plików Bicep. Mamy nadzieję, że na końcu serii masz pewność, że tworzysz własne pliki Bicep i chcesz zautomatyzować wdrożenia przy użyciu plików Bicep.

Jeśli chcesz dowiedzieć się więcej na temat korzyści z używania Bicep i dlaczego należy zautomatyzować wdrażanie za pomocą plików Bicep, zobacz [Bicep](./bicep-overview.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Pobierz narzędzia

Zacznijmy od upewnienia się, że masz narzędzia potrzebne do tworzenia i wdrażania plików Bicep. Zainstaluj te narzędzia na komputerze lokalnym.

### <a name="editor"></a>Edytor

Do tworzenia plików Bicep potrzebny jest dobry edytor. Zalecamy Visual Studio Code z rozszerzeniem Bicep. Jeśli zachodzi potrzeba zainstalowania tych narzędzi, zobacz [Konfigurowanie środowiska deweloperskiego Bicep](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Wdrożenie wiersza polecenia

Pliki Bicep można wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. W przypadku interfejsu wiersza polecenia platformy Azure potrzebna jest wersja 2.20.0 lub nowsza; w przypadku Azure PowerShell wymagana jest wersja 5.6.0 lub nowsza. Instrukcje instalacji znajdują się w temacie:

- [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie macOS](/cli/azure/install-azure-cli-macos)

Po zainstalowaniu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure upewnij się, że logujesz się po raz pierwszy. Aby uzyskać pomoc, zobacz artykuł [Logowanie — PowerShell](/powershell/azure/install-az-ps#sign-in) lub [Logowanie się do interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Teraz możesz zacząć uczenie się o Bicep.

## <a name="create-your-first-bicep-file"></a>Utwórz swój pierwszy plik Bicep

1. Otwórz Visual Studio Code z zainstalowanym rozszerzeniem Bicep.
1. Z menu **plik** wybierz pozycję **nowy plik** , aby utworzyć nowy plik.
1. Z menu **plik** wybierz polecenie **Zapisz jako**.
1. Nazwij plik _azuredeploy_ i wybierz rozszerzenie pliku _Bicep_ . Pełna nazwa pliku to _azuredeploy. Bicep_.
1. Zapisz plik na stacji roboczej. Wybierz ścieżkę, która jest łatwa do zapamiętania, ponieważ będzie ona dostarczana później podczas wdrażania pliku Bicep.
1. Skopiuj i wklej następującą zawartość do pliku:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Oto jak wygląda środowisko Visual Studio Code:

    ![Visual Studio Code pierwszy plik Bicep](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    Deklaracja zasobu ma cztery składniki:

    - **Resource**: słowo kluczowe.
    - **Nazwa symboliczna** (STG): Nazwa symboliczna jest identyfikatorem do odwoływania się do zasobu w pliku Bicep. Nie jest to nazwa zasobu, który będzie wdrażany. Nazwa zasobu jest definiowana przez właściwość **name** .  Zapoznaj się z czwartym składnikiem na tej liście. Aby ułatwić korzystanie z samouczków, **STG** jest używany jako Nazwa symboliczna zasobu konta magazynu w tej serii samouczków. Aby dowiedzieć się, jak użyć nazwy symbolicznej, aby uzyskać pełną listę właściwości obiektu, zobacz [Dodawanie danych wyjściowych](./bicep-tutorial-add-outputs.md).
    - **Typ zasobu** ( Microsoft.Storage/storageAccounts@2019-06-01 ): składa się z dostawcy zasobów (Microsoft. Storage), typu zasobu (StorageAccounts) i apiVersion (2019-06-01). Każdy dostawca zasobów publikuje własne wersje interfejsu API, więc ta wartość jest specyficzna dla tego typu. Więcej typów i apiVersions dla różnych zasobów platformy Azure można znaleźć w temacie [odwołanie do szablonu ARM](/azure/templates/).
    - **Właściwości** (wszystko w elemencie = {...}): są to określone właściwości, które chcesz określić dla danego typu zasobu. Są to dokładnie te same właściwości, które są dostępne dla użytkownika w szablonie ARM. Każdy zasób ma `name` Właściwość. Większość zasobów ma również `location` Właściwość, która ustawia region, w którym zasób jest wdrażany. Inne właściwości różnią się w zależności od typu zasobu i wersji interfejsu API. Ważne jest, aby zrozumieć połączenie między wersją interfejsu API i dostępnymi właściwościami, więc Przyjrzyjmy się bardziej szczegółowo.

        Dla tego konta magazynu można zobaczyć wersję interfejsu API w witrynie [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Zwróć uwagę, że wszystkie właściwości nie zostały dodane do pliku Bicep. Wiele właściwości jest opcjonalnych. `Microsoft.Storage`Dostawca zasobów może wydać nową wersję interfejsu API, ale wdrażaną wersję nie trzeba zmieniać. Możesz dalej korzystać z tej wersji i wiedzieć, że wyniki wdrożenia będą spójne.

        Jeśli zobaczysz starszą wersję interfejsu API, taką jak [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), zobaczysz, że jest dostępny mniejszy zestaw właściwości.

        Jeśli zdecydujesz się zmienić wersję interfejsu API dla zasobu, upewnij się, że są oceniane właściwości tej wersji i odpowiednio Dostosuj plik Bicep.

1. Zastąp tekst `{provide-unique-name}` nawiasem klamrowym `{}` z unikatową nazwą konta magazynu.

    > [!IMPORTANT]
    > Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Nazwa może zawierać tylko małe litery lub cyfry. Nie może być dłuższa niż 24 znaki. Możesz spróbować użyć wzorca nazewnictwa, takiego jak **store1** , jako prefiksu, a następnie dodać inicjały i bieżącą datę. Na przykład użyta nazwa może wyglądać jak **store1abc09092019**.

    Odgadnięcie unikatowej nazwy konta magazynu nie jest proste i nie działa dobrze w przypadku automatyzowania dużych wdrożeń. W dalszej części tej serii samouczków będziesz używać funkcji Bicep, które ułatwiają tworzenie unikatowej nazwy.

1. Zapisz plik.

Gratulacje, utworzono pierwszy plik Bicep.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby rozpocząć pracę z interfejsem wiersza polecenia Azure PowerShell/Azure, zaloguj się przy użyciu poświadczeń platformy Azure.

Wybierz karty w poniższych sekcjach kodu, aby wybrać między Azure PowerShell i interfejsem wiersza polecenia platformy Azure. Przykłady interfejsu wiersza polecenia w tym artykule są przeznaczone dla powłoki bash.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az login
```

---

Jeśli masz wiele subskrypcji platformy Azure, wybierz subskrypcję, której chcesz użyć. Zamień `[SubscriptionID/SubscriptionName]` i nawiasy kwadratowe `[]` zawierające informacje o subskrypcji:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Podczas wdrażania pliku Bicep należy określić grupę zasobów, która będzie zawierać zasoby. Przed uruchomieniem polecenia wdrożenia utwórz grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub usługi Azure PowerShell.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Bicep to przezroczyste streszczenie za pośrednictwem szablonów Azure Resource Manager (szablony ARM). Każdy plik Bicep kompiluje się do standardowego szablonu ARM przed jego wdrożeniem. Możesz skompilować plik Bicep do szablonu ARM przed jego wdrożeniem lub bezpośrednio wdrożyć plik Bicep. Aby wdrożyć plik Bicep, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Użyj utworzonej grupy zasobów. Nadaj nazwę wdrożenia, aby można je było łatwo zidentyfikować w historii wdrażania. Dla wygody należy również utworzyć zmienną, która przechowuje ścieżkę do pliku Bicep. Ta zmienna ułatwia uruchamianie poleceń wdrażania, ponieważ nie trzeba ponownie wpisywać ścieżki przy każdym wdrożeniu. Zamień `{provide-the-path-to-the-bicep-file}` , włączając nawiasy klamrowe `{}` ze ścieżką do pliku Bicep z nazwą rozszerzenia pliku _. Bicep_ .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Polecenie wdrożenia zwraca wyniki. Wyszukaj `ProvisioningState` , aby sprawdzić, czy wdrożenie zakończyło się pomyślnie.

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

1. Wybierz wdrożenie grupy zasobów w ostatniej procedurze. Nazwa domyślna to moja **resourceName**. W grupie zasobów nie ma żadnego wdrożonego zasobu.

1. W prawym górnym rogu przeglądu zostanie wyświetlony stan wdrożenia. Wybieranie **1 zakończyło się pomyślnie**.

   ![Wyświetl stan wdrożenia](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Zobaczysz historię wdrożenia grupy zasobów. Wybierz pozycję **firstbicep**.

   ![Wybieranie wdrożenia](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Zostanie wyświetlone podsumowanie wdrożenia. Zostało wdrożone jedno konto magazynu. Zwróć uwagę na to, że po lewej stronie można wyświetlić dane wejściowe i wyjściowe oraz szablon użyty podczas wdrażania.

   ![Wyświetl podsumowanie wdrożenia](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz teraz, możesz chcieć usunąć grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Utworzono prosty plik Bicep do wdrożenia na platformie Azure.  W kolejnych samouczkach dowiesz się, jak dodawać parametry, zmienne, dane wyjściowe i moduły do pliku Bicep. Te funkcje są blokami konstrukcyjnymi dla znacznie bardziej złożonych plików Bicep.

> [!div class="nextstepaction"]
> [Dodawanie parametrów](bicep-tutorial-add-parameters.md)

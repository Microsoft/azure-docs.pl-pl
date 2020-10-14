---
title: 'Szybki Start: Tworzenie kwerendy udostępnionej przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start wykonaj kroki, aby włączyć rozszerzenie Graf zasobów dla interfejsu wiersza polecenia platformy Azure i utworzyć zapytanie udostępnione.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: daaa0dc4039c37094330148f839fadf7b4013276
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057199"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Szybki Start: Tworzenie zapytania udostępnionego grafu zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Pierwszym krokiem do korzystania z grafu zasobów platformy Azure za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/) jest sprawdzenie, czy rozszerzenie jest zainstalowane. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania rozszerzenia do instalacji interfejsu wiersza polecenia platformy Azure. Rozszerzenia można używać z interfejsem wiersza polecenia platformy Azure zainstalowanym lokalnie lub za pośrednictwem [usługi Azure Cloud Shell](https://shell.azure.com).

Po zakończeniu tego procesu dodaliśmy rozszerzenie do wybranej instalacji interfejsu wiersza polecenia platformy Azure i utworzysz zapytanie udostępnione grafu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Dodawanie rozszerzenia usługi Resource Graph

Aby włączyć interfejs wiersza polecenia platformy Azure do pracy z usługą Azure Resource Graph, należy dodać rozszerzenie. To rozszerzenie działa wszędzie tam, gdzie interfejs wiersza polecenia platformy Azure może być używany, w tym w [funkcji bash w systemie Windows 10](/windows/wsl/install-win10), usłudze [Cloud Shell](https://shell.azure.com) (autonomicznej i wewnątrz portalu), [obrazie platformy Docker interfejsu wiersza polecenia platformy Azure](https://hub.docker.com/_/microsoft-azure-cli), lub zainstalowany lokalnie.

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.8.0**). Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/cli/azure/install-azure-cli-windows).

1. W wybranym środowisku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Extension Add](/cli/azure/extension#az-extension-add) , aby zaimportować rozszerzenie grafu zasobów przy użyciu następujące polecenie:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Sprawdź, czy rozszerzenie zostało zainstalowane i czy jest to oczekiwana wersja (co najmniej **1.1.0**) z [listą rozszerzeń AZ](/cli/azure/extension#az-extension-list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Tworzenie zapytania udostępnionego wykresu zasobów

Po dodaniu rozszerzenia interfejsu wiersza polecenia platformy Azure do wybranego środowiska przekroczenie czasu do zapytania udostępnionego grafu zasobów. Udostępnione zapytanie jest obiektem Azure Resource Manager, w którym można udzielić uprawnienia lub uruchomić je w Eksploratorze Azure Resource Graph. Zapytanie podsumowuje liczbę wszystkich zasobów pogrupowanych według _lokalizacji_.

1. Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az-group-create) , aby przechowywać udostępnione zapytanie grafu zasobów platformy Azure. Ta grupa zasobów ma nazwę `resource-graph-queries` , a lokalizacja to `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Utwórz zapytanie udostępnione wykresu zasobów platformy Azure przy użyciu `graph` rozszerzenia i [AZ Graph Shared-Query Create](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create) :

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Wyświetl listę udostępnionych zapytań w nowej grupie zasobów. Polecenie [AZ Graph Shared-Query List](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) zwraca tablicę wartości.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Aby uzyskać tylko jeden współużytkowany wynik zapytania, użyj polecenia [AZ Graph Shared-Query show](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show) .

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Uruchom zapytanie udostępnione w interfejsie wiersza polecenia platformy Azure z `{{shared-query-uri}}` składnią w poleceniu [AZ Graph Query](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query) .
   Najpierw skopiuj `id` pole z wyniku poprzedniego `show` polecenia. Zastąp `shared-query-uri` tekst w przykładzie wartością z `id` pola, pozostawiając otaczające `{{` `}}` znaki i.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}`Składnia jest funkcją w **wersji zapoznawczej** .

Innym sposobem na znalezienie zapytań udostępnionych grafu zasobów jest użycie Azure Portal. W portalu Użyj paska wyszukiwania, aby wyszukać "zapytania dotyczące wykresów zasobów". Wybierz zapytanie udostępnione. Na stronie **Przegląd** na karcie **zapytanie** zostanie wyświetlone zapisane zapytanie. Przycisk **Edytuj** zostanie otwarty w [Eksploratorze grafu zasobów](./first-query-portal.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zapytanie udostępnione, grupę zasobów i rozszerzenie grafu zasobów ze środowiska interfejsu wiersza polecenia platformy Azure, możesz to zrobić za pomocą następujących poleceń:

- [AZ Graph Shared-Query Delete](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [AZ Extension Remove](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dodano rozszerzenie Graf zasobów do środowiska interfejsu wiersza polecenia platformy Azure i utworzono udostępnione zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
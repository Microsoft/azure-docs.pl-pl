---
title: 'Szybki start: tworzenie udostępnionego zapytania za pomocą interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku Szybki start wykonaj kroki, aby włączyć rozszerzenie interfejsu Resource Graph dla interfejsu wiersza polecenia platformy Azure i utworzyć zapytanie udostępnione.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 2befc2b6895cd9d2c797fc8ed0e28a27eb6e73e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870557"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Szybki start: tworzenie zapytania Resource Graph za pomocą interfejsu wiersza polecenia platformy Azure

Pierwszym krokiem do korzystania Azure Resource Graph z interfejsem wiersza [polecenia platformy Azure](/cli/azure/) jest sprawdzenie, czy rozszerzenie jest zainstalowane. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania rozszerzenia do instalacji interfejsu wiersza polecenia platformy Azure. Rozszerzenia można używać z interfejsem wiersza polecenia platformy Azure zainstalowanym lokalnie lub za pośrednictwem [usługi Azure Cloud Shell](https://shell.azure.com).

Po zakończeniu tego procesu dodano rozszerzenie do wybranej instalacji interfejsu wiersza polecenia platformy Azure i utworzysz Resource Graph udostępnionego.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Dodawanie rozszerzenia usługi Resource Graph

Aby umożliwić interfejsowi wiersza polecenia platformy Azure Azure Resource Graph, należy dodać rozszerzenie. To rozszerzenie działa wszędzie tam, gdzie interfejs wiersza polecenia platformy Azure może być używany, w tym w [funkcji bash w systemie Windows 10](/windows/wsl/install-win10), usłudze [Cloud Shell](https://shell.azure.com) (autonomicznej i wewnątrz portalu), [obrazie platformy Docker interfejsu wiersza polecenia platformy Azure](https://hub.docker.com/_/microsoft-azure-cli), lub zainstalowany lokalnie.

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.8.0).** Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/cli/azure/install-azure-cli-windows).

1. W swoim środowisku interfejsu wiersza polecenia platformy Azure użyj polecenia [az extension add,](/cli/azure/extension#az_extension_add) aby zaimportować rozszerzenie Resource Graph za pomocą następującego polecenia:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Sprawdź, czy rozszerzenie zostało zainstalowane i jest w oczekiwanej wersji (co najmniej **1.1.0**) za pomocą [az extension list](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Tworzenie Resource Graph udostępnionego

Po dodaniu rozszerzenia interfejsu wiersza polecenia platformy Azure do wybranego środowiska czas na Resource Graph udostępnionego. Udostępnione zapytanie jest obiektem Azure Resource Manager który można przyznać uprawnienia lub uruchamiać w Azure Resource Graph Explorer. Zapytanie podsumowuje liczbę wszystkich zasobów pogrupowanych według _lokalizacji_.

1. Utwórz grupę zasobów za [pomocą az group create](/cli/azure/group#az_group_create) w celu przechowywania Azure Resource Graph udostępnionego zapytania. Ta grupa zasobów ma nazwę `resource-graph-queries` , a lokalizacja to `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Utwórz zapytanie Azure Resource Graph za pomocą `graph` rozszerzenia i [polecenia az graph shared-query create:](/cli/azure/graph/shared-query#az_graph_shared_query_create)

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Wyliczanie udostępnionych zapytań w nowej grupie zasobów. Polecenie [az graph shared-query list](/cli/azure/graph/shared-query#az_graph_shared_query_list) zwraca tablicę wartości.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Aby uzyskać tylko jeden udostępniony wynik zapytania, użyj [polecenia az graph shared-query show.](/cli/azure/graph/shared-query#az_graph_shared_query_show)

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Uruchom zapytanie udostępnione w interfejsie wiersza polecenia platformy Azure przy `{{shared-query-uri}}` użyciu składni w [poleceniu az graph query.](/cli/azure/graph#az_graph_query)
   Najpierw skopiuj `id` pole z wyniku poprzedniego `show` polecenia. Zastąp `shared-query-uri` tekst w przykładzie wartością z pola , ale `id` pozostaw otaczające ją znaki i `{{` `}}` .

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > Składnia `{{shared-query-uri}}` jest funkcją **w wersji zapoznawczej.**

Innym sposobem na znalezienie Resource Graph udostępnionych zapytań jest pośrednictwem Azure Portal. W portalu użyj paska wyszukiwania, aby wyszukać "Resource Graph zapytania". Wybierz udostępnione zapytanie. Na stronie **Przegląd** na **karcie Zapytanie** zostanie wyświetlone zapisane zapytanie. Przycisk **Edytuj** otwiera go w [eksploratorze Resource Graph Explorer.](./first-query-portal.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zapytanie udostępnione Resource Graph, grupę zasobów i rozszerzenie ze środowiska interfejsu wiersza polecenia platformy Azure, możesz to zrobić za pomocą następujących poleceń:

- [az graph shared-query delete](/cli/azure/graph/shared-query#az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

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

W tym przewodniku Szybki start dodano rozszerzenie Resource Graph interfejsu wiersza polecenia platformy Azure i utworzono zapytanie udostępnione. Aby dowiedzieć się więcej o języku Resource Graph, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
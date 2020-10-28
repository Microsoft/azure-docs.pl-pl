---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 53d4aa70b55577cdf2f6a1b898b496eb368157f5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755659"
---
Aby dodać dwa tagi do grupy zasobów, użyj polecenia [az group update](/cli/azure/group):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Załóżmy, że chcesz dodać trzeci tag. Uruchom ponownie polecenie z nowym tagiem. Zostanie on dołączony do istniejących tagów.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Zasoby nie dziedziczą tagów z grupy zasobów. Obecnie Twoja grupa zasobów zawiera trzy tagi, ale zasoby nie mają żadnych tagów. Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów i zachować tagi istniejące w zasobach, użyj następującego skryptu:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Alternatywnie możesz zastosować tagi z grupy zasobów do zasobów bez zachowania istniejących tagów:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Aby połączyć kilka wartości w jednym tagu, użyj ciągu JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Aby usunąć wszystkie tagi w grupie zasobów, użyj następującego polecenia:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```

---
title: Tworzenie zestawu skalowania z wyspecjalizowanej wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Utwórz zestaw skalowania za pomocą wyspecjalizowanej wersji obrazu w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 5de9fe7c81059c56c99a55ca066e186cbf83c50f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796982"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Tworzenie zestawu skalowania za pomocą wyspecjalizowanej wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz zestaw skalowania z [wersji obrazu specjalistycznej](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) przechowywanej w galerii obrazów udostępnionych. Jeśli chcesz utworzyć zestaw skalowania przy użyciu uogólnionej wersji obrazu, zobacz [Tworzenie maszyny wirtualnej na podstawie uogólnionej wersji obrazu](instance-generalized-image-version-cli.md).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

W tym przykładzie Zastąp nazwy zasobów zgodnie z wymaganiami. 

Wyświetl listę definicji obrazów w galerii za pomocą polecenia [AZ SIG Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) , aby wyświetlić nazwę i identyfikator definicji.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Utwórz zestaw skalowania przy [`az vmss create`](/cli/azure/vmss#az-vmss-create) użyciu `--specialized` parametru, aby wskazać, że obraz jest obrazem specjalistycznym.

Użyj identyfikatora definicji obrazu do, `--image` aby utworzyć wystąpienia zestawu skalowania z najnowszej wersji dostępnego obrazu. Możesz również utworzyć wystąpienia zestawu skalowania na podstawie określonej wersji, podając identyfikator wersji obrazu dla `--image`. Należy pamiętać, że użycie określonej wersji obrazu oznacza, że Automatyzacja może się nie powieść, jeśli określona wersja obrazu jest niedostępna, ponieważ została usunięta lub usunięta z regionu. Zalecamy użycie identyfikatora definicji obrazu do tworzenia nowej maszyny wirtualnej, o ile nie jest wymagana określona wersja obrazu.

W tym przykładzie tworzymy wystąpienia z najnowszej wersji obrazu *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](../virtual-machines/linux/image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)




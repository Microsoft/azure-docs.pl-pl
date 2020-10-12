---
title: Dodawanie maszyny wirtualnej przy użyciu udostępnionego obrazu w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak dodać maszynę wirtualną (VM) przy użyciu obrazu z dołączonej galerii obrazów udostępnionych w Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483860"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Dodawanie maszyny wirtualnej przy użyciu obrazu z dołączonej galerii obrazów udostępnionych
Azure DevTest Labs umożliwia dołączenie udostępnionej galerii obrazów do laboratorium, a następnie używanie obrazów w galerii jako baz dla maszyn wirtualnych utworzonych w laboratorium. Aby dowiedzieć się, jak dołączyć udostępnioną galerię obrazów do laboratorium, zobacz [Konfigurowanie udostępnionej galerii obrazów](configure-shared-image-gallery.md). W tym artykule pokazano, jak dodać maszynę wirtualną do laboratorium przy użyciu obrazu z dołączonej udostępnionej galerii obrazów jako podstawy. 

## <a name="azure-portal"></a>Azure Portal
W tej sekcji dowiesz się, jak za pomocą Azure Portal dodać maszynę wirtualną do laboratorium na podstawie obrazu z dołączonej galerii obrazów udostępnionych. Ta sekcja nie zawiera szczegółowych instrukcji krok po kroku dotyczących tworzenia maszyny wirtualnej przy użyciu Azure Portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie maszyny wirtualnej — Azure Portal](devtest-lab-add-vm.md). Tylko wyróżniają kroki, w których wybierasz obraz z dołączonej udostępnionej galerii obrazów i wybierz wersję obrazu, którego chcesz użyć. 

Podczas dodawania maszyny wirtualnej do laboratorium można wybrać obraz z dołączonej udostępnionej galerii obrazów jako obrazu podstawowego: 

![Wybierz udostępniony obraz dla bazy](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Następnie na karcie **Ustawienia zaawansowane** na stronie **Tworzenie zasobów laboratorium** możesz wybrać wersję obrazu, którego chcesz użyć jako obrazu podstawowego:

![Wybierz wersję obrazu](./media/add-vm-use-shared-image/select-version-shared-image.png)

Po utworzeniu maszyny wirtualnej możesz użyć innej wersji obrazu. 

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Jeśli używasz szablonu Azure Resource Manager, aby utworzyć maszynę wirtualną przy użyciu obrazu udostępnionej galerii obrazów, określ wartość dla **sharedImageId** w sekcji **Właściwości** . Zobacz poniższy przykład: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Aby zapoznać się z kompletnym przykładem szablonu Menedżer zasobów, zobacz [Tworzenie maszyny wirtualnej za pomocą udostępnionego obrazu galerii obrazów](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) w naszym repozytorium GitHub. 

## <a name="rest-api"></a>Interfejs API REST

1. Najpierw należy uzyskać identyfikator obrazu w galerii obrazów udostępnionych. Jednym ze sposobów jest wyświetlenie listy wszystkich obrazów w dołączonej galerii udostępnionych obrazów przy użyciu następującego polecenia GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Wywołaj metodę PUT na maszynach wirtualnych, przekazując identyfikator udostępnionego obrazu otrzymanego od poprzedniego wywołania do `properties.SharedImageId` .

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak dołączyć galerię udostępnionych obrazów do laboratorium i skonfigurować ją, zobacz [Configure Shared Image Gallery](configure-shared-image-gallery.md).
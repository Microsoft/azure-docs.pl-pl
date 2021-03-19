---
title: Konwertowanie szablonu zestawu skalowania w celu używania dysku zarządzanego
description: Przekonwertuj szablon zestawu skalowania maszyn wirtualnych Azure Resource Manager na szablon zestawu skalowania dysku zarządzanego.
keywords: zestawy skalowania maszyn wirtualnych
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 03cbe4eb56f3b3b99f87048b699f76b30b7937c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85373968"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konwertowanie szablonu zestawu skalowania na szablon zestawu skalowania dysku zarządzanego

Klienci z szablonem Menedżer zasobów do tworzenia zestawu skalowania, który nie korzysta z dysku zarządzanego, mogą chcieć zmodyfikować go tak, aby korzystał z dysku zarządzanego. W tym artykule pokazano, jak korzystać z usługi Managed disks, używając jako przykładowego żądania ściągnięcia z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates), repozytorium opartego na społeczności dla przykładowych szablonów Menedżer zasobów. Pełne żądanie ściągnięcia można zobaczyć tutaj: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) , a istotne części porównania są poniżej, a także wyjaśnienia:

## <a name="making-the-os-disks-managed"></a>Tworzenie zarządzanych dysków systemu operacyjnego

W poniższej różnicy kilka zmiennych związanych z kontem magazynu i właściwościami dysku są usuwane. Typ konta magazynu nie jest już konieczny (Standard_LRS jest wartością domyślną), ale można go określić w razie potrzeby. Tylko Standard_LRS i Premium_LRS są obsługiwane przez dysk zarządzany. W starym szablonie użyto nowego sufiksu konta magazynu, unikatowej tablicy ciągów i liczby skojarzeń zabezpieczeń, aby wygenerować nazwy kont magazynu. Te zmienne nie są już potrzebne w nowym szablonie, ponieważ dysk zarządzany automatycznie tworzy konta magazynu w imieniu klienta. Podobnie, nazwa kontenera VHD i nazwa dysku systemu operacyjnego nie są już potrzebne, ponieważ dysk zarządzany automatycznie Nazwij bazowe kontenery i dyski obiektów blob magazynu.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Poniższa różnica polega na tym, że interfejs API obliczeń został zaktualizowany do wersji 2016-04-30-Preview, która jest najwcześniejszą wersją wymaganą do obsługi dysku zarządzanego przy użyciu zestawów skalowania. W razie potrzeby można użyć dysków niezarządzanych w nowej wersji interfejsu API z poprzednią składnią. Jeśli aktualizujesz tylko wersję interfejsu API obliczeń i nie zmienisz niczego innego, szablon powinien nadal działał jak wcześniej.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

W poniższej różnicy zasób konta magazynu jest usuwany z tablicy zasobów całkowicie. Zasób nie jest już wymagany, ponieważ dysk zarządzany tworzy je automatycznie.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

W poniższym pozostałej różnicy można zobaczyć, że usuwamy klauzulę zależną od zestawu skalowania do pętli, w której zostały utworzone konta magazynu. W starym szablonie zagwarantujemy, że konta magazynu zostały utworzone przed rozpoczęciem tworzenia zestawu skalowania, ale ta klauzula nie jest już potrzebna w przypadku dysku zarządzanego. Właściwość kontenerów VHD jest również usuwana wraz z właściwością nazwa dysku systemu operacyjnego, ponieważ te właściwości są automatycznie obsługiwane pod okapem przez dysk zarządzany. `"managedDisk": { "storageAccountType": "Premium_LRS" }`Jeśli potrzebujesz dysków systemu operacyjnego w warstwie Premium, możesz dodać je w konfiguracji "osDisk". Tylko maszyny wirtualne z dużymi lub małymi literami "w jednostce SKU maszyn wirtualnych mogą używać dysków w warstwie Premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

W konfiguracji zestawu skalowania nie ma jawnej właściwości, czy ma być używany dysk zarządzany lub niezarządzany. Zestaw skalowania wie, który ma być używany na podstawie właściwości znajdujących się w profilu magazynu. Dlatego ważne jest, aby zmodyfikować szablon w celu upewnienia się, że odpowiednie właściwości znajdują się w profilu magazynu zestawu skalowania.


## <a name="data-disks"></a>Dyski danych

Po wprowadzeniu powyższych zmian zestaw skalowania używa dysków zarządzanych dla dysku systemu operacyjnego, ale co o dyskach z danymi? Aby dodać dyski danych, należy dodać właściwość "datadisks" w pozycji "obszarze storageprofile" na tym samym poziomie co "osDisk". Wartość właściwości jest listą obiektów JSON, każdy z nich ma właściwości "LUN" (które muszą być unikatowe dla każdego dysku z danymi na maszynie wirtualnej), "diskSizeGB" ("Empty" jest obecnie jedyną obsługiwaną opcją), a "wartość" (rozmiar dysku w gigabajtach) musi być większa niż 0 i mniejsza niż 1024), jak w poniższym przykładzie:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Jeśli określisz `n` dyski w tej tablicy, każda maszyna wirtualna w zestawie skalowania pobierze `n` dyski danych. Należy jednak pamiętać, że te dyski danych są urządzeniami nieprzetworzonymi. Nie są one sformatowane. Klient może dołączyć, podzielić i sformatować dyski przed ich użyciem. Opcjonalnie można również określić `"managedDisk": { "storageAccountType": "Premium_LRS" }` wartość w każdym obiekcie dysku danych, aby określić, że powinien być dyskiem danych w warstwie Premium. Tylko maszyny wirtualne z dużymi lub małymi literami "w jednostce SKU maszyn wirtualnych mogą używać dysków w warstwie Premium.

Aby dowiedzieć się więcej o korzystaniu z dysków danych z zestawami skalowania, zobacz [ten artykuł](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Następne kroki
Na przykład Menedżer zasobów szablonów przy użyciu zestawów skalowania Wyszukaj ciąg "VMSS" w [repozytorium GitHub dla szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).

Aby uzyskać ogólne informacje, zapoznaj się z główną stroną docelową [dla zestawów skalowania](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


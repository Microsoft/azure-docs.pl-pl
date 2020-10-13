---
title: Informacje o szablonach zestawu skalowania maszyn wirtualnych
description: Dowiedz się, jak utworzyć podstawowy szablon zestawu skalowania dla zestawów skalowania maszyn wirtualnych platformy Azure za pomocą kilku prostych kroków.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 55c826b4baf38732684aaa0465aeaab6a45564db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87831502"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Informacje o szablonach zestawu skalowania maszyn wirtualnych
[Szablony usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md#template-deployment-process) to doskonały sposób wdrażania grup powiązanych zasobów. W tej serii samouczków pokazano, jak utworzyć podstawowy szablon zestawu skalowania i jak zmodyfikować ten szablon w celu dopasowania do różnych scenariuszy. Wszystkie przykłady pochodzą z tego [repozytorium GitHub](https://github.com/gatneil/mvss).

Ten szablon jest przeznaczony do prostej. Aby zapoznać się z bardziej kompletnymi przykładami szablonów zestawu skalowania, zobacz [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) i wyszukiwanie folderów zawierających ciąg `vmss` .

Jeśli masz już doświadczenie w tworzeniu szablonów, możesz przejść do sekcji "następne kroki", aby dowiedzieć się, jak zmodyfikować ten szablon.

## <a name="define-schema-and-contentversion"></a>Zdefiniuj $schema i Contentversion —
Najpierw Zdefiniuj `$schema` i `contentVersion` w szablonie. `$schema`Element definiuje wersję języka szablonu i służy do wyróżniania składni programu Visual Studio i podobnych funkcji sprawdzania poprawności. Ten `contentVersion` element nie jest używany przez platformę Azure. Zamiast tego pomaga śledzić wersję szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definiowanie parametrów
Następnie zdefiniuj dwa parametry `adminUsername` i `adminPassword` . Parametry są wartościami określonymi w momencie wdrożenia. `adminUsername`Parametr jest po prostu `string` typem, ale ponieważ `adminPassword` jest tajny, nadaj mu typ `securestring` . Później te parametry są przesyłane do konfiguracji zestawu skalowania.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definiowanie zmiennych
Szablony Menedżer zasobów pozwalają również definiować zmienne, które będą używane później w szablonie. W przykładzie nie są używane żadne zmienne, więc obiekt JSON jest pusty.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiowanie zasobów
Dalej to sekcja zasobów szablonu. Tutaj możesz zdefiniować, co chcesz wdrożyć. W przeciwieństwie do `parameters` i `variables` (które są obiektami JSON), `resources` jest listą JSON obiektów JSON.

```json
   "resources": [
```

Wszystkie zasoby wymagają `type` `name` właściwości,, `apiVersion` i `location` . Pierwszy zasób tego przykładu ma typ [Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), Name `myVnet` i apiVersion `2018-11-01` . (Aby znaleźć najnowszą wersję interfejsu API dla typu zasobu, zobacz odwołanie do [szablonu Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Określ lokalizację
Aby określić lokalizację sieci wirtualnej, użyj [funkcji Menedżer zasobów Template](../azure-resource-manager/templates/template-functions.md). Ta funkcja musi być ujęta w cudzysłów i nawiasy kwadratowe, takie jak to: `"[<template-function>]"` . W takim przypadku należy użyć `resourceGroup` funkcji. Nie przyjmuje żadnych argumentów i zwraca obiekt JSON z metadanymi o grupie zasobów, do której to wdrożenie jest wdrażane. Grupa zasobów jest ustawiana przez użytkownika w czasie wdrażania. Ta wartość jest następnie indeksowana do tego obiektu JSON w `.location` celu pobrania lokalizacji z obiektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Określ właściwości sieci wirtualnej
Każdy zasób Menedżer zasobów ma własną `properties` sekcję dla konfiguracji specyficznych dla zasobu. W takim przypadku należy określić, że sieć wirtualna powinna mieć jedną podsieć używaną przez prywatny zakres adresów IP `10.0.0.0/16` . Zestaw skalowania jest zawsze zawarty w jednej podsieci. Nie może obejmować podsieci.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Dodaj listę dependsOn
Oprócz wymaganych `type` `name` właściwości,, i, `apiVersion` `location` każdy zasób może zawierać opcjonalną `dependsOn` listę ciągów. Ta lista określa, które inne zasoby z tego wdrożenia muszą zostać zakończone przed wdrożeniem tego zasobu.

W takim przypadku na liście znajduje się tylko jeden element sieci wirtualnej z poprzedniego przykładu. Ta zależność jest określana, ponieważ zestaw skalowania potrzebuje sieci przed utworzeniem jakichkolwiek maszyn wirtualnych. W ten sposób zestaw skalowania może udostępnić te prywatne adresy IP z zakresu adresów IP wcześniej określonych we właściwościach sieci. Format każdego ciągu na liście dependsOn jest `<type>/<name>` . Użyj tej samej `type` i `name` użytej wcześniej w definicji zasobu sieci wirtualnej.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Określ właściwości zestawu skalowania
Zestawy skalowania mają wiele właściwości do dostosowywania maszyn wirtualnych w zestawie skalowania. Aby zapoznać się z pełną listą tych właściwości, zobacz [odwołanie do szablonu](/azure/templates/microsoft.compute/virtualmachinescalesets). Na potrzeby tego samouczka są ustawiane tylko kilka najczęściej używanych właściwości.
### <a name="supply-vm-size-and-capacity"></a>Określanie rozmiaru i pojemności maszyny wirtualnej
Zestaw skalowania musi wiedzieć, jaki rozmiar maszyny wirtualnej utworzyć ("Nazwa jednostki SKU") oraz liczbę takich maszyn wirtualnych do utworzenia ("pojemność jednostki SKU"). Aby sprawdzić, które rozmiary maszyn wirtualnych są dostępne, zobacz [dokumentację dotyczącą rozmiarów maszyn wirtualnych](../virtual-machines/sizes.md).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Wybierz typ aktualizacji
Zestaw skalowania musi również wiedzieć, jak obsługiwać aktualizacje w zestawie skalowania. Obecnie dostępne są trzy opcje, `Manual` `Rolling` i `Automatic` . Aby uzyskać więcej informacji o różnicach między tymi dwoma, zapoznaj się z dokumentacją dotyczącą [uaktualniania zestawu skalowania](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Wybierz system operacyjny maszyny wirtualnej
Zestaw skalowania musi wiedzieć, jaki system operacyjny umieścić na maszynach wirtualnych. W tym miejscu można utworzyć maszyny wirtualne za pomocą w pełni poprawionego obrazu Ubuntu 16,04-LTS.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Określ computerNamePrefix
Zestaw skalowania służy do wdrażania wielu maszyn wirtualnych. Zamiast określania nazw każdej maszyny wirtualnej, określ `computerNamePrefix` . Zestaw skalowania dołącza indeks do prefiksu dla każdej maszyny wirtualnej, więc nazwy maszyn wirtualnych mają postać `<computerNamePrefix>_<auto-generated-index>` .

W poniższym fragmencie kodu Użyj parametrów z przed, aby ustawić nazwę użytkownika i hasło administratora dla wszystkich maszyn wirtualnych w zestawie skalowania. Ten proces używa `parameters` funkcji szablonu. Ta funkcja przyjmuje ciąg, który określa, do którego parametru odwołuje się i wyprowadza wartość dla tego parametru.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Określ konfigurację sieci VMNETWORK
Na koniec Określ konfigurację sieci dla maszyn wirtualnych w zestawie skalowania. W takim przypadku wystarczy określić identyfikator utworzonej wcześniej podsieci. Oznacza to, że zestaw skalowania umieszcza interfejsy sieciowe w tej podsieci.

Identyfikator sieci wirtualnej zawierającej podsieć można uzyskać przy użyciu `resourceId` funkcji szablonu. Ta funkcja przyjmuje typ i nazwę zasobu i zwraca w pełni kwalifikowany identyfikator tego zasobu. Ten identyfikator ma postać: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Jednak identyfikator sieci wirtualnej jest zbyt mały. Podaj określoną podsieć, w której powinny znajdować się maszyny wirtualne zestawu skalowania. Aby to zrobić, Połącz `/subnets/mySubnet` się z identyfikatorem sieci wirtualnej. Wynikiem jest w pełni kwalifikowany identyfikator podsieci. Wykonaj to połączenie za pomocą `concat` funkcji, która wykonuje serię ciągów i zwraca ich połączenie.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
---
title: Przykłady Azure PowerShell — Resetowanie Cloud Services platformy Azure (obsługa rozszerzona)
description: Przykładowe skrypty do resetowania wdrożenia usługi w chmurze platformy Azure (obsługa rozszerzona)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475323"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Resetowanie usługi w chmurze platformy Azure (obsługa rozszerzona) 
Te przykłady obejmują różne sposoby resetowania istniejącego wdrożenia usługi w chmurze platformy Azure (obsługa rozszerzona).

## <a name="reimage-role-instances-of-cloud-service"></a>Odtwarzanie z obrazu wystąpień roli usługi w chmurze
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
To polecenie powoduje odtworzenie obrazów wystąpień roli ContosoFrontEnd_IN_0 i ContosoBackEnd_IN_1 usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Odtwórz z obrazu wszystkie role usługi w chmurze
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
To polecenie odnotuje wszystkie wystąpienia roli usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Odtwarzanie z obrazu pojedynczego wystąpienia roli usługi w chmurze
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
To polecenie powoduje odtworzenie obrazu wystąpienia roli o nazwie ContosoFrontEnd_IN_0 usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Ponowne kompilowanie wystąpień roli usługi w chmurze
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
To polecenie odbudowuje 2 wystąpienia roli ContosoFrontEnd_IN_0 i ContosoBackEnd_IN_1 usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Kompiluj ponownie wszystkie role usługi w chmurze
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
To polecenie odbudowuje wszystkie wystąpienia roli usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Ponowne uruchamianie wystąpień roli usługi w chmurze
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
To polecenie powoduje ponowne uruchomienie 2 wystąpień roli ContosoFrontEnd_IN_0 i ContosoBackEnd_IN_1 usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Uruchom ponownie wszystkie role usługi w chmurze
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
To polecenie powoduje ponowne uruchomienie wszystkich wystąpień roli usługi w chmurze o nazwie ContosoCS należącej do grupy zasobów o nazwie ContosOrg.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat platformy Azure Cloud Services (obsługa rozszerzona), zobacz [Omówienie usługi azure Cloud Services (obsługa rozszerzona)](overview.md).
- Odwiedź [repozytorium przykładów Cloud Services (obsługa rozszerzona)](https://github.com/Azure-Samples/cloud-services-extended-support)

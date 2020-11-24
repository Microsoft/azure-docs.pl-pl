---
title: Jak wdrożyć chmurę z usługą Azure wiosną za pomocą Azure PowerShell
description: Jak wdrożyć chmurę z usługą Azure wiosną za pomocą Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550013"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Wdróż chmurę z platformą Azure z usługą Azure PowerShell

W tym artykule opisano, jak utworzyć wystąpienie chmury wiosennej platformy Azure przy użyciu modułu [AZ. SpringCloud](/powershell/module/Az.SpringCloud) PowerShell.

## <a name="requirements"></a>Wymagania

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Mimo że moduł **AZ. SpringCloud** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell będzie on częścią przyszłych wydań modułu AZ PowerShell i dostępne domyślnie z poziomu Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Inicjowanie obsługi nowego wystąpienia chmury wiosennej platformy Azure

Aby utworzyć nowe wystąpienie chmury wiosennej platformy Azure, użyj polecenia cmdlet [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . W poniższym przykładzie jest tworzona usługa w chmurze Azure Wiosenna o określonej nazwie w wcześniej utworzonej grupie zasobów.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Tworzenie nowej aplikacji w chmurze Azure wiosny

Aby utworzyć nową aplikację, należy użyć polecenia cmdlet [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . Poniższy przykład tworzy aplikację w chmurze Azure wiosną o nazwie `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Tworzenie nowego wdrożenia chmury Azure wiosny

Aby utworzyć nowe wdrożenie, należy użyć polecenia cmdlet [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . Poniższy przykład tworzy wdrożenie chmurowe platformy Azure o nazwie `default` dla `gateway` aplikacji.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Uzyskaj usługę w chmurze Azure wiosną

Aby uzyskać usługę w chmurze Azure wiosną i jej właściwości, należy użyć polecenia cmdlet [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . Poniższy przykład pobiera informacje o określonej usłudze w chmurze Azure wiosennej.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Uzyskiwanie aplikacji w chmurze platformy Azure

Aby uzyskać dostęp do aplikacji w chmurze ze sprężyną Azure i jej właściwości, należy użyć polecenia cmdlet [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . Poniższy przykład pobiera informacje o aplikacji ze `gateway` sprężyną w chmurze.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Uzyskiwanie wdrożenia chmury ze sprężyną Azure

Aby uzyskać wdrożenie chmurowe platformy Azure i jej właściwości, należy użyć polecenia cmdlet [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . Poniższy przykład pobiera informacje o `default` wdrożeniu chmury wiosennej.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, można je usunąć, uruchamiając następujące przykłady.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Usuwanie wdrożenia chmury ze sprężyną Azure

Aby usunąć wdrożenie chmury ze sprężyną Azure, należy użyć polecenia cmdlet [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . Poniższy przykład usuwa wdrożenie aplikacji w chmurze ze sprężyną platformy Azure o nazwie `default` dla określonej usługi i aplikacji.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Usuwanie aplikacji w chmurze Azure wiosennej

Aby usunąć aplikację ze sprężyną w chmurze, należy użyć polecenia cmdlet [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . Poniższy przykład usuwa `gateway` aplikację z określonej usługi i grupy zasobów.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Usuwanie usługi w chmurze Azure wiosny

Aby usunąć usługę Azure wiosny w chmurze, należy użyć polecenia cmdlet [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . Poniższy przykład usuwa określoną usługę w chmurze Azure wiosną.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Następne kroki

[Azure sprężynowe zasoby deweloperów chmury](spring-cloud-resources.md).

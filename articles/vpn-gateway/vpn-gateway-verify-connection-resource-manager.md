---
title: 'VPN Gateway platformy Azure: weryfikowanie połączenia bramy'
description: W tym artykule pokazano, jak zweryfikować połączenie VPN Gateway sieci wirtualnej.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 38015d62077350e0e6f6ed8e7a43f748db58d213
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086990"
---
# <a name="verify-a-vpn-gateway-connection"></a>Weryfikowanie połączenia VPN Gateway

W tym artykule opisano sposób weryfikacji połączenia bramy sieci VPN dla modeli wdrażania klasycznego i Menedżer zasobów.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Aby sprawdzić połączenie bramy sieci VPN dla Menedżer zasobów modelu wdrażania przy użyciu programu PowerShell, zainstaluj najnowszą wersję [Azure Resource Manager poleceń cmdlet programu PowerShell](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby sprawdzić połączenie bramy sieci VPN dla modelu wdrażania Menedżer zasobów za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję [poleceń interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/install-azure-cli) (2,0 lub nowsze).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portal Azure (klasyczny)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasyczny)

Aby sprawdzić połączenie bramy sieci VPN dla klasycznego modelu wdrażania przy użyciu programu PowerShell, należy zainstalować najnowsze wersje poleceń cmdlet Azure PowerShell. Pamiętaj, aby pobrać i zainstalować moduł [zarządzania usługami](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) . Użyj "Add-AzureAccount", aby zalogować się do klasycznego modelu wdrażania.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Następne kroki

* Do sieci wirtualnych można dodać maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).

---
title: Obsługa Active FTP w zaporze platformy Azure
description: Domyślnie usługa Active FTP jest wyłączona w zaporze platformy Azure. Można ją włączyć za pomocą programu PowerShell, interfejsu wiersza polecenia i szablonu ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 47bced3c3c4f01212af6919d51adc5dfc2e6e1b5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200073"
---
# <a name="azure-firewall-active-ftp-support"></a>Obsługa Active FTP w zaporze platformy Azure

Przy użyciu usługi Active FTP serwer FTP inicjuje połączenie danych z określonym portem danych klienta FTP. Zapory w sieci po stronie klienta zwykle blokują zewnętrzne żądanie połączenia do wewnętrznego portu klienta. Aby uzyskać więcej informacji, zobacz temat [Active FTP a pasywne FTP, ostateczne wyjaśnienie](https://slacksite.com/other/ftp.html).

Domyślnie aktywna obsługa usługi FTP jest wyłączona w zaporze platformy Azure w celu ochrony przed atakami za pośrednictwem protokołu FTP przy użyciu `PORT` polecenia FTP. Można jednak włączyć funkcję Active FTP podczas wdrażania przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Azure ARM.

> [!NOTE]
> Obecnie aktywne usługi FTP są obsługiwane tylko dla zapór wdrożonych w sieci wirtualnej. Obsługa wirtualnej sieci WAN zostanie dodana później.

## <a name="azure-powershell"></a>Azure PowerShell

Aby wdrożyć przy użyciu Azure PowerShell, użyj `AllowActiveFTP` parametru. Aby uzyskać więcej informacji, zobacz [Tworzenie zapory z opcją Zezwalaj na aktywne FTP](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `--allow-active-ftp` parametru. Aby uzyskać więcej informacji, zobacz [AZ Network firewall Create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Szablon usługi Azure Resource Manager (ARM)

Aby wdrożyć przy użyciu szablonu ARM, użyj `AdditionalProperties` pola:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Aby uzyskać więcej informacji, zobacz [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć zaporę platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure PowerShell](deploy-ps.md).
---
title: Obsługa Active FTP w zaporze platformy Azure
description: Domyślnie usługa Active FTP jest wyłączona w zaporze platformy Azure. Można ją włączyć za pomocą programu PowerShell, interfejsu wiersza polecenia i szablonu ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: e08be08f2d898b017bb34ed38c9c3a69ee0582fa
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312981"
---
# <a name="azure-firewall-active-ftp-support"></a>Obsługa Active FTP w zaporze platformy Azure

Przy użyciu usługi Active FTP serwer FTP inicjuje połączenie danych z określonym portem danych klienta FTP. Zapory w sieci po stronie klienta zwykle blokują zewnętrzne żądanie połączenia do wewnętrznego portu klienta. Aby uzyskać więcej informacji, zobacz temat [Active FTP a pasywne FTP, ostateczne wyjaśnienie](https://slacksite.com/other/ftp.html).

Domyślnie aktywna obsługa usługi FTP jest wyłączona w zaporze platformy Azure w celu ochrony przed atakami za pośrednictwem protokołu FTP przy użyciu `PORT` polecenia FTP. Można jednak włączyć funkcję Active FTP podczas wdrażania przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Azure ARM.

Aby obsługiwać protokół FTP w trybie aktywnym, należy otworzyć następujące porty TCP:

- Port 21 serwera FTP z dowolnego miejsca (klient inicjuje połączenie)
- Port 21 serwera FTP do portów > 1023 (serwer reaguje na port kontroli klienta)
- Port 20 serwera FTP do portów > 1023 na klientach (serwer inicjuje połączenie danych z portem danych klienta)
- Port 20 serwera FTP z portów > 1023 na klientach (klient wysyła potwierdzenia do portu danych serwera)

## <a name="azure-powershell"></a>Azure PowerShell

Aby wdrożyć przy użyciu Azure PowerShell, użyj `AllowActiveFTP` parametru. Aby uzyskać więcej informacji, zobacz [Tworzenie zapory z opcją Zezwalaj na aktywne FTP](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

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
---
title: Azure Firewall obsługi protokołu FTP active
description: Domyślnie aktywny protokół FTP jest wyłączony na Azure Firewall. Można ją włączyć przy użyciu programu PowerShell, interfejsu wiersza polecenia i szablonu usługi ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864473"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall obsługi protokołu FTP active

W przypadku aktywnego protokołu FTP serwer FTP inicjuje połączenie danych z wyznaczonym portem danych klienta FTP. Zapory w sieci po stronie klienta zwykle blokują zewnętrzne żądanie połączenia do wewnętrznego portu klienta. Aby uzyskać więcej informacji, zobacz Active FTP vs. Passive FTP (Aktywny ftp a pasywny [protokół FTP), a Definitive Explanation (Ostateczne wyjaśnienie).](https://slacksite.com/other/ftp.html)

Domyślnie aktywna obsługa protokołu FTP jest wyłączona na Azure Firewall, aby chronić się przed atakami z użyciem odbicia FTP przy użyciu polecenia `PORT` FTP. Jednak usługę Active FTP można włączyć podczas wdrażania przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu usługi Azure ARM.

Aby obsługiwać protokół FTP w trybie aktywnym, należy otworzyć następujące porty TCP:

- Port 21 serwera FTP z dowolnego miejsca (klient inicjuje połączenie)
- Port 21 serwera FTP do portów > 1023 (serwer odpowiada na port sterowania klienta)
- Port 20 serwera FTP do portów > 1023 na klientach (serwer inicjuje połączenie danych z portem danych klienta)
- Port 20 serwera FTP z portów > 1023 na klientach (klient wysyła AKS do portu danych serwera)

## <a name="azure-powershell"></a>Azure PowerShell

Aby wdrożyć przy Azure PowerShell, użyj `AllowActiveFTP` parametru . Aby uzyskać więcej informacji, zobacz Create a Firewall with Allow Active FTP (Tworzenie zapory [za pomocą zezwalania na aktywny protokół FTP).](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `--allow-active-ftp` parametru . Aby uzyskać więcej informacji, zobacz [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Szablon usługi Azure Resource Manager (ARM)

Aby wdrożyć przy użyciu szablonu usługi ARM, użyj `AdditionalProperties` pola :

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Aby uzyskać więcej informacji, [zobacz Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć Azure Firewall, zobacz [Deploy and configure Azure Firewall using Azure PowerShell](deploy-ps.md)(Wdrażanie i konfigurowanie Azure Firewall przy użyciu Azure PowerShell ).
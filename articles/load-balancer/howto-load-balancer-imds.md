---
title: Pobieranie metadanych modułu równoważenia obciążenia przy użyciu usługi Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Rozpocznij uczenie się, jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure Instance Metadata Service.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739784"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Pobieranie metadanych modułu równoważenia obciążenia przy użyciu usługi Azure Instance Metadata Service (IMDS)

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj [najnowszej wersji interfejsu API](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) dla żądania.

## <a name="sample-request-and-response"></a>Przykładowe żądanie i odpowiedź
> [!IMPORTANT]
> Ten przykład pomija serwery proxy. Podczas wykonywania zapytania dotyczącego IMDS **należy** ominąć serwery proxy. Aby uzyskać więcej informacji, zobacz [proxy](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> Parametr-noproxy został wprowadzony w programie PowerShell 6,0. Jeśli używasz starszej wersji programu PowerShell, Usuń parametr-noproxy w treści żądania i upewnij się, że nie używasz serwera proxy podczas pobierania informacji o IMDS. Dowiedz się więcej [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Przykładowa odpowiedź

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Następne kroki
[Typowe kody błędów i kroki rozwiązywania problemów](troubleshoot-load-balancer-imds.md)

Dowiedz się więcej o [usłudze Azure instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Pobierz wszystkie metadane dla wystąpienia](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Wdrażanie usługi równoważenia obciążenia w warstwie Standardowa](quickstart-load-balancer-standard-public-portal.md)


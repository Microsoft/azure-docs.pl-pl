---
title: Delegowanie poddomeny — Azure PowerShell-Azure DNS
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij delegowanie poddomeny Azure DNS przy użyciu Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712551"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegowanie poddomeny Azure DNS przy użyciu Azure PowerShell

Azure PowerShell można użyć do delegowania poddomeny DNS. Na przykład jeśli jesteś członkiem domeny contoso.com, możesz delegować poddomenę o nazwie *Inżynieria* na inną, osobną strefę, którą można administrować niezależnie od strefy contoso.com.

Jeśli wolisz, możesz delegować poddomenę przy użyciu witryny [Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com jest używany jako przykład w tym artykule. Zastąp contoso.com swoją nazwą domeny.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować poddomenę Azure DNS, należy najpierw delegować domenę publiczną do Azure DNS. Zobacz [delegowanie domeny do Azure DNS,](./dns-delegate-domain-azure-dns.md) Aby uzyskać instrukcje dotyczące konfigurowania serwerów nazw na potrzeby delegowania. Po przeprowadzeniu delegowania domeny do strefy Azure DNS można delegować poddomenę.

## <a name="create-a-zone-for-your-subdomain"></a>Tworzenie strefy dla domeny podrzędnej

Najpierw Utwórz strefę dla poddomeny **inżynierów** .

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Zanotuj serwery nazw

Następnie należy zwrócić uwagę na cztery serwery nazw dla poddomeny inżynierów.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Tworzenie rekordu testowego

Utwórz rekord **A** w strefie Inżynieria, który ma być używany do testowania.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Tworzenie rekordu NS

Następnie Utwórz rekord serwera nazw (NS) dla strefy **inżynierów** w strefie contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testowanie delegowania

Aby przetestować delegowanie, użyj polecenia nslookup.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz `nslookup www.engineering.contoso.com.`
3. Odpowiedź Nieautorytatywna powinna być wyświetlana z adresem **10.10.10.10**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny serwer DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).
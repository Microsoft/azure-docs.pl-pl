---
title: Skonfiguruj strefy wyszukiwania wstecznego na potrzeby kontroli baneru SMTP
titlesuffix: Azure Virtual Network
description: Opisuje sposób konfigurowania stref wyszukiwania wstecznego na potrzeby sprawdzania transparentu SMTP na platformie Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135067"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Skonfiguruj strefy wyszukiwania wstecznego na potrzeby kontroli baneru SMTP

W tym artykule opisano, jak używać strefy odwrotnej w Azure DNS i utworzyć rekord odwrotnej usługi DNS (PTR) dla sprawdzania transparentu SMTP.

## <a name="symptom"></a>Objaw

W przypadku hostowania serwera SMTP w Microsoft Azure może zostać wyświetlony następujący komunikat o błędzie podczas wysyłania lub odbierania komunikatu z serwerów poczty zdalnej:

**554: brak rekordu PTR**

## <a name="solution"></a>Rozwiązanie

W przypadku wirtualnego adresu IP na platformie Azure rekordy odwrotne są tworzone w strefach domen należących do firmy Microsoft, a nie w strefach domen niestandardowych.

Aby skonfigurować rekordy PTR w strefach należących do firmy Microsoft, użyj właściwości-ReverseFqdn zasobu PublicIpAddress. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](../dns/dns-reverse-dns-for-azure-services.md).

Podczas konfigurowania rekordów PTR upewnij się, że adres IP i odwrotna nazwa FQDN są własnością subskrypcji. Jeśli spróbujesz ustawić odwrotną nazwę FQDN, która nie należy do subskrypcji, zostanie wyświetlony następujący komunikat o błędzie:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Jeśli ręcznie zmienisz transparent SMTP w taki sposób, aby był zgodny z domyślną odwrotną nazwą FQDN, zdalny serwer poczty nadal może się nie powieść, ponieważ może oczekiwać, że host transparentu SMTP będzie pasował do rekordu MX dla domeny.

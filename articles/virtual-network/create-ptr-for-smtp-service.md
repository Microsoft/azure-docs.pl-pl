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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77201700"
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

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn dopasowuje nazwę FQDN dowolnego publicznego zasobu IP w ramach subskrypcji;
    2) ReverseFqdn jest rozpoznawana jako nazwa FQDN (za pomocą łańcucha rekordów CName) dowolnego publicznego zasobu IP w ramach subskrypcji;
    3) Jest on rozpoznawany jako adres IP (za pomocą łańcucha CName i rekordu) statycznego publicznego adresu IP w ramach subskrypcji.

Jeśli ręcznie zmienisz transparent SMTP w taki sposób, aby był zgodny z domyślną odwrotną nazwą FQDN, zdalny serwer poczty nadal może się nie powieść, ponieważ może oczekiwać, że host transparentu SMTP będzie pasował do rekordu MX dla domeny.

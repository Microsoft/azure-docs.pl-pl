---
title: Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight
description: Dowiedz się, jak skonfigurować szereg dodatkowych funkcji dla wirtualnego urządzenia sieciowego w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864710"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight

> [!Important]
> Poniższe informacje są wymagane **tylko** wtedy, gdy chcesz skonfigurować wirtualne urządzenie sieciowe (urządzenie WUS) inne niż Zapora platformy Azure.

Zapora platformy Azure jest automatycznie konfigurowana w taki sposób, aby zezwalała na ruch dla wielu typowych scenariuszy. Użycie innego sieciowego urządzenia wirtualnego wymaga skonfigurowania szeregu dodatkowych funkcji. Podczas konfigurowania wirtualnego urządzenia sieciowego należy wziąć pod uwagę następujące czynniki:

* Usługi obsługujące punkt końcowy usługi należy skonfigurować za pomocą punktów końcowych usługi.
* Zależności adresów IP są związane z ruchem innym niż HTTP/S (ruchem TCP i UDP).
* Punkty końcowe HTTP/HTTPS w nazwie FQDN można umieścić na urządzeniu urządzenie WUS.
* Symbole wieloznaczne protokołu HTTP/HTTPS są zależnościami, które mogą się różnić w zależności od liczby kwalifikatorów.
* Przypisz tabelę tras utworzoną w podsieci usługi HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Zależności obsługujące punkt końcowy usługi

| **Punktu końcowego** |
|---|
| Azure SQL |
| Azure Storage |
| Usługa Azure Active Directory |

### <a name="ip-address-dependencies"></a>Zależności adresów IP

| **Punktu końcowego** | **Szczegóły** |
|---|---|
| \*: 123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| Adresy IP opublikowane w [tym miejscu](hdinsight-management-ip-addresses.md) | Te adresy IP to Usługa HDInsight |
| AAD — prywatne adresy IP DS dla klastrów ESP |
| \*: 16800 dla aktywacji usługi KMS systemu Windows |
| \*12000 dla Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS w nazwie FQDN

> [!Important]
> Poniższa lista zawiera tylko kilka najważniejszych nazw FQDN. Aby skonfigurować urządzenie WUS [w tym pliku](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json), można uzyskać dodatkowe nazwy FQDN (głównie Magazyn Azure i Azure Service Bus).

| **Punktu końcowego**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Następne kroki

* [Ograniczanie ruchu wychodzącego za pomocą zapory](./hdinsight-restrict-outbound-traffic.md)
* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)

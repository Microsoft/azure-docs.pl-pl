---
title: Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight
description: Dowiedz się, jak skonfigurować szereg dodatkowych funkcji dla wirtualnego urządzenia sieciowego w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630937"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight

> [!Important]
> Poniższe informacje są wymagane **tylko** wtedy, gdy chcesz skonfigurować wirtualne urządzenie sieciowe (urządzenie WUS) inne niż Zapora platformy Azure.

Zapora platformy Azure jest automatycznie konfigurowana w taki sposób, aby zezwalała na ruch dla wielu typowych scenariuszy. Użycie innego sieciowego urządzenia wirtualnego wymaga skonfigurowania szeregu dodatkowych funkcji. Podczas konfigurowania wirtualnego urządzenia sieciowego należy wziąć pod uwagę następujące czynniki:

* Usługi obsługujące punkt końcowy usługi można skonfigurować za pomocą punktów końcowych usługi, które w wyniku pomijania urządzenie WUS zazwyczaj dotyczą kosztów lub problemów dotyczących wydajności.
* Zależności adresów IP są związane z ruchem innym niż HTTP/S (ruchem TCP i UDP).
* Punkty końcowe HTTP/HTTPS FQDN mogą być zatwierdzone na urządzeniu urządzenie WUS.
* Przypisz tabelę tras utworzoną w podsieci usługi HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Zależności obsługujące punkt końcowy usługi

Opcjonalnie można włączyć co najmniej jeden z następujących punktów końcowych usługi, co spowoduje ominięcie urządzenie WUS. Ta opcja może być przydatna w przypadku dużych ilości transferów danych do oszczędności przy kosztach, a także dla optymalizacji wydajności. 

| **Punkt końcowy** |
|---|
| Azure SQL |
| Azure Storage |
| Usługa Azure Active Directory |

### <a name="ip-address-dependencies"></a>Zależności adresów IP

| **Punkt końcowy** | **Szczegóły** |
|---|---|
| Adresy IP opublikowane w [tym miejscu](hdinsight-management-ip-addresses.md) | Te adresy IP są przeznaczone do celów kontroli usługi HDInsight i powinny być zawarte w UDR, aby uniknąć asymetrycznego routingu |
| AAD — prywatne adresy IP usługi DS | Wymagany tylko w przypadku klastrów ESP|


### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS w nazwie FQDN

> [!Important]
> Poniższa lista zawiera tylko kilka nazw FQDN, które mogą być niezbędne do stosowania poprawek systemu operacyjnego i zabezpieczeń lub ważności certyfikatów po utworzeniu klastra i w okresie istnienia operacji klastra. Aby skonfigurować urządzenie WUS [w tym pliku](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json), można uzyskać listę nazw FQDN (głównie usługi Azure Storage i Azure Service Bus). Te zależności są używane przez dostawcę zasobów usługi HDInsight (RP) do pomyślnego tworzenia i monitorowania klastrów/zarządzania nimi. Obejmują one dzienniki telemetrii/diagnostyki, metadane aprowizacji, konfiguracje związane z klastrem, skrypty, szablony ARM itd. Lista zależności nazw FQDN może ulec zmianie w przypadku zwalniania przyszłych aktualizacji HDIngisht.

| **Punkt końcowy**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>Następne kroki

* [Ograniczanie ruchu wychodzącego za pomocą zapory](./hdinsight-restrict-outbound-traffic.md)
* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)

---
title: Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight
description: Dowiedz się, jak skonfigurować szereg dodatkowych funkcji dla wirtualnego urządzenia sieciowego w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943973"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurowanie sieciowego urządzenia wirtualnego w usłudze Azure HDInsight

> [!Important]
> Poniższe informacje są wymagane **tylko** wtedy, gdy chcesz skonfigurować wirtualne urządzenie sieciowe (urządzenie WUS) inne niż [Zapora platformy Azure](./hdinsight-restrict-outbound-traffic.md).

Tag nazwy FQDN zapory platformy Azure jest automatycznie konfigurowany, aby zezwalać na ruch dla wielu wspólnych ważnych nazw FQDN. Użycie innego sieciowego urządzenia wirtualnego wymaga skonfigurowania szeregu dodatkowych funkcji. Podczas konfigurowania wirtualnego urządzenia sieciowego należy wziąć pod uwagę następujące czynniki:

* Usługi obsługujące punkt końcowy usługi można skonfigurować za pomocą punktów końcowych usługi, które w wyniku pomijania urządzenie WUS zazwyczaj dotyczą kosztów lub problemów dotyczących wydajności.
* Jeśli ResourceProviderConnection jest ustawiony na *wychodzące*, można użyć prywatnych punktów końcowych dla magazynu metadanych i serwerów SQL dla magazynu. nie ma potrzeby dodawania ich do urządzenie WUS.
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
| Adresy IP opublikowane w [tym miejscu](hdinsight-management-ip-addresses.md) | Te adresy IP są przeznaczone dla dostawcy zasobów usługi HDInsight i powinny być zawarte w UDR, aby uniknąć przesyłania asymetrycznego. Ta reguła jest wymagana tylko wtedy, gdy ResourceProviderConnection jest ustawiona na wartość *przychodzące*. Jeśli ResourceProviderConnection jest ustawiony na *wychodzące* , te adresy IP nie są konieczne w UDR.  |
| AAD — prywatne adresy IP usługi DS | Wymagany tylko w przypadku klastrów ESP, jeśli sieci wirtualnych nie są połączone za pomocą komunikacji równorzędnej.|


### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS w nazwie FQDN

Aby skonfigurować urządzenie WUS [w tym repozytorium](https://github.com/Azure-Samples/hdinsight-fqdn-lists/), można uzyskać listę nazw FQDN (w większości usługi Azure Storage i Azure Service Bus). W przypadku listy regionalnej zobacz [tutaj](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Te zależności są używane przez dostawcę zasobów usługi HDInsight (RP) do pomyślnego tworzenia i monitorowania klastrów/zarządzania nimi. Obejmują one dzienniki telemetrii/diagnostyki, metadane aprowizacji, konfiguracje związane z klastrem, skrypty itp. Ta lista zależności nazw FQDN może ulec zmianie w przypadku zwalniania przyszłych aktualizacji usługi HDInsight.

Poniższa lista zawiera tylko kilka nazw FQDN, które mogą być używane do stosowania poprawek systemu operacyjnego i zabezpieczeń lub walidacji certyfikatów *po* utworzeniu klastra i w okresie istnienia operacji klastra:

| **Nazwy FQDN zależności środowiska uruchomieniowego**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Następne kroki

* [Ograniczanie ruchu wychodzącego za pomocą zapory](./hdinsight-restrict-outbound-traffic.md)
* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)

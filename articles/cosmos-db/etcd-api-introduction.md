---
title: Wprowadzenie do interfejsu API Azure Cosmos DB etcd
description: Ten artykuł zawiera omówienie i kluczowe zalety interfejsu API etcd w programie Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498594"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Wprowadzenie do interfejsu API Azure Cosmos DB etcd (wersja zapoznawcza)

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Oferuje ona dystrybucję globalną gotowe, elastyczne skalowanie przepływności i magazynu, milisekundy o pojedynczej liczbie opóźnień w 99 percentylu i gwarantowaną wysoką dostępność, a wszystko to w ramach umowy SLA wiodącej w branży.

[Etcd](https://github.com/etcd-io/etcd) to dystrybuowany Magazyn kluczy/wartości. W [Kubernetes](https://kubernetes.io/), etcd jest używany do przechowywania stanu i konfiguracji klastrów Kubernetes. Zapewnienie dostępności, niezawodności i wydajności etcd ma kluczowe znaczenie dla ogólnej kondycji klastra, skalowalności, dostępności elastyczności i wydajności klastra Kubernetes. 

Interfejs API etcd w Azure Cosmos DB umożliwia korzystanie z Azure Cosmos DB jako magazynu zaplecza dla [usługi Azure Kubernetes](../aks/index.yml). Interfejs API etcd w Azure Cosmos DB jest obecnie w wersji zapoznawczej. Azure Cosmos DB implementuje protokół sieci szkieletowej etcd. Dzięki interfejsowi API etcd w Azure Cosmos DB deweloperzy będą automatycznie otrzymywać wysoce niezawodne, [dostępne](high-availability.md), [globalnie dystrybuowane](distribute-data-globally.md) Kubernetes. Ten interfejs API umożliwia deweloperom skalowanie Kubernetesego zarządzania stanem w w pełni zarządzanej natywnej usłudze PaaS w chmurze. 

> [!NOTE]
> W przeciwieństwie do innych interfejsów API w Azure Cosmos DB nie można zainicjować obsługi konta interfejsu API etcd za pomocą Azure Portal, interfejsu wiersza polecenia lub zestawów SDK. Konto interfejsu API etcd można zainicjować, wdrażając tylko szablon Menedżer zasobów. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [jak udostępnić usługę Azure Kubernetes w artykule Azure Cosmos DB](bootstrap-kubernetes-cluster.md) . Interfejs API usługi Azure Cosmos DB etcd jest obecnie w ograniczonej wersji zapoznawczej. Możesz [utworzyć konto w wersji zapoznawczej](https://aka.ms/cosmosetcdapi-signup), wypełniając formularz rejestracji.

## <a name="wire-level-compatibility"></a>Zgodność poziomu sieci

Azure Cosmos DB implementuje protokół komunikacyjny etcd w wersji 3 i umożliwia serwerom interfejsu API [węzła głównego](https://kubernetes.io/docs/concepts/overview/components/) użycie Azure Cosmos DB podobnie jak w przypadku lokalnego zainstalowanego środowiska etcd. Interfejs API etcd obsługuje wzajemne uwierzytelnianie TLS. 

Na poniższym diagramie przedstawiono składniki klastra Kubernetes. W wzorcu klastra serwer interfejsu API używa Azure Cosmos DB interfejsu API etcd zamiast lokalnie zainstalowanych etcd. 

![Azure Cosmos DB implementowania protokołu telekomunikacyjnych etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="no-etcd-operations-management"></a>Brak etcd Operations Management

Jako w pełni zarządzana usługa w chmurze, Azure Cosmos DB eliminuje konieczność konfigurowania etcd i zarządzania nimi przez deweloperów Kubernetes. Interfejs API etcd w Azure Cosmos DB jest skalowalny, wysoce dostępny, odporny na uszkodzenia i oferuje wysoką wydajność. Obciążenie związane z konfigurowaniem replikacji w wielu węzłach, przeprowadzania aktualizacji stopniowych, poprawek zabezpieczeń i monitorowania kondycji etcd jest obsługiwane przez Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globalna dystrybucja & wysoka dostępność 

Za pomocą interfejsu API etcd, Azure Cosmos DB gwarantuje dostępność na 99,99% dla operacji odczytu i zapisu danych w jednym regionie i o dostępności do 99,999% w wielu regionach. 

### <a name="elastic-scalability"></a>Elastyczna skalowalność

Azure Cosmos DB oferuje elastyczną skalowalność żądań odczytu i zapisu w różnych regionach.
Gdy klaster Kubernetes rośnie, konto interfejsu API etcd w Azure Cosmos DB elastycznie skaluje się bez przestojów. Przechowywanie danych etcd w Azure Cosmos DB, a nie w węzłach głównych Kubernetes umożliwia również bardziej elastyczne skalowanie węzłów głównych. 

### <a name="security--enterprise-readiness"></a>Zabezpieczenia & gotowość przedsiębiorstwa

Gdy etcd dane są przechowywane w Azure Cosmos DB, deweloperzy Kubernetes automatycznie pobierają [wbudowane szyfrowanie w spoczynku](database-encryption-at-rest.md), [certyfikatach i zgodności](compliance.md), a [funkcje tworzenia kopii zapasowych i przywracania](online-backup-and-restore.md) obsługiwane przez Azure Cosmos DB. 

## <a name="next-steps"></a>Następne kroki

* [Jak korzystać z usługi Azure Kubernetes z usługą Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Najważniejsze zalety Azure Cosmos DB](introduction.md)
* [Przewodnik Szybki Start dotyczący aparatu AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)
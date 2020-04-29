---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń i podgrup.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906222"
---
# <a name="sfctl"></a>sfctl
Polecenia służące do zarządzania klastrami i jednostkami Service Fabric. Ta wersja jest zgodna z środowiskiem uruchomieniowym Service Fabric 7,0.

Polecenia są zgodne ze wzorcem rzeczownik-czasownik. Aby uzyskać więcej informacji, zobacz podgrupy.

## <a name="subgroups"></a>Podgrupy
|Podgrupa|Opis|
| --- | --- |
| [aplikacja](service-fabric-sfctl-application.md) | Twórz i usuwaj aplikacje i typy aplikacji oraz zarządzaj nimi. |
| [Chaos](service-fabric-sfctl-chaos.md) | Uruchamianie, zatrzymywanie i raportowanie w usłudze testowej chaos. |
| [hosta](service-fabric-sfctl-cluster.md) | Wybieranie klastrów Service Fabric i zarządzanie nimi. |
| [Compose](service-fabric-sfctl-compose.md) | Twórz i usuwaj Docker Compose aplikacje oraz zarządzaj nimi. |
| [wbudowane](service-fabric-sfctl-container.md) | Uruchom polecenia związane z kontenerem w węźle klastra. |
| [wydarzeniach](service-fabric-sfctl-events.md) | Pobierz zdarzenia ze sklepu Events (Jeśli usługa EventStore jest już zainstalowana). |
| [is](service-fabric-sfctl-is.md) | Wykonywanie zapytań i wysyłanie poleceń do usługi infrastruktury. |
| [siatka](service-fabric-sfctl-mesh.md) | Usuwanie aplikacji siatki Service Fabric i zarządzanie nimi. |
| [większości](service-fabric-sfctl-node.md) | Zarządzaj węzłami, które tworzą klaster. |
| [podzielić](service-fabric-sfctl-partition.md) | Wykonywanie zapytań dotyczących partycji i zarządzanie nimi dla dowolnej usługi. |
| [wartość](service-fabric-sfctl-property.md) | Właściwości magazynu i zapytania w obszarze nazwy Service Fabric. |
| [niesiona](service-fabric-sfctl-replica.md) | Zarządzanie replikami należącymi do partycji usług. |
| [rpm](service-fabric-sfctl-rpm.md) | Wykonywanie zapytań i wysyłanie poleceń do usługi Repair Manager. |
| [sa — klaster](service-fabric-sfctl-sa-cluster.md) | Zarządzaj autonomicznymi klastrami Service Fabric. |
| [usługi](service-fabric-sfctl-service.md) | Tworzenie, usuwanie usług, typów usług i pakietów usług oraz zarządzanie nimi. |
| [ustawienia](service-fabric-sfctl-settings.md) | Skonfiguruj ustawienia lokalne dla tego wystąpienia sfctl. |
| [sklep](service-fabric-sfctl-store.md) | Wykonaj podstawowe operacje na poziomie plików w magazynie obrazów klastra. |

## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
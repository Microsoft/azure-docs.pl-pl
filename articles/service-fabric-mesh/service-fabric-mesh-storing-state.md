---
title: Opcje magazynu stanu na platformie Azure Service Fabric siatkę
description: Dowiedz się więcej na temat niezawodnego przechowywania stanu w aplikacjach Service Fabric siatki działających na platformie Azure Service Fabric siatkę.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259099"
---
# <a name="state-management-with-service-fabric"></a>Zarządzanie stanem za pomocą Service Fabric

Service Fabric obsługuje wiele różnych opcji magazynu Stanów. Omówienie pojęć dotyczących wzorców zarządzania stanów i Service Fabric można znaleźć w temacie [Service Fabric koncepcje: stan](/azure/service-fabric/service-fabric-concepts-state). Wszystkie te same koncepcje dotyczą tego, czy usługi są uruchamiane w sieci Service Fabric lub poza nią. 

Za pomocą Service Fabric siatki możesz łatwo wdrożyć nową aplikację i połączyć ją z istniejącym magazynem danych hostowanym na platformie Azure. Oprócz korzystania ze zdalnej bazy danych istnieje kilka opcji przechowywania danych, zależnie od tego, czy usługa żąda magazynu lokalnego czy zdalnego. 

## <a name="volumes"></a>Woluminy

Kontenery często używają dysków tymczasowych. Dyski tymczasowe są nieulotne, jednak dzięki temu otrzymujesz nowy dysk tymczasowy i utracisz informacje w przypadku awarii kontenera. Trudno jest również udostępniać informacje na dyskach tymczasowych innym kontenerom. Woluminy są katalogami, które są instalowane w ramach wystąpień kontenera, których można użyć do utrwalania stanu. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie i zapisywanie plików przy użyciu standardowych interfejsów API plików we/wy dysku. Zasób woluminu zawiera opis sposobu instalowania katalogu i magazynu zapasowego do użycia. Możesz wybrać magazyn plików platformy Azure lub Service Fabric dysk woluminu do przechowywania danych.

![Woluminy][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric niezawodny wolumin

Service Fabric niezawodny wolumin to sterownik woluminu platformy Docker służący do instalowania woluminu lokalnego w kontenerze. Operacje odczytu i zapisu są operacjami lokalnymi i szybko. Dane są replikowane do węzłów pomocniczych, co zapewnia wysoką dostępność. Tryb failover jest również szybki. Gdy kontener ulega awarii, przejdzie do trybu failover w węźle, który ma już kopię danych. Aby zapoznać się z przykładem, zobacz [jak wdrożyć aplikację przy użyciu Service Fabric niezawodnego woluminu](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Wolumin Azure Files

Wolumin Azure Files to sterownik woluminu platformy Docker używany do instalowania udziału Azure Files w kontenerze. Azure Files Storage korzysta z magazynu sieciowego, więc operacje odczytu i zapisu odbywają się za pośrednictwem sieci. W porównaniu do Service Fabric niezawodnego woluminu Azure Files magazyn jest mniej wydajny, ale udostępnia tańszą i w pełni niezawodny opcję danych. Aby zapoznać się z przykładem, zobacz [jak wdrożyć aplikację przy użyciu woluminu Azure Files](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Następne kroki

Informacje o modelu aplikacji można znaleźć w temacie [Service Fabric Resources](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png

---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904274"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Limity przydziału i plany usług dla chmury wiosennej platformy Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji.   Chmura Wiosnowa platformy Azure oferuje dwie warstwy cenowe: podstawowa i standardowa. Będziemy szczegółowo podawać limity dla obu warstw w tym artykule.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Warstwy i limity usług w chmurze ze sprężyną na platformie Azure

| Zasób | Podstawowa | Standardowa (Standard)
------- | ------- | -------
Procesor wirtualny | 1 na wystąpienie usługi | 4 na wystąpienie usługi
Pamięć | 2 GB na wystąpienie usługi | 8 GB na wystąpienie usługi
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | 10 | 10
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 25 | 500
Trwałe woluminy | 1 GB/aplikacje dla aplikacji x 10 | 50 GB/aplikacje aplikacji x 10

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity można zwiększyć. Jeśli Instalator wymaga zwiększenia, [Utwórz żądanie pomocy technicznej](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

---
title: Plany usługi i przydziały dla chmury wiosennej platformy Azure
description: Dowiedz się więcej na temat przydziałów i planów usług dla chmury wiosennej platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095669"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Limity przydziału i plany usług dla chmury wiosennej platformy Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji.   Chmura Wiosnowa platformy Azure oferuje dwie warstwy cenowe: podstawowa i standardowa. Będziemy szczegółowo podawać limity dla obu warstw w tym artykule.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Warstwy i limity usług w chmurze ze sprężyną na platformie Azure

| Zasób | Zakres | Podstawowa | Standardowa (Standard)
------- | ------- | -------
Procesor wirtualny | na wystąpienie aplikacji | 1 | 4
Pamięć | na wystąpienie aplikacji | 2 GB | 8 GB
Wystąpienia usługi w chmurze ze sprężyną Azure | na region na subskrypcję | 10 | 10
Łączna liczba wystąpień aplikacji | dla wystąpienia usługi w chmurze wiosennej na platformie Azure | 25 | 500
Domeny niestandardowe | dla wystąpienia usługi w chmurze wiosennej na platformie Azure | 0 | 25 
Woluminy trwałe | dla wystąpienia usługi w chmurze wiosennej na platformie Azure | 1 GB/aplikacje dla aplikacji x 10 | 50 GB/aplikacje aplikacji x 10

> [!TIP]
> Stawki wymienione dla wszystkich wystąpień aplikacji na wystąpienie usługi mają zastosowanie w przypadku aplikacji/wdrożeń w stanie zatrzymanym. Usuń aplikacje/wdrożenia, które nie są używane.

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity można zwiększyć. Jeśli Instalator wymaga zwiększenia, [Utwórz żądanie pomocy technicznej](../azure-portal/supportability/how-to-create-azure-support-request.md).

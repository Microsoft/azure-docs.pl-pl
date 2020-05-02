---
title: Azure Event Grid warstwach Premium i Basic
description: W tym artykule opisano różnicę między warstwami Azure Event Grid Premium i Basic oraz kiedy używać poszczególnych
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79300718"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid warstwach Premium i Basic
Azure Event Grid ma dwie warstwy: **Premium** i **Basic**. Warstwa Podstawowa używa zużycia lub cennika płatność zgodnie z rzeczywistym użyciem. Zapewnia ona wszystkie podstawowe narzędzia do publikowania/podsub, których należy używać Event Grid dla modeli programowania opartych na zdarzeniach. Warstwa Premium obejmuje ten sam krok z funkcjami zabezpieczeń, które są przeznaczone dla przedsiębiorstw. Warstwa Premium jest wczesnej **wersji zapoznawczej** z wieloma funkcjami, które są nadal opracowywane.

## <a name="overview"></a>Omówienie
Wszystkie niestandardowe tematy i domeny w Event Grid należą do warstwy Podstawowa lub Premium. Począwszy od `2020-04-01-preview` wersji interfejsu API, można wybrać odpowiednią warstwę w ramach tworzenia tematu lub domeny. Wartość domyślna to warstwa podstawowa. Tematy i domeny utworzone przy użyciu starszych wersji interfejsu API są domyślne w warstwie Podstawowa. Aby zmienić warstwę cenową dla tematów i domen, zobacz [temat jak zaktualizować warstwę dla tematów i domen](update-tier.md).

## <a name="capabilities-and-features"></a>Możliwości i funkcje

W poniższej tabeli opisano różnice między warstwami:

|       &nbsp;                                           | Podstawowy           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Reguły zapory IP dla ruchu przychodzącego                          | Wersja zapoznawcza  | Wersja zapoznawcza |
| Tagi usług dla ruchu wychodzącego                                | Wersja zapoznawcza  | Wersja zapoznawcza |
| Integracja sieci wirtualnej z prywatnym punktem końcowym w przypadku ruchu przychodzącego          | Niedostępne   | Wersja zapoznawcza |

## <a name="availability"></a>Dostępność
Podczas początkowej wersji zapoznawczej tematy i domeny z integracją z prywatnym punktem końcowym są dostępne w następujących regionach:

- Wschodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA

## <a name="pricing-and-quotas"></a>Cennik i przydziały
Zobacz [cennik Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) , aby uzyskać szczegółowe informacje na temat cennika korzystania z warstwy Podstawowa. Cennik warstwy Premium nie został jeszcze ogłoszony i jest bezpłatny do momentu udostępnienia cennika.

Istniejące limity przydziału dotyczące tematu i domeny oraz przepływność dotyczą zasobów warstwy Premium i podstawowa do momentu anonsowania cen warstwy Premium.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Aby przeprowadzić uaktualnienie z warstwy Podstawowa do warstwy Premium, zobacz artykuł dotyczący [aktualizacji warstwy cenowej](update-tier.md) . 
- Można skonfigurować zaporę IP dla zasobu Event Grid, aby ograniczyć dostęp za pośrednictwem publicznej sieci Internet tylko z poziomu wybranych adresów IP lub zakresów adresów IP. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapory](configure-firewall.md).
- Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
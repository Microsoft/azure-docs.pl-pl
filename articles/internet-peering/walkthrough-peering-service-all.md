---
title: Przewodnik partnera usługi Peering Service
titleSuffix: Azure
description: Przewodnik partnera usługi Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592317"
---
# <a name="peering-service-partner-walkthrough"></a>Przewodnik partnera usługi Peering Service

W tej sekcji opisano kroki, które dostawca musi wykonać, aby włączyć bezpośrednią komunikację równorzędną dla usługi komunikacji równorzędnej.

## <a name="create-direct-peering-connection-for-peering-service"></a>Utwórz bezpośrednie połączenie komunikacji równorzędnej dla usługi Komunikacja równorzędna
Dostawcy usług mogą rozszerzyć ich zasięg geograficzny przez utworzenie nowej bezpośredniej komunikacji równorzędnej obsługującej usługę komunikacji równorzędnej. Aby to osiągnąć,
1. Zostań partnerem usługi komunikacji równorzędnej, jeśli nie została jeszcze
1. Postępuj zgodnie z instrukcjami, aby [utworzyć lub zmodyfikować bezpośrednią komunikację równorzędną przy użyciu portalu](howto-direct-portal.md). Upewnij się, że spełnia ono wymagania dotyczące wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Użyj starszego bezpośredniego połączenia komunikacji równorzędnej dla usługi Komunikacja równorzędna
Jeśli masz starszą bezpośrednią komunikację równorzędną, która ma być używana do obsługi komunikacji równorzędnej,
1. Jeśli nie jest, Zostań partnerem usługi komunikacji równorzędnej.
1. Postępuj zgodnie z instrukcjami, aby [skonwertować starszą bezpośrednią komunikację równorzędną do zasobu platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md). W razie potrzeby Zamów dodatkowe obwody w celu spełnienia wymagań dotyczących wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-peering-service-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zasadach komunikacji równorzędnej](https://peering.azurewebsites.net/peering).
* Aby dowiedzieć się więcej o krokach konfigurowania bezpośredniej komunikacji równorzędnej z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby poznać kroki konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).
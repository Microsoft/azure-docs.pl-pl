---
title: Konfigurowanie komunikacji równorzędnej z firmą Microsoft
titleSuffix: Azure
description: Omówienie komunikacji równorzędnej
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586870"
---
# <a name="internet-peering-overview"></a>Internet Komunikacja równorzędna — Omówienie

Komunikacja równorzędna to połączenie między siecią globalną firmy Microsoft (AS8075) a siecią na potrzeby wymiany ruchu internetowego z/do usług Microsoft Usługi online i Microsoft Azure. Operatorzy lub dostawcy usług mogą zażądać połączenia z firmą Microsoft w dowolnej lokalizacji brzegowej. Każde żądanie jest analizowane przez firmę Microsoft, aby upewnić się, że jest ono zgodne z naszymi zasadami komunikacji równorzędnej. Komunikację równorzędną z siecią firmy Microsoft można skonfigurować na dwa sposoby:

* **Bezpośrednia Komunikacja równorzędna:**

    Komunikacja równorzędna jest ustanawiana za pośrednictwem bezpośrednich połączeń fizycznych między siecią firmy Microsoft a siecią firmy Microsoft Edge. Sesje protokołu BGP są konfigurowane w ramach tych połączeń zgodnie z naszymi zasadami routingu i z zastosowaniem wstępnie negocjowanej umowy. Jest to również nazywane PNI.

* **Komunikacja równorzędna programu Exchange:**

    Odnosi się to do standardowych publicznych połączeń komunikacji równorzędnej w wymianach internetowych (IX). Połączenia fizyczne między siecią firmy Microsoft a siecią polegają na przełączeniu sieci szkieletowej obsługiwanej przez 9. Sesje BGP są konfigurowane przy użyciu przestrzeni adresów IP dostarczonej przez IX.

## <a name="benefits-of-peering-with-microsoft"></a>Zalety komunikacji równorzędnej z firmą Microsoft
* Obniż koszty związane z tranzytem, dostarczając ruch firmy Microsoft przy użyciu komunikacji równorzędnej z firmą Microsoft.
* Zwiększ wydajność dla klientów, zmniejszając liczbę przeskoków sieci i opóźnienia do sieci Microsoft Edge.
* Należy chronić ruch klientów przed awariami w sieci lub przez sieć dostawcy tranzytu za pomocą komunikacji równorzędnej z firmą Microsoft w nadmiarowych lokalizacjach.
* Poznaj metryki wydajności połączeń komunikacji równorzędnej i korzystaj ze szczegółowych informacji w celu rozwiązywania problemów z siecią.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Zalety korzystania z platformy Azure w celu skonfigurowania komunikacji równorzędnej

Możesz zażądać komunikacji równorzędnej z firmą Microsoft przy użyciu Azure PowerShell lub portalu. Komunikacja równorzędna skonfigurowana w ten sposób jest zarządzana jako zasób platformy Azure i zapewnia następujące korzyści:
* Uproszczone i automatyzuje kroki służące do konfigurowania komunikacji równorzędnej z firmą Microsoft i zarządzania nią.
* Szybki i łatwy sposób wyświetlania wszystkich połączeń równorzędnych w jednym miejscu i zarządzania nimi.
* Śledź dane o stanie i przepustowości dla wszystkich połączeń.
* Możesz użyć tej samej subskrypcji, aby uzyskać dostęp do usługi Azure Cloud Services.

Jeśli nawiązano już połączenie komunikacji równorzędnej z firmą Microsoft, są one nazywane **starszymi komunikacjami równorzędnymi**. Aby skorzystać z powyższych korzyści, możesz zarządzać takimi komunikacjami równorzędnymi jako zasobami platformy Azure. Aby przesłać nowe żądanie komunikacji równorzędnej lub dokonać konwersji starszej komunikacji równorzędnej na zasób platformy Azure, Skorzystaj z linków w sekcji **następne kroki** poniżej.

## <a name="peering-policy"></a>Zasady komunikacji równorzędnej
Firma Microsoft ma selektywne, ale ogólnie otwarte zasady komunikacji równorzędnej. Elementy równorzędne są wybierane na podstawie wydajności, możliwości i miejsca, w którym istnieje wzajemna korzyść i podlegają określonym wymaganiom technicznym, handlowym i prawnym. Aby uzyskać szczegółowe informacje, zobacz [zasady komunikacji równorzędnej](policy.md).

## <a name="faq"></a>Często zadawane pytania
W przypadku często zadawanych pytań dotyczących komunikacji równorzędnej zobacz [Internet Komunikacja równorzędna — często zadawane](faqs.md)pytania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o krokach konfigurowania bezpośredniej komunikacji równorzędnej z firmą Microsoft, postępuj zgodnie ze wskazówkami [bezpośredniego komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby poznać kroki konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md)
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
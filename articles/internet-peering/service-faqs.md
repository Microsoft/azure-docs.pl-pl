---
title: Usługa komunikacji równorzędnej — często zadawane pytania
titleSuffix: Azure
description: Usługa komunikacji równorzędnej — często zadawane pytania
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775474"
---
# <a name="peering-service---faqs"></a>Usługa komunikacji równorzędnej — często zadawane pytania

Poniżej znajdziesz poniższe informacje.

**Czy może być przewoźnikiem korzystającym z istniejącej bezpośredniej komunikacji równorzędnej z firmą Microsoft w celu obsługi komunikacji równorzędnej?**

Tak. Operator może wykorzystać swój istniejący PNI do obsługi komunikacji równorzędnej. Usługa komunikacji równorzędnej PNI wymaga różnorodności do obsługi wysokiej dostępności. Jeśli istniejący PNI ma już różnorodność, nie jest wymagana żadna Nowa infrastruktura. Jeśli istniejąca PNI wymaga różnorodności, można ją rozszerzyć.

**Czy przewoźnik może użyć nowej bezpośredniej komunikacji równorzędnej z firmą Microsoft w celu obsługi komunikacji równorzędnej?**

Tak, to jest również możliwe. Firma Microsoft będzie współpracować z przewoźnikiem w celu utworzenia nowej bezpośredniej komunikacji równorzędnej w celu obsługi komunikacji równorzędnej.  

**Dlaczego należy kierować komunikację równorzędną do obsługi komunikacji równorzędnej?**

Jednym z podstawowych sterowników usługi komunikacji równorzędnej jest zapewnienie łączności z firmą Microsoft Usługi online za pośrednictwem dobrze połączonego programu SP. PNI są zawsze w zakresie GB/s, a więc podstawowy blok konstrukcyjny do łączności między przewoźnikiem i firmą Microsoft.

**Jakie są wymagania różnorodności dotyczące bezpośredniej komunikacji równorzędnej do obsługi komunikacji równorzędnej?**

PNI musi obsługiwać lokalną nadmiarowość i nadmiarowość geograficzną. Lokalna — nadmiarowość jest definiowana jako dwa różne zestawy ścieżek w określonej witrynie komunikacji równorzędnej. Nadmiarowość geograficzna wymaga, aby operator miał dodatkową łączność z inną lokacją programu Microsoft Edge na wypadek awarii lokacji głównej. W przypadku przewoźnika czas trwania krótkich awarii może kierować ruchem przez lokację kopii zapasowej.

**Przewoźnik oferuje już umowę SLA i Internet klasy korporacyjnej, w jaki sposób ta oferta jest różna?**

Niektórzy przewoźnicy oferują umowę SLA i Internet klasy korporacyjnej w swojej części sieci. W usłudze Komunikacja równorzędna firma Microsoft oferuje ruch oferowany w ramach umowy SLA w ramach sieci firmy Microsoft. Po wybraniu usługi Komunikacja równorzędna klient uzyska kompleksową umowę SLA. Umowa SLA z witryny do przeglądarki Microsoft Edge w sieci usługodawcy internetowego może być objęta przez usługodawcę internetowego. Umowa SLA w sieci globalnej firmy Microsoft od przeglądarki Microsoft Edge do aplikacji użytkownicy końcowi jest teraz objęta firmą Microsoft.

**Jeśli dostawca usług jest już równorzędny z firmą Microsoft przy użyciu usługi PNI, jakiego rodzaju zmiany są wymagane do obsługi komunikacji równorzędnej?**

* Zmiany oprogramowania umożliwiające zidentyfikowanie użytkownika usługi równorzędnej i jego ruchu. Mogą wymagać zmian zasad routingu w celu wymiany ruchu użytkownika w najbliższej przeglądarce Microsoft Edge za pomocą połączenia usługi komunikacji równorzędnej.
* Upewnij się, że łączność ma lokalne ustawienia nadmiarowości i nadmiarowości geograficznej.

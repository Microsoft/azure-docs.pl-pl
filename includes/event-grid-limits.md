---
title: dołączanie pliku
description: dołączanie pliku
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83721503"
---
Poniższe limity mają zastosowanie do tematów systemu Azure Event Grid i tematów niestandardowych, a *nie* domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Wskaźnik publikowania dla tematu niestandardowego (ruch przychodzący) | 5 000 zdarzeń na sekundę na temat |
| Rozmiar zdarzenia | 1 MB. W przypadku operacji jest naliczana wartość 64 KB. Dlatego zdarzenia przekraczające 64 KB powodują naliczanie opłat za operacje, tak jakby były to wiele zdarzeń. Na przykład zdarzenie o 130 KB może pociągać za sobą operacje, tak jakby były to trzy oddzielne zdarzenia.  |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń na sekundę |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |
| Połączenia prywatnych punktów końcowych na temat lub domenę | 64 | 
| Reguły zapory IP dla tematu lub domeny | 16 | 
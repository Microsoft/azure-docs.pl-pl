---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 220759e8ed31c091887bd55f8d12aa4cc03a065f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590285"
---
Poniższe limity mają zastosowanie do tematów systemu Azure Event Grid i tematów niestandardowych, a *nie* domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Wskaźnik publikowania dla tematu niestandardowego (ruch przychodzący) | 5 000 zdarzeń na sekundę na temat |
| Publikuj żądania | 250 na sekundę |
| Rozmiar zdarzenia | 1 MB. W przypadku operacji jest naliczana wartość 64 KB. Dlatego zdarzenia przekraczające 64 KB powodują naliczanie opłat za operacje, tak jakby były to wiele zdarzeń. Na przykład zdarzenie o 130 KB może pociągać za sobą operacje, tak jakby były to trzy oddzielne zdarzenia.  |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń na sekundę |
| Publikuj żądania dla domeny zdarzeń | 250 na sekundę |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |
| Połączenia prywatnych punktów końcowych na temat lub domenę | 64 | 
| Reguły zapory IP dla tematu lub domeny | 16 | 
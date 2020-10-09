---
title: plik dołączania
description: plik dołączania
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859832"
---
Poniższe ograniczenia mają zastosowanie do **tematów** Azure Event Grid (tematów systemowych, niestandardowych i partnerskich). 

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Częstotliwość publikowania dla tematu niestandardowego lub partnera (ruch przychodzący) | 5 000 zdarzeń/s lub 1 MB/s (w zależności od tego, które jest najpierw spełnione) |
| Rozmiar zdarzenia | 1 MB  |
| Połączenia prywatnych punktów końcowych na temat  | 64 | 
| Reguły zapory adresów IP na temat | 16 | 

Do **domen**Azure Event Grid są stosowane następujące ograniczenia. 

| Zasób | Limit |
| --- | --- |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń/s lub 1 MB/s (w zależności od tego, które jest najpierw spełnione) |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |
| Połączenia prywatnych punktów końcowych na domenę | 64 | 
| Reguły zapory adresów IP na domenę | 16 | 



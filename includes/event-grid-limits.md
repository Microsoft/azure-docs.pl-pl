---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645386"
---
Poniższe ograniczenia mają zastosowanie do **tematów** Azure Event Grid (tematów systemowych, niestandardowych i partnerskich). 

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Częstotliwość publikowania dla tematu niestandardowego lub partnera (ruch przychodzący) | 5 000 zdarzeń/s lub 5 MB/s (w zależności od tego, które jest najpierw spełnione) |
| Rozmiar zdarzenia | 1 MB  |
| Połączenia prywatnych punktów końcowych na temat  | 64 | 
| Reguły zapory adresów IP na temat | 16 | 

Do **domen** Azure Event Grid są stosowane następujące ograniczenia. 

| Zasób | Limit |
| --- | --- |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń/s lub 5 MB/s (w zależności od tego, które jest najpierw spełnione) |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |
| Połączenia prywatnych punktów końcowych na domenę | 64 | 
| Reguły zapory adresów IP na domenę | 16 | 



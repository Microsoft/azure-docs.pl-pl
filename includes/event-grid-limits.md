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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745607"
---
Poniższe ograniczenia mają zastosowanie do **tematów** Azure Event Grid (tematów systemowych, niestandardowych i partnerskich). 

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Częstotliwość publikowania dla tematu niestandardowego lub partnera (ruch przychodzący) | 5 000 zdarzeń/s lub 1 MB/s (w zależności od tego, które jest najpierw spełnione)<br/>Nie dotyczy tematów systemowych. |
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



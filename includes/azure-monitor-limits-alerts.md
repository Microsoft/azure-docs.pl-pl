---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734068"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Alerty metryk (klasyczne) |100 aktywnych reguł alertów na subskrypcję. | Obsługa wywołań |
| Alerty dotyczące metryk |5 000 aktywnych reguł alertów na subskrypcję na platformie Azure, usługa Azure — Chiny i chmury Azure Government. W przypadku osiągnięcia tego limitu należy sprawdzić, czy można użyć [tego samego typu alertów o kilku zasobach](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 serii czasu metryk dla reguły alertu. | Skontaktuj się z pomocą techniczną. |
| Alerty dotyczące dziennika aktywności | 100 aktywnych reguł alertów na subskrypcję (nie można zwiększyć). | Analogicznie jak domyślne |
| Alerty dotyczące dzienników | 512 aktywnych reguł alertów na subskrypcję. 200 aktywnych reguł alertów dla zasobu. | Obsługa wywołań |
| Reguły alertów i długość opisu reguł akcji| Alerty przeszukiwania dzienników 4096 znaków<br/>Wszystkie inne 2048 znaków | Analogicznie jak domyślne |
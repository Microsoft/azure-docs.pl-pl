---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504705"
---
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. W przypadku wybrania wyższej jednostki SKU bramy bramie przydzielane jest więcej procesorów CPU oraz wyższa przepustowość sieci, dlatego może obsługiwać wyższą przepływność sieci do sieci wirtualnej. 

Bramy sieci wirtualnej ExpressRoute mogą używać następujących jednostek SKU:

|     | Brama sieci VPN i ExpressRoute współistnieją | FastPath | Maksymalna liczba połączeń obwodu |
| --- | --- | --- | --- |
| **Standardowa** | Tak | Nie | 4 |
| **Wysoka wydajność (HighPerformance)** | Tak | Nie | 4 |
| **UltraPerformance** | Tak | Tak | 16 |
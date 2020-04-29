---
title: Debugowanie nagłówków HTTP dla Azure CDN firmy Microsoft | Microsoft Docs
description: Nagłówki żądań pamięci podręcznej debugowania zawierają dodatkowe informacje dotyczące zasad pamięci podręcznej stosowanych do żądanego elementu zawartości. Te nagłówki są specyficzne dla Azure CDN od firmy Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260416"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debuguj nagłówek HTTP dla Azure CDN firmy Microsoft
Nagłówek `X-Cache`odpowiedzi na Debugowanie, zawiera szczegółowe informacje dotyczące warstwy stosu sieci CDN, z której dana zawartość została obsłużona. Ten nagłówek jest specyficzny dla Azure CDN firmy Microsoft.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek | Opis
-------|------------
X-cache: TCP_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z pamięci podręcznej usługi CDN Edge. 
X-cache: TCP_REMOTE_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z regionalnej pamięci podręcznej CDN (warstwa osłon pochodzenia)
X-cache: TCP_MISS | Ten nagłówek jest zwracany, gdy występuje chybień pamięci podręcznej, a zawartość jest obsługiwana z punktu początkowego. 



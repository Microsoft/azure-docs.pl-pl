---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183120"
---
W poniższej tabeli przedstawiono wymagania dotyczące bram sieci VPN PolicyBased i RouteBased. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym. W przypadku modelu klasycznego bramy sieci VPN PolicyBased są takie same jak bramy statyczne, a bramy oparte na trasach są takie same jak bramy dynamiczne.

|  | **PolicyBased podstawowe VPN Gateway** | **RouteBased podstawowe VPN Gateway** | **RouteBased VPN Gateway standardowa** | **VPN Gateway o wysokiej wydajności RouteBased** |
| --- | --- | --- | --- | --- |
| **Łączność typu „lokacja do lokacji” (Site-to-Site, S2S)** |Konfiguracja sieci VPN PolicyBased |Konfiguracja sieci VPN RouteBased |Konfiguracja sieci VPN RouteBased |Konfiguracja sieci VPN RouteBased |
| **Łączność typu „punkt do lokacji” (Point-to-Site, P2S**) |Nieobsługiwane |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |
| **Metoda uwierzytelniania** |Klucz wstępny |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |
| **Maksymalna liczba połączeń S2S** |1 |10 |10 |30 |
| **Maksymalna liczba połączeń P2S** |Nieobsługiwane |128 |128 |128 |
| **Aktywna obsługa routingu (BGP)** |Nieobsługiwane |Nieobsługiwane |Obsługiwane |Obsługiwane |

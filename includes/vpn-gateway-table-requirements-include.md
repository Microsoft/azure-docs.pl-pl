---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183118"
---
W poniższej tabeli przedstawiono wymagania dotyczące bram sieci VPN PolicyBased i RouteBased. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym. W przypadku modelu klasycznego bramy sieci VPN PolicyBased są takie same jak bramy statyczne, a bramy oparte na trasach są takie same jak bramy dynamiczne.

|  | **PolicyBased podstawowe VPN Gateway** | **RouteBased podstawowe VPN Gateway** | **RouteBased VPN Gateway standardowa** | **VPN Gateway o wysokiej wydajności RouteBased** |
| --- | --- | --- | --- | --- |
| **Łączność typu „lokacja do lokacji” (Site-to-Site, S2S)** |Konfiguracja sieci VPN PolicyBased |Konfiguracja sieci VPN RouteBased |Konfiguracja sieci VPN RouteBased |Konfiguracja sieci VPN RouteBased |
| **Łączność typu „punkt do lokacji” (Point-to-Site, P2S**) |Nieobsługiwane |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |
| **Metoda uwierzytelniania** |Klucz wstępny |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |
| **Maksymalna liczba połączeń S2S** |1 |10 |10 |30 |
| **Maksymalna liczba połączeń P2S** |Nieobsługiwane |128 |128 |128 |
| **Obsługa routingu Active (BGP)** (*) |Nieobsługiwane |Nieobsługiwane |Obsługiwane |Obsługiwane |

  (*) Protokół BGP nie jest obsługiwany dla klasycznego modelu wdrażania.

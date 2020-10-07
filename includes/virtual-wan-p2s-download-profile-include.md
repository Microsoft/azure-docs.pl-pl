---
title: plik dołączania
description: plik dołączania
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812751"
---
1. Na stronie wirtualnej sieci WAN wybierz pozycję **konfiguracje sieci VPN użytkownika**.
1. W górnej części strony wybierz pozycję **Pobierz konfigurację sieci VPN użytkownika**. Podczas pobierania konfiguracji poziomu sieci WAN jest dostępny wbudowany profil sieci VPN użytkownika oparty na Traffic Manager. Aby uzyskać więcej informacji na temat profilów globalnych lub profilu opartego na centrum, zobacz [Profile centrum](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). Scenariusze trybu failover są uproszczone przy użyciu profilu globalnego.

   Jeśli z jakiegoś powodu centrum jest niedostępne, wbudowane zarządzanie ruchem udostępnianym przez usługę zapewnia łączność (za pośrednictwem innego centrum) z zasobami platformy Azure dla użytkowników typu punkt-lokacja. Można zawsze pobrać konfigurację sieci VPN specyficzną dla centrum, przechodząc do centrum. W obszarze **Sieć VPN użytkownika (wskaż lokację)** Pobierz profil **sieci VPN użytkownika** koncentratora wirtualnego.
1. Po zakończeniu tworzenia pliku wybierz łącze, aby je pobrać.
1. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

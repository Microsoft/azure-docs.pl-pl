---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628951"
---
1. Na stronie wirtualnej sieci WAN wybierz pozycję **konfiguracje sieci VPN użytkownika**.
1. Na stronie **konfiguracje sieci VPN użytkownika** wybierz konfigurację, a następnie wybierz pozycję **Pobierz profil sieci VPN użytkownika wirtualnego sieci WAN**. Podczas pobierania konfiguracji poziomu sieci WAN jest dostępny wbudowany profil sieci VPN użytkownika oparty na Traffic Manager. Aby uzyskać więcej informacji na temat profilów globalnych lub profilu opartego na centrum, zobacz [Profile centrum](../articles/virtual-wan/global-hub-profile.md). Scenariusze trybu failover są uproszczone przy użyciu profilu globalnego.

   
   Jeśli z jakiegoś powodu centrum jest niedostępne, wbudowane zarządzanie ruchem udostępnianym przez usługę zapewnia łączność (za pośrednictwem innego centrum) z zasobami platformy Azure dla użytkowników typu punkt-lokacja. Można zawsze pobrać konfigurację sieci VPN specyficzną dla centrum, przechodząc do centrum. W obszarze **Sieć VPN użytkownika (wskaż lokację)** Pobierz profil **sieci VPN użytkownika** koncentratora wirtualnego.
1. Na stronie **Pobieranie profilu sieci VPN użytkownika wirtualnego sieci WAN** wybierz **Typ uwierzytelniania**, a następnie wybierz pozycję **Generuj i Pobierz profil**. Pakiet profilu zostanie wygenerowany i zostanie pobrany plik zip zawierający ustawienia konfiguracji.

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042590"
---
Tego samego pakietu konfiguracyjnego klienta sieci VPN można użyć na każdym komputerze klienckim z systemem Windows, o ile wersja jest zgodna z architekturą klienta. Listę obsługiwanych systemów operacyjnych klienta znajduje się w sekcji "punkt-lokacja [" VPN Gateway często zadawane pytania](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Użytkownik musi mieć uprawnienia administratora na komputerze klienckim z systemem Windows, z którego chcesz się połączyć.
>

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN systemu Windows na potrzeby uwierzytelniania certyfikatu:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”. 
1. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zobaczysz okno podręczne SmartScreen, kliknij pozycję **więcej informacji**, a następnie **Uruchom mimo wszystko**.
1. Na komputerze klienckim przejdź do obszaru **Ustawienia sieci** i kliknij pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.
1. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure.
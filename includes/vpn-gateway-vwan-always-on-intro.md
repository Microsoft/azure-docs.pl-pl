---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829056"
---
Nową funkcją klienta sieci VPN systemu Windows 10, która jest zawsze włączona, jest możliwość utrzymania połączenia sieci VPN. Zawsze, gdy aktywny profil sieci VPN może nawiązywać połączenie automatycznie i pozostaje połączony na podstawie wyzwalaczy, takich jak logowanie użytkownika, zmiana stanu sieci lub aktywny ekran urządzenia.

Bram z systemem Windows 10 można używać do nawiązywania trwałych tuneli użytkowników i tuneli urządzeń na platformie Azure.

Zawsze włączone połączenia sieci VPN obejmują jeden z dwóch typów tuneli:

* **Tunel urządzenia**: nawiązuje połączenie z określonymi serwerami sieci VPN przed zalogowaniem się użytkowników na urządzeniu. Scenariusze łączności przed logowaniem i zarządzaniem urządzeniami używają tunelu urządzenia.

* **Tunel użytkownika**: nawiązuje połączenie tylko po zalogowaniu się użytkowników na urządzeniu. Korzystając z tuneli użytkowników, można uzyskać dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunele urządzeń i tunele użytkownika działają niezależnie od profilów sieci VPN. Mogą być połączone w tym samym czasie i mogą korzystać z innych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN, zgodnie z potrzebami.

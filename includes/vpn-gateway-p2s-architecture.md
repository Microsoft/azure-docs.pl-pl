---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93145003"
---
Natywne połączenia z uwierzytelnianiem certyfikatu platformy Azure między lokacjami używają następujących elementów, które można skonfigurować w tym ćwiczeniu:

* Brama sieci VPN oparta na trasie.
* Klucz publiczny (plik cer) dla certyfikatu głównego, przekazany na platformę Azure. Przekazany certyfikat jest uznawany za certyfikat zaufany i używany do uwierzytelniania.
* Certyfikat klienta generowany na podstawie certyfikatu głównego. Certyfikat klienta instalowany na każdym komputerze klienckim, który będzie łączyć się z siecią wirtualną. Ten certyfikat jest używany do uwierzytelniania klientów.
* Konfiguracja klienta sieci VPN. Klient sieci VPN jest konfigurowany przy użyciu plików konfiguracji klienta sieci VPN. Te pliki zawierają informacje niezbędne do nawiązania połączenia z siecią wirtualną przez klienta. Pliki te służą do konfigurowania istniejącego, natywnego dla systemu operacyjnego klienta sieci VPN . Każdy klient, który nawiązuje połączenie, musi być skonfigurowany przy użyciu ustawień w tych plikach konfiguracji.

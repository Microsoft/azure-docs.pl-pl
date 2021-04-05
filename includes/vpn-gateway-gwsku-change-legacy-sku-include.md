---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010839"
---
Jeśli pracujesz z modelem wdrażania Menedżer zasobów, możesz przejść do nowych jednostek SKU bramy. W przypadku zmiany ze starszej jednostki SKU bramy na nową jednostkę SKU należy usunąć istniejącą bramę sieci VPN i utworzyć nową bramę sieci VPN.

Utworzonego

1. Usuń wszystkie połączenia z bramą sieci wirtualnej.
2. Usuń starą bramę sieci VPN.
3. Utwórz nową bramę sieci VPN.
4. Zaktualizuj lokalne urządzenia sieci VPN do nowego adresu IP bramy sieci VPN (dla połączeń typu lokacja-lokacja).
5. Zaktualizuj wartość adresu IP bramy dla wszystkich bram sieci lokalnej połączeń między sieciami wirtualnymi, które będą łączyć się z tą bramą.
6. Pobierz nowe pakiety konfiguracji sieci VPN klienta dla klientów połączeń punkt-lokacja nawiązujących połączenie z siecią wirtualną przez tę bramę sieci VPN.
7. Odtwórz połączenia z bramą sieci wirtualnej.

Zagadnienia do rozważenia:

* Aby przejść do nowych jednostek SKU, Brama sieci VPN musi znajdować się w Menedżer zasobów model wdrażania.
* Jeśli korzystasz z klasycznej bramy sieci VPN, musisz nadal używać starszych starszych jednostek SKU dla tej bramy, jednak można zmienić rozmiar między starszymi jednostkami SKU. Nie można zmienić nowych jednostek SKU.
* Podczas zmiany ze starszej wersji SKU na nową jednostkę SKU będziesz mieć problemy z łącznością.
* W przypadku zmiany na nową jednostkę SKU bramy zostanie zmieniony publiczny adres IP bramy sieci VPN. Dzieje się tak nawet w przypadku określenia tego samego obiektu publicznego adresu IP, który był wcześniej używany.
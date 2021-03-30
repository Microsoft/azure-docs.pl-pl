---
title: Rozwiązanie VMware firmy Azure według CloudSimple — bramy sieci VPN
description: Dowiedz się więcej o bramach sieci VPN typu lokacja-lokacja i punkt-lokacja, które są używane do wysyłania zaszyfrowanego ruchu między regionem CloudSimple i innymi zasobami.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140636"
---
# <a name="vpn-gateways-overview"></a>Bramy sieci VPN — Omówienie

Brama sieci VPN jest używana do wysyłania zaszyfrowanego ruchu między siecią CloudSimple region w lokalizacji lokalnej lub na komputerze za pośrednictwem publicznej sieci Internet.  Każdy region może mieć jedną bramę sieci VPN, która może obsługiwać wiele połączeń. W przypadku utworzenia wielu połączeń do tej samej bramy sieci VPN wszystkie tunele VPN współdzielą dostępną przepustowość bramy.

CloudSimple zapewnia dwa rodzaje bram sieci VPN:

* Brama sieci VPN typu lokacja-lokacja
* Brama sieci VPN typu punkt-lokacja

## <a name="site-to-site-vpn-gateway"></a>Brama sieci VPN typu lokacja-lokacja

Brama sieci VPN typu lokacja-lokacja jest używana do wysyłania zaszyfrowanego ruchu między siecią regionu CloudSimple i lokalnym centrum danych. To połączenie służy do definiowania podsieci/zakresu CIDR dla ruchu sieciowego między siecią lokalną a siecią CloudSimple region.

Brama sieci VPN umożliwia korzystanie z usług lokalnych w chmurze prywatnej i usług w chmurze prywatnej w sieci lokalnej.  CloudSimple udostępnia serwer sieci VPN oparty na zasadach do nawiązywania połączenia z sieci lokalnej.

Przypadki użycia dla sieci VPN typu lokacja-lokacja:

* Dostępność w chmurze prywatnej vCenter z dowolnej stacji roboczej w sieci lokalnej.
* Używanie Active Directory lokalnego jako źródła tożsamości programu vCenter.
* Wygodny transfer szablonów maszyn wirtualnych, obrazów ISO i innych plików z zasobów lokalnych do programu vCenter w chmurze prywatnej.
* Dostępność obciążeń działających w chmurze prywatnej z sieci lokalnej.

![Topologia połączenia sieci VPN typu lokacja-lokacja](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parametry kryptograficzne

Połączenie sieci VPN typu lokacja-lokacja używa następujących domyślnych parametrów kryptograficznych w celu nawiązania bezpiecznego połączenia.  Podczas tworzenia połączenia z lokalnego urządzenia sieci VPN należy użyć dowolnego z następujących parametrów, które są obsługiwane przez lokalną bramę sieci VPN.

#### <a name="phase-1-proposals"></a>Propozycje fazy 1

| Parametr | Wniosek 1 | Propozycja 2 | Propozycja 3 |
|-----------|------------|------------|------------|
| Wersja IKE | IKEv1 | IKEv1 | IKEv1 |
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm wyznaczania wartości skrótu| SHA 256 | SHA 256 | AGENT SHA 1 |
| Grupa Diffie-Hellmana (Grupa DH) | 2 | 2 | 2 |
| Czas życia | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propozycje fazy 2

| Parametr | Wniosek 1 | Propozycja 2 | Propozycja 3 |
|-----------|------------|------------|------------|
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm wyznaczania wartości skrótu| SHA 256 | SHA 256 | AGENT SHA 1 |
| Doskonałe utajnienie przekazywania dalej (Grupa PFS) | Brak | Brak | Brak |
| Czas życia | 1 800 sekund | 1 800 sekund | 1 800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Na urządzeniu sieci VPN ustaw wartość opcji ograniczania TCP = 1200. Lub jeśli urządzenia sieci VPN nie obsługują funkcji ograniczania, można zamiast tego ustawić jednostkę MTU w interfejsie tunelu na 1240 bajtów.

## <a name="point-to-site-vpn-gateway"></a>Brama sieci VPN typu punkt-lokacja

Sieć VPN typu punkt-lokacja jest używana do wysyłania zaszyfrowanego ruchu między siecią regionu CloudSimple i komputerem klienckim.  Sieć VPN typu punkt-lokacja jest najprostszym sposobem uzyskiwania dostępu do sieci prywatnej chmury, w tym maszyn wirtualnych programu vCenter i obciążeń prywatnych chmury prywatnej.  Jeśli łączysz się z chmurą prywatną zdalnie, Użyj połączenia sieci VPN typu punkt-lokacja.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie bramy sieci VPN](vpn-gateway.md)

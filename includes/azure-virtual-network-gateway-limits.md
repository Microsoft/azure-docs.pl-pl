---
title: plik dołączany
description: plik dołączany
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854118"
---
| Zasób                                | Limit        |
|-----------------------------------------|------------------------------|
| Prefiksy adresów sieci wirtualnej                   | 600 za bramę sieci VPN          |
| Agregowanie tras BGP                    | 4 000 za bramę sieci VPN        |
| Prefiksy adresów bramy sieci lokalnej  | 1000 na bramę sieci lokalnej               |
| Połączenia S2S                         | [Zależy od jednostki SKU bramy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Połączenia P2S                         | [Zależy od jednostki SKU bramy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limit tras P2S-IKEv2                 | 256 dla systemu Windows w systemie innym niż Windows **/**           |
| Limit tras P2S — OpenVPN               | 1000                         |
| Maksymalnie z flows (przepływy)                              | 100 000 dla VpnGw1/AZ  **/**  512K dla VpnGw2-4/AZ|
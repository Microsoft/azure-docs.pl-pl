---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "67666380"
---
Płatność dotyczy dwóch składników: kosztu godzin obliczeniowych bramy sieci wirtualnej i transferu danych wychodzących z bramy sieci wirtualnej. Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway). Aby uzyskać starsze ceny jednostki SKU bramy, zobacz [stronę z cennikiem ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) i przewiń do sekcji **bramy Virtual Network** .

**Koszty obliczeniowe bramy sieci wirtualnej**<br>Każda brama sieci wirtualnej ma koszt godziny obliczeniowej. Cena zależy od jednostki SKU bramy, która została określona podczas tworzenia bramy sieci wirtualnej. Koszt dotyczy samej bramy i jest dodawany do kosztu transferu danych, który przepływa przez bramę. Koszt konfiguracji aktywna-aktywna jest taka sam jak konfiguracji aktywna-pasywna.

**Koszty transferu danych**<br>Koszty transferu danych są obliczane na podstawie ruchu wychodzącego ze źródłowej bramy sieci wirtualnej.

* Jeśli ruch jest wysyłany do lokalnego urządzenia sieci VPN, zostanie naliczona stawka za transfer danych wychodzących do sieci Internet.
* Jeśli ruch jest wysyłany między sieciami wirtualnymi w różnych regionach, cena zależy od regionu.
* Jeśli ruch jest wysyłany tylko między sieciami wirtualnymi w tym samym regionie, nie ma żadnych kosztów danych. Ruch między sieciami wirtualnymi w tym samym regionie jest bezpłatny.

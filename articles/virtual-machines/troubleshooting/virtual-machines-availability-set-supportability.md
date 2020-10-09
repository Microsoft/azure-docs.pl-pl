---
title: Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności | Microsoft Docs
description: Ten artykuł zawiera matrycę z możliwością obsługi, którą serie maszyn wirtualnych można mieszać w tym samym zestawie dostępności
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77122924"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Obsługa dodawania maszyn wirtualnych platformy Azure do istniejącego zestawu dostępności

Czasami mogą wystąpić ograniczenia podczas dodawania nowych maszyn wirtualnych do istniejącego zestawu dostępności. Poniższy wykres zawiera szczegółowe informacje o serii maszyn wirtualnych, które można mieszać w tym samym zestawie dostępności.

Oto macierz obsługi do mieszania różnych typów maszyn wirtualnych:

Zestaw dostępności & serii|Druga maszyna wirtualna|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Pierwsza maszyna wirtualna|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Wszystkie inne serie nie mogą znajdować się w tym samym zestawie dostępności, ponieważ wymagają określonego sprzętu.

Nie można mieszać A8/A9 maszyny wirtualnej ze względu na wymaganie dedykowanej sieci zaplecza RDMA.

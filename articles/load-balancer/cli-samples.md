---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla Load Balancer
titleSuffix: Azure Load Balancer
description: Przykłady interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499351"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Load Balancer

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

* [Równoważenie obciążenia ruchem maszyn wirtualnych w celu uzyskania wysokiej dostępności](./scripts/load-balancer-linux-cli-sample-nlb.md)

  Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i o zrównoważonym obciążeniu.

* [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  Tworzy trzy maszyny wirtualne w różnych strefach dostępności w obrębie regionu i usługa Load Balancer w warstwie Standardowa ze Strefowo nadmiarowego adresu IP frontonu. Ta konfiguracja modułu równoważenia obciążenia pomaga chronić Twoje aplikacje i dane z powodu najprawdopodobniej awarii lub utraty całego centrum danych.

* [Równoważenie obciążenia maszyn wirtualnych w określonej strefie dostępności](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  Tworzy trzy maszyny wirtualne, usługa Load Balancer w warstwie Standardowa ze strefą adresów IP frontonu, który ułatwia wyrównywanie ścieżki danych i zasobów w jednej strefie dla danego regionu.

* [Równoważenie obciążenia wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Tworzy dwie maszyny wirtualne z wieloma konfiguracjami adresów IP, które są przyłączone do zestawu dostępności platformy Azure, dostępne za pośrednictwem Azure Load Balancer.

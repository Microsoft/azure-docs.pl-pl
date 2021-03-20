---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "71266542"
---
Brama sieci wirtualnej używa specyficznej podsieci nazywanej podsiecią bramy. Podsieć bramy jest częścią zakresu adresów IP sieci wirtualnej, który jest wybierany podczas konfigurowania sieci wirtualnej. Zawiera ona adresy IP używane przez zasoby i usługi bramy sieci wirtualnej. 


Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Liczba potrzebnych adresów IP zależy od konfiguracji bramy sieci VPN, którą chce się utworzyć. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Zalecamy utworzenie podsieci bramy korzystającej z maski /27 lub /28.


Jeśli zobaczysz błąd z informacją o tym, że przestrzeń adresowa nakłada się z podsiecią lub że podsieć nie zawiera się w przestrzeni adresowej sieci wirtualnej, sprawdź zakres adresów sieci wirtualnej. Możliwe, że w zakresie adresów utworzonym dla sieci wirtualnej nie masz wystarczającej liczby adresów IP. Na przykład jeśli podsieć domyślna obejmuje cały zakres adresów, nie ma już adresów IP do utworzenia dodatkowych podsieci. Możesz albo dostosować swoje podsieci w obrębie istniejącej przestrzeni adresowej w celu zwolnienia adresów IP, albo określić dodatkowy zakres adresów i w nim utworzyć podsieć bramy.
---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812715"
---
1. W obszarze wirtualne sieci WAN wybierz pozycję centra i wybierz pozycję **+ nowe centrum**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="nowe centrum":::

1. Na stronie Tworzenie wirtualnego centrum Wypełnij poniższe pola.

   * **Region** — wybierz region, w którym chcesz wdrożyć koncentrator wirtualny.
   * **Nazwa** — wprowadź nazwę, która ma być wywoływana z koncentratorem wirtualnym.
   * **Prywatna przestrzeń adresowa centrum** — zakres adresów centrum w notacji CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="Tworzenie koncentratora wirtualnego":::

1. Na karcie punkt-lokacja wykonaj następujące pola:

   * **Jednostki skalowania bramy** , które reprezentują zagregowaną pojemność bramy sieci VPN użytkownika.
   * **Wskaż konfigurację lokacji** , która została utworzona w poprzednim kroku.
   * **Pula adresów klienta** — dla użytkowników zdalnych.
   * **Niestandardowy adres IP serwera DNS**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="centrum z punktem do lokacji":::

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Na stronie **Walidacja została przeniesiona** wybierz pozycję **Utwórz**.
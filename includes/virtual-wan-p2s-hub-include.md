---
title: plik dołączania
description: plik dołączania
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812715"
---
1. W obszarze wirtualne sieci WAN wybierz pozycję centra i wybierz pozycję **+ nowe centrum**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="nowe centrum":::

1. Na stronie Tworzenie wirtualnego centrum Wypełnij poniższe pola.

   * **Region** — wybierz region, w którym chcesz wdrożyć koncentrator wirtualny.
   * **Nazwa** — wprowadź nazwę, która ma być wywoływana z koncentratorem wirtualnym.
   * **Prywatna przestrzeń adresowa centrum** — zakres adresów centrum w notacji CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="nowe centrum":::

1. Na karcie punkt-lokacja wykonaj następujące pola:

   * **Jednostki skalowania bramy** , które reprezentują zagregowaną pojemność bramy sieci VPN użytkownika.
   * **Wskaż konfigurację lokacji** , która została utworzona w poprzednim kroku.
   * **Pula adresów klienta** — dla użytkowników zdalnych.
   * **Niestandardowy adres IP serwera DNS**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="nowe centrum":::

1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Na stronie **Walidacja została przeniesiona** wybierz pozycję **Utwórz**.
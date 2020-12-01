---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356265"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla bezpośredniej komunikacji równorzędnej.

### <a name="add-direct-peering-connections"></a>Dodaj bezpośrednie połączenia komunikacji równorzędnej
1. Wybierz przycisk **+ Dodaj połączenia** i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![Na stronie AshburnPeering (połączenia) znajduje się lista połączeń. Zostanie wyróżniony przycisk + Dodaj połączenia.](../media/setup-direct-modify-addconnection.png)

1. Wypełnij formularz **połączenia bezpośredniego komunikacji równorzędnej** , a następnie wybierz pozycję **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia komunikacji równorzędnej, zapoznaj się z instrukcjami w sekcji "Tworzenie i udostępnianie bezpośredniej komunikacji równorzędnej".
    > [!div class="mx-imgBorder"]
    > ![Formularz połączenia bezpośredniego komunikacji równorzędnej](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Usuń bezpośrednie połączenia komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w Azure Portal. Aby uzyskać więcej informacji, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Przepustowość uaktualnienia lub obniżenia poziomu aktywnych połączeń
1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz pozycję **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Edytuj połączenie](../media/setup-direct-modify-editconnection.png)

1. Zmodyfikuj przepustowość, przesuwając suwak, a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Modyfikuj przepustowość](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Dodaj informacje o sesji IPv4 lub IPv6 dla aktywnych połączeń
1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz pozycję **...**  >  **Edytuj połączenie** , jak pokazano w kroku 1.
1. Wprowadź **prefiks IPv4 sesji** lub **prefiks IPv6 sesji** , a następnie wybierz pozycję **Zapisz**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Usuń informacje o sesji IPv4 lub IPv6 dla aktywnych połączeń
Usuwanie **prefiksu IPv4 sesji** lub **prefiksu IPv6 sesji** nie jest obecnie obsługiwane w portalu. Aby uzyskać więcej informacji, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).

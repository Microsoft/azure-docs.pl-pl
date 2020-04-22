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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681069"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla komunikacji równorzędnej direct.

### <a name="add-direct-peering-connections"></a>Dodawanie bezpośrednich połączeń komunikacji równorzędnej
1. Wybierz przycisk **+ Dodaj połączenia** i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-modify-addconnection.png)

1. Wypełnij formularz **Bezpośrednie połączenie komunikacji równorzędnej** i wybierz pozycję **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia równorzędnego, zapoznaj się z instrukcjami w sekcji "Tworzenie i inicjowanie obsługi administracyjnej bezpośredniej komunikacji równorzędnej".
    > [!div class="mx-imgBorder"]
    > ![Formularz Bezpośrednie połączenie komunikacji równorzędnej](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Usuwanie bezpośrednich połączeń komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w witrynie Azure portal. Aby uzyskać więcej informacji, skontaktuj się z [programem Microsoft peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uaktualnianie lub obniżanie przepustowości połączeń aktywnych
1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Edytowanie połączenia](../media/setup-direct-modify-editconnection.png)

1. Zmodyfikuj przepustowość, przesuwając suwak, a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Modyfikowanie przepustowości](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Dodawanie informacji o sesji IPv4 lub IPv6 w aktywnych połączeniach
1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz **...**  >  **Edytuj połączenie** w sposób pokazany w kroku 1.
1. Wprowadź **prefiks IPv4 sesji** lub informacje o **prefiksie IPv6 sesji** i wybierz pozycję **Zapisz**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Usuwanie informacji o sesji IPv4 lub IPv6 w aktywnych połączeniach
Usuwanie **prefiksu IPv4 sesji** lub informacji o **prefiksie IPv6 sesji** nie jest obecnie obsługiwane w portalu. Aby uzyskać więcej informacji, skontaktuj się z [programem Microsoft peering](mailto:peeringexperience@microsoft.com).

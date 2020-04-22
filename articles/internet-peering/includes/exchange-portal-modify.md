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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680926"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla komunikacji równorzędnej direct.

### <a name="add-exchange-peering-connections"></a>Dodawanie połączeń komunikacji równorzędnej programu Exchange

1. Wybierz przycisk **+ Dodaj połączenia** i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-modify-addconnection.png)
1. Wypełnij formularz **Połączenie komunikacji równorzędnej programu Exchange** i wybierz pozycję **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia równorzędnego, zapoznaj się z instrukcjami w sekcji "Tworzenie i inicjowanie obsługi administracyjnej bezpośredniej komunikacji równorzędnej".
    > [!div class="mx-imgBorder"]
    > ![Formularz Połączenie komunikacji równorzędnej programu Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Usuwanie połączeń komunikacji równorzędnej programu Exchange

1. Wybierz połączenie komunikacji równorzędnej, które chcesz usunąć, a następnie wybierz **...**  >  **Usuń połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Przycisk Usuń połączenie](../media/setup-exchange-modify-deleteconnection.png)
1. Wprowadź identyfikator zasobu w polu **Potwierdź usuwanie** i wybierz pozycję **Usuń**.
    > [!div class="mx-imgBorder"]
    > ![Usuń potwierdzenie](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Dodawanie sesji IPv4 lub IPv6 w usłudze Active connections

1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Przycisk Edytuj połączenie](../media/setup-exchange-modify-editconnection.png)
1. Dodaj **adres IPv4** lub informacje **adresowe IPv6,** a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Modyfikacje połączenia komunikacji równorzędnej](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Usuwanie sesji IPv4 lub IPv6 w aktywnych połączeniach

Usuwanie sesji IPv4 lub IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w portalu. Aby uzyskać więcej informacji, skontaktuj się z [programem Microsoft peering](mailto:peeringexperience@microsoft.com).
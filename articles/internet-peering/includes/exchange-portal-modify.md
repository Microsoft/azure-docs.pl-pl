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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680926"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla bezpośredniej komunikacji równorzędnej.

### <a name="add-exchange-peering-connections"></a>Dodawanie połączeń komunikacji równorzędnej programu Exchange

1. Wybierz przycisk **+ Dodaj połączenia** i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-modify-addconnection.png)
1. Wypełnij formularz **połączenie komunikacji równorzędnej programu Exchange** i wybierz pozycję **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia komunikacji równorzędnej, zapoznaj się z instrukcjami w sekcji "Tworzenie i udostępnianie bezpośredniej komunikacji równorzędnej".
    > [!div class="mx-imgBorder"]
    > ![Formularz połączenia komunikacji równorzędnej programu Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Usuń połączenia komunikacji równorzędnej programu Exchange

1. Wybierz połączenie komunikacji równorzędnej, które chcesz usunąć, a następnie wybierz pozycję **...**  >  **Usuń połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Przycisk Usuń połączenie](../media/setup-exchange-modify-deleteconnection.png)
1. Wprowadź identyfikator zasobu w polu **Potwierdź usunięcie** , a następnie wybierz pozycję **Usuń**.
    > [!div class="mx-imgBorder"]
    > ![Potwierdzenie usunięcia](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Dodaj sesję IPv4 lub IPv6 dla aktywnych połączeń

1. Wybierz połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie wybierz pozycję **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Przycisk Edytuj połączenie](../media/setup-exchange-modify-editconnection.png)
1. Dodaj **adres IPv4** lub **adres IPv6** , a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Modyfikowanie połączeń komunikacji równorzędnej](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Usuń sesję IPv4 lub IPv6 dla aktywnych połączeń

Usuwanie sesji IPv4 lub IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w portalu. Aby uzyskać więcej informacji, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).
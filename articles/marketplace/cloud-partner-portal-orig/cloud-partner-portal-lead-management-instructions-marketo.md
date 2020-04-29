---
title: Konfigurowanie zarządzania liderem w programie Marketo | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla programu Marketo dla klientów portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288533"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w programie Marketo

W tym artykule opisano sposób konfigurowania programu Marketo do obsługi potencjalnych klientów sprzedaży firmy Microsoft.

1. Zaloguj się do programu Marketo.
2. Wybierz pozycję **projekt Studio**.
    ![Projekt programu Marketo Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Wybierz pozycję **nowy formularz**.
    ![Nowy formularz dla programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Nowy formularz dla programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  W polu Szczegóły pola wybierz pozycję **Zakończ**.
    ![Formularz zakończenia programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Zatwierdź i Zamknij.

6.  Na karcie MarketplaceLeadBacked wybierz pozycję **Osadź kod**.
    ![Opcja kodu osadzania programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kod osadzania programu Marketo wyświetla kod podobny do poniższego przykładu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Skopiuj wartości wyświetlane w polu osadź kod, aby skonfigurować **Identyfikator serwera**, **Identyfikator Munchkin**i **Identyfikator formularza** w polach Marketo na portal Cloud partner.

W następnym przykładzie przedstawiono Przewodnik dotyczący uzyskiwania wymaganych identyfikatorów z przykładu kodu osadzania programu Marketo.

- Identyfikator serwera = **ys12**
- Identyfikator Munchkin = **123-PQR-789**
- Identyfikator formularza = **1179**

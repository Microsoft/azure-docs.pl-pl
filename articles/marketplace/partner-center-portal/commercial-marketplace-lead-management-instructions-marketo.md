---
title: Konfigurowanie zarządzania potencjalnymi klientami w Marketo | Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla marketo dla platformy Azure.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 90f42954fd4d7324a7684795fca6ec302411790c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731113"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w Marketo

W tym artykule opisano, jak skonfigurować system Marketo CRM do przetwarzania potencjalnych klientów sprzedaży z oferty marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Skonfiguruj swój system Marketo CRM

1. Zaloguj się do Marketo.

2. Wybierz **Design Studio**.

    ![Studio Projektowe Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Wybierz **pozycję Nowy formularz**.

    ![Marketo nowa forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.

    ![Marketo tworzenie nowego formularza](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  W obszarze Szczegóły pola wybierz pozycję **Zakończ**.

    ![Formularz wykończenia Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Zatwierdź i zamknij.

7. Na karcie *MarketplaceLeadBacked* wybierz pozycję **Osadź kod**. 

    ![Kod osadzania](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Embed Code wyświetla kod podobny do poniższego przykładu.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Skopiuj wartości poniższych pól wyświetlanych w formularzu Kod osadzania. Użyjesz tych wartości, aby skonfigurować ofertę do odbierania potencjalnych klientów w następnym kroku. Użyj następnego przykładu jako przewodnika do uzyskiwania identyfikatorów, których potrzebujesz w przykładzie Marketo Embed Code.

    - Identyfikator serwera = **ys12**
    - Identyfikator Munchkin = **123-PQR-789**
    - Identyfikator formularza = **1179**

    **Innym sposobem, aby dowiedzieć się tych wartości**

    - Identyfikator serwera znajduje się w adresie URL wystąpienia Marketo, na przykład "`serverID.marketo.com`".
    - Uzyskaj identyfikator Munching ID subskrypcji, przechodząc do menu Administrator>Munchkin w polu "Identyfikator konta Munchkin" lub z pierwszej części poddomeny hosta interfejsu Marketo REST: `https://{Munchkin ID}.mktorest.com`.
    - Identyfikator formularza to identyfikator formularza Embed Code utworzonego w kroku 7 w celu kierowania potencjalnych klientów z naszego rynku.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Skonfiguruj ofertę wysyłania potencjalnych klientów do Marketo

Gdy wszystko będzie gotowe do skonfigurowania informacji o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj następujące kroki: 

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.

1. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami. 

    ![Zarządzanie potencjalnymi klientami - Połącz](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. W oknie podręcznym Szczegóły połączenia wybierz **pozycję Marketo** dla miejsca docelowego potencjalnego klienta.

    ![Wybieranie miejsca docelowego potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Podaj **identyfikator serwera,** **identyfikator konta munching**i **identyfikator formularza**.

    > [!Note]
    > Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty. 

5. **Kontaktowy adres e-mail** — udostępniaj wiadomości e-mail osobom w firmie, które powinny otrzymywać powiadomienia e-mail po otrzymaniu nowego potencjalnego klienta. Możesz dostarczyć wiele wiadomości e-mail, oddzielając je średnikiem.

6. Kliknij przycisk **OK**.

   Aby upewnić się, że udało ci się połączyć z miejscem docelowym potencjalnego klienta, kliknij przycisk sprawdź poprawność. Jeśli się powiedzie, będziesz miał potencjalnego klienta testowego w głównym miejscu docelowym.

   ![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)

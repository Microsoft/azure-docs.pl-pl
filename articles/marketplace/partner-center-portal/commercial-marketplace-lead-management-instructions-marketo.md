---
title: Zarządzanie potencjalnymi klientami w programie Marketo — Microsoft Commercial Marketplace
description: Dowiedz się, jak zarządzać potencjalnymi klientami z Microsoft AppSource i Azure Marketplace przy użyciu systemu programu Marketo.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: be1f21f927b01d66e19dc5e97b38e5c35e6664cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120266"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Korzystanie z programu Marketo do zarządzania komercyjnymi klientami portalu Marketplace

W tym artykule opisano, jak skonfigurować system CRM dla programu Marketo, aby przetwarzać potencjalni klienci z ofert w Microsoft AppSource i witrynie Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Skonfiguruj system CRM dla programu Marketo

1. Zaloguj się do programu Marketo.

1. Wybierz pozycję **projekt Studio**.

    ![Projekt programu Marketo Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Wybierz pozycję **nowy formularz**.

    ![Nowy formularz dla programu Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Wypełnij pola wymagane w **nowym formularzu** okna dialogowego, a następnie wybierz pozycję **Utwórz**.

    ![Nowy formularz dla programu Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na stronie **Szczegóły pola** wybierz pozycję **Zakończ**.

    ![Formularz zakończenia programu Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Zatwierdź i Zamknij.

1. Na karcie **MarketplaceLeadBackend** wybierz pozycję **Osadź kod**. 

    ![Kod osadzania programu Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Kod osadzania programu Marketo wyświetla kod podobny do poniższego przykładu.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Skopiuj wartości dla następujących pól przedstawionych w formularzu kodu osadzania. Te wartości zostaną użyte do skonfigurowania oferty, aby otrzymywać potencjalni klienci w następnym kroku. W następnym przykładzie przedstawiono Przewodnik dotyczący uzyskiwania wymaganych identyfikatorów z przykładu kodu osadzania programu Marketo.

    - Identyfikator serwera = **ys12**
    - Identyfikator Munchkin = **123-PQR-789**
    - Identyfikator formularza = **1179**

    Innym sposobem ustalenia następujących wartości:

    - Identyfikator serwera znajduje się w adresie URL wystąpienia programu Marketo, na przykład `serverID.marketo.com` .
    - Aby uzyskać identyfikator Munchkin subskrypcji, przejdź do menu **administratora**  >  **Munchkin** w polu **Identyfikator konta Munchkin** lub z pierwszej części poddomeny hosta interfejsu API REST usługi Marketo: `https://{Munchkin ID}.mktorest.com` .
    - Identyfikator formularza jest IDENTYFIKATORem formularza kodu osadzania utworzonego w kroku 7 do kierowania potencjalnych klientów z portalu Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące kroki. 

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).

1. Wybierz ofertę i przejdź do karty **Konfiguracja oferty** .

1. W sekcji **potencjalni klienci** wybierz pozycję **Połącz**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Potencjalni klienci":::

1. W oknie podręcznym **szczegóły połączenia** wybierz pozycję **Marketo** dla **miejsca docelowego potencjalnego klienta**.

    ![Wybierz miejsce docelowe potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Podaj **Identyfikator serwera**, **Identyfikator konta Munchkin**i **Identyfikator formularza**.

    > [!NOTE]
    > Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty. 

1. W obszarze **kontaktowy adres e-mail**wprowadź adresy e-mail osób w firmie, które powinny otrzymywać powiadomienia e-mail po odebraniu nowego potencjalnego klienta. Można podać wiele adresów e-mail, rozdzielając je średnikami.

1. Kliknij przycisk **OK**.

   Aby upewnić się, że pomyślnie nawiązano połączenie z miejscem docelowym potencjalnego klienta, wybierz pozycję **Weryfikuj**. Jeśli się powiedzie, będziesz mieć test w miejscu docelowym potencjalnego klienta.

   ![Okno podręczne szczegółów połączenia](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)

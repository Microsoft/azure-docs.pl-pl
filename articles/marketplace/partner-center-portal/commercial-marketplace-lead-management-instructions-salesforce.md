---
title: Zarządzanie potencjalnymi klientami w usłudze Salesforce — Microsoft Commercial Marketplace
description: Dowiedz się, jak skonfigurować potencjalnych klientów dla Microsoft AppSource i Azure Marketplace przy użyciu usługi Salesforce
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: c3667de6a9bf85109a94201aa8e144869e4b033f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86117119"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurowanie zarządzania liderem w usłudze Salesforce

W tym artykule opisano sposób konfigurowania systemu Salesforce do przetwarzania potencjalnych klientów z ofert w Microsoft AppSource i witrynie Azure Marketplace.

> [!NOTE]
> Portal Azure Marketplace nie obsługuje wstępnie wypełnionych list, takich jak lista wartości pola **kraj** . Przed kontynuowaniem upewnij się, że nie ma skonfigurowanych list. Alternatywnie można skonfigurować [punkt końcowy https](./commercial-marketplace-lead-management-instructions-https.md) lub [tabelę platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) w celu otrzymywania potencjalnych klientów.

## <a name="set-up-your-salesforce-system"></a>Konfigurowanie systemu Salesforce

1. Zaloguj się do usługi Salesforce.
1. Przejdź do ustawień **Sieć Web-potencjalny klient** . 
    
    Jeśli używasz środowiska oświetleniowego Salesforce
    1. Na stronie głównej usługi Salesforce wybierz pozycję **Konfiguracja** .

       ![Konfiguracja usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stronie **Instalator** wybierz pozycję **Narzędzia platformy**  >  **Ustawienia funkcja**  >  **Marketing**  >  **Sieć Web-potencjalny klient**.

        ![Sieć Web do realizacji usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Jeśli korzystasz z klasycznego środowiska Salesforce:

    1. Na stronie głównej usługi Salesforce wybierz pozycję **Konfiguracja** .

       ![Klasyczna konfiguracja usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stronie **Instalator** wybierz kolejno opcje **kompilacja**  >  **Dostosuj**  >  **potencjalni klienci**  >  **Web-to-potencjalny klient**.

        ![Klasyczny sieci Web dla usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Pozostałe kroki są takie same dla obu środowisk usług Salesforce.

1. Na stronie **Konfiguracja sieci Web-potencjalny klient** wybierz przycisk **Utwórz formularz sieci Web-potencjalny klient** .
1. W obszarze **Konfiguracja sieci Web-lider**wybierz pozycję **Utwórz formularz sieć Web-potencjalny klient**.

    ![Konfiguracja sieci Web do potencjalnego klienta usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Na stronie **Tworzenie formularza sieć Web-potencjalny klient**upewnij się, że `Include reCAPTCHA in HTML` ustawienie jest wyczyszczone, a następnie wybierz pozycję **Generuj**.

    ![Okienko Tworzenie formularza sieci Web do potencjalnego klienta w usłudze Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Zostanie wyświetlony jakiś tekst HTML. Wyszukaj tekst "OID" i skopiuj **wartość "OID"** z tekstu HTML (tylko tekst między znakami cudzysłowu) i Zapisz go. Ta wartość zostanie wklejona w polu **identyfikator organizacji** w portalu wydawców.

    ![Tworzenie formularza sieci Web do potencjalnego klienta w usłudze Salesforce — wartość HTML "OID"](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Wybierz pozycję **gotowe**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące kroki.

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).

1. Wybierz ofertę i przejdź do karty **Konfiguracja oferty** .

1. W sekcji **potencjalni klienci** wybierz pozycję **Połącz**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Potencjalni klienci":::

1. W oknie podręcznym **szczegóły połączenia** wybierz pozycję **Salesforce** dla **miejsca docelowego potencjalnego klienta** i wklej `oid` wartość z formularza sieci Web do potencjalnego klienta utworzonego w polu **identyfikator organizacji** .

    ![Okno podręczne szczegóły połączenia Sprawdź poprawność kontaktu z adresem e-mail](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. W obszarze **kontaktowy adres e-mail**wprowadź adresy e-mail osób w firmie, które powinny otrzymywać powiadomienia e-mail po odebraniu nowego potencjalnego klienta. Można podać wiele wiadomości e-mail, rozdzielając je średnikami.

1. Kliknij przycisk **OK**.

Aby upewnić się, że pomyślnie nawiązano połączenie z miejscem docelowym potencjalnego klienta, wybierz pozycję **Weryfikuj**. Jeśli się powiedzie, będziesz mieć test w miejscu docelowym potencjalnego klienta.

>[!NOTE]
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

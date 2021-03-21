---
title: Jak utworzyć ofertę aplikacji platformy Azure w portalu komercyjnym
description: Dowiedz się, jak utworzyć nową ofertę aplikacji platformy Azure do wystawiania lub sprzedawania w portalu Azure Marketplace albo za pośrednictwem programu Cloud Solution Provider (CSP) w witrynie Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370223"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Jak utworzyć ofertę aplikacji platformy Azure w portalu komercyjnym

Jako komercyjny wydawca portalu Marketplace możesz utworzyć ofertę aplikacji platformy Azure, aby potencjalni klienci mogli kupować Twoje rozwiązanie. W tym artykule opisano proces tworzenia oferty aplikacji platformy Azure dla portalu komercyjnego firmy Microsoft.

Jeśli jeszcze tego nie zrobiono, przeczytaj artykuł [Planowanie oferty aplikacji platformy Azure dla komercyjnej witryny Marketplace](plan-azure-application-offer.md). Udostępnienie zasobów i pomoże Ci zebrać informacje i zasoby, które będą potrzebne podczas tworzenia oferty.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).

1. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .

1. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **aplikacji platformy Azure**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. W oknie dialogowym **Nowa oferta** wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie. Ten identyfikator jest widoczny w adresie URL aukcji komercyjnej witryny Marketplace i szablonów Azure Resource Manager, jeśli ma to zastosowanie. Jeśli na przykład w tym polu wprowadzisz wartość test-Offer-1, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Każda oferta w Twoim koncie musi mieć unikatowy identyfikator oferty.
     * Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków.
     * Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

1. Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

     * Ta nazwa jest widoczna tylko w centrum partnerskim i różni się od nazwy oferty i innych wartości widocznych dla klientów.
     * Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

1. Aby wygenerować ofertę i kontynuować, wybierz pozycję  **Utwórz**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Skonfiguruj szczegóły konfiguracji oferty aplikacji platformy Azure

Na karcie **Konfiguracja oferty** w obszarze **szczegóły instalacji** wybierz, czy chcesz skonfigurować dysk testowy. Będziesz również łączyć system zarządzania relacjami z klientami (CRM) z ofertą komercyjnej witryny Marketplace.

### <a name="enable-a-test-drive-optional"></a>Włącz stację testową (opcjonalnie)

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, zapewniając im dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Oferowanie dysku testowego skutkuje zwiększoną szybkością konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [Test Drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Aby włączyć stację testową

- W obszarze **testowanie dysku** zaznacz pole wyboru **Włącz dysk testowy** .

### <a name="customer-lead-management"></a>Zarządzanie potencjalnymi klientami

Połącz system zarządzania relacjami z klientami (CRM) z ofertą komercyjnej witryny Marketplace, aby otrzymywać informacje kontaktowe klienta, gdy klient wyraża zainteresowanie lub wdroży produkt.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Aby skonfigurować szczegóły połączenia w centrum partnerskim

1. W obszarze **potencjalni klienci** wybierz łącze **Połącz** .
1. W oknie dialogowym **szczegóły połączenia** wybierz lokalizację docelową potencjalnego klienta z listy.
1. Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Aby sprawdzić podaną konfigurację, wybierz link **Weryfikuj** , jeśli ma zastosowanie.
1. Aby zamknąć okno dialogowe, wybierz pozycję **Połącz**.
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: właściwości.

> [!NOTE]
> Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest aktualne, więc nie utracisz żadnych potencjalnych klientów. Upewnij się, że te połączenia zostały zaktualizowane, gdy coś się zmieniło.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować właściwości oferty aplikacji platformy Azure](create-new-azure-apps-offer-properties.md)

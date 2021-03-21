---
title: Jak utworzyć ofertę usługi zarządzanej w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć nową ofertę usługi zarządzanej dla portalu Azure Marketplace, korzystając z komercyjnego programu w witrynie Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918412"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Jak utworzyć ofertę usługi zarządzanej dla komercyjnego portalu Marketplace

W tym artykule opisano sposób tworzenia oferty usługi zarządzanej dla portalu komercyjnego firmy Microsoft przy użyciu Centrum partnerskiego.

Aby opublikować ofertę usługi zarządzanej, należy uzyskać kompetencje firmy Microsoft dotyczące złota lub srebra na platformie chmurowej. Jeśli jeszcze tego nie zrobiono, przeczytaj artykuł [Planowanie oferty usługi zarządzanej dla komercyjnej witryny Marketplace](./plan-managed-service-offer.md). Ułatwi to przygotowanie zasobów potrzebnych podczas tworzenia oferty w centrum partnerskim.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .
3. Na karcie Przegląd wybierz pozycję **+ Nowa oferowana**  >  **usługa zarządzana**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Ilustruje menu nawigacji po lewej stronie.":::

4. W oknie dialogowym **Nowa oferta** wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie. Ten identyfikator jest widoczny w adresie URL aukcji komercyjnej witryny Marketplace i szablonów Azure Resource Manager, jeśli ma to zastosowanie. Jeśli na przykład w tym polu wprowadzisz wartość test-Offer-1, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Każda oferta w Twoim koncie musi mieć unikatowy identyfikator oferty.
    * Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków.
    * Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

5. Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim. Nie jest on widoczny w sklepach online i różni się od nazwy oferty widocznej dla klientów.
6. Aby wygenerować ofertę i kontynuować, wybierz pozycję **Utwórz**.

## <a name="configure-lead-management"></a>Konfigurowanie zarządzania potencjalnymi klientami

Połącz system zarządzania relacjami z klientami (CRM) z ofertą komercyjnej witryny Marketplace, aby otrzymywać informacje kontaktowe klienta, gdy klient wyraża zainteresowanie usługą doradczą. To połączenie można zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Aby skonfigurować zarządzanie potencjalnymi klientami w centrum partnerskim:

1. W centrum partnerskim przejdź do karty **Konfiguracja oferty** .
2. W obszarze **potencjalni klienci** wybierz łącze **Połącz** .
3. W oknie dialogowym **szczegóły połączenia** wybierz lokalizację docelową potencjalnego klienta z listy.
4. Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Aby sprawdzić podaną konfigurację, wybierz **link Weryfikuj**.
6. Po skonfigurowaniu szczegółów połączenia wybierz pozycję **Połącz**.
7. Wybierz pozycję **Zapisz wersję roboczą**.

Po przesłaniu oferty do publikacji w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem Przetestuj połączenie z liderem, próbując zakupić swoją ofertę samodzielnie w środowisku wersji zapoznawczej.

> [!TIP]
> Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

## <a name="configure-offer-properties"></a>Konfigurowanie właściwości oferty

Na stronie właściwości oferty w centrum partnerskim zdefiniujesz kategorie mające zastosowanie do oferty oraz umów prawnych. Te informacje zapewniają, że usługa zarządzana jest prawidłowo wyświetlana w sklepie online i oferowana w odpowiednim zestawie klientów.

### <a name="select-a-category"></a>Wybierz kategorię

W obszarze **Kategorie** wybierz co najmniej jedną i maksymalnie pięć kategorii do grupowania oferty w odpowiednie obszary wyszukiwania komercyjnego portalu Marketplace.

### <a name="provide-terms-and-conditions"></a>Podaj warunki i postanowienia

W obszarze **prawne** Podaj warunki i postanowienia dotyczące tej oferty. Klienci będą musieli zaakceptować je przed skorzystaniem z oferty. Możesz także podać adres URL, pod którym można znaleźć warunki i postanowienia.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-step"></a>Następny krok

* [Skonfiguruj listę oferty usługi zarządzanej](./create-managed-service-offer-listing.md)
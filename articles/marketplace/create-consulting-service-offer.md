---
title: Jak utworzyć ofertę usługi konsultingowej w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć nową ofertę usługi konsultingowej dla Microsoft AppSource lub Azure Marketplace przy użyciu programu komercyjnego portalu Marketplace w witrynie Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754451"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Jak utworzyć ofertę usługi konsultingowej w komercyjnej witrynie Marketplace

W tym artykule opisano sposób tworzenia oferty usługi konsultingowej dla komercyjnej witryny Marketplace firmy Microsoft przy użyciu Centrum partnerskiego. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby opublikować ofertę usługi konsultingowej, musisz spełnić pewne wymagania dotyczące uprawnień, aby przedstawić wiedzę fachową w Twoim zakresie. Jeśli jeszcze tego nie zrobiono, zapoznaj się z artykułem [Planowanie oferty usługi konsultingowej dla komercyjnej witryny Marketplace](./plan-consulting-service-offer.md). Zawiera opis wymagań wstępnych dotyczących usługi konsultingowej oraz zasobów potrzebnych podczas tworzenia oferty przy użyciu Centrum partnerskiego.

## <a name="create-a-new-consulting-service-offer"></a>Utwórz nową ofertę usługi konsultingowej

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2.  W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview** .
3.  Na karcie Przegląd wybierz pozycję **+ Nowa**  >  **Usługa konsultingowa** oferty.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-consulting-service.png)

4. W oknie dialogowym **Nowa oferta** wprowadź **Identyfikator oferty** . Ten identyfikator jest widoczny w adresie URL aukcji komercyjnej na rynku. Jeśli na przykład w tym polu wprowadzisz wartość test-Offer-1, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Każda oferta w Twoim koncie musi mieć unikatowy identyfikator oferty.
    * Użyj tylko małych liter i cyfr. Identyfikator oferty może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczony do 50 znaków.
    * Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz** .

5. Wprowadź **alias oferty** . Jest to nazwa używana dla oferty w centrum partnerskim. Nie jest on widoczny w sklepach online i różni się od nazwy oferty widocznej dla klientów.
6. Aby wygenerować ofertę i kontynuować, wybierz pozycję **Utwórz** .

## <a name="configure-lead-management"></a>Konfigurowanie zarządzania potencjalnymi klientami

Połącz system zarządzania relacjami z klientami (CRM) z ofertą komercyjnej witryny Marketplace, aby otrzymywać informacje kontaktowe klienta, gdy klient wyraża zainteresowanie usługą doradczą. To połączenie można zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Aby skonfigurować zarządzanie potencjalnymi klientami w centrum partnerskim:

1.  W centrum partnerskim przejdź do karty **Konfiguracja oferty** .
2.  W obszarze **potencjalni klienci** wybierz łącze **Połącz** .
3.  W oknie dialogowym **szczegóły połączenia** wybierz lokalizację docelową potencjalnego klienta z listy.
4.  Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Aby sprawdzić podaną konfigurację, wybierz **link Weryfikuj** .
6.  Po skonfigurowaniu szczegółów połączenia wybierz pozycję **Połącz** .
7.  Wybierz pozycję **Zapisz wersję roboczą** .

Po przesłaniu oferty do publikacji w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem Przetestuj połączenie z liderem, próbując zakupić swoją ofertę samodzielnie w środowisku wersji zapoznawczej.

> [!TIP]
> Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

## <a name="next-steps"></a>Następne kroki

* [Jak skonfigurować właściwości oferty usługi konsultingowej](./create-consulting-service-offer-properties.md)
---
title: Jak utworzyć ofertę SaaS w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć nową ofertę SaaS (Software as a Service) w celu uzyskania listy lub sprzedaży w Microsoft AppSource, witrynie Azure Marketplace lub za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu portalu komercyjnej witryny Marketplace w witrynie Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: f652858f22e26eb7167a0cc7a2cfb8ef4b600ace
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047304"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Jak utworzyć ofertę SaaS w komercyjnej witrynie Marketplace

Jako komercyjny wydawca portalu Marketplace możesz utworzyć ofertę oprogramowania jako usługi (SaaS), aby potencjalni klienci mogli zakupić rozwiązanie techniczne oparte na SaaS. W tym artykule opisano proces tworzenia oferty SaaS dla komercyjnego portalu Microsoft Marketplace.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli jeszcze tego nie zrobiono, zapoznaj się z artykułem [Planowanie oferty SaaS dla komercyjnej witryny Marketplace](plan-saas-offer.md). Wyjaśni wymagania techniczne dotyczące aplikacji SaaS oraz informacje i zasoby, które będą potrzebne podczas tworzenia oferty. Jeśli nie planujesz opublikowania prostej listy (**skontaktuj się ze mną** ) w komercyjnej witrynie Marketplace, Twoja aplikacja SaaS musi spełniać wymagania techniczne dotyczące uwierzytelniania.

> [!IMPORTANT]
> Zalecamy utworzenie oddzielnej oferty deweloperskiej/testowej (DEV) i oddzielną ofertę produkcyjną (PROD). W tym artykule opisano sposób tworzenia oferty PRODUKCYJNEj. Aby uzyskać szczegółowe informacje na temat tworzenia oferty DEWELOPERSKIej, zobacz [Tworzenie oferty tworzenia i testowania](create-saas-dev-test-offer.md).

## <a name="create-a-new-saas-offer"></a>Tworzenie nowej oferty SaaS

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
1. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .
1. Na karcie **Przegląd** wybierz pozycję **+ nowe oferowane**  >  **oprogramowanie jako usługa**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Ilustruje menu nawigacji po lewej stronie i nową listę oferty.":::

1. W oknie dialogowym **Nowa oferta** wprowadź **Identyfikator oferty**. Ten identyfikator jest widoczny w adresie URL aukcji komercyjnej witryny Marketplace i szablonów Azure Resource Manager, jeśli ma to zastosowanie. Jeśli na przykład w tym polu wprowadzisz wartość **test-Offer-1** , adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Każda oferta w Twoim koncie musi mieć unikatowy identyfikator oferty.
   + Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków.
   + Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

1. Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

   + Ta nazwa nie jest widoczna w komercyjnej witrynie Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
   + Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.
1. Aby wygenerować ofertę i kontynuować, wybierz pozycję **Utwórz**.

## <a name="configure-your-saas-offer-setup-details"></a>Skonfiguruj szczegóły konfiguracji oferty SaaS

Na karcie **Konfiguracja oferty** w obszarze **szczegóły instalacji** wybierz, czy chcesz sprzedawać swoją ofertę przez firmę Microsoft, czy zarządzać transakcjami niezależnie. Oferty sprzedawane za pośrednictwem firmy Microsoft są określane jako _oferty transakcyjne_, co oznacza, że firma Microsoft ułatwia wymianę pieniędzy na licencję na oprogramowanie w imieniu wydawcy. Aby uzyskać więcej informacji na temat tych opcji, zobacz temat [Opcje wyświetlania](plan-saas-offer.md#listing-options) i [określania opcji publikowania](determine-your-listing-type.md).

1. Aby sprzedawać transakcje w firmie Microsoft i korzystać z nich, wybierz opcję **tak**. Kontynuuj, aby [włączyć stację testową](#enable-a-test-drive-optional).

1. Aby wystawić swoją ofertę za pomocą komercyjnej witryny Marketplace i osobno Przetwarzaj transakcje, wybierz pozycję **nie**, a następnie wykonaj jedną z następujących czynności:
   + Aby zapewnić bezpłatną subskrypcję oferty, wybierz pozycję **Pobierz teraz (bezpłatnie)**. Następnie w wyświetlonym polu **adres URL oferty** wprowadź adres URL (zaczynający się od *http* lub *https*), w którym klienci mogą uzyskać próbę uwierzytelniania za pomocą [jednego kliknięcia przy użyciu Azure Active Directory (Azure AD)](azure-ad-saas.md). Na przykład `https://contoso.com/saas-app`.
   + Aby zapewnić 30-dniową bezpłatną wersję próbną, wybierz opcję **bezpłatna wersja próbna**, a następnie w wyświetlonym polu **adres URL wersji próbnej** wprowadź adres URL (zaczynający się od *http* lub *https*), w którym klienci mogą uzyskać dostęp do bezpłatnej wersji próbnej za pośrednictwem [uwierzytelniania przy użyciu usługi Azure Active Directory (Azure AD)](azure-ad-saas.md). Na przykład `https://contoso.com/trial/saas-app`.
   + Aby potencjalni klienci mogli skontaktować się z Tobą w celu zakupienia oferty, wybierz pozycję **skontaktuj się ze mną**.

### <a name="enable-a-test-drive-optional"></a>Włącz stację testową (opcjonalnie)

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, zapewniając im dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Oferowanie dysku testowego skutkuje zwiększoną szybkością konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](./what-is-test-drive.md)

> [!TIP]
> Stacja testowa jest różna od bezpłatnej wersji próbnej. Możesz zaoferować dysk testowy, bezpłatny okres próbny lub oba te elementy. Oba te osoby zapewniają klientom swoje rozwiązanie przez stały okres. Jednak dysk testowy zawiera również praktyczny przewodnik po najważniejszych funkcjach produktu i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

**Aby włączyć stację testową**
1.  W obszarze **testowanie dysku** zaznacz pole wyboru **Włącz dysk testowy** .
1.  Wybierz typ dysku testowego z wyświetlonej listy.

### <a name="configure-lead-management"></a>Konfigurowanie zarządzania potencjalnymi klientami

Połącz system zarządzania relacjami z klientami (CRM) z ofertą komercyjnej witryny Marketplace, aby otrzymywać informacje kontaktowe klienta, gdy klient wyraża zainteresowanie lub wdroży produkt. To połączenie można zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej.

> [!NOTE]
> Jeśli sprzedajesz ofertę przez firmę Microsoft lub wybrano opcję **kontakt ze mną** , musisz skonfigurować zarządzanie potencjalnymi klientami. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Aby skonfigurować szczegóły połączenia w centrum partnerskim

1.  W obszarze **potencjalni klienci** wybierz łącze **Połącz** .
1. W oknie dialogowym **szczegóły połączenia** wybierz lokalizację docelową potencjalnego klienta z listy.
1. Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Aby sprawdzić podaną konfigurację, wybierz link **Weryfikuj** .
1. Aby zamknąć okno dialogowe, wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować właściwości oferty SaaS](create-new-saas-offer-properties.md)
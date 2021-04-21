---
title: Utwórz ofertę usługi Dynamics 365 for Customer Engagement & PowerApps na Microsoft AppSource (Azure Marketplace).
description: Utwórz ofertę usługi Dynamics 365 for Customer Engagement & PowerApps na Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820508"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Jak utworzyć ofertę usługi Dynamics 365 for Customer Engagement & PowerApps

W tym artykule opisano sposób tworzenia oferty usługi Dynamics 365 for Customer Engagement & usługi PowerApps. Wszystkie oferty dotyczące usługi Dynamics 365 są w naszym procesie certyfikacji. Środowisko próbne umożliwia użytkownikom wdrażanie rozwiązania w środowisku usługi Dynamics 365 na żywo.

Przed rozpoczęciem utwórz konto platformy handlowej w [usłudze Partner Center](partner-center-portal/create-account.md) i upewnij się, że jest ono zarejestrowane w programie platformy handlowej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zapoznaj [się z tematem Planowanie oferty usługi Dynamics 365.](marketplace-dynamics-365.md) Objaśnia ona wymagania techniczne dotyczące tej oferty oraz zawiera listę informacji i zasobów, które będą potrzebne podczas jej tworzenia.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).
2. W menu nav po lewej stronie wybierz pozycję **Omówienie komercyjnej platformy**  >  **handlowej.**
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta** Dynamics  >  **365 for Customer Engagement & PowerApps.**

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Przedstawia opcje menu okienka po lewej stronie i przycisk &quot;Nowa oferta&quot; z wybraniem pozycji Customer Engagement.":::

> [!IMPORTANT]
> Po opublikowaniu oferty wszelkie zmiany, które w nim w nim w Partner Center pojawiają się Microsoft AppSource tylko po jej ponownej opublikowaniu. Pamiętaj, aby zawsze ponownie opublikować ofertę po jej zmianie.

## <a name="new-offer"></a>Nowa oferta

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty i w szablonach Azure Resource Manager, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Identyfikator może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczony do 50 znaków. Na przykład jeśli identyfikatorem wydawcy jest , a po wprowadzeniu ciągu `testpublisherid` **test-offer-1** adres internetowy oferty będzie miał wartość `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz.**

Wprowadź alias **oferty**. Jest to nazwa używana dla oferty w Partner Center.

- Ta nazwa nie jest używana w usłudze AppSource. Różni się ona od nazwy oferty i innych wartości widocznych dla klientów.
- Tej nazwy nie można zmienić po wybraniu opcji **Utwórz**.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę. Partner Center zostanie otwarta **strona Konfiguracja** oferty.

## <a name="alias"></a>Alias

Wprowadź opisową nazwę, która będzie używane do odwoływać się do tej oferty wyłącznie w Partner Center. Alias oferty (wstępnie wypełniony wartościami wprowadzonymi podczas tworzenia oferty) nie będzie używany na platformie handlowej i jest inny niż nazwa oferty wyświetlana klientom. Jeśli chcesz później zaktualizować nazwę oferty, zobacz stronę [Oferty.](dynamics-365-customer-engage-offer-listing.md)

## <a name="setup-details"></a>Szczegóły konfiguracji

W **pozycji Jak chcesz, aby potencjalni** klienci wchodzili w interakcje z tą ofertą? wybierz opcję, której chcesz użyć dla tej oferty.

- **Pobierz teraz (bezpłatnie)** — bezpłatnie wyekscyj swoją ofertę dla klientów.
- **Bezpłatna wersja próbna (lista)** — lista oferty dla klientów z linkiem do bezpłatnej wersji próbnej. Bezpłatne wersje próbne oferty są tworzone, zarządzane i konfigurowane przez usługę, a subskrypcje nie są zarządzane przez firmę Microsoft.

    > [!NOTE]
    > Tokenów, które aplikacja otrzyma za pośrednictwem linku wersji próbnej, można użyć tylko do uzyskania informacji o użytkowniku za pośrednictwem usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia konta w aplikacji. Konta Microsoft nie są obsługiwane w przypadku uwierzytelniania przy użyciu tego tokenu.

- **Skontaktuj się ze mną** — zbieraj informacje kontaktowe klientów, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnianie swoich informacji. Te szczegóły klienta wraz z nazwą oferty, identyfikatorem i źródłem witryny Marketplace, w którym znalazła Ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji na temat konfigurowania systemu CRM, zobacz [Customer leads (Potencjalni klienci).](#customer-leads)

## <a name="test-drive"></a>Dysk testowy

Dysk testowy to doskonały sposób prezentowania oferty potencjalnym klientom przez udzielenie im dostępu do wstępnie skonfigurowanego środowiska przez stałą liczbę godzin. Oferowanie dysku testowego powoduje zwiększenie współczynnika konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej, zacznij od [tematu Co to jest dysk testowy?](what-is-test-drive.md).

> [!TIP]
> Wersja testowa różni się od bezpłatnej wersji próbnej. Możesz zaoferować wersję testową, bezpłatną wersję próbną lub obie te wersje. Oba te rozwiązania zapewniają klientom Twoje rozwiązanie na stały okres. Jednak test obejmuje również praktyczne, samodzielne przewodnik po kluczowych funkcjach i korzyściach produktu, które są demonstrowane w rzeczywistym scenariuszu implementacji.

Aby włączyć dysk testowy, zaznacz **pole wyboru Włącz dysk testowy** i wybierz typ dysku **testowego.** Później skonfigurujesz dysk testowy. W przypadku wersji testowej należy również skonfigurować ofertę dla systemu CRM dla potencjalnych klientów (zobacz następną sekcję). Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

## <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz Customer leads from your commercial marketplace offer (Potencjalni klienci [z oferty platformy handlowej).](partner-center-portal/commercial-marketplace-get-customer-leads.md)

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem do następnej karty w menu nav po lewej **stronie, Właściwości.**

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie właściwości oferty](dynamics-365-customer-engage-properties.md)
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
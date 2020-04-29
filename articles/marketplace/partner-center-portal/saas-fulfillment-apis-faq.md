---
title: Interfejsy API realizacji SaaS — często zadawane pytania | Portal Azure Marketplace
description: Funkcje odnajdywania i kupowania oferowane przez klientów oferty SaaS w portalu Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275700"
---
# <a name="saas-fulfillment-apis---faq"></a>Interfejsy API realizacji SaaS — często zadawane pytania

Wymagania dotyczące integracji z portalem Azure Marketplace umożliwiają klientom platformy Azure subskrybowanie oferty SaaS.

## <a name="discovery-experience"></a>Środowisko odnajdywania

Po opublikowaniu oferty Użytkownicy platformy Azure będą mogli odnaleźć ofertę SaaS w portalu Azure Marketplace. Klienci będą mogli filtrować oferty na podstawie typu produktu (SaaS) i odnajdywania interesujących Cię usług SaaS.

## <a name="purchase-experience"></a>Zakup — środowisko

Gdy użytkownik interesuje konkretną usługę SaaS, użytkownik może subskrybować go z poziomu portalu Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co oznacza użytkownik platformy Azure, aby subskrybować ofertę SaaS w portalu Azure Marketplace?

Oznacza to, że użytkownik może wyświetlić warunki użytkowania i zasady zachowania poufności informacji powiązane z usługą SaaS, a następnie wyrazić zgodę na płatność zgodnie z warunkami rozliczeniowymi ustawionymi przez użytkownika, wydawcy oferty SaaS na fakturze firmy Microsoft. Użytkownicy mogą korzystać z istniejącego profilu płatności na platformie Azure, aby zapłacić za użycie usługi SaaS.

Jest to korzystne z wielu powodów. Klienci mogą teraz odkrywać i subskrybować jedno miejsce przy użyciu Microsoft Cloud platformy jako zaufanego źródła, bez konieczności Zweryfikuj każdego oprogramowania niezależnego od dostawcy. Ponadto klienci mogą korzystać z istniejącego profilu płatniczego bez konieczności jawnego płacenia każdego oprogramowania niezależnego od dostawcy.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Czy użytkownik jest obciążany automatycznie po zasubskrybowaniu oferty?

Podczas subskrybowania oferty SaaS użytkownik wyraził zgodę na płatność za użycie usługi SaaS za pomocą platformy firmy Microsoft. Jednak opłaty będą naliczane dopiero po wykorzystaniu oferty. Użytkownik musi przejść do oferty SaaS i potwierdzić utworzenie konta, aby rozpocząć korzystanie z oferty. Następnie powiadomimy firmę Microsoft o konieczności rozpoczęcia rozliczeń dla tej subskrypcji SaaS klienta.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Jak otrzymasz powiadomienie, gdy użytkownik zasubskrybuje ofertę usługi SaaS?

Po zasubskrybowaniu oferty użytkownik platformy Azure może odnaleźć wszystkie oferty na platformie Azure i zarządzać nimi. Domyślnie stan nowo zasubskrybowanej oferty SaaS to **"Provisioning, oczekiwanie na realizację"**. W tym stanie użytkownikowi systemu Azure zostanie wyświetlony monit z akcją **"Skonfiguruj konto"**, aby przejść do środowiska zarządzania subskrypcją usługi SaaS w Azure Portal.

Gdy użytkownik kliknie przycisk **"Konfiguruj konto"**, zostanie przekierowany do witryny sieci Web usługi SaaS. Adres URL, do którego się odnosi, jest dostarczany przez wydawcę w momencie opublikowania oferty. Ta strona jest nazywana stroną docelową wydawcy. Użytkownicy platformy Azure powinni mieć możliwość zalogowania się na SaaS stronie docelowej na podstawie istniejących poświadczeń usługi AAD na platformie Azure.

Gdy użytkownik platformy Azure zostanie przekierowany do strony docelowej, zostanie dodany token do adresu URL zapytania. Token ten jest krótko i ważny przez okres 24 godzin. Następnie możesz wykryć obecność tego tokenu i wywoływać interfejs API firmy Microsoft, aby uzyskać więcej kontekstu skojarzonego z tokenem.

![Przepływ subskrypcji klienta](media/saas-metering-service-integration-flow-a.png)

Zobacz dokument [interfejsu API realizacji SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) , aby uzyskać więcej informacji na temat kontraktu interfejsu API na potrzeby obsługi scenariuszy Transact w cyklu życia oferty SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Jak poznać ofertę SaaS, do której użytkownik subskrybuje platformę Azure?

Odpowiedź do `Resolve` interfejsu API zawiera informacje o ofercie i planie powiązane z subskrypcją usługi SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak użytkownik platformy Azure może zmienić plan skojarzony z tą subskrypcją platformy Azure?

* Użytkownik platformy Azure może zmienić plan skojarzony z subskrypcją SaaS bezpośrednio w środowisku SaaS lub za pomocą platformy firmy Microsoft.

* Konwersje można wykonać w dowolnym momencie w cyklu rozliczeniowym. Musisz potwierdzić każdą konwersję, która zacznie obowiązywać po potwierdzeniu.

* Stawki za plan przedpłaty (**miesięczny** lub **roczny**) są oceniane proporcjonalnie. Wszelkie nadwyżki wyemitowane do czasu konwersji zostaną rozliczone na następnej fakturze. Nowe nadwyżki będą emitowane na podstawie nowego planu.

>[!Note]
>Można zablokować obniżanie poziomu, jeśli nie chcesz obsługiwać określonych ścieżek konwersji.

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure zmieni plan w środowisku SaaS:

![Przepływ zmian planu klienta](media/saas-metering-service-integration-flow-b.png)

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure zmieni plan w witrynie sklepu Microsoft

![Przepływ zmian planu w sklepie dla klientów](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Jak użytkownik platformy Azure może anulować subskrypcję planu skojarzonego z subskrypcją platformy Azure?

Użytkownik platformy Azure może anulować subskrypcję zakupionej oferty SaaS bezpośrednio w środowisku SaaS lub za pośrednictwem platformy firmy Microsoft. Gdy użytkownik anuluje subskrypcję, nie będzie już naliczana opłata od następnego cyklu rozliczeniowego.

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure anuluje subskrypcję oferty SaaS w środowisku SaaS:

![Klient anulował subskrypcję w środowisku SaaS](media/saas-metering-service-integration-flow-d.png)

Sekwencja poniżej przechwytuje przepływ, gdy użytkownik platformy Azure anuluje subskrypcję w witrynie sklepu Microsoft:

![Klient anulował subskrypcję w witrynie Microsoft w sklepie](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [interfejsy API usługi pomiaru witryny Marketplace](./marketplace-metering-service-apis.md) .

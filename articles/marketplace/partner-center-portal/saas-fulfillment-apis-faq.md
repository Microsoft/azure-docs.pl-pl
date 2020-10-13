---
title: Interfejs API realizacji SaaS — często zadawane pytania — komercyjne witryny Marketplace firmy Microsoft
description: Poznaj kilka wymagań dotyczących integracji dla komercyjnej witryny Marketplace firmy Microsoft, aby umożliwić klientom platformy Azure subskrybowanie ofert SaaS.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4c5d8b438764fa9aa3838b2225c63d412afc519b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606812"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Często zadawane pytania dotyczące interfejsów API realizacji SaaS

W tym artykule opisano kilka wymagań dotyczących integracji dla komercyjnej witryny Marketplace firmy Microsoft, aby umożliwić klientom platformy Azure subskrybowanie ofert SaaS.

## <a name="discovery-experience"></a>Środowisko odnajdywania

Po opublikowaniu oferty SaaS Użytkownicy platformy Azure będą mogli ją odnaleźć w witrynie Azure Marketplace. Klienci mogą filtrować oferty w oparciu o typ produktu (SaaS) i odkrywać interesujące Cię usługi SaaS.

## <a name="purchase-experience"></a>Zakup — środowisko

Gdy użytkownik interesuje konkretną usługę SaaS, użytkownik może subskrybować go z poziomu portalu Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co oznacza użytkownik platformy Azure, aby subskrybować ofertę SaaS w portalu Azure Marketplace?

Oznacza to, że użytkownik może wyświetlić warunki użytkowania i zasady zachowania poufności informacji powiązane z usługą SaaS, a następnie wyrazić zgodę na płatność zgodnie z warunkami rozliczeniowymi ustawionymi przez użytkownika, wydawcy oferty SaaS na fakturze firmy Microsoft. Użytkownicy mogą korzystać z istniejącego profilu płatności na platformie Azure, aby zapłacić za użycie usługi SaaS.

Ta możliwość jest korzystna na kilka sposobów. Klienci mogą teraz odkrywać i subskrybować jedno miejsce przy użyciu Microsoft Cloud platformy jako zaufanego źródła, bez konieczności Zweryfikuj każdego oprogramowania niezależnego od dostawcy. Ponadto klienci mogą korzystać z istniejącego profilu płatniczego bez konieczności jawnego płacenia każdego oprogramowania niezależnego od dostawcy.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Czy użytkownik jest obciążany automatycznie po zasubskrybowaniu oferty?

Podczas subskrybowania oferty SaaS użytkownik wyraził zgodę na płatność za użycie usługi SaaS za pomocą platformy firmy Microsoft. Jednak opłaty będą naliczane dopiero po wykorzystaniu oferty. Użytkownik musi przejść do oferty SaaS i potwierdzić utworzenie konta, aby rozpocząć korzystanie z oferty. Następnie powiadomimy firmę Microsoft o konieczności rozpoczęcia rozliczeń dla tej subskrypcji SaaS klienta.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Jak otrzymasz powiadomienie, gdy użytkownik zasubskrybuje ofertę usługi SaaS?

Po zasubskrybowaniu oferty użytkownik platformy Azure może odnaleźć wszystkie oferty na platformie Azure i zarządzać nimi. Domyślnie stan nowo zasubskrybowanej oferty SaaS jest pokazywany jako **Inicjowanie obsługi administracyjnej**. W tym stanie użytkownik platformy Azure zostanie monitowany z akcją w celu **skonfigurowania konta**, aby przejść do środowiska zarządzania subskrypcją usługi SaaS w Azure Portal.

Gdy użytkownik wybierze opcję **Skonfiguruj konto**, zostanie przekierowany do witryny sieci Web usługi SaaS. Wydawca skonfigurował adres URL w momencie opublikowania oferty. Ta strona jest nazywana stroną docelową wydawcy. Użytkownicy platformy Azure logują się na stronie docelowej SaaS na podstawie istniejących poświadczeń usługi AAD na platformie Azure.

Gdy użytkownik platformy Azure zostanie przekierowany do strony docelowej, zostanie dodany token do adresu URL zapytania. Token ten jest krótkoterminowy i ważny przez 24 godziny. Następnie możesz wykryć obecność tego tokenu i wywoływać interfejs API firmy Microsoft, aby uzyskać więcej kontekstu skojarzonego z tokenem.

![Przepływ subskrypcji klienta](media/saas-metering-service-integration-flow-a.png)

Aby uzyskać więcej informacji na temat kontraktu interfejsu API do obsługi scenariuszy Transact w cyklu życia oferty SaaS, zobacz [SaaS API](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Jak poznać ofertę SaaS, do której użytkownik subskrybuje platformę Azure?

Odpowiedź do `Resolve` interfejsu API zawiera informacje o ofercie i planie powiązane z subskrypcją usługi SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak użytkownik platformy Azure może zmienić plan skojarzony z tą subskrypcją platformy Azure?

* Użytkownik platformy Azure może zmienić plan skojarzony z subskrypcją SaaS bezpośrednio w środowisku SaaS lub za pomocą platformy firmy Microsoft.

* Konwersje można wykonać w dowolnym momencie w cyklu rozliczeniowym. Zostanie wyświetlony monit o potwierdzenie konwersji, która zacznie obowiązywać po potwierdzeniu.

* Stawki za plan przedpłaty (**miesięczny** lub **roczny**) są oceniane proporcjonalnie. Wszelkie nadwyżki wyemitowane do czasu konwersji zostaną rozliczone na następnej fakturze. Nowe nadwyżki będą emitowane na podstawie nowego planu.

>[!Note]
>Można zablokować obniżanie poziomu, jeśli nie chcesz obsługiwać określonych ścieżek konwersji.

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure zmieni plan w środowisku SaaS:

![Przepływ zmian planu klienta](media/saas-metering-service-integration-flow-b.png)

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure zmieni plan w sklepie online firmy Microsoft:

![Przepływ zmian planu sklepu online Customer](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Jak użytkownik platformy Azure może anulować subskrypcję planu skojarzonego z subskrypcją platformy Azure?

Użytkownik platformy Azure może anulować subskrypcję zakupionej oferty SaaS bezpośrednio w środowisku SaaS lub za pośrednictwem platformy firmy Microsoft. Gdy użytkownik anuluje subskrypcję, nie będzie już naliczana opłata od następnego cyklu rozliczeniowego.

Sekwencja poniżej przechwytuje przepływ, gdy klient platformy Azure anuluje subskrypcję oferty SaaS w środowisku SaaS:

![Klient anulował subskrypcję w środowisku SaaS](media/saas-metering-service-integration-flow-d.png)

Sekwencja poniżej przechwytuje przepływ, gdy użytkownik platformy Azure anuluje subskrypcję w sklepie online firmy Microsoft:

![Klient anulował subskrypcję w Sklepie Microsoft Online](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Następne kroki

[Interfejsy API usługi pomiaru w witrynie Marketplace](./marketplace-metering-service-apis.md)

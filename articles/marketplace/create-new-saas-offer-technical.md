---
title: Jak dodać szczegóły techniczne dla oferty usługi SaaS w centrum partnerskim firmy Microsoft
description: Dowiedz się, jak dostarczać szczegółowe informacje techniczne dotyczące oferty oprogramowania jako usługi (SaaS) w portalu komercyjnym dla firmy Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 78a81a2d392363ac45979fd28138f838653a2358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380972"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Jak dodać szczegóły techniczne dla oferty SaaS

W tym artykule opisano, jak wprowadzać szczegóły techniczne, które ułatwiają firmie Microsoft komercyjne nawiązywanie połączenia z rozwiązaniem. To połączenie umożliwia nam zainicjowanie oferty dla klienta, jeśli zdecydują się ją nabyć i zarządzać. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Informacje techniczne](plan-saas-offer.md#technical-information).

> [!NOTE]
> Jeśli zdecydujesz się na przetwarzanie transakcji niezależnie, ta opcja nie zostanie wyświetlona. Zamiast tego przejdź do [sposobu wprowadzenia na rynek oferty SaaS](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Konfiguracja techniczna

Na karcie **konfiguracja techniczna** określisz szczegóły techniczne używane przez komercyjne witryny Marketplace do komunikowania się z Twoją aplikacją lub rozwiązaniem SaaS. 

- **Adres URL strony docelowej** (wymagany) — Zdefiniuj adres URL witryny sieci Web SaaS (na przykład: `https://contoso.com/signup` ), do której klienci będą musieli skorzystać po uzyskaniu oferty od komercyjnego rynku i wyzwalając proces konfiguracji od nowo utworzonej subskrypcji SaaS.

  > [!IMPORTANT]
  > Strona docelowa powinna być uruchomiona 24/7. Jest to jedyny sposób, w jaki otrzymasz powiadomienie o nowych zakupach ofert SaaS w portalu komercyjnym lub żądaniach konfiguracji aktywnej subskrypcji oferty.

- **Element webhook połączenia** (wymagany) — dla wszystkich zdarzeń asynchronicznych, które firma Microsoft musi wysłać do Ciebie (na przykład subskrypcja SaaS została anulowana), wymagane jest podanie adresu URL elementu webhook połączenia. Ten adres URL zostanie wywołany w celu powiadomienia użytkownika o zdarzeniu.

  > [!IMPORTANT]
  > Element webhook powinien działać w trybie 24/7, ponieważ jest to jedyny sposób powiadomienia o aktualizacjach subskrypcji SaaS klientów kupowanych za pośrednictwem portalu Comercial.

- **Azure Active Directory identyfikator dzierżawy** (wymagane) — aby znaleźć identyfikator dzierżawy dla aplikacji Azure Active Directory (Azure AD), przejdź do bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację. Następnie poszukaj podanego numeru **identyfikatora dzierżawy** (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory identyfikator aplikacji** (wymagane) — aby znaleźć [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), przejdź do bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację. Następnie wyszukaj numer IDENTYFIKACYJNy aplikacji (klienta) na liście (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: przegląd planu.

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć plany dla oferty usługi SaaS](create-new-saas-offer-plans.md).

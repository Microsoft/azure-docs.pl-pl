---
title: Wyświetlanie kont rozliczeniowych w witrynie Azure Portal
description: Dowiedz się, jak wyświetlać konta rozliczeniowe w witrynie Azure Portal. Wyświetl informacje o zakresie dotyczące umowy Enterprise Agreement, Umowy z Klientem Microsoft i umowy Microsoft Partner Agreement.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 42d9c2b4b1edac62449fa4120463a384400d6d27
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360700"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Konta i zakresy rozliczeniowe w witrynie Azure Portal

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe.

W witrynie Azure Portal są obsługiwane następujące typy kont rozliczeniowych:

- **Microsoft Online Services Program**: Konto rozliczeniowe dla programu Microsoft Online Services Program jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Umowa Enterprise Agreement**: Konto rozliczeniowe dla umowy Enterprise Agreement jest tworzone, gdy organizacja podpisuje [umowę Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) w celu korzystania z platformy Azure. W umowie Enterprise Agreement możesz mieć maksymalnie 2000 subskrypcji.

- **Umowa klienta firmy Microsoft**: Konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe do umowy klienta firmy Microsoft. W Umowie z Klientem Microsoft możesz mieć maksymalnie 20 subskrypcji. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy klienta firmy Microsoft](../understand/mca-overview.md).

- **Umowa partnerska firmy Microsoft**: Konto rozliczeniowe dla umowy partnerskiej firmy Microsoft jest tworzone dla dostawców rozwiązań w chmurze (CSP) w celu zarządzania ich klientami w nowym środowisku handlowym. Partnerzy muszą mieć co najmniej jednego klienta z [planem platformy Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan), aby mogli zarządzać kontem rozliczeniowym w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontem rozliczeniowym do umowy partnerskiej firmy Microsoft](../understand/mpa-overview.md).

Aby określić typ swojego konta rozliczeniowego, zobacz [Sprawdzanie typu konta rozliczeniowego](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł w ramach konta rozliczeniowego umożliwiający wyświetlanie rozliczeń i zarządzanie nimi. Jest to miejsce, w którym zarządzasz danymi rozliczeniowymi, płatnościami i fakturami oraz przeprowadzasz ogólne czynności dotyczące zarządzania kontami.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

![Zrzut ekranu przedstawiający hierarchię programu MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje umowę, w której klient zgadza się na korzystanie z platformy Azure. Zawiera co najmniej jedną subskrypcję.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W tym zakresie jest generowana faktura. Z tym zakresem są skojarzone pozostałe informacje dotyczące rozliczeń, takie jak formy płatności i adres użycia.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Zrzut ekranu przedstawiający hierarchię umowy EA](./media/view-all-accounts/ea-hierarchy.png)

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. Zawiera co najmniej jeden dział i konto. W tym zakresie jest generowana faktura. |
|Dział     |  Opcjonalna grupa kont umożliwiająca podział kosztów na grupy logiczne i ustawianie budżetu.     |
|Konto     |  Reprezentuje pojedynczego właściciela konta. Właściciele kont mają uprawnienia do tworzenia subskrypcji platformy Azure, które są rozliczane w ramach rejestracji, i zarządzania nimi. |

### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

![Zrzut ekranu przedstawiający hierarchię umowy MCA](./media/view-all-accounts/mca-hierarchy.png)

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę, w której klient zgadza się na korzystanie z produktów i usług firmy Microsoft. Zawiera co najmniej jeden profil rozliczeniowy. |
|Profil rozliczeniowy     |   Reprezentuje fakturę i powiązane informacje rozliczeniowe, takie jak formy płatności i adres rozliczeniowy. Zawiera co najmniej jedną sekcję faktury. |
|Sekcja faktury     |   Reprezentuje grupę kosztów na fakturze. Z tym zakresem są skojarzone subskrypcje platformy Azure i inne zakupy, takie jak Azure Marketplace i produkty App Source.    |

### <a name="microsoft-partner-agreement"></a>Umowa partnerska firmy Microsoft

![Zrzut ekranu przedstawiający hierarchię umowy MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę partnerską umożliwiającą zarządzanie produktami i usługami firmy Microsoft klientów w nowym środowisku handlowym. Zawiera co najmniej jeden profil rozliczeniowy i klienta.   |
|Profil rozliczeniowy     |   Reprezentuje fakturę dla waluty.     |
|Klient    |   Reprezentuje klienta dla dostawcy rozwiązań w chmurze (CSP).  Z tym zakresem są skojarzone subskrypcje platformy Azure i inne zakupy, takie jak Azure Marketplace i produkty App Source.  |
|Odsprzedawca    |   Odsprzedawca, który świadczy usługi dla klienta. To jest pole opcjonalne dla subskrypcji, które ma zastosowanie tylko do dostawców pośrednich w dwuwarstwowym modelu usług CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Przełączanie zakresu rozliczeniowego w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie narzędzia Cost Management + Billing w witrynie Azure Portal.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na stronie przeglądu wybierz pozycję **Przełącz zakres**.

   ![Zrzut ekranu przedstawiający zakresy rozliczeniowe.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Pozycja Przełącz zakres nie jest widoczna, jeśli masz dostęp tylko do jednego zakresu.

4. Wybierz zakres, aby wyświetlić szczegóły.

   ![Zrzut ekranu przedstawiający zakresy rozliczeniowe, które można wybrać, aby uzyskać szczegółowe informacje.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Sprawdzanie typu konta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak rozpocząć [analizowanie kosztów](../costs/quick-acm-cost-analysis.md).

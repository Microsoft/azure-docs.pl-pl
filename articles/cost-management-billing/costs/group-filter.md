---
title: Opcje grupowania i filtrowania w usłudze Azure Cost Management
description: W tym artykule wyjaśniono, jak używać opcji grupowania i filtrowania w usłudze Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: ac9828ca61009eb3ee39412169b2b454b9ecbd00
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131874"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Opcje grupowania i filtrowania w analizie kosztów

Analiza kosztów ma wiele opcji grupowania i filtrowania. Ten artykuł pomaga zrozumieć, kiedy należy ich używać.

Aby obejrzeć wideo na temat opcji grupowania i filtrowania, zobacz [Cost Management reporting by dimensions and tags](https://www.youtube.com/watch?v=2Vx7V17zbmk) (Raportowanie według wymiarów i tagów w usłudze Cost Management). Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Właściwości grupowania i filtrowania

W poniższej tabeli wymieniono niektóre z najpopularniejszych opcji grupowania i filtrowania dostępnych w analizie kosztów oraz ich przeznaczenie.

| Właściwość | Kiedy stosować | Uwagi |
| --- | --- | --- |
| **Strefy dostępności** | Podział kosztów platformy AWS według strefy dostępności. | Dotyczy tylko zakresów platformy AWS i grup zarządzania. Dane platformy Azure nie zawierają strefy dostępności i będą wyświetlane jako **Nie dotyczy**. |
| **Okres rozliczeniowy** | Podział kosztów płatności zgodnie z rzeczywistym użyciem według miesiąca, w którym zostały (lub zostaną) zafakturowane. | Aby uzyskać dokładną reprezentację zafakturowanych płatności zgodnie z rzeczywistym użyciem, użyj właściwości **Okres rozliczeniowy**. W przypadku filtrowania do niestandardowego zakresu dat uwzględnij 2 dodatkowe dni przed okresem rozliczeniowym i po nim. Ograniczenie do dokładnych dat okresu rozliczeniowego nie będzie pasować do faktury. Zostaną wyświetlone koszty ze wszystkich faktur w okresie rozliczeniowym. Aby filtrować do określonej faktury, użyj właściwości **Identyfikator faktury**. Dotyczy tylko subskrypcji płatnych zgodnie z rzeczywistym użyciem, ponieważ umowy EA i umowy klienta Microsoft (MCA) są rozliczane według miesięcy kalendarzowych. Konta umów EA/MCA mogą używać miesięcy kalendarzowych w selektorze daty lub miesięcznego stopnia szczegółowości do osiągnięcia tego samego celu. |
| **Typ opłaty** | Podział kosztów użycia, zakupów, zwrotów i niewykorzystanych rezerwacji. | Zakupy rezerwacji i zwroty są dostępne tylko w przypadku korzystania z kosztów rzeczywistych, a nie w przypadku korzystania z kosztów zamortyzowanych. Koszty niewykorzystanych rezerwacji są dostępne tylko podczas przeglądania kosztów zamortyzowanych. |
| **Dział** | Podział kosztów według działów EA. | Dostępne tylko w przypadku umów EA i grup zarządzania. Subskrypcje płatne zgodnie z rzeczywistym użyciem nie mają działu i będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Konto rejestracji** | Podział kosztów według właściciela konta EA. | Dostępne tylko w przypadku kont rozliczeniowych, działów i grup zarządzania umowy EA. Subskrypcje płatne zgodnie z rzeczywistym użyciem nie mają kont rejestracji umowy EA i będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Częstotliwość** | Podział kosztów opartych na użyciu, jednorazowych i cyklicznych. | |
| **Identyfikator faktury** | Podział kosztów według naliczonej faktury. | Nienaliczone opłaty nie mają jeszcze identyfikatora faktury, a koszty umowy EA nie zawierają szczegółów faktury i są wyświetlane jako **Nie dotyczy**.  |
| **Miernik** | Podział kosztów według miernika użycia. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy**. Zapoznaj się z właściwością **Typ opłaty**, aby zidentyfikować zakupy, oraz **Typ wydawcy**, aby zidentyfikować opłaty z witryny Marketplace. |
| **Operacja** | Podział kosztów platformy AWS według operacji. | Dotyczy tylko zakresów platformy AWS i grup zarządzania. Dane platformy Azure nie zawierają operacji i będą wyświetlane jako **Nie dotyczy**. Zamiast nich użyj właściwości **Miernik**. |
| **Model cen** | Podział kosztów według rodzaju: na żądanie, rezerwacja lub typu spot. | Zakupy są wyświetlane jako **Na żądanie**. Jeśli widoczny jest komunikat **Nie dotyczy**, grupuj według **rezerwacji**, aby ustalić, czy użycie jest rezerwacją, czy użyciem na żądanie, oraz **typu opłaty** w celu zidentyfikowania zakupów.
| **Dostawca** | Podział kosztów według platformy AWS i platformy Azure. | Dostępne tylko w przypadku grup zarządzania. |
| **Typ wydawcy** | Podział kosztów platformy AWS, platformy Azure i witryny Marketplace. |  |
| **Rezerwacja** | Podział kosztów według rezerwacji. | Każde użycie lub wszystkie zakupy, które nie są skojarzone z rezerwacją, będą wyświetlane jako **Nie dotyczy**. Grupuj według **typu wydawcy**, aby zidentyfikować inne zakupy na platformie Azure, na platformie AWS lub w witrynie Marketplace. |
| **Zasób** | Podział kosztów według zasobu. | Zakupy są wyświetlane jako **Nie dotyczy**, ponieważ są stosowane na koncie rozliczeniowym umowy EA/płatności zgodnie z rzeczywistym użyciem lub na poziomie profilu rozliczeniowego umowy MCA i nie są skojarzone z określonym zasobem. Grupuj według **typu wydawcy**, aby zidentyfikować inne zakupy na platformie Azure, na platformie AWS lub w witrynie Marketplace. |
| **Grupa zasobów** | Podział kosztów według grupy zasobów. | Zakupy, zasoby dzierżawy nieskojarzone z subskrypcjami, zasoby subskrypcji niewdrożone w grupie zasobów i zasoby klasyczne nie mają grupy zasobów i będą wyświetlane jako **inne**, **usługi klasyczne**, **$system** lub **Nie dotyczy**. |
| **Typ zasobu** | Podział kosztów według typu zasobu. | Zakupy i usługi klasyczne nie mają typu zasobu usługi Azure Resource Manager i będą wyświetlane jako **inne**, **usługi klasyczne** lub **Nie dotyczy**. |
| **Lokalizacja zasobu** | Podział kosztów według lokalizacji lub regionu. | Zakupy i użycie witryny Marketplace mogą być wyświetlane jako **nieprzypisane**, **nieznane**, **niezamapowane** lub **Nie dotyczy**. |
| **Nazwa usługi** lub **Kategoria miernika** | Podział kosztów według usługi platformy Azure. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Warstwa usługi** lub **Podkategoria miernika** | Podział kosztów według podklasyfikacji miernika użycia platformy Azure. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Subskrypcja** | Podział kosztów według subskrypcji platformy Azure i połączonego konta platformy AWS. | Zakupy i zasoby dzierżawy mogą być wyświetlane jako **Nie dotyczy**. |
| **Tag** | Podział kosztów według wartości tagów dla określonego klucza tagu. | Tagi są niedostępne w przypadku zakupów, zasobów dzierżawy nieskojarzonych z subskrypcjami, zasobów subskrypcji niewdrożonych w grupie zasobów lub zasobów klasycznych. Niektóre usługi nie zawierają tagów w danych użycia. Dowiedz się więcej na temat [obsługi tagów dla każdego typu zasobu](../../azure-resource-manager/management/tag-support.md). |

Aby uzyskać więcej informacji na temat terminów, zobacz [Omówienie terminów używanych w pliku użycia i opłat platformy Azure](../understand/understand-usage.md).

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie analizy kosztów](./quick-acm-cost-analysis.md).
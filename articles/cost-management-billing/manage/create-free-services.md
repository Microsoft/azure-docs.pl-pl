---
title: Tworzenie bezpłatnych usług przy użyciu bezpłatnego konta platformy Azure
description: Dowiedz się, jak tworzyć usługi uwzględnione w ramach bezpłatnego konta platformy Azure. Możesz utworzyć te usługi w dowolnym regionie, w którym są dostępne.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: b475990a6b079de5e9ff01e52c2135f3c99a4f2f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055641"
---
# <a name="create-services-included-with-azure-free-account"></a>Tworzenie usług uwzględnionych w ramach bezpłatnego konta platformy Azure

W ciągu pierwszych 30 dni po utworzeniu bezpłatnego konta platformy Azure w walucie rozliczeniowej można korzystać z 200 USD, z wyjątkiem zakupów w portalu Marketplace innych firm. Korzystając z bezpłatnych środków, możesz poeksperymentować z różnymi warstwami i typami usług platformy Azure, aby wypróbować platformę Azure. Jeśli w tym okresie skorzystasz z usług lub zasobów platformy Azure, które nie są bezpłatne, z Twoich środków zostaną potrącone opłaty.

Jeśli nie wykorzystasz wszystkich środków przed upływem pierwszych 30 dni, zostaną one utracone. Po upływie pierwszych 30 dni w ciągu 12 miesięcy od zarejestrowania możesz skorzystać tylko z ograniczonej liczby *niektórych usług* — nie wszystkie usługi platformy Azure są bezpłatne. Jeśli przeprowadzisz uaktualnienie przed upływem 30 dni i zostaną Ci jakieś środki, przez pozostałe dni możesz wykorzystać je na subskrypcję płatną zgodnie z rzeczywistym użyciem. Na przykład jeśli utworzysz bezpłatne konto 1 listopada i uaktualnisz je 5 listopada, to do 30 listopada możesz wykorzystać posiadane środki w ramach nowej subskrypcji płatnej zgodnie z rzeczywistym użyciem. 

Bezpłatne konto platformy Azure obejmuje *określoną liczbę* bezpłatnych usług przez 12 miesięcy oraz zestaw usług, które są zawsze bezpłatne. Tylko niektóre warstwy usług są dostępne bezpłatnie w ramach określonych ilości. Na przykład platforma Azure ma wiele maszyn wirtualnych przeznaczonych do różnych celów. Bezpłatne konto obejmuje bezpłatny dostęp tylko do jednego typu maszyny wirtualnej — B1S z serii B z możliwością zwiększania szybkości do wykorzystania przez maksymalnie 750 godzin miesięcznie. W ramach limitów bezpłatnego konta możesz korzystać z bezpłatnych usług w różnych konfiguracjach. Aby uzyskać więcej informacji na temat bezpłatnego konta platformy Azure i produktów, które są dostępne bezpłatnie, zobacz [Bezpłatne konto Azure — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq/).

## <a name="create-free-services-in-the-azure-portal"></a>Tworzenie bezpłatnych usług w witrynie Azure Portal

Bezpłatne usługi zalecamy tworzyć przy użyciu [strony bezpłatnych usług](https://go.microsoft.com/fwlink/?linkid=859151) w witrynie Azure Portal. Możesz też zalogować się do witryny [Azure Portal](https://portal.azure.com) i wyszukać **bezpłatne usługi**. Jeśli tworzysz zasoby poza stronami bezpłatnych usług, opcje konfiguracji bezpłatnych warstw lub bezpłatnych konfiguracji zasobów nie zawsze są domyślnie zaznaczone. Aby uniknąć naliczania opłat, upewnij się, że tworzysz zasoby na stronie bezpłatnych usług. Następnie po utworzeniu zasobu pamiętaj o wybraniu warstwy, która jest bezpłatna.

![Zrzut ekranu przedstawiający stronę bezpłatnych usług](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>Usługi można tworzyć w dowolnym regionie

O ile nie przekraczasz limitów, możesz bezpłatnie tworzyć usługi w dowolnym regionie, w którym te usługi są dostępne. Na przykład mając bezpłatne konto platformy Azure, możesz co miesiąc skorzystać z bezpłatnych 750 godzin maszyny wirtualnej B1S z systemem Windows. Tę maszynę wirtualną możesz utworzyć w dowolnym regionie, w którym są dostępne maszyny wirtualne z serii B. Opłaty nie są naliczane za platformę Azure, dopóki nie zostanie przekroczony limit 750 godzin. Na przykład klient ze Stanów Zjednoczonych może zaaprowizować maszynę wirtualną B1S z systemem Windows w Europie Zachodniej i bezpłatnie korzystać z niej przez 750 godzin.

Aby dowiedzieć się więcej o dostępności usług platformy Azure w konkretnym regionie, zobacz [Dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).

## <a name="create-multiple-service-instances-in-allowed-limits"></a>Tworzenie wielu wystąpień usług w ramach dozwolonych limitów

Możesz bezpłatnie utworzyć wiele wystąpień usług, jeśli ich łączne użycie mieści się w limicie użycia. Na przykład mając bezpłatne konto platformy Azure, możesz co miesiąc skorzystać z bezpłatnych 750 godzin maszyny wirtualnej B1S z systemem Windows. Z tego 750-godzinnego limitu możesz korzystać w dowolnej kombinacji. Możesz utworzyć 5 maszyn wirtualnych B1S z systemem Windows i korzystać z każdej z nich przez 150 godzin.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [sprawdzać użycie bezpłatnych usług dostępnych w ramach bezpłatnego konta platformy Azure](check-free-service-usage.md).
- Dowiedz się, jak [uniknąć naliczania opłat za bezpłatne konto platformy Azure](avoid-charges-free-account.md).

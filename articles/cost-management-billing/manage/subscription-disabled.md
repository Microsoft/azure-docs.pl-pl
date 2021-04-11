---
title: Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure
description: W tym artykule opisano, dlaczego subskrypcja platformy Azure może być wyłączona i jak można ją ponownie uaktywnić.
keywords: wyłączona subskrypcja platformy azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: 37093900bf49e8a7613e2e3f4311548675791ceb
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220887"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure

Twoja subskrypcja platformy Azure może zostać wyłączona, ponieważ środki wygasły, osiągnięto limit wydatków, istnieje zaległy rachunek, osiągnięto limit karty kredytowej lub administrator konta anulował subskrypcję. Sprawdź, który problem dotyczy Ciebie, i wykonaj kroki opisane w tym artykule, aby ponownie uaktywnić subskrypcję.

## <a name="your-credit-is-expired"></a>Twoje środki wygasły

Po zarejestrowaniu się w celu korzystania z bezpłatnego konta platformy Azure możesz uzyskać bezpłatną subskrypcję próbną, która zapewnia BEZPŁATNYMI ŚRODKAMIą środki na korzystanie z platformy Azure w walucie rozliczeniowej przez 30 dni i 12 miesięcy bezpłatnych usług. Na końcu 30-dniowego okresu platforma Azure wyłączy subskrypcję. Twoja subskrypcja jest wyłączona, aby chronić Cię przed przypadkowym naliczeniem opłat za użycie wykraczające poza posiadane środki i bezpłatne usługi zawarte w subskrypcji. Aby nadal korzystać z usług Azure, musisz [uaktualnić subskrypcję](upgrade-azure-subscription.md). Po uaktualnieniu subskrypcja nadal ma dostęp do bezpłatnych usług przez 12 miesięcy. Opłata jest naliczana tylko za użycie wykraczające poza limity ilości bezpłatnych usług.

## <a name="you-reached-your-spending-limit"></a>Osiągnięto limit wydatków

Subskrypcje platformy Azure oferujące środki, takie jak bezpłatna wersja próbna i Visual Studio Enterprise, mają limity wydatków. Usług można używać tylko do środków uwzględnionych w rachunku. Gdy użycie osiągnie limit wydatków, platforma Azure wyłącza subskrypcję dla pozostałej części okresu rozliczeniowego. Twoja subskrypcja jest wyłączona, aby chronić Cię przed przypadkowym naliczeniem opłat za użycie wykraczające poza posiadane środki zawarte w subskrypcji. Aby usunąć limit wydatków, zobacz sekcję [usuwanie limitu wydatków w Azure Portal](spending-limit.md#remove).

> [!NOTE]
> Jeśli masz subskrypcję bezpłatnej wersji próbnej i usuniesz limit wydatków, Twoja subskrypcja zostanie przekonwertowana na pojedynczą subskrypcję ze stawkami zgodnymi z rzeczywistym użyciem po wygaśnięciu bezpłatnej wersji próbnej. Zachowasz swoje pozostałe środki przez pełne 30 dni od utworzenia subskrypcji. Masz również dostęp do bezpłatnych usług przez 12 miesięcy.

Aby monitorować działania związane z rozliczeniami i zarządzać nimi na platformie Azure, zobacz [Planowanie zarządzania kosztami platformy Azure](../understand/plan-manage-costs.md).

## <a name="your-bill-is-past-due"></a>Upłynął termin zapłaty należności

Aby rozwiązać zaległe saldo, zobacz jeden z następujących artykułów:

- Subskrypcje programu subskrypcji usługi Microsoft Online, w tym płatność zgodnie z rzeczywistym użyciem, można znaleźć w temacie [Rozwiązywanie zaległych bilansów dla subskrypcji platformy Azure po otrzymaniu wiadomości e-mail z platformy Azure](resolve-past-due-balance.md).
- W przypadku subskrypcji umów dla klientów firmy Microsoft zapoznaj [się z tematem jak uregulować rachunek za Microsoft Azure](../understand/pay-bill.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>Należność na rachunku przekracza limit Twojej karty kredytowej

Aby rozwiązać ten problem, [Przejdź do innej karty kredytowej](change-credit-card.md). Jeśli reprezentujesz firmę, możesz [przełączyć się na płatność przy użyciu faktury](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Subskrypcja została przypadkowo anulowana

Jeśli jesteś administratorem konta i przypadkowo Anulowano subskrypcję z płatność zgodnie z rzeczywistym użyciem, możesz ją ponownie aktywować w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do pozycji subskrypcje, a następnie wybierz anulowaną subskrypcję.
1. Wybierz pozycję **Uaktywnij ponownie**.
1. Potwierdź ponowną aktywację, wybierając **przycisk OK**.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="Zrzut ekranu przedstawiający potwierdzenie ponownej aktywacji" :::

W przypadku innych typów subskrypcji [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) w celu ponownego uaktywnienia subskrypcji.

## <a name="after-reactivation"></a>Po ponownym uaktywnieniu

Po ponownym uaktywnieniu subskrypcji może wystąpić opóźnienie w tworzeniu zasobów lub zarządzaniu nimi. Jeśli opóźnienie przekracza 30 minut, skontaktuj się z [pomocą techniczną dotyczącą rozliczeń systemu Azure](https://go.microsoft.com/fwlink/?linkid=2083458) . Działanie większości zasobów platformy Azure jest wznawiane automatycznie i nie wymaga żadnej akcji. Zalecamy jednak sprawdzenie zasobów usługi platformy Azure i ponowne uruchomienie wszystkich tych, których działanie nie zostanie wznowione automatycznie.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [planować zarządzanie kosztami platformy Azure](../understand/plan-manage-costs.md).

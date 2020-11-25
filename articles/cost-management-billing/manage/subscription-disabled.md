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
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: cad3082981bcfc699bc230badf44e2ffc2e1bed3
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744429"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Ponowne uaktywnianie wyłączonej subskrypcji platformy Azure

Twoja subskrypcja platformy Azure może zostać wyłączona, ponieważ środki wygasły, osiągnięto limit wydatków, istnieje zaległy rachunek, osiągnięto limit karty kredytowej lub administrator konta anulował subskrypcję. Sprawdź, który problem dotyczy Ciebie, i wykonaj kroki opisane w tym artykule, aby ponownie uaktywnić subskrypcję.

## <a name="your-credit-is-expired"></a>Twoje środki wygasły

Po zarejestrowaniu się w celu korzystania z bezpłatnego konta platformy Azure uzyskujesz subskrypcję w bezpłatnej wersji próbnej, w ramach której masz 200 USD środków na korzystanie z platformy Azure przez 30 dni i 12 miesięcy bezpłatnych usług. Na końcu 30-dniowego okresu platforma Azure wyłączy subskrypcję. Twoja subskrypcja jest wyłączona, aby chronić Cię przed przypadkowym naliczeniem opłat za użycie wykraczające poza posiadane środki i bezpłatne usługi zawarte w subskrypcji. Aby nadal korzystać z usług Azure, musisz [uaktualnić subskrypcję](upgrade-azure-subscription.md). Po uaktualnieniu subskrypcja nadal ma dostęp do bezpłatnych usług przez 12 miesięcy. Opłata jest naliczana tylko za użycie wykraczające poza bezpłatne usługi i ilości.

## <a name="you-reached-your-spending-limit"></a>Osiągnięto limit wydatków

Subskrypcje platformy Azure oferujące środki, takie jak bezpłatna wersja próbna i Visual Studio Enterprise, mają limity wydatków. Oznacza to, że można korzystać z usług tylko do wysokości przyznanych środków. Gdy użycie osiągnie limit wydatków, platforma Azure wyłączy subskrypcję na pozostałą część okresu rozliczeniowego. Twoja subskrypcja jest wyłączona, aby chronić Cię przed przypadkowym naliczeniem opłat za użycie wykraczające poza posiadane środki zawarte w subskrypcji. Aby usunąć limit wydatków, zobacz temat [Usuwanie limitu wydatków w Centrum konta](spending-limit.md#remove).

> [!NOTE]
> Jeśli masz subskrypcję bezpłatnej wersji próbnej i usuniesz limit wydatków, Twoja subskrypcja zostanie przekonwertowana na pojedynczą subskrypcję ze stawkami zgodnymi z rzeczywistym użyciem po wygaśnięciu bezpłatnej wersji próbnej. Zachowasz swoje pozostałe środki przez pełne 30 dni od utworzenia subskrypcji. Masz również dostęp do bezpłatnych usług przez 12 miesięcy.

Aby monitorować działania związane z rozliczeniami i zarządzać nimi na platformie Azure, zobacz [Planowanie zarządzania kosztami platformy Azure](../understand/plan-manage-costs.md).


## <a name="your-bill-is-past-due"></a>Upłynął termin zapłaty należności

Aby rozwiązać problemy dotyczące zaległego salda, zobacz temat [Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure po otrzymaniu wiadomości e-mail z platformy Azure](resolve-past-due-balance.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>Należność na rachunku przekracza limit Twojej karty kredytowej

Aby rozwiązać ten problem, [użyj innej karty kredytowej](change-credit-card.md). Jeśli reprezentujesz firmę, możesz [przełączyć się na płatność przy użyciu faktury](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Subskrypcja została przypadkowo anulowana

Jeśli jesteś administratorem konta i doszło do przypadkowego anulowania pojedynczej subskrypcji ze stawkami zgodnymi z rzeczywistym użyciem, możesz ją ponownie uaktywnić w Centrum konta.

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/Subscriptions).
1. Wybierz anulowaną subskrypcję.
1. Kliknij przycisk **Uaktywnij ponownie**.

    ![Zrzut ekranu, na którym przedstawiono hiperlinki do ponownego uaktywnienia w okienku po prawej stronie](./media/subscription-disabled/reactivate-sub.png)

W przypadku innych typów subskrypcji [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) w celu ponownego uaktywnienia subskrypcji.

## <a name="after-reactivation"></a>Po ponownym uaktywnieniu

Po ponownym uaktywnieniu subskrypcji może wystąpić opóźnienie w tworzeniu zasobów lub zarządzaniu nimi. Jeśli opóźnienie przekroczy 30 minut, skontaktuj się z [pomocą techniczną platformy Azure](https://go.microsoft.com/fwlink/?linkid=2083458) w celu uzyskania pomocy. Działanie większości zasobów platformy Azure jest wznawiane automatycznie i nie wymaga żadnej akcji. Zalecamy jednak sprawdzenie zasobów usługi platformy Azure i ponowne uruchomienie wszystkich tych, których działanie nie zostanie wznowione automatycznie.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [planować zarządzanie kosztami platformy Azure](../understand/plan-manage-costs.md).

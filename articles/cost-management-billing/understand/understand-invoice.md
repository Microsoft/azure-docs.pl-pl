---
title: Informacje dotyczące faktury za użycie platformy Azure
description: Dowiedz się, jak czytać i zrozumieć dane użycia oraz rachunek dotyczące subskrypcji platformy Azure
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b7b115f909c57470bfbdab9cc44e6a258f85b8f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685922"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Omówienie terminów występujących na fakturze za platformę Microsoft Azure

Faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Można ją pobrać w formacie PDF (Portable Document Format) z witryny [Azure Portal](https://portal.azure.com/) lub otrzymać w wiadomości e-mail. Aby uzyskać więcej informacji, zobacz [Jak otrzymać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](../manage/download-azure-invoice-daily-usage-date.md).

Kilka kwestii, na które warto zwrócić uwagę:

-   Jeśli korzystasz z subskrypcji bezpłatnej wersji próbnej, możesz uzyskać szczegółowe informacje o użyciu z witryny Azure Portal, ale nie masz faktury.

-   Na bieżącej fakturze mogą zostać wykazane dane użycia z maksymalnie 24 godzin z końca poprzedniego okresu rozliczeniowego.

-   Opłaty wymienione na rozliczeniach dla klientów międzynarodowych służą jedynie do oszacowania kosztów. Banki mogą stosować różne kursy wymiany.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Szczegółowe opisy terminów znajdujących się na fakturze
W poniższych sekcjach wymieniono ważne terminy występujące na fakturze i ich opisy.

### <a name="account-information"></a>Informacje o koncie

W górnej części pierwszej strony faktury znajduje się sekcja z informacjami o koncie. Zawiera ona informacje o profilu i subskrypcji.

![Sekcja faktury z informacjami o koncie](./media/understand-invoice/account-information-section.png)

| Okres | Opis |
| --- | --- |
| Numer zamówienia zakupu klienta |Opcjonalny numer zamówienia zakupu przypisany do Ciebie na potrzeby śledzenia |
| Nr faktury |Unikatowy, wygenerowany przez firmę Microsoft numer faktury używany na potrzeby śledzenia |
| Cykl rozliczeń |Zakres dat, który obejmuje ta faktura |
| Data faktury |Data wygenerowania faktury. Zazwyczaj jest to jeden dzień po zakończeniu okresu rozliczeniowego |
| Metoda płatności |Typ płatności używany na koncie (faktura lub karta kredytowa) |
| Rachunek dla |Adres na potrzeby rozliczeń przypisany do konta |
| Oferta subskrypcji („płatność zgodnie z rzeczywistym użyciem”) |Typ zakupionej oferty subskrypcji (płatność zgodnie z rzeczywistym użyciem, BizSpark Plus, Azure — dostęp próbny itp.). Aby uzyskać więcej informacji, zobacz [Typy ofert platformy Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| Adres e-mail właściciela konta | Adres e-mail konta, w ramach którego zarejestrowane jest konto platformy Microsoft Azure. <br /><br />Aby zmienić adres e-mail, zobacz [Jak zmienić informacje o profilu konta platformy Azure, takie jak kontaktowy adres e-mail, adres i numer telefonu](../manage/change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Opis sekcji podsumowania faktury
Sekcja **Podsumowanie faktury** zawiera łączne kwoty transakcji od ostatniego okresu rozliczeniowego oraz bieżące opłaty za użycie.

![Sekcja podsumowania faktury](./media/understand-invoice/invoice-summary-section.png)

Nazwa subskrypcji („Magazyn produkcyjny”) to nazwa subskrypcji dla tej faktury.

#### <a name="understand-the-previous-charges"></a>Omówienie poprzednich opłat
Sekcje faktury dotyczące poprzedniego salda, płatności i zaległego salda podsumowują transakcje od ostatniego okresu rozliczeniowego.

| Okres | Opis |
| --- | --- |
| Poprzednie saldo |Łączna kwota należności z ostatniego okresu rozliczeniowego |
| Płatności |Łączne płatności i kredyty zastosowane do ostatniego okresu rozliczeniowego |
| Zaległe saldo (od poprzedniego cyklu rozliczeń) |Wszelkie kredyty lub pozostałe saldo na koncie od czasu ostatniego okresu rozliczeniowego |

#### <a name="understand-the-current-charges"></a>Opis sekcji opłat bieżących
Sekcja Opłaty bieżące faktury zawiera szczegółowe informacje o opłatach miesięcznych za bieżący okres rozliczeniowy.

| Okres | Opis |
| --- | --- |
| Opłaty za zużycie |Opłaty za zużycie to łączne miesięczne opłaty za subskrypcję dla bieżącego okresu rozliczeniowego|
| Rabaty |Rabaty dotyczące usług zastosowane do bieżącego okresu rozliczeniowego|
| Korekty |Różne kredyty (bezpłatne użycie, środki itp.) lub opłaty zaległe stosowane do bieżącego okresu rozliczeniowego.<br/><br/>Jeśli na przykład korzystasz z oferty Visual Studio Enterprise z subskrypcją MSDN, w tym miejscu jest wyświetlany miesięczny kredyt. Jeśli anulujesz subskrypcję, zobaczysz miesięczne opłaty za użycie, które przekraczają miesięczny kredyt uzyskany w ramach oferty subskrypcji. Opłaty są naliczane na początku bieżącego okresu rozliczeniowego do daty anulowania subskrypcji. |

#### <a name="sold-to-and-payment-instructions"></a>Informacje o nabywcy i instrukcje dotyczące płatności

W poniższej tabeli opisano widoczną na drugiej stronie faktury sekcję z informacjami o nabywcy oraz instrukcjami dotyczącymi płatności.

| Okres |Opis |
| --- | --- |
| Sprzedano dla |Adres profilu wymieniony na koncie. <br/><br/>Aby zmienić adres, zobacz [Jak zmienić informacje o profilu konta platformy Azure, takie jak kontaktowy adres e-mail, adres i numer telefonu](../manage/change-azure-account-profile.md).|
| Instrukcje dokonywania płatności |Instrukcje dotyczące sposobu dokonywania płatności w zależności od formy płatności (na przykład za pomocą karty kredytowej lub na podstawie faktury). |

#### <a name="usage-charges"></a>Opłaty za użycie

W sekcji Opłaty za użycie faktury są wyświetlane informacje dotyczące opłat na poziomie miernika.

![Sekcja opłat za użycie](./media/understand-invoice/usage-charges-section.png)

W poniższej tabeli opisano nagłówki kolumn opłat za użycie wyświetlane na fakturze.

| Okres |Opis |
| --- | --- |
| Name (Nazwa) |Określa użycie dla usługi najwyższego poziomu |
| Typ |Definiuje typ usługi platformy Azure, który może wpływać na stawkę |
| Zasób |Określa jednostkę miary wykorzystywanego miernika |
| Region |Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych |
| Zużyte |Ilość miernika użyta w okresie rozliczeniowym |
| Dołączono |Niepłatna ilość miernika w trakcie bieżącego okresu rozliczeniowego |
| Płatne |Pokazuje różnicę między zużytą ilością a uwzględnioną ilością. Rachunek jest wystawiany na tę kwotę. W przypadku ofert płatności zgodnie z rzeczywistym użyciem bez żadnej kwoty wliczonej w ofertę ta wartość jest taka sama jak wartość Zużyta ilość |
| Stawka |Określa stawkę naliczaną na jednostkę płatną |
| Wartość |Zawiera wynik mnożenia wartości z kolumny Ilość nadwyżkowego użycia przez wartość z kolumny Stawka. Jeśli zużyta ilość nie przekracza uwzględnionej ilości, ta kolumna nie zawiera żadnych opłat. |
| Suma częściowa |Suma wszystkich opłat naliczanych przed opodatkowaniem dla tego okresu rozliczeniowego |
| Suma końcowa |Suma wszystkich opłat naliczanych po opodatkowaniu dla tego okresu rozliczeniowego |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Jak mogę się upewnić, że opłaty na mojej fakturze są poprawne?
Jeśli na fakturze znajduje się opłata, na temat której chcesz uzyskać więcej informacji, zobacz [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](review-individual-bill.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

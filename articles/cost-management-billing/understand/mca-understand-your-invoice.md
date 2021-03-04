---
title: Omówienie faktury za umowę klienta firmy Microsoft na platformie Azure
description: Dowiedz się, jak interpretować treść rachunku za umowę klienta firmy Microsoft na platformie Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 996c974473c53833dd26abae2fb61585999f33b4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050447"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Terminy na fakturze za umowę klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego platformy Azure związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do Umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Można ją pobrać w formacie PDF (Portable Document Format) z witryny [Azure Portal](https://portal.azure.com/) lub otrzymać w wiadomości e-mail. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](download-azure-invoice.md).

Obejrzyj swoją zawartość wideo z [faktury umów dla klientów firmy Microsoft](https://www.youtube.com/watch?v=e2LGZZ7GubA) , aby dowiedzieć się więcej na temat faktury i jak analizować opłaty.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Okres rozliczeniowy

Faktury są wystawiane co miesiąc. Aby dowiedzieć się, w który dzień miesiąca są wystawiane faktury, sprawdź *datę faktury* w obszarze właściwości profilu rozliczeniowego w witrynie [Azure Portal](https://portal.azure.com/). Opłaty naliczane od końca okresu rozliczeniowego do daty faktury są uwzględniane w następnym okresie rozliczeniowym, czyli na fakturze za kolejny miesiąc. Data początkowa i data końcowa okresu rozliczeniowego są widoczne nad sekcją **Podsumowanie rozliczeń** w pliku PDF z fakturą.

## <a name="invoice-terms-and-descriptions"></a>Terminy na fakturze wraz z opisami

W poniższych sekcjach wymieniono i opisano ważne terminy występujące na fakturze.

### <a name="invoice-summary"></a>Podsumowanie faktury

**Podsumowanie faktury** znajduje się w górnej części pierwszej strony i zawiera informacje o profilu rozliczeniowym oraz sposobie płatności.

![Sekcja podsumowania faktury](./media/mca-understand-your-invoice/invoicesummary.png)

| Okres | Opis |
| --- | --- |
| Sprzedano dla |Adres podmiotu prawnego, wyświetlany we właściwościach konta rozliczeniowego|
| Rachunek dla |Adres do rozliczeń w profilu rozliczeniowym, do którego jest wysyłana faktura, wyświetlany we właściwościach tego profilu|
| Profil rozliczeniowy |Nazwa profilu rozliczeniowego, do którego jest wysyłana faktura |
| Numer zamówienia liczba |Opcjonalny numer zamówienia zakupu przypisany do Ciebie na potrzeby śledzenia |
| Numer faktury |Unikatowy, wygenerowany przez firmę Microsoft numer faktury używany na potrzeby śledzenia |
| Data faktury |Data wygenerowania faktury. Zazwyczaj jest to od 5 do 12 dni po zakończeniu okresu rozliczeniowego. Datę faktury można sprawdzić we właściwościach profilu rozliczeniowego.|
| Warunki płatności |Sposób płatności rachunku od firmy Microsoft. Zapis *Do 30 dni* oznacza, że płatność powinna nastąpić w ciągu 30 dni od daty faktury. |

### <a name="billing-summary"></a>Podsumowanie rozliczeń

Sekcja **Podsumowanie rozliczeń** zawiera opłaty naliczone od poprzedniego okresu rozliczeniowego, wszystkie zastosowane środki, podatek i łączną kwotę należności.

![Sekcja podsumowania rozliczeń](./media/mca-understand-your-invoice/billingsummary.png)

| Okres | Opis |
| --- | --- |
| Opłaty|Łączna kwota opłat naliczonych na rzecz firmy Microsoft dla tego profilu rozliczeniowego od ostatniego okresu rozliczeniowego |
| Środki |Środki otrzymane ze zwrotów |
| Zastosowane środki na korzystanie z platformy Azure | Środki na korzystanie z platformy Azure, automatycznie uwzględniane podczas naliczania opłat w każdym okresie rozliczeniowym |
| Suma częściowa |Kwota należności przed opodatkowaniem |
| Podatek |Typ i kwota podatku w zależności od kraju/regionu wprowadzonego w profilu rozliczeniowym. W przypadku braku obowiązku płacenia podatku faktura nie zawiera kwoty podatku. |
| Szacowane łączne oszczędności |Szacowana, łączna kwota zaoszczędzona dzięki rabatom. Jeśli ma to zastosowanie, zastosowane rabaty są wyświetlane poniżej pozycji zakupów w sekcji Szczegóły według faktury. |

### <a name="invoice-sections"></a>Sekcje faktury

Każda sekcja faktury w profilu rozliczeniowym zawiera opłaty, zastosowane środki na korzystanie z platformy Azure, podatek oraz łączną kwotę należności.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Sekcja szczegółów według faktury

Szczegóły przedstawiają koszty w poszczególnych sekcjach faktury z podziałem na zamówienia produktów. W ramach każdego zamówienia produktu koszty są podzielone na typy usług. Informacje dotyczące opłat za dzienne użycie produktów i usług można znaleźć w witrynie Azure Portal oraz pliku CSV zawierającym opłaty i użycie zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz [Opłaty na fakturze za umowę klienta firmy Microsoft](review-customer-agreement-bill.md).

Łączną kwotę należności w poszczególnych rodzinach usług oblicza się, odejmując wartość *środków na korzystanie z platformy Azure* od wartości *Środki/opłaty* i dodając wartość *podatku*:


![Sekcja szczegółów według faktury](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Okres |Opis |
| --- | --- |
| Cena jednostkowa | Obowiązująca cena jednostkowa usługi (w walucie podanej w cenniku) używana do oceniania użycia. Jest ona unikatowa dla produktu, rodziny usług, miernika i oferty. |
| Ilość | Ilość zakupiona lub zużyta w okresie rozliczeniowym |
| Opłaty/środki | Opłaty netto po zastosowaniu środków/zwrotów |
| Środki na korzystanie z platformy Azure | Wartość środków na korzystanie z platformy Azure uwzględniana podczas obliczania wartości opłat/środków|
| Stawka podatku | Stawka podatku w zależności od kraju/regionu |
| Kwota podatku | Kwota naliczonego podatku zależna od stawki podatku |
| Łącznie | Łączna kwota należności za zakup |

### <a name="how-to-pay"></a>Sposób płatności

W dolnej części faktury znajdują się instrukcje dotyczące płatności. Rachunek można opłacić czekiem, przelewem bankowym lub w trybie online. W przypadku płatności w trybie online można skorzystać z karty kredytowej lub środków na korzystanie z platformy Azure, jeśli to możliwe.

### <a name="publisher-information"></a>Informacje o wydawcy

Jeśli rachunek obejmuje usługi innych firm, u dołu faktury są widoczne nazwa i adres każdego wydawcy.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Informacje o opłatach na fakturze w profilu rozliczeniowym](review-customer-agreement-bill.md)
- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](../manage/download-azure-invoice-daily-usage-date.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](../manage/ea-pricing.md)
- [Wyświetlanie dokumentów podatkowych dla profilu rozliczeniowego](mca-download-tax-document.md)

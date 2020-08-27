---
title: Omówienie faktury za umowę partnerską firmy Microsoft na platformie Azure
description: Dowiedz się, jak interpretować treść rachunku za umowę partnerską firmy Microsoft na platformie Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 9ff151071d42308137bdb8e9064f9b27ed194cae
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688848"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>Terminy na fakturze za umowę partnerską firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego platformy Azure związanego z umową partnerską firmy Microsoft. [Sprawdź, czy masz dostęp do umowy partnerskiej firmy Microsoft](#check-access-to-a-microsoft-partner-agreement).

Faktura zawiera podsumowanie opłat i instrukcje dotyczące płatności. Można ją pobrać w formacie PDF (Portable Document Format) z witryny [Azure Portal](https://portal.azure.com/) lub otrzymać w wiadomości e-mail. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](download-azure-invoice.md).

W poniższych sekcjach wymieniono i opisano ważne terminy występujące na fakturze.

## <a name="billing-period"></a>Okres rozliczeniowy

Faktury są wystawiane co miesiąc. Opłaty naliczane od końca okresu rozliczeniowego do daty faktury są uwzględniane w następnym okresie rozliczeniowym, czyli na fakturze za kolejny miesiąc. Data początkowa i data końcowa okresu rozliczeniowego są widoczne nad sekcją **Podsumowanie rozliczeń** w pliku PDF z fakturą.

## <a name="invoice-summary"></a>Podsumowanie faktury

**Podsumowanie faktury** znajduje się w górnej części pierwszej strony i zawiera informacje o profilu rozliczeniowym oraz sposobie płatności.

<!-- add screenshot -->

| Okres | Opis |
| --- | --- |
| Sprzedano dla |Adres podmiotu prawnego, wyświetlany we właściwościach konta rozliczeniowego|
| Rachunek dla |Adres do rozliczeń w profilu rozliczeniowym, do którego jest wysyłana faktura, wyświetlany we właściwościach tego profilu|
| Profil rozliczeniowy |Nazwa profilu rozliczeniowego, do którego jest wysyłana faktura |
| Numer zamówienia numer |Opcjonalny numer zamówienia zakupu przypisany do Ciebie na potrzeby śledzenia |
| Numer faktury |Unikatowy, wygenerowany przez firmę Microsoft numer faktury używany na potrzeby śledzenia |
| Data faktury |Data wygenerowania faktury. Zazwyczaj jest to od 5 do 12 dni po zakończeniu okresu rozliczeniowego. Datę faktury można sprawdzić we właściwościach profilu rozliczeniowego.|
| Warunki płatności |Sposób płatności rachunku od firmy Microsoft. Zapis *Do 60 dni* oznacza, że płatność powinna nastąpić w ciągu 60 dni od daty faktury. |

## <a name="billing-summary"></a>Podsumowanie rozliczeń

Sekcja **Podsumowanie rozliczeń** zawiera opłaty naliczone od poprzedniego okresu rozliczeniowego, wszystkie zastosowane środki, podatek i łączną kwotę należności.

<!-- add screenshot -->

| Okres | Opis |
| --- | --- |
| Opłaty|Łączna kwota opłat naliczonych na rzecz firmy Microsoft dla tego profilu rozliczeniowego od ostatniego okresu rozliczeniowego |
| Środki |Środki otrzymane ze zwrotów |
| Zastosowane środki na korzystanie z platformy Azure | Środki na korzystanie z platformy Azure, automatycznie uwzględniane podczas naliczania opłat w każdym okresie rozliczeniowym |
| Suma częściowa |Kwota należności przed opodatkowaniem |
| Podatek |Typ i kwota podatku w zależności od kraju/regionu wprowadzonego w profilu rozliczeniowym. W przypadku braku obowiązku płacenia podatku faktura nie zawiera kwoty podatku. |
| Szacowane łączne oszczędności |Szacowana, łączna kwota zaoszczędzona dzięki rabatom. Jeśli ma to zastosowanie, zastosowane rabaty są wyświetlane poniżej pozycji zakupów w sekcji Szczegóły według faktury. |


## <a name="billing-details-by-product"></a>Szczegóły rozliczeń według produktu

Sekcja **Szczegóły rozliczeń według produktu** zawiera listę łącznych opłat za poszczególne produkty skojarzone z tym profilem rozliczeniowym. Możesz wyświetlić dzienny podział poszczególnych opłat za produkty w pliku CSV użycia i opłat za platformę Azure. Aby uzyskać więcej informacji o uzgadnianiu pliku pdf faktury z plikiem CSV użycia i opłat za platformę Azure, zobacz [Opis zawartości rachunku — umowa partnerska firmy Microsoft](review-partner-agreement-bill.md).

## <a name="how-to-pay"></a>Sposób płatności

W dolnej części faktury znajdują się instrukcje dotyczące płatności. Płatność za pomocą czeku lub przelewu w ciągu 60 dni od daty faktury.

## <a name="publisher-information"></a>Informacje o wydawcy

Jeśli rachunek obejmuje usługi innych firm, u dołu faktury są widoczne nazwa i adres każdego wydawcy.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Sprawdzanie dostępu do umowy partnerskiej firmy Microsoft
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Informacje o opłatach na fakturze w profilu rozliczeniowym](review-customer-agreement-bill.md)
- [Jak uzyskać fakturę rozliczeniową za platformę Azure i dane dziennego użycia](../manage/download-azure-invoice-daily-usage-date.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](../manage/ea-pricing.md)
- [Wyświetlanie dokumentów podatkowych dla profilu rozliczeniowego](mca-download-tax-document.md)

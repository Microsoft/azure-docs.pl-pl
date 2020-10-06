---
title: Przeglądanie rachunku dla Umowy z Klientem Microsoft — Azure
description: Dowiedz się, jak przejrzeć informacje o rozliczeniach i użyciu zasobów oraz sprawdzić opłaty za fakturę dotyczącą Umowy z Klientem Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 619dec1b2246b7e74b058e570df4021888196b0c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447538"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Samouczek: Przeglądanie faktury dla Umowy z Klientem Microsoft

Możesz przeglądać opłaty na fakturze, analizując poszczególne transakcje. Na koncie rozliczeniowym umowy klienta firmy Microsoft co miesiąc generowana jest jedna faktura na każdy profil rozliczeniowy. Faktura zawiera wszystkie opłaty z poprzedniego miesiąca. W witrynie Azure Portal możesz wyświetlić faktury i porównać opłaty ze szczegółowym zestawieniem użycia.

Ten samouczek dotyczy tylko klientów platformy Azure z Umową z Klientem Microsoft.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych transakcji w witrynie Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto rozliczeniowe dla Umowy z Klientem Microsoft.

Musisz mieć dostęp do Umowy z Klientem Microsoft. Musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego, aby móc wyświetlić informacje dotyczące rozliczeń i użycia. Aby dowiedzieć się więcej o rolach rozliczeniowych na potrzeby umów klienta firmy Microsoft, zobacz [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Od dnia zasubskrybowania platformy Azure musi upłynąć więcej niż 30 dni. Na platformie Azure opłaty są naliczane na koniec okresu fakturowania.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft

Sprawdź typ umowy, aby określić, czy masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft.

W witrynie Azure Portal wpisz w polu wyszukiwania *zarządzanie kosztami i rozliczenia*, a następnie wybierz pozycję **Zarządzanie kosztami i rozliczenia**.

![Zrzut ekranu przedstawia wyszukiwanie narzędzia Cost Management + Billing w witrynie Azure Portal.](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Jeśli masz dostęp tylko do jednego zakresu rozliczeniowego, wybierz pozycję **Właściwości** po lewej stronie. Masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft, jeśli typ konta rozliczeniowego to **Umowa klienta firmy Microsoft**.

![Zrzut ekranu przedstawiający Umowę z Klientem Microsoft na stronie właściwości](./media/review-customer-agreement-bill/billing-mca-property.png)

Jeśli masz dostęp do wielu zakresów rozliczeniowych, sprawdź typ w kolumnie konta rozliczeniowego. Masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft, jeśli typ konta rozliczeniowego w dowolnym z Twoich zakresów to **Umowa klienta firmy Microsoft**.

![Zrzut ekranu przedstawiający Umowę z Klientem Microsoft na stronie listy kont rozliczeniowych](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Przeglądanie zafakturowanych transakcji w witrynie Azure Portal

W witrynie Azure Portal wybierz pozycję **Wszystkie transakcje** po lewej stronie. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego, profilu rozliczeniowego lub sekcji faktury, a następnie wybranie pozycji **Wszystkie transakcje**.

Na stronie Wszystkie transakcje są wyświetlane następujące informacje:

![Zrzut ekranu przedstawiający listę rozliczonych transakcji](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Kolumna  |Definicja  |
|---------|---------|
|Data     | Data transakcji  |
|Identyfikator faktury     | Identyfikator faktury, na której jest uwzględniona transakcja. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure |
|Typ transakcji     |  Typ transakcji, na przykład zakup, anulowanie, opłaty za użycie  |
|Rodzina produktów     | Kategoria produktu, na przykład obliczenia w przypadku maszyn wirtualnych lub baza danych w przypadku usługi Azure SQL Database|
|Jednostka SKU produktu     | Unikatowy kod identyfikujący wystąpienie produktu |
|Kwota     |  Kwota transakcji      |
|Sekcja faktury     | Sekcja faktury danego profilu rozliczeniowego, w której jest widoczna ta transakcja |
|Profil rozliczeniowy     | Profil rozliczeniowy, na którego fakturze jest widoczna ta transakcja |

Możesz wyszukać identyfikator faktury, aby odfiltrować transakcje z tej faktury.

### <a name="view-transactions-by-invoice-sections"></a>Wyświetlanie transakcji według sekcji faktury

Sekcje faktur ułatwiają organizowanie kosztów na fakturze danego profilu rozliczeniowego. Więcej informacji. Po wygenerowaniu faktury są na niej widoczne opłaty z wszystkich sekcji w ramach danego profilu rozliczeniowego.

Na poniżej ilustracji przedstawiono opłaty z sekcji działu księgowości na przykładowej fakturze.

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

Po znalezieniu opłat w odpowiedniej sekcji faktury możesz wyświetlić transakcje w witrynie Azure Portal, aby wyjaśnić opłaty.

Przejdź na stronę Wszystkie transakcje w witrynie Azure Portal, aby wyświetlić transakcje z faktury.

Możesz filtrować według nazwy sekcji faktury, aby wyświetlić odpowiednie transakcje.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat

Na koncie rozliczeniowym Umowy z Klientem Microsoft niezafakturowane opłaty są szacowane i wyświetlane jako oczekujące. Możesz wyświetlić oczekujące opłaty w witrynie Azure Portal, aby oszacować kwotę kolejnej faktury. Oczekujące opłaty są szacunkowe i nie zawierają podatków. Rzeczywiste opłaty na kolejnej fakturze mogą się różnić od opłat oczekujących.

### <a name="view-summary-of-pending-charges"></a>Wyświetlanie podsumowania opłat oczekujących

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz kartę **Podsumowanie** w górnej części ekranu.

W sekcji Opłaty są wyświetlane opłaty za bieżący i poprzedni miesiąc.

![Zrzut ekranu przedstawia podsumowanie profilu rozliczeniowego.](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące i pojawią się na najbliższej fakturze.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Gdy znajdziesz oczekujące opłaty, możesz je wyjaśnić, analizując poszczególne transakcje, które im odpowiadają. Obecnie oczekujące opłaty za użycie nie są wyświetlane na stronie Wszystkie transakcje. Możesz wyświetlić je na stronie subskrypcji platformy Azure.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

W witrynie Azure Portal wpisz w polu wyszukiwania *zarządzanie kosztami i rozliczenia*, a następnie wybierz pozycję **Zarządzanie kosztami i rozliczenia**.

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Wszystkie transakcje** po lewej stronie.

Wyszukaj *oczekujące*. Użyj filtru **Przedział czasu**, aby wyświetlić opłaty za bieżący lub ubiegły miesiąc.

![Zrzut ekranu przedstawiający listę oczekujących transakcji](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Wyświetlanie oczekujących opłat za użycie

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

W witrynie Azure Portal wpisz w polu wyszukiwania *zarządzanie kosztami i rozliczenia*, a następnie wybierz pozycję **Zarządzanie kosztami i rozliczenia**.

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Wszystkie subskrypcje** po lewej stronie.

Na stronie subskrypcji platformy Azure są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych subskrypcji z profilu rozliczeniowego. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

![Zrzut ekranu przedstawia subskrypcje z opłatami za bieżący miesiąc i opłatami za ostatni miesiąc.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizowanie opłat za użycie platformy Azure

Możesz przeanalizować opłaty związane z użyciem, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach. Możesz pobrać taki plik dla faktury lub dla oczekujących opłat.

### <a name="download-your-invoice-and-usage-details"></a>Pobieranie faktury i szczegółów użycia

W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego w sekcji Zarządzanie kosztami i rozliczenia. W menu po lewej stronie wybierz pozycję **Faktury** w obszarze **Rozliczenia**. W siatce faktur znajdź wiersz faktury, którą chcesz pobrać. Kliknij symbol pobierania lub symbol wielokropka (...) na końcu wiersza. W polu **Pobieranie** pobierz plik szczegółów użycia i fakturę.

### <a name="view-detailed-usage-by-invoice-section"></a>Wyświetlanie szczegółów użycia według sekcji faktury

Możesz przefiltrować plik z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych sekcji faktury.

Poniższe informacje zawierają omówienie uzgadniania opłat za obliczenia w ramach sekcji faktury działu księgowości:

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

| Faktura (PDF) | Plik CSV użycia platformy Azure i opłata |
| --- | --- |
|Dział księgowości |invoiceSectionName |
|Opłaty za użycie — plan Microsoft Azure |productOrderName |
|Wystąpienia obliczeniowe |serviceFamily |

Przefiltruj kolumnę **invoiceSectionName** w pliku CSV według nazwy **Dział księgowości**. Następnie przefiltruj kolumnę **productOrderName** w pliku CSV według produktu **plan Microsoft Azure**. Następnie przefiltruj kolumnę **servicefamily** w pliku CSV według wartości **Microsoft.Compute**.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według sekcji faktury](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Wyświetlanie szczegółów użycia według subskrypcji

Możesz przefiltrować plik CSV z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych subskrypcji. Gdy znajdziesz opłaty w ramach określonej subskrypcji, możesz je przeanalizować, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach.

Na poniższej ilustracji przedstawiono listę subskrypcji w witrynie Azure Portal.

![Zrzut ekranu przedstawia listę subskrypcji w witrynie Azure Portal z wywołaną jedną subskrypcją.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Przefiltruj kolumnę **subscriptionName** w pliku CSV użycia platformy Azure i opłat według wartości **WA_Subscription**, aby wyświetlić szczegółowe informacje o opłatach za użycie w ramach tej subskrypcji.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według subskrypcji](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Płatność za rachunek

Instrukcje dotyczące płatności znajdują się na końcu faktury. [Dowiedz się, jak zapłacić](mca-understand-your-invoice.md#how-to-pay).

Jeśli już zapłacono za rachunek, możesz sprawdzić stan płatności na stronie Faktury w witrynie Azure Portal.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych transakcji w witrynie Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

Ukończ przewodnik Szybki start, aby rozpocząć korzystanie z analizy kosztów.

> [!div class="nextstepaction"]
> [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)

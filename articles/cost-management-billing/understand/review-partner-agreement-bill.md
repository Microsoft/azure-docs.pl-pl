---
title: Przeglądanie faktury dla umowy Microsoft Partner Agreement — Azure
description: Dowiedz się, jak przejrzeć informacje o rozliczeniach i użyciu zasobów oraz sprawdzić opłaty za fakturę dotyczącą umowy partnerskiej firmy Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: bf3f454296acafcff6fe775b07abeb7a253749b5
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253532"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Samouczek: Przeglądanie faktury dla umowy partnerskiej firmy Microsoft

 Na koncie rozliczeniowym umowy partnerskiej firmy Microsoft co miesiąc generowana jest jedna faktura na każdy profil rozliczeniowy. Faktura zawiera wszystkie opłaty dla klienta z poprzedniego miesiąca. Aby zrozumieć opłaty widoczne na fakturze, przeanalizuj poszczególne transakcje w witrynie Azure Portal. W witrynie Azure Portal możesz również wyświetlić faktury i porównać opłaty ze szczegółowym zestawieniem użycia.

Aby uzyskać więcej informacji, zobacz [Pobieranie faktur z witryny Azure Portal](download-azure-invoice.md).

Ten samouczek dotyczy tylko partnerów platformy Azure z umową partnerską firmy Microsoft.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych transakcji w witrynie Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć dostęp do konta rozliczeniowego związanego z umową partnerską firmy Microsoft.

Od dnia zasubskrybowania platformy Azure musi upłynąć więcej niż 30 dni. Na platformie Azure opłaty są naliczane na koniec okresu fakturowania.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft

Sprawdź typ umowy, aby określić, czy masz dostęp do konta rozliczeniowego umowy partnerskiej firmy Microsoft.

W witrynie Azure Portal wpisz w polu wyszukiwania *zarządzanie kosztami i rozliczenia*, a następnie wybierz pozycję **Zarządzanie kosztami i rozliczenia**.

![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Jeśli masz dostęp tylko do jednego zakresu rozliczeniowego, wybierz pozycję **Właściwości** po lewej stronie. Masz dostęp do konta rozliczeniowego umowy partnerskiej firmy Microsoft, jeśli typ konta rozliczeniowego to **Umowa partnerska firmy Microsoft**.

![Zrzut ekranu przedstawiający umowę partnerską firmy Microsoft na stronie właściwości](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Jeśli masz dostęp do wielu zakresów rozliczeniowych, sprawdź typ w kolumnie konta rozliczeniowego. Masz dostęp do konta rozliczeniowego umowy partnerskiej firmy Microsoft, jeśli typ konta rozliczeniowego w dowolnym z Twoich zakresów to **Umowa partnerska firmy Microsoft**.

![Zrzut ekranu przedstawiający umowę partnerską firmy Microsoft na stronie listy kont rozliczeniowych](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Przeglądanie zafakturowanych transakcji w witrynie Azure Portal

W obszarze Zarządzanie kosztami i rozliczenia wybierz pozycję **Wszystkie transakcje** po lewej stronie. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego, profilu rozliczeniowego lub klienta, a następnie wybranie pozycji **Wszystkie transakcje**.

Na stronie Wszystkie transakcje są wyświetlane następujące informacje:

![Zrzut ekranu przedstawiający listę rozliczonych transakcji](./media/review-partner-agreement-bill/all-transactions.png)

|Kolumna  |Definicja  |
|---------|---------|
|Data     | Data transakcji  |
|Identyfikator faktury     | Identyfikator faktury, na której jest uwzględniona transakcja. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure |
|Typ transakcji     |  Typ transakcji, na przykład zakup, anulowanie, opłaty za użycie  |
|Rodzina produktów     | Kategoria produktu, na przykład obliczenia w przypadku maszyn wirtualnych lub baza danych w przypadku usługi Azure SQL Database|
|SDKU produktu     | Unikatowy kod identyfikujący wystąpienie produktu |
|Kwota     |  Kwota transakcji      |
|Profil rozliczeniowy     | Profil rozliczeniowy, na którego fakturze jest widoczna ta transakcja |

Możesz wyszukać identyfikator faktury, aby odfiltrować transakcje z tej faktury.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat

Opłaty mają charakter szacunkowy i są uznawane za oczekujące, dopóki nie zostaną zafakturowane. Możesz wyświetlić oczekujące opłaty za profil rozliczeniowy umowy partnerskiej firmy Microsoft w witrynie Azure Portal, aby oszacować następną fakturę. Oczekujące opłaty są szacunkowe i nie zawierają podatków. Rzeczywiste opłaty na kolejnej fakturze mogą się różnić od opłat oczekujących.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Gdy znajdziesz oczekujące opłaty, możesz je wyjaśnić, analizując poszczególne transakcje, które im odpowiadają. Obecnie oczekujące opłaty za użycie nie są wyświetlane na stronie Wszystkie transakcje. Możesz wyświetlić je na stronie subskrypcji platformy Azure.

W obszarze Zarządzanie kosztami i rozliczenia wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Wszystkie transakcje** po lewej stronie.

Wyszukaj *oczekujące*. Użyj filtru **Przedział czasu**, aby wyświetlić opłaty za bieżący lub ubiegły miesiąc.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Wyświetlanie oczekujących opłat według klienta

W obszarze Zarządzanie kosztami i rozliczenia wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Klienci** w lewej części strony.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

Na stronie Klienci są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych klientów skojarzonych z profilem rozliczeniowym. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

### <a name="view-pending-usage-charges"></a>Wyświetlanie oczekujących opłat za użycie

W obszarze Zarządzanie kosztami i rozliczenia wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Subskrypcje platformy Azure** w lewej części strony. Na stronie subskrypcji platformy Azure są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych subskrypcji z profilu rozliczeniowego. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analizowanie opłat za użycie platformy Azure

Możesz przeanalizować opłaty związane z użyciem, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach. Możesz przefiltrować plik z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie dla poszczególnych produktów znajdujących się w pliku PDF faktury. Aby wyświetlić szczegółowe opłaty za użycie określonego produktu, przefiltruj kolumnę **produkt** w pliku CSV użycia i opłat platformy Azure w celu uwzględnienia tylko nazwy tego produktu.

Możesz również filtrować kolumnę **customerName** w pliku CSV użycia i opłat platformy Azure, aby wyświetlić opłaty za dzienne użycie dla poszczególnych klientów. Aby wyświetlić opłaty za dzienne użycie według subskrypcji platformy Azure, przefiltruj kolumnę **subscriptionName**.

## <a name="pay-your-bill"></a>Płatność za rachunek

Instrukcje dotyczące płatności znajdują się na końcu faktury. Możesz uiścić płatność za pomocą przelewu lub czeku w ciągu 60 dni od daty faktury.

Jeśli już zapłacono za rachunek, możesz sprawdzić stan płatności na stronie Faktury w witrynie Azure Portal.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych transakcji w witrynie Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

Dowiedz się więcej o korzystaniu z usługi Azure Cost Management dla partnerów.

> [!div class="nextstepaction"]
> [Wprowadzenie do usługi Azure Cost Management dla partnerów](../costs/get-started-partners.md)

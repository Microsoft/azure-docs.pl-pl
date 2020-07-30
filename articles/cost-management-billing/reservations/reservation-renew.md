---
title: Automatyczne odnawianie rezerwacji platformy Azure
description: Dowiedz się, jak można automatycznie odnawiać rezerwacje platformy Azure, aby kontynuować korzystanie z rabatów na rezerwację.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 644156c29ec084bbc3050aa1d12b779b53084524
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286550"
---
# <a name="automatically-renew-reservations"></a>Automatyczne odnawianie rezerwacji

Możesz odnowić rezerwacje, aby automatycznie zakupić zastąpienie w przypadku wygaśnięcia istniejącej rezerwacji. Automatyczne odnawianie umożliwia łatwe uzyskiwanie rabatów na rezerwacje. Eliminuje również konieczność dokładnego monitorowania terminów wygaśnięcia rezerwacji. Dzięki automatycznemu odnawianiu można zapobiec utracie oszczędności, ponieważ nie jest konieczne ręczne odnawianie. Ustawienie odnowienia jest domyślnie wyłączone. Ustawienie odnowienia można włączyć lub wyłączyć w dowolnym momencie, aż do wygaśnięcia istniejącej rezerwacji.

Odnowienie rezerwacji powoduje utworzenie nowej rezerwacji po wygaśnięciu istniejącej. Nie powoduje to przedłużenia okresu istniejącej rezerwacji.

Zezwól na automatyczne odnawianie w dowolnym momencie. Cena odnowienia jest dostępna 30 dni przed wygaśnięciem istniejącej rezerwacji. Jeśli włączysz odnowienie przed tym terminem, 30 dni przed wygaśnięciem rezerwacji otrzymasz wiadomość e-mail z informacjami o kosztach odnowienia. Cena rezerwacji może ulec zmianie między terminem zablokowania ceny odnowienia a terminem odnowienia. W takim przypadku jako koszt odnowienia zostanie zastosowana niższa z tych dwóch wartości. Można wprowadzać zmiany w ilości rezerwacji. W takim przypadku odnowienie zostanie zaktualizowane z użyciem ceny rynkowej obowiązującej w momencie zmiany ilości.

Korzystanie z odnowienia nie jest obowiązkowe i można z niego zrezygnować w dowolnym momencie przed wygaśnięciem istniejącej rezerwacji.

## <a name="set-up-renewal"></a>Konfigurowanie odnowienia

Przejdź do witryny Azure Portal > **Rezerwacje**.

1. Wybierz rezerwację.
2. Kliknij pozycję **Odnawianie**.
3. Wybierz pozycję **Automatycznie kup nową rezerwację po wygaśnięciu**.  
  ![Przykład przedstawiający odnowienie rezerwacji](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Jeśli nie odnowisz

Usługi nadal działają normalnie. Po wygaśnięciu rezerwacji opłaty są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="required-renewal-permissions"></a>Uprawnienia wymagane do odnawiania

W celu odnowienia rezerwacji trzeba spełnić następujące warunki:

- Musisz być właścicielem istniejącej rezerwacji.
- Jeśli rezerwacja obejmuje zakres pojedynczej subskrypcji lub grupy zasobów, musisz być właścicielem subskrypcji.
- Musisz być właścicielem subskrypcji, jeśli ma ona zakres udostępniony.

## <a name="default-renewal-settings"></a>Domyślne ustawienia odnowienia

Domyślnie odnowienie dziedziczy wszystkie właściwości z wygasającej rezerwacji. Zakup odnowienia rezerwacji dotyczy takiej samej jednostki SKU, regionu, zakresu, subskrypcji rozliczeniowej, okresu i ilości.

Można jednak zaktualizować ilość zakupu odnowienia rezerwacji, aby zoptymalizować oszczędności.

## <a name="when-the-new-reservation-is-purchased"></a>Termin zakupu nowej rezerwacji

Zakup nowej rezerwacji jest realizowany, gdy istniejąca rezerwacja wygaśnie. Staramy się zapobiec wszelkim opóźnieniem między obiema rezerwacjami. Ciągłość zapewnia przewidywalność kosztów i nieprzerwane korzystanie z rabatów.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Zmiana rezerwacji nadrzędnej po ustawieniu odnowienia

Jeśli w wygasającej rezerwacji wprowadzisz jakąkolwiek z następujących zmian, odnowienie rezerwacji zostanie anulowane:

- Podział
- Merge
- Przeniesienie rezerwacji z jednego konta na inne
- Przeniesienie rezerwacji z subskrypcji usługi WebDirect do subskrypcji umowy Enterprise Agreement (EA) lub dowolnej innej metody zakupu
- Odnowienie rejestracji

Podczas odnawiania nowa rezerwacja dziedziczy zakres i ustawienie elastyczności rozmiaru wystąpienia z rezerwacji wygasającej.

## <a name="new-reservation-permissions"></a>Uprawnienia do nowej rezerwacji

Na platformie Azure uprawnienia z wygasającej rezerwacji są kopiowane do nowej rezerwacji. Ponadto dostęp do nowej rezerwacji ma administrator konta subskrypcji użytego do zakupu rezerwacji.

## <a name="potential-renewal-problems"></a>Potencjalne problemy z odnowieniem

Platforma Azure może nie przetworzyć odnowienia, jeśli:

- Nie można pobrać płatności
- Wystąpił błąd systemu podczas odnawiania
- Wygasające jednostki SKU nie są aktywne podczas odnawiania
- Umowa EA jest odnawiana z użyciem innej umowy EA

Jeśli wystąpi którykolwiek z powyższych warunków i odnowienie zostanie dezaktywowane, otrzymasz powiadomienie e-mail.

## <a name="renewal-notification"></a>Powiadomienie o odnowieniu

Wiadomości e-mail są wysyłane do różnych osób w zależności od metody zakupu:

- Klienci z umowami EA — wiadomości e-mail są wysyłane do kontaktów dla powiadomień ustawionych w portalu EA.
- Klienci korzystający z indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem — wiadomości e-mail są wysyłane do użytkowników skonfigurowanych jako administratorzy konta.
- Klienci z subskrypcją dostawcy rozwiązań w chmurze — wiadomości e-mail są wysyłane do kontaktu dla powiadomień partnera.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz temat [Co to jest Azure Reservations?](save-compute-costs-reservations.md)

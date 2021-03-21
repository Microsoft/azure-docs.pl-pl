---
title: Zapisz koszty obliczeń z zarezerwowaną pojemnością
description: Dowiedz się, jak kupić Azure Data Factory zarezerwowaną pojemność przepływu danych, aby zaoszczędzić na kosztach obliczeniowych.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 26a4692603d8e8a80a52ea77bdd56617131cea5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593904"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Oszczędność kosztów zasobów przy użyciu zarezerwowanej pojemności Azure Data Factory przepływów danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Oszczędność pieniędzy dzięki Azure Data Factory kosztom przepływu danych dzięki zatwierdzeniu do rezerwacji zasobów obliczeniowych w porównaniu z cenami z płatność zgodnie z rzeczywistym użyciem. Dzięki zarezerwowanej pojemności zobowiązuje się do użycia przepływu danych ADF przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby zakupić zastrzeżoną pojemność, należy określić region świadczenia usługi Azure, typ obliczeń, liczbę rdzeni i termin.

Nie trzeba przypisywać rezerwacji do określonej fabryki lub środowiska Integration Runtime. Istniejące fabryki lub nowo wdrożone fabryki automatycznie uzyskają korzyść. Zakup rezerwacji polega na przekazaniu użycia kosztów obliczeniowych przepływu danych przez okres jeden lub trzy lata. Po zakupieniu rezerwacji opłaty za obliczenia, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. 

Możesz zakupić [zarezerwowaną pojemność](https://portal.azure.com) , wybierając rezerwacje [z przodu lub z miesięcznymi opłatami](../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić pojemność zarezerwowaną:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji. Zarezerwowana pojemność.

Aby uzyskać więcej informacji na temat sposobu, w jaki Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym użyciem są obciążani opłatami za zakupy rezerwacji, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) i informacje [dotyczące użycia rezerwacji na platformie Azure dla subskrypcji z opcją płatność za](../cost-management-billing/reservations/understand-reserved-instance-usage.md)użycie.

> [!NOTE]
> Zakup zarezerwowanej pojemności nie powoduje wstępnego przydzielenia lub zarezerwowania określonych zasobów infrastruktury (maszyn wirtualnych lub klastrów) do użytku.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Przed zakupem określ odpowiednie rozmiary Azure IR

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone przepływy danych przy użyciu tej samej warstwy obliczeniowej.

Załóżmy na przykład, że wykonujesz potok co godzinę przy użyciu pamięci zoptymalizowanej z 32 rdzeniami. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu dodatkowego potoku korzystającego z rdzeni ogólnego przeznaczenia 64. Załóżmy również, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku wprowadź liczbę rdzeni wymaganych dla każdego typu obliczenia przez 1 godzinę. W witrynie Azure Portal Wyszukaj rezerwacje. Wybierz Data Factory > przepływy danych, a następnie wprowadź 32 dla zoptymalizowanej pod kątem pamięci i 64.

## <a name="buy-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku **rezerwacje zakupu** wybierz pozycję **przepływy danych ADF** , aby zakupić nową rezerwację dla przepływów danych ADF.
4. Wypełnij wymagane pola i wybrane atrybuty kwalifikuj, aby uzyskać rabat zarezerwowanej pojemności. Rzeczywista liczba przepływów danych, które pobiera rabat, zależy od wybranego zakresu i ilości.
5. Sprawdź koszt rezerwacji zdolności produkcyjnych w sekcji **koszty** .
6. Wybierz pozycję **Kup**.
7. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby zobaczyć stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Informacje na temat rabatu na rezerwacje platformy Azure](data-flow-understand-reservation-charges.md)

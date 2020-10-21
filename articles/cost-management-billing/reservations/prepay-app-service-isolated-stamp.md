---
title: Oszczędzanie w usłudze Azure App Service za pomocą wydajności rezerwowej
description: Dowiedz się, jak możesz zaoszczędzić koszty w przypadku podatku od czynności cywilnoprawnych za korzystanie z usługi Azure App Service w izolowanym środowisku za pomocą wydajności rezerwowej.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: feb7be6ad5dbd0dfbd04b05836842d0476780d40
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151530"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Oszczędzanie kosztów w przypadku podatku od czynności cywilnoprawnych za korzystanie z usługi Azure App Service w izolowanym środowisku za pomocą wydajności rezerwowej

Możesz zaoszczędzić pieniądze na podatku od czynności cywilnoprawnych za korzystanie z usługi Azure App Service w izolowanym środowisku, zobowiązując się do użycia zarezerwowanych sygnatur przez okres trzech lat. Aby kupić wydajność rezerwową objętą podatkiem od czynności cywilnoprawnych w izolowanym środowisku, należy wybrać region świadczenia usługi Azure, w którym zostanie wdrożona sygnatura, oraz liczbę kupowanych sygnatur.

Gdy wykupisz rezerwację, za użycie w ramach podatku od czynności cywilnoprawnych w izolowanym środowisku zgodne z atrybutami rezerwacji nie będą naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacja jest stosowana automatycznie do izolowanych sygnatur, które są zgodne z zakresem i regionem wydajności rezerwowej. Nie musisz przypisywać rezerwacji do izolowanej sygnatury. Rezerwacja nie dotyczy procesów roboczych, dlatego opłaty za inne zasoby związane z sygnaturą są naliczane oddzielnie.

Gdy wydajność rezerwowa wygaśnie, izolowane sygnatury nadal będą działać, ale będą za nie naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

## <a name="determine-the-right-reservation-to-purchase"></a>Określanie odpowiedniej rezerwacji do kupienia

Kupując rezerwację, zobowiązujesz się, że w ciągu kolejnych trzech lat będziesz używać ilości zarezerwowanych. Sprawdź dane dotyczące użycia, aby określić, ilu izolowanych sygnatur usługi App Service stale używasz i ilu możesz używać w przyszłości.

Ponadto upewnij się, że wiesz, jak izolowana sygnatura emituje miernik systemu Linux lub Windows.

- Pusta izolowana sygnatura domyślnie emituje miernik sygnatur systemu Windows. Na przykład bez wdrożonych procesów roboczych. W dalszym ciągu emituje ten miernik, jeśli procesy robocze systemu Windows są wdrożone w sygnaturze.
- Miernik zostaje zmieniony na miernik sygnatur systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux.
- Jeśli wdrożone są zarówno procesy robocze systemu Linux, jak i Windows, sygnatura emituje miernik systemu Windows.

Zatem w czasie trwania sygnatury miernik sygnatur może być zmieniany na miernik systemu Windows lub na miernik systemu Linux.

Jeśli masz co najmniej jeden proces roboczy systemu Windows w sygnaturze, kup rezerwacje sygnatur systemu Windows. Jedyna sytuacja, w której należy kupić rezerwację sygnatury systemu Linux ma miejsce, gdy planujesz mieć w sygnaturze _tylko_ procesy robocze systemu Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Kupowanie wydajności rezerwowej izolowanej sygnatury

Wydajność rezerwową izolowanej sygnatury można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](./prepare-buy-reservation.md). Do zakupu wydajności rezerwowej potrzebna jest rola właściciela co najmniej w jednej subskrypcji Enterprise lub w subskrypcji indywidualnej z ratami płatności zgodnie z rzeczywistym użyciem.

- W przypadku subskrypcji Enterprise w witrynie [EA portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) wydajność rezerwową usługi Azure Synapse Analytics mogą kupić tylko agenci administracyjni lub agenci sprzedaży.

**Aby dokonać zakupu:**

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Wybierz subskrypcję. Z listy **Subskrypcja** wybierz subskrypcję, w ramach której jest opłacana wydajność rezerwowa. Kosztami wydajności rezerwowej jest obciążana forma płatności subskrypcji. Wymagany typ subskrypcji to Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) albo Płatność zgodnie z rzeczywistym użyciem (numer ofert: MS-AZR-0003P lub MS-AZR-0023P) lub Subskrypcja CSP.
    - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
    - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
1. Za pomocą listy **Zakres** wybierz zakres subskrypcji.
    - **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
    - **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
    - **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
1. Wybierz pozycję **Region**, aby wskazać region świadczenia usługi Azure, którego ma dotyczyć wydajność rezerwowa.
1. Wybierz typ Plan izolowany, a następnie kliknij pozycję **Wybierz**.  
    ![Przykład ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Wprowadź liczbę izolowanych sygnatur usługi App Service, które mają zostać zarezerwowane. Na przykład liczba trzy daje trzy zarezerwowane sygnatury na region. Kliknij pozycję **Next: Przejrzyj i kup**.
1. Przejrzyj i kliknij pozycję **Kup teraz**.

Po zakupie przejdź do pozycji [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), aby wyświetlić stan zakupu i monitorować go w dowolnym momencie.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot kosztów rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Zastosowanie rabatu widoczne w danych dotyczących użycia

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Dane użycia pokazują rabat rezerwacji dla każdego wystąpienia sygnatury w każdej rezerwacji.

Aby uzyskać więcej informacji na temat tego, jak jest wyświetlany rabat rezerwacji w danych użycia, zobacz [Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement](understand-reserved-instance-usage-ea.md), jeśli jesteś klientem z umową Enterprise Agreement (EA). W przeciwnym razie zobacz [Informacje na temat użycia rezerwacji platformy Azure dla indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Czym są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Omówienie sposobu stosowania rabatu rezerwacji izolowanej sygnatury usługi Azure App Service](reservation-discount-app-service-isolated-stamp.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
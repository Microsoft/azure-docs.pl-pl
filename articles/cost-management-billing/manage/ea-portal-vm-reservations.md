---
title: Wystąpienia zarezerwowane maszyn wirtualnych z umowami EA platformy Azure
description: W tym artykule przedstawiono podsumowanie sposobu, w jaki rezerwacje platformy Azure dla wystąpień zarezerwowanych maszyn wirtualnych mogą pomóc w zaoszczędzeniu pieniędzy dzięki rejestracji w przedsiębiorstwie.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 7c8a35e7c8ecd0952f8e02b66c5071253d9b0445
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348929"
---
# <a name="azure-ea-vm-reserved-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych z umowami EA platformy Azure

W tym artykule przedstawiono podsumowanie sposobu, w jaki rezerwacje platformy Azure dla wystąpień zarezerwowanych maszyn wirtualnych mogą pomóc w zaoszczędzeniu pieniędzy dzięki rejestracji w przedsiębiorstwie. Aby uzyskać więcej informacji na temat rezerwacji, zobacz temat [Co to są rezerwacje platformy Azure?](../reservations/save-compute-costs-reservations.md)

## <a name="reservation-exchanges-and-refunds"></a>Wymiana rezerwacji i zwrot kosztów

Rezerwację można wymienić na inną rezerwację tego samego typu. Jeśli rezerwacja nie jest już potrzebna, można uzyskać zwrot jej kosztów, maksymalnie do 50 000 USD rocznie. Aby wymienić rezerwację lub uzyskać za nią zwrot pieniędzy, można użyć witryny Azure Portal. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

### <a name="partial-refunds"></a>Częściowe zwroty

Dokonamy częściowego zwrotu, gdy klienci z umową EA zwrócą rezerwacje, które zakupili jako nadwyżkowe, a nie przy użyciu zobowiązania pieniężnego.

Zwrot będzie wyświetlany w portalu EA Portal jako ujemna korekta w poprzednim miesiącu oraz dodatnia korekta w bieżącym miesiącu. Będzie on widoczny w sposób podobny do wymiany rezerwacji. Faktura korygująca będzie zawierać numer pierwotnej faktury. W związku z tym, aby uzgodnić początkowy zakup z fakturą korygującą, skorzystaj z numeru pierwotnej faktury.

## <a name="reservation-costs-and-usage"></a>Koszty i użycie rezerwacji

Klienci z umową Enterprise Agreement mogą wyświetlać dane dotyczące kosztów i użycia w witrynie Azure Portal i interfejsach API REST. W przypadku kosztów i użycia rezerwacji można:

- Pobrać dane zakupu rezerwacji.
- Dowiedzieć się, w przypadku której subskrypcji, grupy zasobów lub zasobu została użyta rezerwacja.
- Dokonać obciążenia zwrotnego za użycie rezerwacji.
- Obliczyć oszczędności z rezerwacji.
- Pobrać dane dotyczące niedostatecznego użycia rezerwacji.
- Amortyzować koszty rezerwacji.

Aby uzyskać więcej informacji na temat kosztów i użycia wystąpień zarezerwowanych, zobacz [Pobieranie kosztów i użycia wystąpień zarezerwowanych w ramach umowy Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

Aby uzyskać informacje o cenach, zobacz temat [Ceny maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Ceny maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Obsługa interfejsu API wystąpień zarezerwowanych

Używając interfejsów API platformy Azure, można programowo uzyskiwać informacje dotyczące rezerwacji oprogramowania lub usług platformy Azure dla organizacji. Interfejsy API umożliwiają na przykład:

- Znajdowanie rezerwacji do zakupienia
- Kupowanie rezerwacji
- Wyświetlanie zakupionych rezerwacji
- Wyświetlanie dostępu do rezerwacji i zarządzanie nim
- Dzielenie lub scalanie rezerwacji
- Zmienianie zakresu rezerwacji

Aby uzyskać więcej informacji, zobacz [Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych na platformie Azure

Wystąpienia zarezerwowane umożliwiają obniżenie kosztów maszyn wirtualnych nawet o 72% w stosunku do cen w modelu płatności zgodnie z rzeczywistym użyciem na wszystkich maszynach wirtualnych. lub uzyskanie nawet do 82% oszczędności w przypadku połączenia z korzyścią użycia hybrydowego platformy Azure. Wystąpienia zarezerwowane ułatwiają lepsze zarządzanie obciążeniami, budżetem i prognozami dzięki płatności z góry za rok lub trzy lata. Możesz też zmieniać lub anulować rezerwacje w miarę zachodzenia zmian w potrzebach biznesowych.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Jak kupić wystąpienia zarezerwowane maszyn wirtualnych

Aby kupić wystąpienia zarezerwowane maszyn wirtualnych platformy Azure, administrator rejestracji na platformie Azure z umową Enterprise musi włączyć opcję zakupu _Zarezerwuj wystąpienia_. Ta opcja znajduje się w sekcji _Szczegóły rejestracji_ na karcie _Rejestracja_ w witrynie [Azure EA Portal](https://ea.azure.com/).

Kiedy będzie można już dodawać wystąpienia zarezerwowane w rejestracji EA, dowolny właściciel konta z aktywną subskrypcją skojarzoną z rejestracją EA będzie mógł kupić wystąpienia zarezerwowane maszyn wirtualnych w witrynie [Azure Portal](https://aka.ms/reservations). Aby uzyskać więcej informacji, zobacz temat [Przedpłaty za maszyny wirtualne i oszczędzanie pieniędzy przy użyciu wystąpień zarezerwowanych maszyn wirtualnych](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Jak wyświetlić szczegóły zakupu wystąpienia zarezerwowanego

Możesz wyświetlić szczegóły zakupu wystąpienia zarezerwowanego za pomocą menu _Rezerwacje_ po lewej stronie witryny [Azure Portal](https://aka.ms/reservations) lub z poziomu witryny [Azure EA Portal](https://ea.azure.com/). Wybierz pozycję **Raporty** z menu po lewej stronie i przewiń w dół do sekcji _Opłaty według usług_ na karcie _Podsumowanie użycia_. Przewiń do dołu sekcji. Twoje zakupy i użycie wystąpień zarezerwowanych zostaną wyświetlone na końcu z opisem `1 year` lub `3 years` obok nazwy usługi, na przykład: `Standard_DS1_v2 eastus 1 year` lub `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Jak mogę zmienić subskrypcję skojarzoną z wystąpieniem zarezerwowanym lub przenieść swoje korzyści z wystąpienia zarezerwowanego do innej subskrypcji w ramach tego samego konta?

Subskrypcję, która otrzymuje korzyści z wystąpienia zarezerwowanego, możesz zmienić w następujący sposób:

- Zaloguj się w witrynie [Azure Portal](https://aka.ms/reservations).
- Zaktualizuj zastosowany zakres subskrypcji przez skojarzenie innej subskrypcji w ramach tego samego konta.

Aby uzyskać więcej informacji na temat zmiany zakresu rezerwacji, zobacz [Zmienianie zakresu rezerwacji](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Jak wyświetlić szczegóły użycia wystąpienia zarezerwowanego

Możesz wyświetlić szczegóły użycia wystąpienia zarezerwowanego w witrynie [Azure Portal](https://aka.ms/reservations) lub w witrynie [Azure EA Portal](https://ea.azure.com/) (w przypadku klientów z umowami EA, którzy mają dostęp do wyświetlania informacji dotyczących rozliczeń) w obszarze _Raporty_ > _Podsumowania użycia_ > _Opłaty według usług_. Wystąpienia zarezerwowane mogą być identyfikowane jako nazwy usług zawierające element „Reservation”, na przykład: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Szczegóły użycia i zaawansowany raport do pobrania w formie pliku CSV zawierają dodatkowe informacje o użyciu wystąpienia zarezerwowanego. Pole _Informacje dodatkowe_ ułatwia sprawdzenie użycia wystąpienia zarezerwowanego.

Jeśli nie użyto korzyści z użycia hybrydowego platformy Azure do zakupu wystąpień zarezerwowanych maszyn wirtualnych platformy Azure, wystąpienia zarezerwowane będą emitować dwa mierniki (sprzęt i oprogramowanie). Jeśli do zakupu wystąpienia zarezerwowanego użyto korzyści z użycia hybrydowego platformy Azure, licznik oprogramowania nie będzie widoczny w szczegółach użycia wystąpienia zarezerwowanego.

### <a name="reserved-instance-billing"></a>Rozliczenia wystąpień zarezerwowanych

Klienci korporacyjni kupują wystąpienia zarezerwowane maszyn wirtualnych platformy Azure, korzystając z przedpłaty za platformę Azure. Jeśli rejestracja ma wystarczająco duże saldo przedpłaty za platformę Azure na pokrycie zakupu wystąpienia zarezerwowanego, kwota zostanie odjęta od salda przedpłaty za platformę Azure i nie otrzymasz faktury za zakup.

Jeśli klient z umową Azure EA wykorzystał już całą przedpłatę za platformę Azure, nadal może kupić wystąpienie zarezerwowane, przy czym ten zakup zostanie umieszczony na następnej fakturze z rozliczeniem nadwyżki. Nadwyżka za wystąpienie zarezerwowane, jeśli istnieje, będzie częścią zwykłej faktury z rozliczeniem nadwyżki.

### <a name="reserved-instance-expiration"></a>Wygaśnięcie wystąpienia zarezerwowanego

Powiadomienia e-mail będą wysyłane na 30 dni przed wygaśnięciem rezerwacji. Po wygaśnięciu rezerwacji wdrożone maszyny wirtualne będą nadal działać, a opłaty będą naliczane przy użyciu stawki dla płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [ofertę wystąpień zarezerwowanych maszyn wirtualnych.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o rezerwacjach platformy Azure, zobacz temat [Co to są rezerwacje platformy Azure?](../reservations/save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o kosztach i użyciu rezerwacji w przedsiębiorstwie, zobacz temat [Get Enterprise Agreement reservation costs and usage](../reservations/understand-reserved-instance-usage-ea.md) (Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement).
- Aby uzyskać informacje o cenach, zobacz temat [Ceny maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Ceny maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
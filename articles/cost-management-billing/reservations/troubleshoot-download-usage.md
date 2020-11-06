---
title: Rozwiązywanie problemów z pobieraniem szczegółowych danych użycia rezerwacji platformy Azure
description: Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z pobieraniem szczegółowych danych użycia wystąpienia zarezerwowanego wyświetlanych w witrynie Azure Portal jako niedostępne.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147341"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Rozwiązywanie problemów z pobieraniem szczegółowych danych użycia rezerwacji platformy Azure

Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z pobieraniem szczegółowych danych użycia wystąpienia zarezerwowanego wyświetlanych w witrynie Azure Portal jako niedostępne.

## <a name="symptoms"></a>Objawy

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i przejdź do obszaru **Rezerwacje**.
1. Wybierz rezerwację.
1. Na stronie przeglądu rezerwacji w widoku domyślnym są wyświetlane dane użycia z co najmniej ostatnich siedmiu dni. Możesz wybrać opcję **Pobierz jako plik CSV** , aby pobrać szczegóły użycia rezerwacji.
1. Gdy zmienisz zakres czasu, opcja **Pobierz jako plik CSV** stanie się niedostępna.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Przykład pokazujący, że opcja Pobierz jako plik CSV jest niedostępna" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Przyczyna

Ze względu na ograniczenia techniczne platforma Azure nie obsługuje pobierania danych z przedziału czasu dłuższego niż siedem dni. W przypadku klientów mających dużą liczbę rezerwacji dłuższe okresy generują duże ilości danych. Zwracanie danych za pomocą interfejsów API powoduje ograniczenie zasobów platformy Azure.

## <a name="solution"></a>Rozwiązanie

Zdajemy sobie sprawę, że klienci chcą pobierać dane z dłuższych przedziałów czasu. Obecnie jednak nie ma możliwości pobierania danych użycia rezerwacji z długich okresów.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat rezerwacji, zobacz temat [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
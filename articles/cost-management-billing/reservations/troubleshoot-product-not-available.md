---
title: Rozwiązywanie problemów z brakiem dostępności typu rezerwacji platformy Azure
description: Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z wystąpieniami zarezerwowanymi wyświetlanymi w witrynie Azure Portal jako niedostępne.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798206"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Rozwiązywanie problemów z brakiem dostępności typu rezerwacji

Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z wystąpieniami zarezerwowanymi wyświetlanymi w witrynie Azure Portal jako niedostępne.

## <a name="symptoms"></a>Objawy

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i przejdź do obszaru **Rezerwacje** .
2. Wybierz pozycję **+ Dodaj** , a następnie wybierz produkt.
3. Wybierz kartę **Wszystkie produkty** .
4. Wybierz jeden produkt z listy. Może zostać wyświetlony jeden z następujących komunikatów:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Przykład pokazujący komunikat o niedostępności produktu w wybranej subskrypcji lub regionie" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Przykład pokazujący komunikat o niedostępności produktu w wybranej subskrypcji lub regionie" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Przyczyna

Niektóre rezerwacje są niedostępne do zakupu, ponieważ:

- Nie każdy produkt wystąpienia zarezerwowanego jest dostępny do zakupu w każdym regionie
- Twoja subskrypcja ma ograniczenie limitu przydziału

### <a name="cause-1"></a>Przyczyna 1

Nie wszystkie produkty wystąpień zarezerwowanych są dostępne do zakupu. Platforma Azure decyduje o tym, czy zezwalać na dostęp do niektórych produktów, w zależności od kosztów związanych z oferowaniem rabatu pieniężnego dla klientów. W innych przypadkach platforma Azure nie oferuje niektórych produktów ze względu na warunki dotyczące pojemności w określonych centrach danych. Ponadto niektóre grupy deweloperów produktów platformy Azure mogą teraz zezwalać na dostęp do określonych produktów, ponieważ chcą wycofać starszy produkt.

W innych przypadkach platforma Azure nakłada ograniczenia pojemności na różne produkty w zależności od popytu na te zasoby w niektórych regionach. Na przykład takie ograniczenie może zostać wprowadzone, gdy kończy się zapotrzebowanie na określony rozmiar maszyny wirtualnej w centrum danych. W tym przykładzie platforma Azure nie może zagwarantować pojemności klientom, którzy kupili rezerwację tego rozmiaru w tym regionie. Istnieją także pewne produkty, które są unikatowe z różnych powodów. Te produkty są udostępniane tylko dla małego, wybranego grona klientów.

Skoro niektóre rezerwacje są niedostępne do zakupu, to dlaczego są wyświetlane w witrynie Azure Portal? Dzieje się tak dlatego, że użytkownicy tworzą wnioski o pomoc techniczną, informując o tym, że nie mogą znaleźć produktów, których potrzebują. Zespół deweloperów usługi Azure Reservation stwierdził, że wyświetlanie wszystkich produktów z komunikatem `Product unavailable` powoduje, że liczba zgłaszanych wniosków o pomoc techniczną jest mniejsza niż w przypadku, gdy niektóre produkty nie są wyświetlane.

### <a name="cause-2"></a>Przyczyna 2

Twoja subskrypcja ma ograniczenie limitu przydziału. Subskrypcje mają limity dotyczące liczby rdzeni procesora, których mogą używać. Limit dotyczy niektórych produktów wystąpień zarezerwowanych, w szczególności maszyn wirtualnych. Platforma Azure potrzebuje pewności, że osoby, które kupują wystąpienie zarezerwowane, mogą go używać. Platforma Azure przeprowadza w witrynie Azure Portal proste, pobieżne sprawdzenie, aby upewnić się, że w Twojej subskrypcji jest wystarczająca liczba bezpłatnych rdzeni do wdrożenia maszyny wirtualnej i uzyskania korzyści wynikających z zakupu rezerwacji.

Sprawdzenie umożliwia dodanie określonego produktu do koszyka, a zakup rezerwacji jest prosty. Platforma Azure szacuje łączną liczbę rdzeni procesora dostępnych dla Twojej subskrypcji i sprawdza, czy jest ona większa niż liczba rdzeni dla wybranego elementu.

Platforma Azure nie sprawdza limitu przydziału dla wystąpień zarezerwowanych zakresu **Współdzielona** . Korzyść z wystąpienia zarezerwowanego zakresu Współdzielona dotyczy wszystkich subskrypcji w ramach rejestracji. Platforma Azure nie może ustalić, czy we wszystkich Twoich subskrypcjach jest wystarczająca liczba bezpłatnych rdzeni, aby wdrożyć zasób. Niezależnie od limitu przydziału platforma Azure zawsze umożliwia wybranie rozmiaru maszyny wirtualnej, gdy wybranym zakresem jest Współdzielona.

Ponadto platforma Azure nie sprawdza limitu przydziału w przypadku zakupów **Zalecane** . Zalecenia są oparte na aktywnym użyciu. Platforma Azure zakłada, że masz wystarczającą liczbę rdzeni, aby uruchomić określony rozmiar maszyny wirtualnej, ponieważ użycie wymagane do utworzenia zalecenia zostało już wygenerowane.

## <a name="solution"></a>Rozwiązanie

W zależności od wyświetlanego komunikatu o błędzie zastosuj jedno z poniższych rozwiązań problemu.

### <a name="solution-1"></a>Rozwiązanie 1

Jeśli jest wyświetlany komunikat _Produkt niedostępny_ , wybierz link **Kontakt z pomocą techniczną** w komunikacie o błędzie, aby poprosić o dodanie wyjątku dla Twojej subskrypcji. Wyjątki nie są zawsze udzielane.

### <a name="solution-2"></a>Rozwiązanie 2

Jeśli jest wyświetlany komunikat o błędzie _Niewystarczający limit przydziału rdzeni_ , możesz zmienić zakres na **Współdzielona** . Po zakupie rezerwacji możesz zmienić zakres rezerwacji **Współdzielona** na **Pojedyncza** .

Możesz także wybrać link **Poproś o zwiększenie limitu przydziału** w komunikacie o błędzie, aby poprosić o dodatkowy limit przydziału rdzeni procesora dla subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat opcji zakres rezerwacji, zobacz [Zakresy rezerwacji](prepare-buy-reservation.md#scope-reservations).
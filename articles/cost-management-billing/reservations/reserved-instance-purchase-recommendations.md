---
title: Zalecenia dotyczące rezerwacji na platformie Azure
description: Dowiedz się więcej na temat zaleceń dotyczących rezerwacji na platformie Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928526"
---
# <a name="reservation-recommendations"></a>Rekomendacje dotyczące rezerwacji

Zalecenia dotyczące zakupu wystąpień zarezerwowanych na platformie Azure są udostępniane za pomocą [interfejsu API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations) użycia platformy Azure, usługi [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) i środowiska zakupów rezerwacji w witrynie Azure Portal.

Poniższe kroki definiują sposób obliczania zaleceń:

1. Aparat zaleceń oblicza godzinowe użycie zasobów w danym zakresie w ciągu ostatnich 7, 30 i 60 dni.
2. W oparciu o dane użycia aparat symuluje koszty z rezerwacjami i bez nich.
3. Koszty są symulowane dla różnych ilości i zalecana jest ilość, która maksymalizuje oszczędności.
4. Jeśli Twoje zasoby są regularnie zamykane, symulacja nie znajdzie żadnych oszczędności i nie poda żadnych zaleceń dotyczących zakupu.
5. Obliczenia rekomendacje obejmują wszelkie specjalne rabaty, które mogą mieć zastosowanie na żądanie.

## <a name="recommendations-in-the-azure-portal"></a>Zalecenia w witrynie Azure Portal

Zalecenia dotyczące zakupu rezerwacji są również wyświetlane w witrynie Azure Portal w środowisku zakupu. Zalecenia są wyświetlane wraz z **zalecaną ilością**. W przypadku zakupu ilość zalecana przez platformę Azure zapewni maksymalne oszczędności. Mimo że możesz kupić dowolną ilość, której potrzebujesz, w przypadku zakupienia innej ilości oszczędności nie będą optymalne.

Przyjrzyjmy się kilku przykładom.

Na poniższym przykładowym obrazie przedstawiającym wybrane zalecenie platforma Azure zaleca zakupienie 6 rezerwacji.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Przykład przedstawiający zalecenie dotyczące zakupu rezerwacji" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Więcej informacji na temat zalecenia pojawia się po wybraniu pozycji **Pokaż szczegóły**. Na poniższym obrazie przedstawiono szczegółowe informacje na temat zalecenia. Zalecana ilość jest obliczana dla największego możliwego użycia i zależy od historycznego użycia. Zalecenie może nie dotyczyć 100-procentowego wykorzystania, jeśli Twoje użycie nie jest spójne. W przykładzie należy zauważyć, że wykorzystanie jest wahania w czasie. Przedstawiony jest koszt rezerwacji, możliwe oszczędności i procent wykorzystania.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Przykład pokazujący szczegóły rekomendacji dotyczącej zakupu rezerwacji " :::

Wykres i szacowane wartości zmieniają się po zwiększeniu zalecanej ilości. Zwiększając ilość rezerwacji, oszczędności zostaną zmniejszone, ponieważ będziesz mieć ograniczone użycie rezerwacji. Innymi słowy, zapłacisz za rezerwacje, które nie będą w pełni wykorzystywane.

Zmniejszenie ilości rezerwacji spowoduje zmniejszenie oszczędności. Mimo iż wykorzystanie będzie wyższe, prawdopodobnie będą występowały okresy, w których rezerwacje nie będą w pełni pokrywały użycia. Wykorzystanie przekraczające ilość rezerwacji będzie używane przez droższe zasoby płatne zgodnie z rzeczywistym użyciem. Ilustruje to poniższy przykładowy obraz. Ręcznie zmniejszyliśmy ilość rezerwacji do 4. Użycie rezerwacji zostało zwiększone, ale ogólne oszczędności są ograniczone, ponieważ obecne są koszty płatność zgodnie z rzeczywistym użyciem.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Przykład przedstawiający zmienione szczegóły zalecenia dotyczącego zakupu rezerwacji" :::

Aby zmaksymalizować oszczędności na rezerwacjach, staraj się kupować rezerwacje w ilości jak najbardziej zbliżonej do zalecenia.

## <a name="recommendations-in-azure-advisor"></a>Zalecenia w usłudze Azure Advisor

Zalecenia dotyczące zakupu rezerwacji są dostępne w usłudze Azure Advisor. Należy pamiętać o następujących kwestiach:

- Usługa Advisor zapewnia zalecenia wyłącznie w zakresie jednej subskrypcji. Jeśli chcesz zobaczyć zalecenia dotyczące całego zakresu rozliczeniowego (konto rozliczeniowe lub profil rozliczeń), wówczas:
  -  W Azure Portal przejdź do pozycji **rezerwacje**  >  **Dodaj** , a następnie wybierz typ, dla którego chcesz zobaczyć zalecenia.
- Zalecenia dostępne w usłudze Advisor uwzględniają poprzedni 30-dniowy trend użycia.
- Liczba zaleceń i oszczędności są przeznaczone dla rezerwacji z trzech lat, jeśli są dostępne. Jeśli dla usługi nie jest sprzedawana rezerwacja roczna, zalecenie jest obliczane przy użyciu ceny rezerwacji rocznej.
- Obliczenia rekomendacje obejmują wszelkie specjalne rabaty, które mogą mieć zastosowanie na żądanie.
- W przypadku zakupienia rezerwacji dotyczącej zakresu współdzielonego, zalecenia dotyczące zakupu rezerwacji usługi Advisor mogą potrwać do pięciu dni.

## <a name="other-expected-api-behavior"></a>Inne oczekiwane zachowanie interfejsu API

- W przypadku korzystania z okresu ostatnich siedmiu dni zalecenia mogą nie zostać przedstawione, gdy maszyny wirtualne były zamknięte przez więcej niż jeden dzień.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../manage/understand-vm-reservation-charges.md).

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
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767929"
---
# <a name="reservation-recommendations"></a>Rekomendacje dotyczące rezerwacji

Zalecenia dotyczące zakupu wystąpień zarezerwowanych na platformie Azure są udostępniane za pomocą [interfejsu API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations) użycia platformy Azure, usługi [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) i środowiska zakupów rezerwacji w witrynie Azure Portal.

Poniższe kroki definiują sposób obliczania zaleceń:

1. Aparat zaleceń oblicza godzinowe użycie zasobów w danym zakresie w ciągu ostatnich 7, 30 i 60 dni.
2. W oparciu o dane użycia aparat symuluje koszty z rezerwacjami i bez nich.
3. Koszty są symulowane dla różnych ilości i zalecana jest ilość, która maksymalizuje oszczędności.
4. Jeśli Twoje zasoby są regularnie zamykane, symulacja nie znajdzie żadnych oszczędności i nie poda żadnych zaleceń dotyczących zakupu.
5. Obliczenia rekomendacji obejmują wszelkie rabaty specjalne, które mogą mieć zastosowanie do stawek użycia na żądanie.

## <a name="recommendations-in-the-azure-portal"></a>Zalecenia w witrynie Azure Portal

Zalecenia dotyczące zakupu rezerwacji są również wyświetlane w witrynie Azure Portal w środowisku zakupu. Zalecenia są wyświetlane wraz z **zalecaną ilością**. W przypadku zakupu ilość zalecana przez platformę Azure zapewni maksymalne oszczędności. Chociaż możesz kupić dowolną ilość, to w przypadku zakupu innej ilości oszczędności nie będą optymalne.

Przyjrzyjmy się kilku przykładom.

Na poniższym przykładowym obrazie przedstawiającym wybrane zalecenie platforma Azure zaleca zakupienie 6 rezerwacji.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Przykład przedstawiający zalecenie dotyczące zakupu rezerwacji" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Więcej informacji o rekomendacji pojawia się po wybraniu opcji **Zobacz szczegóły.** Na poniższym obrazie przedstawiono szczegółowe informacje na temat zalecenia. Zalecana ilość jest obliczana dla największego możliwego użycia i jest oparta na historycznym użyciu. Zalecenie może nie dotyczyć 100-procentowego wykorzystania, jeśli Twoje użycie nie jest spójne. W tym przykładzie zwróć uwagę, że wykorzystanie zmieniało się w czasie. Przedstawiony jest koszt rezerwacji, możliwe oszczędności i procent wykorzystania.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Przykład przedstawiający szczegóły zalecenia dotyczącego zakupu rezerwacji " :::

Wykres i szacowane wartości zmieniają się po zwiększeniu zalecanej ilości. Zwiększenie ilości rezerwacji spowoduje zmniejszenie oszczędności, ponieważ spowoduje to zmniejszenie użycia rezerwacji. Innymi słowy, zapłacisz za rezerwacje, które nie będą w pełni wykorzystywane.

Jeśli zmniejszysz ilość rezerwacji, oszczędności również się zmniejszą. Mimo iż wykorzystanie będzie wyższe, prawdopodobnie będą występowały okresy, w których rezerwacje nie będą w pełni pokrywały użycia. Wykorzystanie przekraczające ilość rezerwacji będzie używane przez droższe zasoby płatne zgodnie z rzeczywistym użyciem. Ilustruje to poniższy przykładowy obraz. Ręcznie zmniejszyliśmy ilość rezerwacji do 4. Wykorzystanie rezerwacji zwiększa się, ale ogólne oszczędności są mniejsze, ponieważ są obecne koszty płatności zgodnie z ty.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Przykład przedstawiający zmienione szczegóły zalecenia dotyczącego zakupu rezerwacji" :::

Aby zmaksymalizować oszczędności na rezerwacjach, staraj się kupować rezerwacje w ilości jak najbardziej zbliżonej do zalecenia.

## <a name="recommendations-in-azure-advisor"></a>Zalecenia w usłudze Azure Advisor

Zalecenia dotyczące zakupu rezerwacji są dostępne w usłudze Azure Advisor. Należy pamiętać o następujących kwestiach:

- Usługa Advisor zapewnia zalecenia wyłącznie w zakresie jednej subskrypcji. Jeśli chcesz wyświetlić zalecenia dotyczące całego zakresu rozliczeniowego (konta rozliczeniowego lub profilu rozliczeniowego), wówczas:
  -  W Azure Portal przejdź do **opcji Rezerwacje** Dodaj, a następnie wybierz typ, dla którego  >   chcesz wyświetlić rekomendacje.
- Rekomendacje dostępne w u programie Advisor rozważają trend użycia z ostatnich 30 dni.
- Ilość zaleceń i oszczędności są dostępne dla rezerwacji 3-letniego, jeśli jest dostępna. Jeśli rezerwacja na trzy lata nie jest sprzedawana dla usługi, zalecenie jest obliczane na podstawie ceny jednorocznej rezerwacji.
- Obliczenia rekomendacji obejmują wszelkie rabaty specjalne, które mogą mieć zastosowanie do stawek użycia na żądanie.
- W przypadku zakupu rezerwacji w zakresie udostępnionym znikanie zaleceń dotyczących zakupu rezerwacji usługi Advisor może potrwać do pięciu dni.

## <a name="other-expected-api-behavior"></a>Inne oczekiwane zachowanie interfejsu API

- W przypadku korzystania z okresu ostatnich siedmiu dni zalecenia mogą nie zostać przedstawione, gdy maszyny wirtualne były zamknięte przez więcej niż jeden dzień.

## <a name="next-steps"></a>Następne kroki
- Uzyskiwanie [zaleceń dotyczących rezerwacji przy użyciu interfejsów API REST.](/rest/api/consumption/reservationrecommendations/list)
- Dowiedz się, [jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../manage/understand-vm-reservation-charges.md).

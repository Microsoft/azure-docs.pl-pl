---
title: Zalecenia dotyczące rezerwacji na platformie Azure
description: Dowiedz się więcej na temat zaleceń dotyczących rezerwacji na platformie Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 661e3bfa149718eb2893c5722ab3931a8a9f9afe
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797938"
---
# <a name="reservation-recommendations"></a>Rekomendacje dotyczące rezerwacji

Zalecenia dotyczące zakupu wystąpień zarezerwowanych na platformie Azure są udostępniane za pomocą [interfejsu API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations) użycia platformy Azure, usługi [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) i środowiska zakupów rezerwacji w witrynie Azure Portal.

Poniższe kroki definiują sposób obliczania zaleceń:

1. Aparat zaleceń oblicza godzinowe użycie zasobów w danym zakresie w ciągu ostatnich 7, 30 i 60 dni.
2. W oparciu o dane użycia aparat symuluje koszty z rezerwacjami i bez nich.
3. Koszty są symulowane dla różnych ilości i zalecana jest ilość, która maksymalizuje oszczędności.
4. Jeśli Twoje zasoby są regularnie zamykane, symulacja nie znajdzie żadnych oszczędności i nie poda żadnych zaleceń dotyczących zakupu.

## <a name="recommendations-in-the-azure-portal"></a>Zalecenia w witrynie Azure Portal

Zalecenia dotyczące zakupu rezerwacji są również wyświetlane w witrynie Azure Portal w środowisku zakupu. Zalecenia są wyświetlane wraz z **zalecaną ilością**. W przypadku zakupu ilość zalecana przez platformę Azure zapewni maksymalne oszczędności. Możesz kupić dowolną ilość, której potrzebujesz, ale w przypadku zakupienia innej ilości oszczędności nie będą optymalne.

Przyjrzyjmy się kilku przykładom.

Na poniższym przykładowym obrazie przedstawiającym wybrane zalecenie platforma Azure zaleca zakupienie 6 rezerwacji.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Przykład przedstawiający zalecenie dotyczące zakupu rezerwacji" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Dodatkowe informacje na temat zalecenia są wyświetlane po wybraniu linku **Zobacz szczegóły**. Na poniższym obrazie przedstawiono szczegółowe informacje na temat zalecenia. Zalecana ilość jest obliczana dla największego możliwego użycia na podstawie historycznych danych użycia. Zalecenie może nie dotyczyć 100-procentowego wykorzystania, jeśli Twoje użycie nie jest spójne. Zauważ, że w tym przykładzie wykorzystanie było zmienne w czasie. Przedstawiony jest koszt rezerwacji, możliwe oszczędności i procent wykorzystania.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Przykład przedstawiający szczegóły zalecenia dotyczącego zakupu rezerwacji" :::

Gdy zwiększysz lub zmniejszysz ilość rezerwacji do ilości wykraczającej poza zalecenie, wykres i szacowane wartości ulegną zmianie. Zwiększenie ilości rezerwacji spowoduje zmniejszenie oszczędności, ponieważ wykorzystanie rezerwacji będzie mniejsze. Innymi słowy, zapłacisz za rezerwacje, które nie będą w pełni wykorzystywane.

Jeśli zmniejszysz ilość rezerwacji, oszczędności również będą mniejsze. Mimo iż wykorzystanie będzie wyższe, prawdopodobnie będą występowały okresy, w których rezerwacje nie będą w pełni pokrywały użycia. Wykorzystanie przekraczające ilość rezerwacji będzie używane przez droższe zasoby płatne zgodnie z rzeczywistym użyciem. Ilustruje to poniższy przykładowy obraz. Ręcznie zmniejszyliśmy ilość rezerwacji do 4. Wykorzystanie rezerwacji jest większe, ale całkowite oszczędności są mniejsze, ponieważ występują płatności zgodnie z rzeczywistym użyciem.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Przykład przedstawiający zmienione szczegóły zalecenia dotyczącego zakupu rezerwacji" :::

Aby zmaksymalizować oszczędności na rezerwacjach, staraj się kupować rezerwacje w ilości jak najbardziej zbliżonej do zalecenia.

## <a name="recommendations-in-azure-advisor"></a>Zalecenia w usłudze Azure Advisor

Zalecenia dotyczące zakupu rezerwacji są dostępne w usłudze Azure Advisor. Należy pamiętać o następujących kwestiach:

- Usługa Advisor zapewnia zalecenia wyłącznie w zakresie jednej subskrypcji.
- Zalecenia są obliczane z uwzględnieniem trendu użycia z ostatnich 30 dni.
- Zalecana ilość i oszczędności dotyczą 3-letniej rezerwacji (tam, gdzie jest dostępna). Jeśli 3-letnia rezerwacja nie jest sprzedawana dla danej usługi, zalecenie jest obliczane przy użyciu ceny rezerwacji 1-rocznej.
- W zaleceniach uwzględniane są wszelkie rabaty specjalne, które mogą mieć zastosowanie do Twoich stawek użycia na żądanie.
- W przypadku zakupienia rezerwacji dotyczącej zakresu współdzielonego może minąć do 30 dni, zanim zalecenia zakupu rezerwacji usługi Advisor przestaną być wyświetlane.

## <a name="other-expected-api-behavior"></a>Inne oczekiwane zachowanie interfejsu API

- W przypadku korzystania z okresu ostatnich siedmiu dni zalecenia mogą nie zostać przedstawione, gdy maszyny wirtualne były zamknięte przez więcej niż jeden dzień.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../manage/understand-vm-reservation-charges.md).

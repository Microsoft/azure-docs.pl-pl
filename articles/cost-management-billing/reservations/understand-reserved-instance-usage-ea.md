---
title: Informacje na temat użycia rezerwacji platformy Azure w ramach umów Enterprise Agreement
description: Dowiedz się, jak odczytywać dane użycia, aby zrozumieć, w jaki sposób stosowana jest rezerwacja platformy Azure w przypadku rejestracji Twojego przedsiębiorstwa.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 6e51363ff2f3782b77288f9dc1fcb8e59e9ffe0c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151795"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement

Koszty rezerwacji i dane użycia są dostępne dla klientów z umową Enterprise Agreement w witrynie Azure Portal oraz w interfejsach API REST. Ten artykuł zawiera informacje, które pomogą:

- Pobrać dane zakupu rezerwacji
- Dowiedzieć się, w przypadku której subskrypcji, grupy zasobów lub którego zasobu została użyta rezerwacja
- Dokonać obciążenia zwrotnego za użycie rezerwacji
- Obliczyć oszczędności rezerwacji
- Pobrać dane dotyczące niedostatecznego użycia rezerwacji
- Amortyzować koszty rezerwacji

Opłaty w witrynie Marketplace są łączone w danych użycia. Można wyświetlać opłaty za użycie tej samej firmy, użycie platformy handlowej oraz zakupy z jednego źródła danych.

## <a name="reservation-charges-in-azure-usage-data"></a>Opłaty za rezerwację w danych użycia platformy Azure

Dane zostają podzielone na dwa oddzielne zestawy danych: _Koszt rzeczywisty_ i _Koszt zamortyzowany_. Różnice między tymi dwoma zestawami danych:

**Koszt rzeczywisty** — zawiera dane do uzgodnienia odnośnie do rachunku miesięcznego. Te dane obejmują koszty zakupu rezerwacji oraz szczegóły zastosowania rezerwacji. Dzięki tym danym można uzyskać informacje, która subskrypcja lub grupa zasobów bądź który zasób otrzymał rabat rezerwacji w określonym dniu. Wartość EffectivePrice dla użycia, które otrzymuje rabat rezerwacji wynosi zero.

**Koszt zamortyzowany** — ten zestaw danych jest podobny do zestawu danych Kosztu rzeczywistego, z tym że wartością EffectivePrice dla użycia, która otrzymuje rabat na rezerwację, jest koszt proporcjonalny rezerwacji (a nie wartość zero). Pomaga to poznać wartość pieniężną użycia rezerwacji w ramach subskrypcji, grupy zasobów lub zasobu, a także może pomóc w obciążeniu wewnątrz firmy za użycie rezerwacji. Zestaw danych ma także nieużywane godziny rezerwacji. Zestaw danych nie zawiera rekordów zakupu rezerwacji.

Porównanie dwóch zestawów danych:

| Dane | Rzeczywisty koszt — zestaw danych | Amortyzowany koszt — zestaw danych |
| --- | --- | --- |
| Zakupy rezerwacji | Dostępne w tym widoku.<br><br>  Aby pobrać ten filtr danych w zakresie ChargeType = &quot;Purchase&quot;. <br><br> Sprawdź ReservationID lub ReservationName, aby dowiedzieć się, której rezerwacji dotyczy opłata.  | Nie dotyczy tego widoku. <br><br> Koszty zakupu nie są podawane w danych amortyzowanych. |
| EffectivePrice | Wartość jest równa zero w przypadku użycia, do którego przypisany jest rabat rezerwacji. | Wartość jest kosztem proporcjonalnym rezerwacji za godzinę dla użycia objętego rabatem rezerwacji. |
| Niewykorzystana rezerwacja (zawiera liczbę godzin, przez którą rezerwacja nie została użyta w ciągu dnia i wartość pieniężną straty) | Nie dotyczy tego widoku. | Dostępne w tym widoku.<br><br> Aby pobrać te dane, filtruj pod kątem ChargeType = &quot;UnusedReservation&quot;.<br><br>  Sprawdź ReservationID lub ReservationName, aby dowiedzieć się, która rezerwacja była niedostatecznie wykorzystywana. Oto wartość przedstawiająca rezerwację zmarnowaną w ciągu dnia.  |
| UnitPrice(Cena zasobu z arkusza cen) | Dostępne | Dostępne |

Inne informacje dostępne w danych użycia platformy Azure uległy zmianie:

- Informacje o produkcie i mierniku — platforma Azure nie zastępuje pierwotnie zużytego miernika ReservationId i ReservationName, tak jak wcześniej.
- ReservationId i ReservationName — są to niezależne pola w danych. Wcześniej były dostępne tylko w obszarze AdditionalInfo.
- ProductOrderId — identyfikator zamówienia rezerwacji dodany jako niezależne pole.
- ProductOrderName — nazwa produktu zakupionej rezerwacji.
- Okres — 12 miesięcy lub 36 miesięcy.
- RINormalizationRatio — dostępne w obszarze AdditionalInfo. Jest to stosunek rezerwacji do rekordu użycia. Jeśli na potrzeby rezerwacji włączono elastyczność rozmiaru wystąpienia, może ona mieć zastosowanie w przypadku innych rozmiarów. Wartość przedstawia współczynnik, do którego została zastosowana rezerwacja dla rekordu użycia.

[Zobacz definicję pola](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Pobieranie danych użycia platformy Azure i rezerwacji przy użyciu interfejsu API

Można pobrać dane przy użyciu interfejsu API lub pobrać je z witryny Azure Portal.

Można wywołać [interfejs API szczegółów użycia](/rest/api/consumption/usagedetails/list), aby pobrać nowe dane. Aby uzyskać szczegółowe informacje na temat terminologii, zobacz [warunki użytkowania](../understand/understand-usage.md). Wywołujący powinien być administratorem Enterprise w ramach umowy Enterprise Agreement korzystającym z witryny [EA Portal](https://ea.azure.com). Administratorzy Enterprise z uprawnieniami tylko do odczytu mogą również pobierać dane.

Pamiętaj, że te dane nie są dostępne w [interfejsach API raportowania dla klientów Enterprise — szczegóły użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Oto przykład wywołania interfejsu API szczegółów użycia:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Aby uzyskać więcej informacji na temat parametrów {enrollmentId} i {billingPeriodId}, zobacz artykuł dotyczący interfejsu API [Usage Details – List](/rest/api/consumption/usagedetails/list) (Szczegóły użycia — lista).

Informacje przedstawione w poniższej tabeli dotyczące metryki i filtru mogą pomóc w rozwiązywaniu typowych problemów z rezerwacją.

| **Typ danych interfejsu API** | Akcja wywołania interfejsu API |
| --- | --- |
| **Wszystkie opłaty (użycie i zakupy)** | Zastąp parametr {metric} ciągiem ActualCost |
| **Użycie, któremu przyznano rabat rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} ciągiem properties/reservationId%20ne%20 |
| **Użycie, któremu nie przyznano rabatu rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/reservationId%20eq%20 |
| **Opłaty zamortyzowane (użycie i zakupy)** | Zastąp parametr {metric} ciągiem AmortizedCost |
| **Nieużywany raport rezerwacji** | Zastąp parametr {metric} ciągiem AmortizedCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'UnusedReservation' |
| **Zakupy rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'Purchase'  |
| **Zwroty** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Pobierz plik CSV użycia z nowymi danymi

Jeśli jesteś administratorem EA, możesz pobrać plik CSV zawierający nowe dane użycia z witryny Azure Portal. Te dane nie są dostępne w portalu EA (ea.azure.com) — aby wyświetlić nowe dane, musisz pobrać plik użycia z witryny Azure Portal (portal.azure.com).

W witrynie Azure Portal przejdź do obszaru [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wybierz konto rozliczeniowe.
2. Kliknij pozycję **Użycie i opłaty**.
3. Kliknij pozycję **Pobierz**.  
![Przykład pokazujący, gdzie pobrać plik CSV z danymi użycia w witrynie Azure Portal](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. W obszarze **Pobierz użycie i opłaty** pod pozycją **Szczegóły użycia — wersja 2** wybierz pozycję **Wszystkie opłaty (użycie i zakupy)** , a następnie kliknij przycisk Pobierz. Powtórz dla **Opłaty zamortyzowane (użycie i zakupy)** .

Pobierane przez Ciebie pliki CSV zawierają koszty rzeczywiste i zamortyzowane.

## <a name="common-cost-and-usage-tasks"></a>Typowe zadania związane z kosztami i użyciem

Poniższe sekcje to typowe zadania, których większość osób używa do wyświetlania danych dotyczących kosztów i użycia rezerwacji.

### <a name="get-reservation-purchase-costs"></a>Pobieranie kosztów zakupu rezerwacji

Koszty zakupu rezerwacji są dostępne w danych dotyczących Kosztu rzeczywistego. Filtruj dla _ChargeType = Purchase_. Sprawdź element ProductOrderID, aby ustalić, dla którego zamówienia rezerwacji jest zakup.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Pobieranie ilości i kosztów nieużywanej rezerwacji

Pobierz dane dotyczące Kosztu zamortyzowanego i filtruj dla _ChargeType_ _= UnusedReservation_. Otrzymasz ilość dziennej nieużywanej rezerwacji i koszt. Dane dla rezerwacji lub zamówienia rezerwacji można filtrować odpowiednio przy użyciu pól _ReservationId_ i _ProductOrderId_. Jeśli rezerwacja została wykorzystana w 100%, rekord ma ilość 0.

### <a name="amortize-reservation-costs"></a>Amortyzować koszty rezerwacji

Pobierz dane dotyczące Kosztu zamortyzowanego i filtruj dla zamówienia rezerwacji przy użyciu _ProductOrderID_, aby uzyskać dzienne koszty zamortyzowane dla rezerwacji.

### <a name="chargeback-for-a-reservation"></a>Obciążanie zwrotne za rezerwację

Można dokonać obciążenia zwrotnego za użycie rezerwacji na rzecz innych organizacji w oparciu o subskrypcję, grupy zasobów lub tagi. Dane dotyczące kosztu zamortyzowanego zawierają wartość pieniężną wykorzystania rezerwacji dla następujących typów danych:

- Zasoby (na przykład maszyna wirtualna)
- Grupa zasobów
- Tagi
- Subskrypcja

### <a name="get-the-blended-rate-for-chargeback"></a>Pobieranie stawki uśrednionej dla obciążenia zwrotnego

Aby określić stawkę uśrednioną, należy uzyskać dane dotyczące kosztów zamortyzowanych i zsumować łączny koszt. W przypadku maszyn wirtualnych można użyć informacji MeterName lub ServiceType z danych JSON AdditionalInfo. Podziel łączny koszt przez ilość używaną do uzyskania stawki uśrednionej.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Inspekcja optymalnego użycia rezerwacji na potrzeby elastyczności rozmiaru wystąpienia

Pomnóż ilość razy _RINormalizationRatio_ z AdditionalInfo. Wyniki wskazują, ile godzin użycia rezerwacji zostało zastosowanych do rekordu użycia.

### <a name="determine-reservation-savings"></a>Określanie oszczędności rezerwacji

Pobierz dane dotyczące kosztów zamortyzowanych i filtruj dane dla wystąpienia zarezerwowanego. Następnie:

1. Wylicz szacowane koszty zgodnie z rzeczywistym użyciem. Pomnóż wartość _UnitPrice_ razy wartość _Quantity_, aby wyliczyć szacowane koszty zgodnie z rzeczywistym użyciem, jeśli rabat rezerwacji nie dotyczył użycia.
2. Wylicz koszty rezerwacji. Zsumuj wartości _Koszt_, aby wyliczyć wartość pieniężną zapłaconą za zarezerwowane wystąpienie. Obejmuje ona wykorzystane i niewykorzystane koszty rezerwacji.
3. Odejmij koszty rezerwacji od szacowanych kosztów zgodnie z rzeczywistym użyciem, aby wyliczyć szacowane oszczędności.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Zakupy i amortyzacja rezerwacji w analizie kosztów

Koszty rezerwacji są dostępne w [analizie kosztów](https://aka.ms/costanalysis). Domyślnie analiza kosztów przedstawia **koszt rzeczywisty**, czyli sposób wyświetlania kosztów na rachunku. Aby wyświetlić zakupy rezerwacji podzielone i skojarzone z zasobami, w których wykorzystano świadczenie, przełącz na **Koszt zamortyzowany**:

![Przykład, w którym przedstawiono, gdzie wybierać koszt zamortyzowany w analizie kosztów](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Grupuj według typu opłaty, aby wyświetlić podział użycia, zakupów i refundacji; lub według rezerwacji na potrzeby podziału rezerwacji i kosztów na żądanie. Należy pamiętać, że jedyne koszty rezerwacji, które będą widoczne w kosztach rzeczywistych, to zakupy, ale koszty będą przydzielone do poszczególnych zasobów, które wykorzystały świadczenie podczas naliczania kosztu zamortyzowanego. Zobaczysz także nowy typ opłaty **UnusedReservation** podczas naliczania kosztu zamortyzowanego.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../azure-sql/database/reserved-capacity-overview.md)
- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Jak jest stosowany rabat na rezerwacje](../manage/understand-vm-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)
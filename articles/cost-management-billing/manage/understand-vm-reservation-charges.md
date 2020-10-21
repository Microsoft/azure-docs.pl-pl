---
title: Objaśnienie rabatu na wystąpienia usługi Azure Reserved VM Instances
description: Dowiedz się, jak rabat na wystąpienie zarezerwowane maszyny wirtualnej platformy Azure jest stosowany do uruchamiania maszyn wirtualnych.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: e9ee1d9ff8145051bbbe6b65004f7f358b5fdade
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132146"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych

Po zakupie wystąpienia zarezerwowanego maszyny wirtualnej platformy Azure rabat na rezerwację jest automatycznie stosowany do maszyn wirtualnych pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje koszty zasobów obliczeniowych maszyn wirtualnych.

Rabat na rezerwację dotyczy podstawowych maszyn wirtualnych zakupionych w witrynie Azure Marketplace.

Aby uzyskać informacje o wydajności rezerwowej usługi SQL Database, zobacz [Objaśnienie rabatu na wystąpienia zarezerwowane platformy Azure](../reservations/understand-reservation-charges.md).

W poniższej tabeli przedstawiono koszty używania maszyny wirtualnej po zakupie wystąpienia zarezerwowanego maszyny wirtualnej. We wszystkich przypadkach opłaty za magazyn i sieć są naliczane według normalnych stawek.

| Typ maszyny wirtualnej  | Opłaty z wystąpieniem zarezerwowanym maszyny wirtualnej |
|-----------------------|--------------------------------------------|
|Maszyny wirtualne z systemem Linux bez dodatkowego oprogramowania | Rezerwacja obejmuje koszty infrastruktury maszyn wirtualnych.|
|Maszyny wirtualne z systemem Linux z opłatami za oprogramowanie (na przykład Red Hat) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za dodatkowe oprogramowanie.|
|Maszyny wirtualne z systemem Windows bez dodatkowego oprogramowania |Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za oprogramowanie systemu Windows.|
|Maszyny wirtualne z systemem Windows z dodatkowym oprogramowaniem (na przykład programem SQL Server) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za oprogramowanie systemu Windows i dodatkowe oprogramowanie.|
|Maszyny wirtualne z systemem Windows z [korzyścią użycia hybrydowego platformy Azure](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Rezerwacja obejmuje koszty związane z infrastrukturą. Koszty używania oprogramowania systemu Windows są zawarte w korzyści użycia hybrydowego platformy Azure. Za używanie dodatkowego oprogramowania są naliczane oddzielne opłaty.|

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="reservation-discount-for-non-windows-vms"></a>Rabat na rezerwację dla maszyn wirtualnych z systemem innym niż Windows

 Rabat na rezerwację platformy Azure jest stosowany do uruchomionych wystąpień maszyn wirtualnych co godzinę. Stosowanie rabatu na rezerwację polega na dopasowywaniu zakupionych rezerwacji do użycia emitowanego przez uruchomione maszyny wirtualne. W przypadku maszyn wirtualnych, które nie działają przez pełną godzinę, rezerwacja jest wypełniana innymi maszynami, również tymi, które są uruchomione równocześnie. Po upływie godziny stosowanie rezerwacji dla maszyn wirtualnych z tej godziny jest blokowane. Jeśli maszyna wirtualna nie pozostaje uruchomiona przez godzinę lub równocześnie uruchomione maszyny wirtualne nie wypełniają godziny rezerwacji, rezerwacja nie jest w pełni wykorzystywana. Poniższa ilustracja przedstawia zastosowanie rezerwacji do płatnego użycia maszyn wirtualnych. Ilustracja jest oparta na jednym zakupie rezerwacji i dwóch zgodnych wystąpieniach maszyn wirtualnych.

![Zrzut ekranu przedstawiający jedną zastosowaną rezerwację i dwa zgodne wystąpienia maszyn wirtualnych](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. Każde użycie przekraczające limit rezerwacji jest obciążane opłatami według zwykłych stawek płatności zgodnie z rzeczywistym użyciem. Za użycie poniżej limitu rezerwacji opłaty nie są naliczane — zostało ono opłacone w ramach zakupu rezerwacji.
2. W godzinie 1 wystąpienie 1 działa przez 0,75 godz., a wystąpienie 2 działa przez 0,5 godz. Łączne użycie w godzinie 1 to 1,25 godz. Za pozostałe 0,25 godz. opłaty są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.
3. W godzinie 2 i godzinie 3 oba wystąpienia były uruchomione przez 1 godzinę. Jedno wystąpienie jest objęte rezerwacją, a drugie jest obciążane opłatami według stawek płatności zgodnie z rzeczywistym użyciem.
4. W godzinie 4 wystąpienie 1 działa przez 0,5 godz., a wystąpienie 2 działa przez 1 godz. Wystąpienie 1 jest w pełni objęte rezerwacją. W wystąpieniu 2 rezerwacją jest objęte 0,5 godz. Za pozostałe 0,5 godz. opłaty są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Rabat na rezerwację dla maszyn wirtualnych z systemem Windows

W przypadku uruchamiania wystąpień maszyn wirtualnych z systemem Windows rezerwacja jest stosowana w celu pokrycia kosztów infrastruktury. Stosowanie rezerwacji do kosztów infrastruktury maszyn wirtualnych z systemem Windows wygląda tak samo jak w przypadku maszyn wirtualnych z systemem innym niż Windows. Za używanie oprogramowania systemu Windows jest naliczana oddzielna opłata, która zależy od liczby procesorów wirtualnych. Zobacz [Koszty oprogramowania systemu Windows z usługą Reservations](../reservations/reserved-instance-windows-software-costs.md). Koszty licencjonowania systemu Windows można objąć [korzyścią użycia hybrydowego platformy Azure dla systemu Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Rabat może być stosowany do różnych rozmiarów

Po zakupieniu wystąpienia zarezerwowanego maszyny wirtualnej i wybraniu opcji **Zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia** rabat dotyczy wybranego rozmiaru maszyny wirtualnej. Może również obejmować inne rozmiary maszyn wirtualnych, które znajdują się w tej samej grupie elastyczności rozmiaru wystąpienia serii. Aby uzyskać więcej informacji, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>Maszyny wirtualne magazynu w warstwie Premium nie są objęte rabatami spoza tej warstwy

Oto przykład. Załóżmy, że zakupiono rezerwację dla pięciu maszyn wirtualnych w warstwie Standard_D1. W takim wypadku rabat przy rezerwacji dotyczy wyłącznie maszyn wirtualnych Standard_D1 lub innych maszyn wirtualnych należących do tej samej rodziny wystąpień. Rabat nie dotyczy maszyny wirtualnej w warstwie Standard_DS1 ani innych rozmiarów w grupie elastyczności rozmiaru wystąpienia warstwy DS1.

Przy stosowaniu rabatu na rezerwację miernik używany dla maszyn wirtualnych jest ignorowany i uwzględniana jest tylko wartość ServiceType. Znajdź wartość `ServiceType` w elemencie `AdditionalInfo`, aby sprawdzić dane dotyczące grupy/serii elastyczności wystąpienia dla maszyn wirtualnych. Odpowiednie wartości znajdziesz w pliku CSV dotyczącym użycia.

Po zakupie nie możesz zmienić grupy/serii elastyczności wystąpienia rezerwacji. Możesz jednak *zamienić* rezerwację maszyny wirtualnej z jednej grupy/serii elastyczności na inną.

## <a name="services-that-get-vm-reservation-discounts"></a>Usługi, dla których są stosowane rabaty na rezerwacje maszyn wirtualnych

Rezerwacje maszyn wirtualnych mogą dotyczyć użycia maszyn wirtualnych emitowanego przez różne usługi — nie tylko w przypadku wdrożeń maszyn wirtualnych. Zasoby, dla których są stosowane rabaty na rezerwację, zmieniają się w zależności od ustawienia elastyczności rozmiaru wystąpienia.

### <a name="instance-size-flexibility-setting"></a>Ustawienie elastyczności rozmiaru wystąpienia

Ustawienie elastyczności rozmiaru wystąpienia określa, dla których usług są stosowane rabaty na wystąpienia zarezerwowane.

Niezależnie od tego, czy to ustawienie jest włączone czy wyłączone, rabaty na rezerwację są automatycznie stosowane do każdego pasującego użycia maszyn wirtualnych, dla którego wartość *ConsumedService* to `Microsoft.Compute`. Należy zatem sprawdzić dane użycia dla wartości *ConsumedService*. Oto niektóre przykłady:

- Maszyny wirtualne
- Zestawy skalowania maszyn wirtualnych
- Usługa kontenera
- Wdrożenia usługi Azure Batch (w trybie subskrypcji użytkownika)
- Azure Kubernetes Service (AKS)
- Service Fabric

Jeśli to ustawienie jest włączone, rabaty na rezerwację są automatycznie stosowane do pasującego użycia maszyn wirtualnych, które ma dowolną z następujących wartości *ConsumedService*:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Sprawdź wartość *ConsumedService* w danych użycia, aby ustalić, czy użycie kwalifikuje się do zastosowania rabatów na rezerwację.

Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../../virtual-machines/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](../reservations/save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../azure-sql/database/reserved-capacity-overview.md)
- [Zarządzanie rezerwacjami platformy Azure](../reservations/manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../reservations/understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)
- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](../reservations/reserved-instance-windows-software-costs.md)
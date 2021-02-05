---
title: Rabaty za wystąpienia zarezerwowane w przypadku usługi Azure App Service
description: Dowiedz się, w jaki sposób rabaty rezerwacji mają zastosowanie do Azure App Service wystąpień Premium v3 i wyizolowanych sygnatur.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: banders
ms.openlocfilehash: debe02a89e10712ad8a0b8d61b0fdc3f8a4bd7b2
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577805"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Jak są stosowane rabaty rezerwacji do Azure App Service wystąpień Premium v3 i wyizolowanych sygnatur

Ten artykuł pomaga zrozumieć, jak zniżki mają zastosowanie do Azure App Service wystąpień Premium v3 i wyizolowanych sygnatur.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Jak są stosowane rabaty rezerwacji do wystąpień Premium v3

Po zakupieniu zarezerwowanego wystąpienia Azure App Service Premium v3 rabat dla rezerwacji jest automatycznie stosowany do App Service wystąpień, które pasują do atrybutów i ilości rezerwacji. Rezerwacja obejmuje koszt wystąpień Premium v3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Sposób zastosowania rabatu do Azure App Service 

Rabat rezerwacji jest *używany-lub-traci*. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.
Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

### <a name="reservation-discount-for-premium-v3-instances"></a>Rabat rezerwacji dla wystąpień Premium v3

Rabat w ramach rezerwacji platformy Azure jest stosowany do uruchamiania wystąpień Premium v3 co godzinę. Zakupione rezerwacje są dopasowywane do użycia emitowanego przez wystąpienia z systemem Premium V3 w celu zastosowania rabatu rezerwacji. W przypadku wystąpień Premium v3, które mogą nie działać w pełnej godzinie, rezerwacja zostanie wypełniona z innych wystąpień, w tym współbieżnie uruchomionych wystąpień. Na koniec godziny aplikacja rezerwacji dla wystąpień w godzinie jest zablokowana. W przypadku wystąpienia nie jest uruchamiane przez godzinę lub współbieżne wystąpienia w ciągu godziny nie wypełniaj godziny rezerwacji, rezerwacja jest niewystarczająco wykorzystywana przez daną godzinę. Poniższa ilustracja przedstawia zastosowanie rezerwacji do płatnego użycia maszyn wirtualnych. Ilustracja jest oparta na jednym zakupie rezerwacji i dwóch zgodnych wystąpieniach maszyn wirtualnych.

![Obraz przedstawiający zastosowanie rezerwacji do rozliczanego użycia maszyn wirtualnych](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Każde użycie przekraczające limit rezerwacji jest obciążane opłatami według zwykłych stawek płatności zgodnie z rzeczywistym użyciem. Za użycie poniżej limitu rezerwacji opłaty nie są naliczane — zostało ono opłacone w ramach zakupu rezerwacji.
2.  W godzinie 1 wystąpienie 1 działa przez 0,75 godz., a wystąpienie 2 działa przez 0,5 godz. Łączne użycie w godzinie 1 to 1,25 godz. Za pozostałe 0,25 godz. opłaty są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.
3.  W godzinie 2 i godzinie 3 oba wystąpienia były uruchomione przez 1 godzinę. Jedno wystąpienie jest objęte rezerwacją, a drugie jest obciążane opłatami według stawek płatności zgodnie z rzeczywistym użyciem.
4.  W godzinie 4 wystąpienie 1 działa przez 0,5 godz., a wystąpienie 2 działa przez 1 godz. Wystąpienie 1 jest w pełni objęte rezerwacją. W wystąpieniu 2 rezerwacją jest objęte 0,5 godz. Za pozostałe 0,5 godz. opłaty są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji](understand-reserved-instance-usage-ea.md).

### <a name="discount-can-apply-to-different-sizes"></a>Rabat może być stosowany do różnych rozmiarów

W przypadku zakupu zarezerwowanego wystąpienia Premium v3 i wybrania opcji **zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia**, pokrycie rabatu dotyczy wybranych rozmiarów wystąpienia Premium v3. Może również mieć zastosowanie do innych rozmiarów wystąpień, które znajdują się w tej samej grupie elastycznej rozmiaru wystąpienia serii.

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Jak rabaty rezerwacji dotyczą odizolowanych sygnatur

Po zakupie wydajności rezerwowej w ramach opłaty jednostkowej za korzystanie z izolowanej usługi App Service rabat za wystąpienie zarezerwowane zostanie automatycznie zastosowany do opłaty jednostkowej w danym regionie. Rabat za wystąpienie zarezerwowane ma zastosowanie do użycia emitowanego przez miernik opłaty jednostkowej za korzystanie w izolowanym środowisku. Procesy robocze, dodatkowe frontony i inne zasoby skojarzone z sygnaturą są nadal rozliczane według zwykłej stawki.

### <a name="reservation-discount-application"></a>Stosowanie rabatu za wystąpienia zarezerwowane

Rabat na opłatę jednostkową za korzystanie z usługi App Service w izolowanym środowisku jest stosowany do uruchamiania izolowanych sygnatur co godzinę. Jeśli przez godzinę nie zostanie wdrożona żadna sygnatura, wydajność rezerwowa dla tej godziny zostanie utracona. Niewykorzystane wartości nie są przenoszone na następne okresy.

Po zakupie wystąpienie zarezerwowane zostaje dopasowane do izolowanej sygnatury działającej w określonym regionie. Jeśli wyłączysz tę sygnaturę, rabaty za wystąpienia zarezerwowane zostaną automatycznie zastosowane do wszelkich pozostałych sygnatur działających w regionie. Jeśli żadne sygnatury nie występują, wystąpienie zarezerwowane zostanie zastosowane do następnej sygnatury utworzonej w regionie.

Jeśli sygnatury nie są uruchamiane przez pełną godzinę, wystąpienie zarezerwowane zostaje automatycznie zastosowane do innych zgodnych sygnatur w tym samym regionie w ciągu tej samej godziny.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Wybieranie typu sygnatury — system Windows lub Linux

Pusta izolowana sygnatura domyślnie emituje miernik sygnatur systemu Windows. Na przykład wtedy, gdy nie wdrożono procesów roboczych. W dalszym ciągu emituje miernik podczas wdrażania procesów roboczych systemu Windows. Miernik zostaje zmieniony na miernik sygnatur systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux. Sygnatura emituje miernik systemu Windows podczas wdrażania zarówno procesów roboczych systemu Linux, jak i Windows.

W związku z tym w czasie trwania sygnatury miernik sygnatur może być zmieniany na miernik systemu Windows, a innym razem na miernik systemu Linux. Tymczasem rezerwacje są powiązane z konkretnym systemem operacyjnym. Musisz zakupić wystąpienie zarezerwowane obsługujące procesy robocze, które planujesz wdrożyć do sygnatury. Sygnatury tylko systemu Windows i sygnatury mieszane używają wystąpienia zarezerwowanego systemu Windows. Sygnatury obejmujące tylko procesy robocze systemu Linux korzystają z wystąpienia zarezerwowanego systemu Linux.

Jedyna sytuacja, w której należy zakupić wystąpienie zarezerwowane systemu Linux, ma miejsce, gdy planujesz mieć w sygnaturze _tylko_ procesy robocze systemu Linux.

### <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak w zależności od wdrożenia jest stosowany rabat za wystąpienie zarezerwowane w ramach opłaty jednostkowej w izolowanym środowisku.

- **Przykład 1**: Dokonujesz zakupu jednego wystąpienia wydajności izolowanej sygnatury zarezerwowanej w regionie, dla którego nie występują sygnatury izolowanej usługi App Service. Wdrażasz nową sygnaturę w regionie i płacisz stawki zarezerwowane dla tej sygnatury.
- **Przykład 2**: Dokonujesz zakupu jednego wystąpienia pojemności sygnatury zarezerwowanej w izolowanym środowisku w regionie, w którym jest już wdrożona sygnatura izolowana usługi App Service. Zaczynasz otrzymywać zarezerwowaną stawkę dla wdrożonej sygnatury.
- **Przykład 3**: Dokonujesz zakupu jednego wystąpienia pojemności izolowanej usługi Reserved Stamp w regionie z już wdrożoną sygnaturą izolowanej usługi App Service. Zaczynasz otrzymywać zarezerwowaną stawkę dla wdrożonej sygnatury. Później należy usunąć sygnaturę i wdrożyć nową. Otrzymujesz zarezerwowaną stawkę dla nowej sygnatury. Zniżki nie są przenoszone na czasy trwania bez wdrożonych sygnatur.
- **Przykład 4**: Dokonujesz zakupu jednego wystąpienia pojemności sygnatury zarezerwowanej systemu Linux w izolowanym środowisku w regionie, następnie wdrażasz nową sygnaturę w regionie. Gdy sygnatura zostaje początkowo wdrożona bez procesów roboczych, emituje miernik sygnatur systemu Windows. Rabat nie zostaje przyznany. Gdy pierwszy proces roboczy systemu Linux wdraża sygnaturę, emituje miernik sygnatur systemu Linux i ma zastosowanie rabat rezerwacji. Jeśli proces roboczy systemu Windows zostaje później wdrożony w sygnaturze, miernik sygnatur zostaje przywrócony do systemu Windows. Nie otrzymujesz już rabatu na rezerwację sygnatury zarezerwowanej systemu Linux w izolowanym środowisku.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat wstępnego kupowania App Service Premium v3 i oddzielonej sygnatury zarezerwowanej do oszczędności, zobacz [przedpłata za Azure App Service z zarezerwowaną pojemnością](prepay-app-service.md).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Zarządzanie rezerwacjami na platformie Azure](manage-reserved-vm-instance.md)
  - [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

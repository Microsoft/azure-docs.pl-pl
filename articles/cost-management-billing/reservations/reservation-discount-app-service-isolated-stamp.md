---
title: Rabaty za wystąpienia zarezerwowane w przypadku usługi Azure App Service
description: Dowiedz się, jak są stosowane rabaty za wystąpienia zarezerwowane w przypadku sygnatur izolowanej usługi Azure App Service. Rabaty są automatycznie stosowane do opłaty jednostkowej w danym regionie.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 7a4229d60a31d61caf050dabf632c8718ef3c9f3
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460341"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Jak są stosowane rabaty za wystąpienia zarezerwowane w przypadku sygnatur izolowanej usługi Azure App Service

Po zakupie wydajności rezerwowej w ramach opłaty jednostkowej za korzystanie z izolowanej usługi App Service rabat za wystąpienie zarezerwowane zostanie automatycznie zastosowany do opłaty jednostkowej w danym regionie. Rabat za wystąpienie zarezerwowane ma zastosowanie do użycia emitowanego przez miernik opłaty jednostkowej za korzystanie w izolowanym środowisku. Procesy robocze, dodatkowe frontony i inne zasoby skojarzone z sygnaturą są nadal rozliczane według zwykłej stawki.

## <a name="reservation-discount-application"></a>Stosowanie rabatu za wystąpienia zarezerwowane

Rabat na opłatę jednostkową za korzystanie z usługi App Service w izolowanym środowisku jest stosowany do uruchamiania izolowanych sygnatur co godzinę. Jeśli przez godzinę nie zostanie wdrożona żadna sygnatura, wydajność rezerwowa dla tej godziny zostanie utracona. Niewykorzystane wartości nie są przenoszone na następne okresy.

Po zakupie wystąpienie zarezerwowane zostaje dopasowane do izolowanej sygnatury działającej w określonym regionie. Jeśli wyłączysz tę sygnaturę, rabaty za wystąpienia zarezerwowane zostaną automatycznie zastosowane do wszelkich pozostałych sygnatur działających w regionie. Jeśli żadne sygnatury nie występują, wystąpienie zarezerwowane zostanie zastosowane do następnej sygnatury utworzonej w regionie.

Jeśli sygnatury nie są uruchamiane przez pełną godzinę, wystąpienie zarezerwowane zostaje automatycznie zastosowane do innych zgodnych sygnatur w tym samym regionie w ciągu tej samej godziny.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Wybieranie typu sygnatury — system Windows lub Linux

Pusta izolowana sygnatura domyślnie emituje miernik sygnatur systemu Windows. Na przykład wtedy, gdy nie wdrożono procesów roboczych. W dalszym ciągu emituje miernik podczas wdrażania procesów roboczych systemu Windows. Miernik zostaje zmieniony na miernik sygnatur systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux. Sygnatura emituje miernik systemu Windows podczas wdrażania zarówno procesów roboczych systemu Linux, jak i Windows.

W związku z tym w czasie trwania sygnatury miernik sygnatur może być zmieniany na miernik systemu Windows, a innym razem na miernik systemu Linux. Tymczasem rezerwacje są powiązane z konkretnym systemem operacyjnym. Musisz zakupić wystąpienie zarezerwowane obsługujące procesy robocze, które planujesz wdrożyć do sygnatury. Sygnatury tylko systemu Windows i sygnatury mieszane używają wystąpienia zarezerwowanego systemu Windows. Sygnatury obejmujące tylko procesy robocze systemu Linux korzystają z wystąpienia zarezerwowanego systemu Linux.

Jedyna sytuacja, w której należy zakupić wystąpienie zarezerwowane systemu Linux, ma miejsce, gdy planujesz mieć w sygnaturze _tylko_ procesy robocze systemu Linux.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak w zależności od wdrożenia jest stosowany rabat za wystąpienie zarezerwowane w ramach opłaty jednostkowej w izolowanym środowisku.

- **Przykład 1**: Dokonujesz zakupu jednego wystąpienia wydajności izolowanej sygnatury zarezerwowanej w regionie, dla którego nie występują sygnatury izolowanej usługi App Service. Wdrażasz nową sygnaturę w regionie i płacisz stawki zarezerwowane dla tej sygnatury.
- **Przykład 2**: Dokonujesz zakupu jednego wystąpienia pojemności sygnatury zarezerwowanej w izolowanym środowisku w regionie, w którym jest już wdrożona sygnatura izolowana usługi App Service. Zaczynasz otrzymywać zarezerwowaną stawkę dla wdrożonej sygnatury.
- **Przykład 3**: Dokonujesz zakupu jednego wystąpienia pojemności izolowanej usługi Reserved Stamp w regionie z już wdrożoną sygnaturą izolowanej usługi App Service. Zaczynasz otrzymywać zarezerwowaną stawkę dla wdrożonej sygnatury. Później należy usunąć sygnaturę i wdrożyć nową. Otrzymujesz zarezerwowaną stawkę dla nowej sygnatury. Zniżki nie są przenoszone na czasy trwania bez wdrożonych sygnatur.
- **Przykład 4**: Dokonujesz zakupu jednego wystąpienia pojemności sygnatury zarezerwowanej systemu Linux w izolowanym środowisku w regionie, następnie wdrażasz nową sygnaturę w regionie. Gdy sygnatura zostaje początkowo wdrożona bez procesów roboczych, emituje miernik sygnatur systemu Windows. Rabat nie zostaje przyznany. Gdy pierwszy proces roboczy systemu Linux wdraża sygnaturę, emituje miernik sygnatur systemu Linux i ma zastosowanie rabat rezerwacji. Jeśli proces roboczy systemu Windows zostaje później wdrożony w sygnaturze, miernik sygnatur zostaje przywrócony do systemu Windows. Nie otrzymujesz już rabatu na rezerwację sygnatury zarezerwowanej systemu Linux w izolowanym środowisku.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat wcześniejszego zakupu wydajności rezerwowej w ramach opłaty jednostkowej za korzystanie z usługi App Service w środowisku izolowanym, zobacz temat [Prepay for Azure App Service Isolated Stamp Fee with reserved capacity](prepay-app-service-isolated-stamp.md) (Uiszczanie z góry opłaty jednostkowej za korzystanie z usługi Azure App Service w środowisku izolowanym z wydajnością rezerwową).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Zarządzanie rezerwacjami na platformie Azure](manage-reserved-vm-instance.md)
  - [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

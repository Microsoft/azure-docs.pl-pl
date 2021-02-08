---
title: Co to jest usługa Azure Resource przenosząca?
description: Dowiedz się więcej o usłudze Azure Resource przenoszącej
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820167"
---
# <a name="what-is-azure-resource-mover"></a>Co to jest usługa Azure Resource przenosząca?

Ten artykuł zawiera omówienie usługi Azure Resource przenoszącej. Narzędzie do przenoszenia zasobów ułatwia przenoszenie zasobów platformy Azure między regionami platformy Azure.

Możesz przenosić zasoby do różnych regionów świadczenia usługi Azure, aby:

- **Wyrównaj do uruchamiania regionu**: Przenieś zasoby do nowo wprowadzonego regionu platformy Azure, który nie był wcześniej dostępny.
- **Wyrównanie do usług/funkcji**: Przenieś zasoby, aby skorzystać z usług lub funkcji dostępnych w określonym regionie.
- **Reagowanie na rozwój biznesowy**: przenoszenie zasobów do regionu w odpowiedzi na zmiany biznesowe, takie jak fuzje lub pozyskiwania.
- **Wyrównaj do sąsiedztwa**: Przenieś zasoby do regionu lokalnego dla Twojej firmy.
- **Spełnianie wymagań dotyczących danych**: Przenieś zasoby, aby dostosować je do wymagań dotyczących miejsca zamieszkania lub klasyfikacji danych.
- **Odpowiadanie na wymagania dotyczące wdrażania**: Przenieś zasoby, które zostały wdrożone w ramach błędu, lub przejdź w odpowiedzi na potrzeby pojemności.
- **Odpowiedz na likwidowanie**: Przenieś zasoby, ponieważ region został zlikwidowany.


## <a name="why-use-resource-mover"></a>Dlaczego warto używać przenoszenia zasobów?

Program do przenoszenia zasobów oferuje następujące informacje:

- Jedno centrum do przemieszczania zasobów między regionami.
- Krótszy czas przenoszenia i złożoność. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
- Proste i spójne środowisko do przemieszczania różnych typów zasobów platformy Azure.
- Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ułatwia to przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po przeniesieniu.
- Automatyczne czyszczenie zasobów w regionie źródłowym, jeśli chcesz je usunąć po przeniesieniu.
- Testowy. Możesz wypróbować przechodzenie, a następnie odrzucić go, jeśli nie chcesz wykonać pełnego przeniesienia.

## <a name="move-across-regions"></a>Przechodzenie między regionami

Aby przenieść zasoby między regionami, należy wybrać zasoby, które mają zostać przeniesione. Program do przenoszenia zasobów weryfikuje te zasoby i rozwiązuje wszelkie zależności, które mają na innych zasobach. Jeśli istnieją zależności, masz kilka opcji:
- Przenieś zasoby zależne do regionu docelowego.
- Nie przenoś zasobów zależnych, ale zamiast tego użyj ekwiwalentnych zasobów w regionie docelowym.

Po rozwiązaniu wszystkich zależności, program do przenoszenia zasobów przeprowadzi Cię przez prosty proces przenoszenia.

1. Zaczynasz od początku przenoszenia.
2. Po początkowym przeniesieniu możesz zdecydować, czy zatwierdzić i zakończyć przenoszenie, czy też odrzucić przeniesienie.
3. Po zakończeniu przenoszenia możesz zdecydować, czy chcesz usunąć zasoby z lokalizacji źródłowej.

Zasoby można przenosić między regionami w ramach centrum przenoszenia zasobów lub z grupy zasobów. [Dowiedz się więcej](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Jakie zasoby można przenosić między regionami?

Korzystając z funkcji przenoszenia zasobów, można obecnie przenosić następujące zasoby między regionami:

- Maszyny wirtualne platformy Azure i skojarzone dyski
- Zaszyfrowane maszyny wirtualne platformy Azure i skojarzone z nią dyski. Obejmuje to maszyny wirtualne z włączoną funkcją Azure Disk Encryption oraz maszyny wirtualne platformy Azure korzystające z domyślnego szyfrowania po stronie serwera (zarówno z kluczami zarządzanymi przez platformę, jak i kluczami zarządzanymi przez klienta)
- Karty interfejsów sieciowych
- Zestawy dostępności 
- Sieci wirtualne platformy Azure 
- Publiczne adresy IP
- Sieciowe grupy zabezpieczeń
- Wewnętrzne i publiczne usługi równoważenia obciążenia 
- Bazy danych SQL Azure i pule elastyczne


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](about-move-process.md) na temat składników przenoszenia zasobów i procesu przenoszenia.

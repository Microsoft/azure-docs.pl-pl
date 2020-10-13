---
title: Szablony skoroszytów Azure Monitor — przenoszenie regionów
description: Jak przenieść szablon skoroszytu do innego regionu
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875808"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Przenoszenie szablonu skoroszytu platformy Azure do innego regionu

W tym artykule opisano sposób przenoszenia zasobów szablonów skoroszytów platformy Azure do innego regionu platformy Azure. Możesz przenieść zasoby do innego regionu z kilku powodów. Na przykład w celu skorzystania z nowego regionu platformy Azure, wdrożenia funkcji lub usług dostępnych tylko w określonych regionach, spełnienia wymagań wewnętrznych zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności.

Obecnie nie ma interfejsu użytkownika portalu do tworzenia zasobów szablonów skoroszytów. jedynym bieżącym sposobem ich tworzenia jest [wdrożenie Azure Resource Manager szablonu szablon (ARM)](./workbooks-automate.md). W związku z tym Najprostszym sposobem przeniesienia szablonu jest ponowne użycie poprzedniego szablonu ARM i zaktualizowanie go do wdrożenia w nowym regionie.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że szablony skoroszytów są obsługiwane w regionie docelowym.

## <a name="prepare"></a>Przygotowywanie

* Zidentyfikuj poprzednio używany szablon ARM dla szablonu skoroszytu.

## <a name="move"></a>Move

1. Zaktualizuj poprzednio używany szablon w celu odwoływania się do nowego regionu.

   > [!NOTE]
   > Może być konieczne użycie nowej nazwy szablonu skoroszytu, aby uniknąć zduplikowanych nazw.

2. Wdróż zaktualizowany szablon za pomocą wdrożenia szablonu ARM, aby utworzyć nowy szablon skoroszytu w żądanym regionie.

## <a name="verify"></a>Weryfikacja

Użyj interfejsu użytkownika przeglądania skoroszytów platformy Azure, aby zlokalizować nowo wdrożony szablon skoroszytu. Upewnij się, że lokalizacja jest lokalizacją docelową.

## <a name="clean-up"></a>Czyszczenie

Po utworzeniu szablonu skoroszytu w nowym regionie Usuń oryginalny szablon skoroszytu w poprzednim regionie.
1. Znajdź szablon skoroszytu w interfejsie użytkownika przeglądania skoroszytów platformy Azure.
2. Wybierz szablon skoroszytu do usunięcia.
3. Wybierz polecenie "Usuń".

Jeśli zmieniono nazwę szablonu skoroszytu w celu zaimportowania go do nowego regionu, można zmienić nazwę szablonu skoroszytu na poprzednią nazwę po usunięciu oryginalnego elementu przy użyciu polecenia "Zmień nazwę" w widoku zasobów szablonu skoroszytu platformy Azure.

## <a name="next-steps"></a>Następne kroki

Chcesz przenieść skoroszyt zamiast szablonu? Zobacz, jak [przenieść skoroszyt platformy Azure do innego regionu](./workbooks-move-region.md).


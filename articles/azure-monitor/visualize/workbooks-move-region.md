---
title: Azure Monitor skoroszyty — przenoszenie regionów
description: Jak przenieść skoroszyt do innego regionu
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615980"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Przenoszenie skoroszytu platformy Azure do innego regionu

W tym artykule opisano sposób przenoszenia zasobów skoroszytów platformy Azure do innego regionu platformy Azure. Możesz przenieść zasoby do innego regionu z kilku powodów. Na przykład w celu skorzystania z nowego regionu platformy Azure, wdrożenia funkcji lub usług dostępnych tylko w określonych regionach, spełnienia wymagań wewnętrznych zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że skoroszyty są obsługiwane w regionie docelowym.

* Te instrukcje dotyczą zarówno udostępnionych skoroszytów ( `microsoft.insights/workbooks` ), jak i prywatnych skoroszytów ( `microsoft.insights/myworkbooks` ) zapisanych w Azure monitor i w większości typów zasobów.

  Jednak w przypadku skoroszytów powiązanych z typem zasobu Application Insights te skoroszyty są przechowywane w regionie świadczenia usługi Azure, w którym zapisywany jest zasób Application Insights.

  Skoroszyty te nie mogą być przenoszone indywidualnie do innego regionu.

## <a name="move"></a>Move

Najprostszym sposobem przenoszenia skoroszytu platformy Azure jest użycie funkcji Zapisz jako w narzędziu skoroszyty w interfejsie użytkownika portalu:

1. Otwórz skoroszyt docelowy w podglądzie skoroszytu.
2. Użyj przycisku paska narzędzi "Edytuj", aby przejść do trybu edycji.
3. Użyj przycisku paska narzędzi "Zapisz jako".
4. W formularzu Zapisz wybierz nazwę i żądany region skoroszytu. Upewnij się, że inne pola dotyczące subskrypcji, grupy zasobów i udostępniania są odpowiednie.

   > [!NOTE]
   > Aby uniknąć zduplikowanych nazw, może być konieczne użycie nowej nazwy skoroszytu.

5. Zapisz zmiany. 

## <a name="verify"></a>Weryfikacja

Użyj interfejsu użytkownika przeglądania skoroszytów platformy Azure, aby zlokalizować nowy skoroszyt. Upewnij się, że lokalizacja jest lokalizacją docelową.

## <a name="clean-up"></a>Czyszczenie

Gdy skoroszyt zostanie utworzony w nowym regionie, Usuń oryginalny skoroszyt w poprzednim regionie.
1. Otwórz oryginalny skoroszyt w podglądzie skoroszytu.
2. Użyj przycisku paska narzędzi "Edytuj", aby przejść do trybu edycji.
3. Z listy rozwijanej Edytuj narzędzia (ikona ołówka) wybierz pozycję "Usuń skoroszyt".

Jeśli zmieniono nazwę skoroszytu w celu zaimportowania go do nowego regionu, można zmienić nazwę skoroszytu na poprzednią nazwę po usunięciu oryginalnego skoroszytu przy użyciu paska narzędzi Edytuj narzędzia trybu edycji menu rozwijanego "Zmień nazwę".

## <a name="next-steps"></a>Następne kroki

Chcesz przenieść szablon skoroszytu zamiast skoroszytu? Zobacz, jak [przenieść szablon skoroszytu platformy Azure do innego regionu](./workbook-templates-move-region.md).

Zobacz [wdrażanie skoroszytów i szablonów skoroszytów](../visualize/workbooks-automate.md) za pomocą szablonów ARM.

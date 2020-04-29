---
title: Aktualizowanie istniejącego przypisania z portalu
description: Dowiedz się więcej o mechanizmie aktualizowania istniejącego przypisania strategii z portalu w planach platformy Azure.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381789"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak zaktualizować istniejące przypisanie strategii

Po przypisaniu planu można zaktualizować przypisanie. Istnieje kilka powodów, dla których należy zaktualizować istniejące przypisanie, w tym:

- Dodawanie lub usuwanie [blokowania zasobów](../concepts/resource-locking.md)
- Zmień wartość [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowanej** wersji planu

## <a name="updating-assignments"></a>Aktualizowanie przypisań

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **przypisane plany** ze strony po lewej stronie.

1. Na liście planów kliknij lewym przyciskiem myszy przypisanie planu. Następnie kliknij przycisk **Aktualizuj przypisanie** lub kliknij prawym przyciskiem myszy przypisanie planu i wybierz pozycję **Aktualizuj przypisanie**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Aktualizowanie istniejącego przypisania strategii" border="false":::

1. Strona **Przypisz plan** spowoduje załadowanie wstępnie wypełnionych wszystkich wartości z oryginalnego przypisania.
   Można zmienić **wersję definicji**planu, stan **przypisania blokady** i wszystkie parametry dynamiczne, które istnieją w definicji strategii. Po wprowadzeniu zmian kliknij przycisk **Przypisz** .

1. Na stronie zaktualizowane informacje o przypisaniu Zobacz nowy stan. W tym przykładzie dodaliśmy **blokadę** do przypisania.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Zaktualizowano istniejący tryb blokowania przypisania" border="false":::

1. Poznaj szczegółowe informacje o innych **operacjach przypisywania** przy użyciu listy rozwijanej. Tabela aktualizacji **zarządzanych zasobów** według wybranej operacji przypisania.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Operacje przypisania planu" border="false":::

## <a name="rules-for-updating-assignments"></a>Reguły dotyczące aktualizowania przypisań

Wdrożenie zaktualizowanych przypisań następuje po kilku ważnych regułach. Te reguły określają, co się dzieje z już wdrożonymi zasobami. Żądana zmiana i typ zasobu artefaktu, który jest wdrażany lub aktualizowany, decyduje o tym, które akcje są wykonywane.

- Przypisania ról
  - W przypadku zmiany roli lub osoby przydzielonej roli (użytkownika, grupy lub aplikacji) zostanie utworzone nowe przypisanie roli. Wcześniej wdrożone przypisania ról są pozostawione w miejscu.
- Przypisania zasad
  - W przypadku zmiany parametrów przypisania zasad istniejące przypisanie zostanie zaktualizowane.
  - Jeśli definicja przypisania zasad zostanie zmieniona, zostanie utworzone nowe przypisanie zasad.
    Wcześniej wdrożone przypisania zasad są pozostawione w miejscu.
  - Jeśli artefakt przypisania zasad zostanie usunięty z planu, wdrożone przypisania zasad są pozostawiane w miejscu.
- Szablony usługi Azure Resource Manager
  - Szablon jest przetwarzany przez Menedżer zasobów jako **Put**. Ponieważ każdy typ zasobu obsługuje tę akcję inaczej, zapoznaj się z dokumentacją każdego dołączonego zasobu, aby określić wpływ tej akcji w przypadku uruchamiania przez plany.

## <a name="possible-errors-on-updating-assignments"></a>Możliwe błędy podczas aktualizowania przypisań

Podczas aktualizowania przypisań można wprowadzać zmiany, które są przerywane po wykonaniu. Przykładem jest zmiana lokalizacji grupy zasobów, która została już wdrożona. Można wprowadzić wszelkie zmiany, które są obsługiwane przez [Azure Resource Manager](../../../azure-resource-manager/management/overview.md) , ale wszelkie zmiany, które spowodują wystąpienie błędu za pomocą Azure Resource Manager, spowodują również niepowodzenie przypisania.

Nie ma żadnego limitu, ile razy można zaktualizować przypisanie. W przypadku wystąpienia błędu należy określić błąd i wprowadzić kolejną aktualizację do przypisania.  Przykładowe scenariusze błędów:

- Nieprawidłowy parametr
- Już istniejący obiekt
- Zmiana nie jest obsługiwana przez Azure Resource Manager

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).
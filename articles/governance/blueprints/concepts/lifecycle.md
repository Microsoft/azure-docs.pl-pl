---
title: Zapoznaj się z cyklem życia planu
description: Dowiedz się więcej na temat cyklu życia, przez który przechodzą definicje planu i szczegółowe informacje o każdym z etapów, w tym aktualizowanie i usuwanie przypisań strategii.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918521"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Zapoznaj się z cyklem życia Azure Blueprint

Podobnie jak w przypadku wielu zasobów platformy Azure, plan w planach platformy Azure ma typowy i naturalny cykl życia. Są one tworzone, wdrażane i usuwane, gdy nie są już potrzebne lub są istotne. Plany platformy Azure obsługują standardowe operacje cyklu życia. Następnie kompiluje je w celu zapewnienia dodatkowych poziomów stanu, które obsługują wspólną ciągłą integrację i ciągłe wdrażanie potoków dla organizacji, które zarządzają infrastrukturą jako kodu — elementu klucza w DevOps.

Aby w pełni zrozumieć plany i etapy, zajmiemy się standardowym cyklem życia:

> [!div class="checklist"]
> - Tworzenie i Edytowanie planu
> - Publikowanie planu
> - Tworzenie i edytowanie nowej wersji planu
> - Publikowanie nowej wersji planu
> - Usuwanie określonej wersji planu
> - Usuwanie planu

## <a name="creating-and-editing-a-blueprint"></a>Tworzenie i Edytowanie planu

Podczas tworzenia planu Dodaj do niego artefakty, Zapisz je w grupie zarządzania lub subskrypcji i podano unikatową nazwę i unikatową wersję. Plan jest teraz w trybie **wersji roboczej** i nie można go jeszcze przypisać. W trybie **wersji roboczej** można nadal aktualizować i zmieniać.

Nieopublikowana strategia w trybie **wersji roboczej** wyświetla inną ikonę na stronie **definicje** strategii niż te, które zostały **opublikowane**. **Najnowsza wersja** jest wyświetlana jako **wersja robocza** dla tych, które nigdy nie zostały opublikowane.

Utwórz i Edytuj plan za pomocą [Azure Portal](../create-blueprint-portal.md#create-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikowanie strategii

Gdy wszystkie planowane zmiany zostały wprowadzone do planu w trybie **wersji roboczej** , można je **opublikować** i udostępnić do przypisania. **Opublikowana** wersja planu nie może zostać zmieniona. Po **opublikowaniu** plan jest wyświetlany z inną ikoną niż szkice **robocze** i wyświetla podany numer wersji w kolumnie **Najnowsza wersja** .

Opublikuj plan za pomocą [Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) lub [interfejsu API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Tworzenie i edytowanie nowej wersji planu

Nie można zmienić **opublikowanej** wersji planu. Jednak nową wersję planu można dodać do istniejącego planu i zmodyfikować zgodnie z wymaganiami. Wprowadź zmiany w istniejącym planie, edytując je. Po zapisaniu nowych zmian plan ma teraz **nieopublikowane zmiany**. Te zmiany są nową wersją **projektu** planu.

Edytuj plan przy użyciu [Azure Portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikowanie nowej wersji planu

Przed przypisaniem każdej edytowanej wersji strategii należy ją **opublikować** . Po dokonaniu **nieopublikowanych zmian** w planie, ale nie **opublikowanych**, przycisk **Publikuj plan** jest dostępny na stronie Edytuj plan. Jeśli przycisk nie jest widoczny, plan został już **opublikowany** i nie ma żadnych **nieopublikowanych zmian**.

> [!NOTE]
> Jeden plan może mieć wiele **opublikowanych** wersji, które można przypisać do subskrypcji.

Aby opublikować plan z **nieopublikowanymi zmianami**, wykonaj te same kroki w celu opublikowania nowego planu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Usuwanie określonej wersji planu

Każda wersja planu jest unikatowym obiektem i może być indywidualnie **opublikowana**. W związku z tym można również usunąć każdą wersję planu. Usunięcie wersji planu nie ma żadnego wpływu na inne wersje tego planu.

> [!NOTE]
> Nie można usunąć planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie Usuń wersję, którą chcesz usunąć.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **definicje** strategii ze strony po lewej stronie, a następnie użyj opcji filtru, aby zlokalizować plan, dla którego chcesz usunąć wersję. Wybierz go, aby otworzyć stronę Edycja.

1. Wybierz kartę **opublikowane wersje** i Znajdź wersję, którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wersję, którą chcesz usunąć, a następnie wybierz pozycję **Usuń tę wersję**.

## <a name="deleting-the-blueprint"></a>Usuwanie planu

Można również usunąć podstawowe plany. Usunięcie podstawowego planu spowoduje również usunięcie wszystkich wersji strategii tego planu, w tym **projektów** i **opublikowanych** planów. Podobnie jak w przypadku usuwania wersji planu, usunięcie podstawowego planu nie powoduje usunięcia istniejących przypisań żadnej wersji planu.

> [!NOTE]
> Nie można usunąć planu, który ma aktywne przypisania. Najpierw usuń przypisania, a następnie Usuń wersję, którą chcesz usunąć.

Usuń plan z [Azure portalem](../create-blueprint-portal.md#delete-a-blueprint) lub [interfejsem API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Przypisania

W trakcie cyklu życia można przypisać plan do subskrypcji. Po **opublikowaniu** trybu wersji planu ta wersja może być przypisana do subskrypcji. Ten cykl życia umożliwia korzystanie z wersji planu i aktywnie przypisywania podczas tworzenia nowszej wersji.

Gdy są przypisane wersje planów, ważne jest, aby zrozumieć, gdzie są przypisane, i z którymi parametrami zostały przypisane. Parametry mogą być statyczne lub dynamiczne. Aby dowiedzieć się więcej, zobacz [Parametry statyczne i dynamiczne](./parameters.md).

### <a name="updating-assignments"></a>Aktualizowanie przypisań

Po przypisaniu planu można zaktualizować przypisanie. Istnieje kilka powodów, dla których należy zaktualizować istniejące przypisanie, w tym:

- Dodawanie lub usuwanie [blokowania zasobów](./resource-locking.md)
- Zmień wartość [parametrów dynamicznych](./parameters.md#dynamic-parameters)
- Uaktualnij przypisanie do nowszej **opublikowanej** wersji planu

Aby dowiedzieć się, jak to zrobić, zobacz [aktualizowanie istniejących przypisań](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Cofanie przypisania przypisań

Jeśli plan nie jest już wymagany, można go przypisać do grupy zarządzania lub subskrypcji. Podczas przypisywania strategii następuje:

- Usuwanie [blokowania zasobów](./resource-locking.md) planu
- Usuwanie obiektu przypisania strategii
- Warunk Jeśli użyto **tożsamości zarządzanej przypisanej do systemu** , zostanie ona również usunięta

> [!NOTE]
> Wszystkie zasoby wdrożone przez przypisanie planu pozostają na miejscu, ale nie są już chronione przez plany platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](./resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).
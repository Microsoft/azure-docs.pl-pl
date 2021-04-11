---
title: Instruktaż Azure Advisor Windows Virtual Desktop — Azure
description: Jak rozwiązać Azure Advisor zalecenia dotyczące pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 666f882dec6c00b9fe5421485afded1fbd57d961
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448308"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Jak rozwiązać zalecenia dotyczące Azure Advisor

W tym artykule opisano, jak można rozwiązać zalecenia, które pojawiają się w Azure Advisor dla pulpitu wirtualnego systemu Windows.

## <a name="no-validation-environment-enabled"></a>"Brak włączonego środowiska sprawdzania poprawności"

>[!div class="mx-imgBorder"]
>![Zrzut ekranu przedstawiający stronę Azure Advisorą doskonałości operacyjnej. Zalecenie "Brak włączonej ochrony środowiska" zostało wyróżnione kolorem czerwonym.](media/no-validation-environment.png)

To zalecenie jest wyświetlane w obszarze doskonałości operacyjnej. Zalecenie powinno również zawierać komunikat ostrzegawczy podobny do tego:

"Nie masz włączonego środowiska walidacji w tej subskrypcji. Podczas konfigurowania pul hostów **nie** wybrano "środowisko walidacji" na karcie właściwości. Aby zapewnić ciągłość działania w ramach wdrożeń usług pulpitu wirtualnego systemu Windows, upewnij się, że masz co najmniej jedną pulę hostów ze środowiskiem walidacji, w której można testować potencjalne problemy.

Możesz sprawić, aby ten komunikat ostrzegawczy został wystawiony przez włączenie środowiska walidacji w jednej z pul hosta.

Aby włączyć środowisko walidacji:

1. Przejdź do strony głównej Azure Portal i wybierz pulę hostów, którą chcesz zmienić.

2. Następnie wybierz pulę hostów, którą chcesz zmienić ze środowiska produkcyjnego na środowisko walidacji.

3. W puli hostów wybierz pozycję **Właściwości** w lewej kolumnie. Następnie przewiń w dół do momentu wyświetlenia pozycji "środowisko sprawdzania poprawności". Wybierz pozycję **tak**, a następnie wybierz pozycję **Zastosuj**.

>[!div class="mx-imgBorder"]
>![Zrzut ekranu przedstawiający menu właściwości. "Środowisko walidacji" jest wyróżnione kolorem czerwonym, a zaznaczony jest dymek "tak".](media/validation-yes.png)

Te zmiany nie sprawiają, że ostrzeżenie zostanie natychmiast odrzucone, ale powinno być niewidoczne. Azure Advisor aktualizacje dwa razy dziennie. Do tego czasu możesz odroczyć lub odrzucić zalecenie ręcznie. Zalecamy, aby Rekomendacja nie była zależna. Dzięki temu Azure Advisor mogą poinformować o ewentualnych problemach w przypadku zmiany ustawień.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Nie ma wystarczającej liczby włączonych środowisk produkcyjnych (bez sprawdzania poprawności)"

To zalecenie jest wyświetlane w obszarze doskonałości operacyjnej.

W przypadku tego zalecenia komunikat ostrzegawczy jest wyświetlany z jednego z następujących powodów:

- W środowisku walidacji masz zbyt wiele pul hostów.
- Nie masz żadnych pul hostów produkcyjnych.

Zalecamy, aby użytkownicy mieli mniej niż połowę pul hostów w środowisku walidacji.

Aby usunąć to ostrzeżenie:

1. Przejdź do strony głównej Azure Portal.

2. Wybierz pule hostów, które chcesz zmienić przed walidacją na produkcyjną.

3. W puli hostów wybierz kartę **Właściwości** w kolumnie po prawej stronie ekranu. Następnie przewiń w dół do momentu wyświetlenia pozycji "środowisko sprawdzania poprawności". Wybierz pozycję **nie**, a następnie wybierz pozycję **Zastosuj**.

>[!div class="mx-imgBorder"]
>![Zrzut ekranu przedstawiający menu właściwości. "Środowisko walidacji" jest wyróżnione kolorem czerwonym, a zaznaczony jest dymek "No".](media/validation-no.png)

Te zmiany nie sprawiają, że ostrzeżenie zostanie natychmiast odrzucone, ale powinno być niewidoczne. Azure Advisor aktualizacje dwa razy dziennie. Do tego czasu możesz odroczyć lub odrzucić zalecenie ręcznie. Zalecamy, aby Rekomendacja nie była zależna. Dzięki temu Azure Advisor mogą poinformować o ewentualnych problemach w przypadku zmiany ustawień.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Za mało zablokowanych linków, aby pomyślnie zaimplementować maszynę wirtualną"

To zalecenie jest wyświetlane w obszarze doskonałości operacyjnej.

Należy odblokować określone adresy URL, aby upewnić się, że maszyna wirtualna (VM) działa poprawnie. Możesz wyświetlić listę z [bezpiecznym adresem URL](safe-url-list.md). Jeśli adresy URL nie są blokowane, maszyna wirtualna nie będzie działała prawidłowo.

Aby rozwiązać ten problem, upewnij się, że wszystkie adresy URL zostały odblokowane na [liście bezpiecznych adresów URL](safe-url-list.md). W celu odblokowania adresów URL można użyć tagów lub tagów FQDN usługi.

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz bardziej szczegółowych wskazówek dotyczących sposobu rozwiązywania typowych problemów, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną dla pulpitu wirtualnego systemu Windows](troubleshoot-set-up-overview.md).

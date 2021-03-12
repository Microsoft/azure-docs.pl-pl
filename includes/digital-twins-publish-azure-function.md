---
author: baanders
description: plik dołączany do procesu publikowania funkcji platformy Azure z programu Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630281"
---
Aby opublikować projekt w aplikacji funkcji na platformie Azure, zaznacz projekt prawym przyciskiem myszy w *Eksplorator rozwiązań* a następnie wybierz polecenie **Publikuj**.

> [!IMPORTANT] 
> Publikowanie w aplikacji funkcji na platformie Azure wiąże się z dodatkowymi opłatami za subskrypcję, niezależnie od bliźniaczych reprezentacji cyfrowych platformy Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Zrzut ekranu przedstawiający program Visual Studio z menu wyboru rozwiązania z prawej strony. Polecenie Publikuj jest wyróżnione w menu.":::

Na poniższej stronie *Publikuj* pozostaw domyślny wybór dla **platformy Azure** i wybierz pozycję *dalej*. 

Dla konkretnego obiektu docelowego wybierz pozycję **Azure aplikacja funkcji (Windows)** i wybierz pozycję *dalej*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Zrzut ekranu programu Visual Studio w oknie dialogowym Publikowanie funkcji platformy Azure. Na określonej stronie docelowej została wybrana usługa Azure aplikacja funkcji (system Windows).":::

Na stronie *wystąpienie funkcji* wybierz swoją subskrypcję. Następnie wybierz *+* ikonę, aby utworzyć nową funkcję platformy Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Zrzut ekranu programu Visual Studio w oknie dialogowym Publikowanie funkcji platformy Azure. Przycisk +, aby utworzyć nową funkcję, zostanie wyróżniony na stronie wystąpienie funkcji.":::

W *aplikacja funkcji (Windows) — Utwórz nowe* okno, wypełnij pola w następujący sposób:
* **Nazwa** to nazwa planu zużycia, który będzie używany przez platformę Azure do hostowania aplikacji Azure Functions. Będzie to również nazwa aplikacji funkcji, która zawiera rzeczywistą funkcję. Możesz wybrać własną unikatową wartość lub pozostawić domyślną sugestię.
* Upewnij się, że **subskrypcja** jest zgodna z subskrypcją, której chcesz użyć 
* Upewnij się, że **Grupa zasobów** jest grupą zasobów, której chcesz użyć
* Pozostaw **Typ planu** jako *zużycie*
* Wybierz **lokalizację** pasującą do lokalizacji grupy zasobów
* Utwórz nowy zasób **usługi Azure Storage** przy użyciu nowego linku. *..* . Ustaw lokalizację zgodną z grupą zasobów, użyj innych wartości domyślnych i wybierz pozycję "OK".

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Zrzut ekranu programu Visual Studio w oknie dialogowym Publikowanie funkcji platformy Azure. Są wypełniane szczegóły nowej aplikacji funkcji, w tym nazwy, subskrypcji, grupy zasobów, typu planu, lokalizacji i usługi Azure Storage.":::

Następnie wybierz przycisk **Utwórz**.

Po krótkim czasie oczekiwania na utworzenie usługi App Service okno dialogowe powinno wrócić do strony *wystąpienia funkcji* , a nowa aplikacja funkcji jest wyświetlana w obszarze **aplikacje funkcji** zagnieżdżony pod grupą zasobów. Wybierz pozycję *Zakończ*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publikowanie funkcji platformy Azure w programie Visual Studio: wystąpienie funkcji (po aplikacji funkcji)":::

W okienku *Publikowanie* , które zostanie otwarte z powrotem w głównym oknie programu Visual Studio, sprawdź, czy wszystkie informacje są poprawne, a następnie wybierz pozycję **Publikuj**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Zrzut ekranu programu Visual Studio w oknie dialogowym Publikowanie funkcji platformy Azure. Nowa aplikacja funkcji zostanie wyświetlona na liście aplikacji funkcji i istnieje przycisk Zakończ.":::

> [!NOTE]
> Jeśli zobaczysz podręczny ekran podobny do tego: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="zrzut ekranu okna podręcznego programu Visual Studio o nazwie Publikuj poświadczenia. Zawiera pola dla nazwy użytkownika i hasła oraz przycisk próbujący pobrać poświadczenia z platformy Azure." border="false":::
> Wybierz pozycję **próba pobrania poświadczeń z platformy Azure** i **Zapisz**.
>
> Jeśli zostanie wyświetlone ostrzeżenie, aby *uaktualnić wersję funkcji na platformie Azure* lub że *wersja środowiska uruchomieniowego funkcji jest niezgodna z wersją działającą na platformie Azure*:
>
> Postępuj zgodnie z monitami, aby przeprowadzić uaktualnienie do najnowszej wersji środowiska uruchomieniowego Azure Functions. Ten problem może wystąpić, jeśli używasz starszej wersji programu Visual Studio.

Aplikacja funkcji jest teraz publikowana na platformie Azure.
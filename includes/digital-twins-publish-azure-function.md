---
author: baanders
description: plik dołączania na proces publikowania funkcji platformy Azure z Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480750"
---
Aby opublikować projekt w aplikacji funkcji na platformie Azure, zacznij od Eksplorator rozwiązań. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Opublikuj.**

> [!IMPORTANT] 
> Publikowanie w aplikacji funkcji na platformie Azure wiąże się z dodatkowymi opłatami w ramach subskrypcji, niezależnie od Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Zrzut ekranu Visual Studio przedstawiający menu rozwiązania wyświetlane po kliknięciu prawym przyciskiem myszy. W menu opcja Publikuj jest wyróżniona.":::

Na **otwartej stronie** Publikowanie pozostaw domyślną docelową opcję **Azure.** Następnie wybierz przycisk **Dalej**. 

Dla określonego obiektu docelowego wybierz pozycję **Aplikacja funkcji platformy Azure (Windows),** a następnie wybierz pozycję **Dalej.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Zrzut ekranu przedstawiający Visual Studio funkcji platformy Azure. Na stronie Określony element docelowy wybierz aplikację funkcji platformy Azure (Windows).":::

Na karcie **Wystąpienie usługi Functions** wybierz swoją subskrypcję. Następnie wybierz ikonę plusa (+), aby utworzyć nową funkcję.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe Publikowanie funkcji platformy Azure. Ikona plusa jest wyróżniona.":::

W **oknie Aplikacja funkcji (Windows)** — Tworzenie nowej wypełnij następujące pola:
* **Nazwa** to nazwa planu zużycia, który będzie przez platformę Azure hostować twoją Azure Functions aplikację. Ta nazwa będzie również dotyczyć aplikacji funkcji, która przechowuje rzeczywistą funkcję. Możesz wybrać unikatową wartość lub pozostawić sugestię domyślną.
* Upewnij **się, że subskrypcja** odpowiada subskrypcji, której chcesz użyć. 
* Upewnij **się, że grupa zasobów** jest grupą, której chcesz użyć.
* Pozostaw **dla opcji Typ planu** wybór jako **Zużycie.**
* Wybierz **lokalizację** grupy zasobów.
* Utwórz nowy **zasób usługi Azure Storage,** **wybierając** link Nowy. Ustaw lokalizację tak, aby dopasować ją do grupy zasobów, użyj innych wartości domyślnych, a następnie wybierz przycisk **OK.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe Publikowanie funkcji platformy Azure. Wypełniane są szczegóły nowej aplikacji funkcji, w tym nazwa, subskrypcja, grupa zasobów, typ planu, lokalizacja i usługa Azure Storage.":::

Następnie wybierz pozycję **Utwórz**.

Po utworzeniu usługi App Service zostanie otwarta **karta Wystąpienie** usługi Functions. Nowa aplikacja funkcji zostanie wyświetlona w obszarze **Aplikacje funkcji** poniżej grupy zasobów. Wybierz pozycję **Zakończ**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe Publikowanie funkcji platformy Azure. Wybrana jest karta Wystąpienie usługi Functions. Nowa aplikacja funkcji zostanie wyświetlona w obszarze grupy zasobów.":::

W **okienku Publikowanie,** które zostanie otwarte w głównym oknie Visual Studio sprawdź, czy wszystkie informacje wyglądają prawidłowo. Następnie wybierz pozycję **Opublikuj**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okienko Publikowanie. Przycisk Publikuj został wyróżniony.":::

> [!NOTE]
> Jeśli zostanie otwarte okno podręczne podobne do poniższego przykładu, wybierz pozycję Spróbuj pobrać poświadczenia z platformy **Azure,** a następnie wybierz pozycję **Zapisz.**
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno podręczne o nazwie Publikuj poświadczenia. Zawiera pola nazwy użytkownika i hasła. Zawiera również przycisk Próba pobrania poświadczeń z platformy Azure." border="false":::
>
> Jeśli widzisz jedno z następujących ostrzeżeń, postępuj zgodnie z monitami, aby uaktualnić do najnowszej Azure Functions środowiska uruchomieniowego:
> * "Uaktualnianie wersji funkcji na platformie Azure".
> * "Wersja środowiska uruchomieniowego funkcji nie jest dopasowana do wersji uruchomionej na platformie Azure".
>
> Te ostrzeżenia mogą pojawić się, jeśli używasz starej wersji Visual Studio.

Aplikacja funkcji jest teraz opublikowana na platformie Azure.

---
title: Konfigurowanie materiałów renderowania opartych na fizycznie w Maya
description: Wyjaśniono, jak skonfigurować fizyczne materiały renderowania w Maya i wyeksportować je do formatu FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 56aa0d91372ac2d21a20f28b1044f0811c716b0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358036"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Samouczek: Konfigurowanie fizycznie opartych na materiałach renderowania w Maya

## <a name="overview"></a>Omówienie
Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Przypisuj materiały z zaawansowanymi oświetleniem do obiektów w scenie.
> * Obsługa wystąpień obiektów i materiałów.
> * Wyeksportuj scenę do formatu FBX i wybierz ważne opcje.

Tworzenie [materiałów renderowania opartych na fizycznie (PBR)](../../overview/features/pbr-materials.md) w Maya to stosunkowo proste zadanie. Jest to podobne na wiele sposobów w przypadku instalacji w innych aplikacjach do tworzenia zawartości, takich jak 3DS Max. Ten samouczek to przewodnik dotyczący podstawowej instalacji modułu cieniującego w programie PBR i eksportowania FBX na potrzeby projektów zdalnego renderowania na platformie Azure. 

Przykładowa scena w tym samouczku zawiera wiele obiektów pól wielokątów. Są do nich przypisane różne materiały, takie jak drewno, metal, malowanie metalowe, plastikowe i gumowe. Ogólnie mówiąc, każdy materiał zawiera wszystkie lub większość następujących tekstur:

* **Albedo**, czyli Mapa kolorów materiałów i jest również nazywana **rozpraszaniem** lub **BaseColor**.
* **Metalowy**, który określa, czy materiał jest metaliczny i które części są metalowe. 
* **Niesztywność**, która określa, jak sztywne lub gładko powierzchnię i ma wpływ na ostrość lub blurriness odbicia i wyróżnienia na powierzchni.
* **Normalne**, które dodaje szczegóły do powierzchni bez konieczności dodawania większej liczby wielokątów. Przykłady szczegółów mogą być wygrane i mieć wcięcia na powierzchni metalowej lub ziarna w drewnie.
* **Zamknięcia otoczenia**, który służy do dodawania cieniowania miękkiego i cieniowania do modelu. Jest to mapa w skali szarości, która wskazuje, które obszary modelu otrzymują pełne oświetlenie (białe) lub pełny cień (czarny). 

## <a name="prerequisites"></a>Wymagania wstępne
* Autodesk Maya 2017 lub nowszy

## <a name="set-up-materials-in-the-scene"></a>Konfigurowanie materiałów na scenie
Poniżej przedstawiono sposób konfigurowania materiału PBR w Maya.

Jak zobaczysz w przykładowej scenie, utworzyliśmy wiele obiektów Box. Każdy obiekt reprezentuje inny typ materiału. Jak pokazano na obrazie, każdy z tych obiektów uzyskał własną odpowiednią nazwę.

Renderowanie zdalne na platformie Azure używa liczników do pomiarów, a kierunek w górę jest osią Y. Przed rozpoczęciem tworzenia zasobów zalecamy ustawienie jednostek sceny w Maya na liczniki. W obszarze eksportowanie Określ wartości jednostek do liczników w ustawieniach eksportu FBX.

> [!TIP]
> Nadaj modelom odpowiednie nazwy na podstawie odpowiednich części lub typu materiału. Znaczenie nazw ułatwia nawigowanie w dużych scenach obiektów.

![Nazwy obiektów](media/object-names.jpg)

Po utworzeniu lub pozyskaniu niektórych tekstur można także utworzyć unikatowe tekstury. Można używać aplikacji texturing, takich jak Quixel Suite, PhotoShop lub Suite, lub pobierać ogólne tekstury z innych źródeł.

Aby zastosować tekstury do modelu:

1. W okienku ekranu sceny wybierz model lub geometrię, a następnie kliknij go prawym przyciskiem myszy. W wyświetlonym menu wybierz pozycję **Przypisz nowy materiał**.
1. W oknie dialogowym **przypisywanie nowego materiału** przejdź do **Maya**  >  **Stingray PBS**. Ta akcja przypisuje materiał PBR do modelu. 

W Maya 2020 dostępnych jest wiele różnych programów do cieniowania. Obejmują one **powierzchnię standardową Maya**, **powierzchnię standardową Arnold**i **Stingray PBR**. Standardowy program do **cieniowania powierzchni Maya** nie jest jeszcze eksportowany za pośrednictwem wtyczki FBX 2020. **Arnolde standardowe** można eksportować za pomocą plików FBX. W większości innych aspektów jest on identyczny z **Mayaem programu do cieniowania powierzchni standardowej**. Jest to analogiczne do **materiału fizycznego** w programie 3D Studio Max.

Program **STINGRAY PBR** jest zgodny z wieloma innymi aplikacjami i najlepiej odpowiada wymaganiom zdalnego renderowania platformy Azure. Jest ona obsługiwana od Maya 2017. Gdy ten typ materiału jest wizualizacją w okienku ekranu, jest podobny do wizualizacji w dalszej części renderowania zdalnego na platformie Azure.

![Materiał Stingray](media/stingray-material.jpg)

Wraz z materiałem przypisanym do zasobu i odpowiednio nazwanym, możesz teraz przypisywać różne tekstury. Na poniższych ilustracjach pokazano, gdzie każdy typ tekstury mieści się w materiale PBR. Materiał Stingray PBR umożliwia wybranie atrybutów, które można aktywować. Przed przystąpieniem do mapowania tekstury należy aktywować odpowiednie atrybuty.

![Konfiguracja materiału](media/material-setup.jpg)

Nazwij odpowiednie materiały, biorąc pod uwagę ich użycie lub typ. Materiał, który jest używany w ramach unikatowej części, może być nazwany dla tej części. Materiał, który jest używany w szerszym zakresie obszarów, może być nazwany dla jego właściwości lub typu.

Przypisz tekstury, jak pokazano na obrazie.

![Konfiguracja tekstury](media/texture-setup.jpg)

Po utworzeniu i skonfigurowaniu materiałów PBR należy rozważyć utworzenie wystąpienia [obiektów](../../how-tos/conversion/configure-model-conversion.md#instancing) w scenie. Tworzenie wystąpień podobnych obiektów w scenie, takich jak orzechy, pioruny, wkręty i spryskiwacze, daje znaczący oszczędności w rozmiarze pliku. Wystąpienia obiektu głównego mogą mieć własną skalę, rotację i przekształcenia, aby można je było umieścić zgodnie z wymaganiami w scenie. 

W programie Maya proces tworzenia wystąpień jest prosty.

1. W menu **Edycja** przejdź do pozycji **Duplikuj specjalne** , aby otworzyć Opcje.
1. W oknie dialogowym **zduplikowane opcje specjalne** w polu **typ geometrii** wybierz opcję **wystąpienie** . 
1. Wybierz pozycję **Duplikuj specjalne**.

   ![Zrzut ekranu przedstawia okno Maya z otwartym oknem dialogowym zduplikowane opcje specjalne i z wybraną opcją Duplikuj specjalnie.](media/instancing.jpg)

Ta akcja powoduje utworzenie wystąpienia obiektu. Można je przenosić, obracać i skalować niezależnie od elementów nadrzędnych i innych wystąpień tego elementu nadrzędnego. 

Wszelkie zmiany wprowadzone w wystąpieniu w trybie składników są przesyłane do wszystkich wystąpień obiektu. Można na przykład współpracować z składnikami wystąpienia obiektu, takimi jak wierzchołki i wielokąta. Upewnij się, że wszystkie zmiany zostały wprowadzone, aby wpływać na wszystkie te wystąpienia. 

W przykładowej scenie każdy obiekt pojedynczego pola został wystąpiły. Ta akcja będzie miała znaczenie podczas eksportowania sceny do formatu FBX.

![Omówienie sceny](media/scene-overview.jpg)

> [!TIP]
> Twórz wystąpienia w Twojej scenie, jak to możliwe. Późniejsze zastępowanie kopii przy użyciu obiektów wystąpień jest niezwykle trudne. 

## <a name="fbx-export-process"></a>Proces eksportowania FBX

Przejdźmy do FBXego eksportu zasobów sceny lub sceny. Podczas eksportowania zasobów warto wybrać tylko obiekty lub zasoby z Twojej sceny, które mają zostać wyeksportowane. Na przykład może istnieć 100 obiektów w scenie. Jeśli chcesz używać tylko 30 z nich, nie ma żadnych punktów eksportowania całej sceny. 

Aby wybrać:

1. Przejdź do **File**  >  **obszaru eksportowanie** plików, aby otworzyć okno dialogowe **eksport zaznaczenia** .
1. W polu **Pliki typu** wybierz pozycję **FBX Export** , aby wyświetlić ustawienia eksportu FBX. Podstawowe ustawienia eksportu FBX są wyróżnione na czerwono na obrazie.

   ![FBX eksportowanie](media/FBX-exporting.jpg)

Na przykład w zależności od wymagań można wysłać zasób do klienta programu. Być może nie chcesz wysyłać dużej liczby plików tekstury z elementem zawartości. Możesz osadzić tekstury w wyeksportowanym pliku FBX. Ta opcja oznacza, że masz tylko jeden plik do spakowania, ale rozmiar tego zasobu FBX znacznie się zwiększy. Możesz włączyć opcję osadzania tekstur, wybierając opcję **Osadź multimedia** , jak pokazano.

> [!TIP]
> W tym przykładzie plik miał nazwę, aby odzwierciedlał ten warunek. Ten styl nazewnictwa jest dobrym sposobem śledzenia zasobów. 

Po ustawieniu konfiguracji na potrzeby eksportu wybierz pozycję **Eksportuj zaznaczenie** w prawym dolnym rogu.

![Osadzanie multimediów](media/embedding-media.jpg)

## <a name="conclusion"></a>Podsumowanie

Ogólnie rzecz biorąc, ten typ materiału wygląda bardziej realistycznie, ponieważ jest oparty na rzeczywistej fizyki ziemskiej. Tworzy dodatkowy efekt immersyjny, aby sceny pojawiły się w świecie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak skonfigurować materiały z zaawansowanymi oświetleniem dla obiektów w scenie. Wiesz również, jak wyeksportować obiekty do formatu FBX, który jest obsługiwany przez renderowanie zdalne na platformie Azure. Następnym krokiem jest przekonwertowanie pliku FBX i wizualizacja go w ramach renderowania zdalnego na platformie Azure.

> [!div class="nextstepaction"]
> [Szybki Start: konwertowanie modelu do renderowania](../../quickstarts\convert-model.md)
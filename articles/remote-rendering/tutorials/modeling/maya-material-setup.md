---
title: Konfigurowanie fizycznych materiałów do renderowania w programie Maya
description: Wyjaśniono, jak skonfigurować fizyczne materiały renderowania w Maya i wyeksportować je do formatu FBX
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977835"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Samouczek: Konfigurowanie fizycznie opartych na materiałach renderowania w Maya

## <a name="overview"></a>Omówienie
Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Przypisz materiały z modelem oświetlenia zaawansowanego do obiektów w scenie.
> * Obsługa wystąpień obiektów i materiałów.
> * Wyeksportuj scenę do formatu FBX i ważnych opcji do wyboru.

Tworzenie [opartych na fizycznie materiałach renderowania (PBR)](../../overview/features/pbr-materials.md) w programie `Maya` to stosunkowo proste zadanie do przesyłania dalej, podobnie jak w przypadku innych aplikacji do tworzenia zawartości, takich jak `3DS Max` . Poniższy samouczek przedstawia przewodnik dotyczący podstawowej konfiguracji programu do cieniowania PBR i eksportu FBX dla projektów ARR. 

Przykładowa scena w tym samouczku zawiera kilka `Polygon Box` obiektów, do których przypisano wiele różnych materiałów — drewna, metal, folia metalowa, plastikowa i gumowa. Ogólnie mówiąc, każdy materiał zawiera wszystkie lub większość następujących tekstur 

* `Albedo`, czy Mapa kolorów materiałów, nazywana również `Diffuse` lub`BaseColor`
* `Metalness`, które określa, czy materiał jest metaliczny i które części są metalowe. 
* `Roughness`, która określa, jak sztywne lub gładko powierzchnię, wpływając na ostrość lub blurriness odbicia i wyróżnienia na powierzchni.
* `Normals`, który dodaje szczegóły do powierzchni, na przykład odpestki i zwiększa wcięcie powierzchni metalowej lub ziarna w drewnie bez konieczności dodawania większej liczby wielokątów.
* `Ambient Occlusion`, który służy do dodawania cieniowania miękkiego i cieniowania do modelu. Jest to mapa odcienie szarości, która wskazuje, które obszary modelu otrzymują pełne oświetlenie (białe) lub pełny cień (czarny). 

## <a name="prerequisites"></a>Wymagania wstępne
* `Autodesk Maya 2017`lub nowszy

## <a name="setting-up-materials-in-the-scene"></a>Konfigurowanie materiałów na scenie
W programie Maya proces konfigurowania materiału PBR jest następujący:

Aby zacząć od, jak zobaczysz w przykładowej scenie, utworzymy kilka obiektów Box, z których każdy reprezentuje inny typ materiału. Zwróć uwagę, jak pokazano na poniższej ilustracji, że każdy z tych obiektów otrzymał własną nazwę 

> Warto zauważyć, że przed rozpoczęciem tworzenia zasobów dla zdalnego renderowania na platformie Azure (ARR), który używa mierników dla pomiaru, a kierunek w górę jest osią Y. W związku z tym zaleca się ustawienie jednostek sceny w Maya na liczniki. Ponadto zaleca się, aby podczas eksportowania skonfigurować jednostki do liczników w ustawieniach eksportu FBX. 

> [!TIP]
Dobrym rozwiązaniem jest odpowiednie nazywanie zasobów modelu, zazwyczaj z istotną częścią lub typem materiału, ponieważ nazwy ułatwiają szybkie przechodzenie do dużych scen.

![Nazwy obiektów](media/object-names.jpg)

Po utworzeniu/uzyskaniu tekstury — w zależności od potrzeb, możesz chcieć utworzyć unikatowe tekstury dla modelu w aplikacjach texturing, takich jak `Quixel Suite` , `Photoshop` , lub `Substance Suite` lub użyć rodzajowych tekstur tekstury z innych źródeł, można zastosować je do modelu w następujący sposób:

* W okienku ekranu sceny wybierz model/geometrię, a następnie kliknij go prawym przyciskiem myszy. W wyświetlonym menu kliknij pozycję`Assign New Material`
* W obszarze `Assign New Material` Opcje przejdź do `Maya` > `Stingray PBS` . Ta akcja spowoduje przypisanie materiału PBR do modelu. 

W programie `Maya 2020` dostępnych jest wiele różnych programów do cieniowania (PBR) — `Maya Standard Surface` , `Arnold Standard Surface` i `Stingray PBR` . Element `Maya Standard Surface Shader` nie jest jeszcze eksportowany za pośrednictwem programu `FBX plugin 2020` , podczas gdy `Arnold Standard Surface Shader` można go wyeksportować z plikami FBX. W większości innych kwestii jest on identyczny z `Maya Standard Surface Shader` i jest analogiczny do `Physical Material` `3D Studio Max` .

**`The Stingray PBR Shader`** jest zgodny z wieloma innymi aplikacjami i najlepiej pasuje do wymagań i jest `ARR` obsługiwana od `Maya 2017` . Ponadto wygodnie jest, aby ten typ materiałów był wizualizacją w okienku ekranu podobnym do tego, co będzie wizualizowane później w ARR.

![Materiał "Stingray"](media/stingray-material.jpg)

Wraz z materiałem przypisanym do zasobu i odpowiednio nazwanym, możesz teraz przypisywać różne tekstury. Na poniższych ilustracjach przedstawiono szczegółowe informacje o tym, gdzie każdy typ tekstury mieści się w materiale PBR. `Stingray PBR`Materiał umożliwia wybranie atrybutów, które można aktywować, dlatego przed zapisaniem `plug in` mapy tekstury należy aktywować odpowiednie atrybuty: 

![Konfiguracja materiału](media/material-setup.jpg)

> [!TIP]
Dobrym sposobem jest odpowiednie nazwy materiałów, biorąc pod uwagę ich użycie i/lub typ. Materiał, który ma być używany na unikatowej części, może być nazwany dla tej części, podczas gdy materiał, który może być używany w szerszej liczbie obszarów, może być nazwany dla jego właściwości lub typu.

Przypisz tekstury w następujący sposób:

![Konfiguracja tekstury](media/texture-setup.jpg)

Wraz z utworzonymi i skonfigurowanymi materiałami PBR warto zastanowić się nad tworzeniem [wystąpień obiektów](../../how-tos/conversion/configure-model-conversion.md#instancing) na scenie. Tworzenie wystąpień podobnych obiektów w scenie — takich jak orzechy, pioruny, spryskiwacze wkrętów — zasadniczo wszystkie obiekty, które są takie same, mogą przynieść znaczne oszczędności pod względem rozmiaru pliku. Wystąpienia obiektu głównego mogą mieć własną skalę, rotację i przekształcenia, aby można je było umieścić zgodnie z wymaganiami w scenie. W programie Maya proces tworzenia wystąpień jest prosty.

* W `Edit` menu Przejdź do `Duplicate Special` i Otwórz `Options` , 
* W `Duplicate Special` opcji Przełącz `Geometry Type` z `Copy` do `Instance` , 
* Kliknij pozycję `Duplicate Special`.

![Tworzenie wystąpienia](media/instancing.jpg)

Ta akcja spowoduje utworzenie wystąpienia obiektu, które można przenieść obrócone lub skalowane niezależnie od jego elementu nadrzędnego i innych wystąpień tego elementu nadrzędnego. 
>Jednak wszelkie zmiany wprowadzone w wystąpieniu w trybie składnika będą przesyłane do wszystkich wystąpień obiektu, dlatego w przypadku pracy z wystąpieniami obiektów wystąpień-wierzchołków, wielokątów itp. należy najpierw upewnić się, że wszystkie zmiany mają wpływ na wszystkie te wystąpienia.

W przykładowej scenie każdy obiekt pojedynczego pola został wystawiony. Ta akcja będzie miała znaczenie podczas eksportowania sceny do formatu FBX.

![Omówienie sceny](media/scene-overview.jpg)

>Najlepszym rozwiązaniem dotyczącym tworzenia wystąpienia w scenie jest tworzenie ich w miarę ich wykonywania, ponieważ zamienianie "kopii" obiektów wystąpień później jest niezwykle trudne. 

## <a name="fbx-export-process"></a>Proces eksportowania FBX

Teraz możemy przejść do FBXego eksportu elementów zawartości sceny lub sceny. Ogólnie mówiąc, warto mieć sens, gdy eksportowanie zasobów jest możliwe tylko do eksportu tych obiektów/zasobów z Twojej sceny. Jeśli masz 100 obiektów w scenie, ale chcesz użyć tylko 30 z nich, nie ma żadnych punktów eksportowania całej sceny. Jeśli więc nie masz szczęśliwej eksportu całej sceny, wybierz pozycję i przejdź do:

* `File` > `Export Selection`a w oknie dialogowym eksportowania przejdź na dół i ustaw wartość `Files of Type` `FBX Export` . W tym oknie zostaną ujawnione ustawienia eksportu FBX. Podstawowe ustawienia eksportu FBX są wyróżnione kolorem czerwonym na poniższej ilustracji.

![FBX eksportowanie](media/FBX-exporting.jpg)

W zależności od wymagań — na przykład możesz chcieć wysłać zasób do klienta, ale nie chcesz wysyłać dużej liczby plików tekstury z elementem zawartości, możesz osadzić tekstury w wyeksportowanym pliku FBX. Ta opcja oznacza, że masz tylko jeden plik do spakowania, ale znacznie zwiększy rozmiar tego elementu zawartości FBX. Możesz włączyć opcję osadzania tekstur, przełączając się do `Embed Media` opcji, jak pokazano poniżej.

> [!TIP]
> Zauważ, że plik w tym przypadku ma nazwę, aby odzwierciedlić ten warunek. Jest to dobrym sposobem na śledzenie zasobów. 

Po zakończeniu ustawiania konfiguracji na potrzeby eksportu kliknij przycisk "Eksportuj zaznaczenie" w prawym dolnym rogu.

![Osadzanie multimediów](media/embedding-media.jpg)

## <a name="conclusion"></a>Podsumowanie

Ogólnie rzecz biorąc, ten typ materiałów wygląda bardziej realistycznie, ponieważ jest oparty na rzeczywistej fizyki ziemskiej. Tworzy dodatkowy efekt immersyjny, że scena istnieje w świecie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

Teraz znasz najważniejsze funkcje konfigurowania materiałów z zaawansowanymi oświetleniem do obiektów w scenie i eksportując je do formatu FBX, który jest obsługiwany przez ARR. Następnym krokiem jest przekonwertowanie pliku FBX i wizualizacja w ARR.

> [!div class="nextstepaction"]
> [Szybki Start: konwertowanie modelu do renderowania](../../quickstarts\convert-model.md)
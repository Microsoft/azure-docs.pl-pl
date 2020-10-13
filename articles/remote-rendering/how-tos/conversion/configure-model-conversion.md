---
title: Konfigurowanie konwersji modelu
description: Opis wszystkich parametrów konwersji modelu
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 2134dde0fa0b92bec4519c0d6a24dcaad3792baa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575184"
---
# <a name="configure-the-model-conversion"></a>Konfigurowanie konwersji modelu

W tym rozdziale udokumentowano opcje konwersji modelu.

## <a name="settings-file"></a>Plik ustawień

Jeśli plik o nazwie `<modelName>.ConversionSettings.json` znajduje się w kontenerze danych wejściowych obok modelu wejściowego `<modelName>.<ext>` , będzie używany do zapewnienia dodatkowej konfiguracji procesu konwersji modelu.
Na przykład, `box.ConversionSettings.json` będzie używany podczas konwertowania `box.gltf` .

Zawartość pliku powinna spełniać następujący schemat JSON:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Przykładowy plik `box.ConversionSettings.json` może być:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametry geometrii

* `scaling` — Ten parametr skaluje model jednolicie. Skalowanie może służyć do powiększania lub zmniejszania modelu, na przykład w celu wyświetlenia modelu budynku na górze tabeli.
Skalowanie jest również ważne, gdy model jest zdefiniowany w jednostkach innych niż liczniki, ponieważ aparat renderowania oczekuje liczników.
Na przykład jeśli model jest zdefiniowany w centymetrach, zastosowanie skali 0,01 powinno spowodować, że model ma prawidłowy rozmiar.
Niektóre formaty danych źródłowych (na przykład. FBX) zapewniają wskazówkę skalowania jednostki, w tym przypadku konwersja niejawnie skaluje model do jednostek miary. Niejawne skalowanie dostarczone przez format źródła zostanie zastosowane w górnej części parametru skalowania.
Końcowy współczynnik skalowania jest stosowany do wierzchołków geometrii i lokalnych przekształceń węzłów wykresu sceny. Skalowanie dla przekształcenia jednostki głównej pozostaje niemodyfikowane.

* `recenterToOrigin` -Określa, że model powinien zostać przekonwertowany, aby jego pole ograniczenia zostało wyśrodkowane w miejscu pochodzenia.
Jeśli model źródłowy jest odsunięty od źródła, problemy z dokładnością do liczby zmiennoprzecinkowej mogą spowodować artefakty renderowania.
Wyśrodkowanie modelu może pomóc w takiej sytuacji.

* `opaqueMaterialDefaultSidedness` -Aparat renderowania zakłada, że nieprzezroczyste materiały są dwustronne.
Jeśli to założenie nie jest prawdziwe względem określonego modelu, ten parametr powinien być ustawiony na wartość "SingleSided". Aby uzyskać więcej informacji, zobacz [ :::no-loc text="single sided"::: renderowanie](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Usuwanie materiału z deduplikacji

* `deduplicateMaterials` -Ten parametr włącza lub wyłącza automatyczne usuwanie duplikatów materiałów, które mają te same właściwości i tekstury. Dezinstalacja jest wykonywana po przetworzeniu zastąpień materiału. Jest on domyślnie włączony.

* Jeśli nawet po usunięciu deduplikacji model ma więcej niż 65 535 materiałów, usługa podejmie próbę scalenia materiałów z podobnymi właściwościami. Jako ostatni z nich materiały przekroczenia limitu zostaną zastąpione czerwonym materiałem błędu.

![Obraz przedstawia dwa moduły 68 921 kolorowych trójkątów.](media/mat-dedup.png?raw=true)

Dwa moduły z 68 921 kolorowych trójkątów. Pozostało: przed cofnięciem duplikacji z 68 921 materiałami koloru. Prawo: po cofnięciu duplikowania z materiałami kolorów 64 000. Limit to 65 535 materiałów. (Zobacz [limity](../../reference/limits.md)).

### <a name="color-space-parameters"></a>Parametry przestrzeni kolorów

Aparat renderowania oczekuje, że wartości koloru mają być w przestrzeni liniowej.
Jeśli model jest zdefiniowany przy użyciu przestrzeni gamma, te opcje powinny być ustawione na wartość true.

* `gammaToLinearMaterial` — Konwertuj kolory materiału z przestrzeni gamma na miejsce liniowe
* `gammaToLinearVertex` — Konwertuj :::no-loc text="vertex"::: kolory z przestrzeni gamma na miejsce liniowe

> [!NOTE]
> Dla plików FBX te ustawienia są domyślnie ustawione na `true` . Dla wszystkich innych typów plików wartością domyślną jest `false` .

### <a name="scene-parameters"></a>Parametry sceny

* `sceneGraphMode` -Definiuje sposób konwersji grafu sceny w pliku źródłowym:
  * `dynamic` (ustawienie domyślne): wszystkie obiekty w pliku są ujawniane jako [jednostki](../../concepts/entities.md) w interfejsie API i mogą być niezależne. Hierarchia węzłów w czasie wykonywania jest taka sama jak struktura w pliku źródłowym.
  * `static`: Wszystkie obiekty są uwidocznione w interfejsie API, ale nie mogą być niezależne.
  * `none`: Wykres sceny jest zwinięty do jednego obiektu.

Każdy tryb ma inną wydajność środowiska uruchomieniowego. W `dynamic` trybie, koszt wydajności jest skalowany liniowo wraz z liczbą [jednostek](../../concepts/entities.md) na grafie, nawet jeśli żadna część nie zostanie przeniesiona. `dynamic`Trybu Używaj tylko wtedy, gdy konieczne jest przeniesienie części indywidualnie, na przykład dla animacji "widok wybuchu".

`static`Tryb eksportuje wykres pełnej sceny, ale części wewnątrz tego wykresu mają stałą transformację względem jej głównej części. Węzeł główny obiektu, jednak nadal można przenieść, obrócić lub skalować bez znaczącego kosztu wydajności. Ponadto [zapytania przestrzenne](../../overview/features/spatial-queries.md) zwracają poszczególne części, a każda część może być modyfikowana przy użyciu [przesłonięć stanu](../../overview/features/override-hierarchical-state.md). W tym trybie obciążenie środowiska uruchomieniowego na obiekt jest nieznaczne. Jest to idealne rozwiązanie w przypadku dużych scen, w których nadal potrzebna jest inspekcja poszczególnych obiektów, ale nie ma żadnych zmian przekształceń na obiekt.

`none`Tryb ma najniższe obciążenie środowiska uruchomieniowego, a także nieco lepsze czasy ładowania. Inspekcja lub przekształcanie pojedynczych obiektów nie jest możliwe w tym trybie. Przypadki użycia to na przykład modele photogrammetry, które nie mają znaczącego wykresu sceny w pierwszym miejscu.

> [!TIP]
> Wiele aplikacji będzie ładować wiele modeli. Należy zoptymalizować parametry konwersji dla każdego modelu w zależności od sposobu jego użycia. Jeśli na przykład chcesz wyświetlić model samochodu, aby użytkownik mógł się z nim oddzielić i szczegółowo sprawdzić, należy go przekonwertować z `dynamic` trybem. Jeśli jednak chcesz umieścić samochód w środowisku pokazywania pokoju, ten model można przekonwertować na `sceneGraphMode` wartość `static` lub nawet `none` .

### <a name="physics-parameters"></a>Parametry fizyki

* `generateCollisionMesh` — Jeśli potrzebujesz obsługi [zapytań przestrzennych](../../overview/features/spatial-queries.md) w modelu, ta opcja musi być włączona. W najgorszym przypadku tworzenie siatki kolizji może być podwojone czasu konwersji. Modele ze oczkami kolizji zajmują więcej czasu, a w przypadku korzystania z `dynamic` wykresu sceny są również większe obciążenie wydajności środowiska uruchomieniowego. W celu uzyskania ogólnej optymalnej wydajności należy wyłączyć tę opcję dla wszystkich modeli, w których nie są potrzebne zapytania przestrzenne.

### <a name="unlit-materials"></a>Materiały bez oświetlenia

* `unlitMaterials` -Domyślnie konwersja będzie preferować tworzenie [materiałów PBR](../../overview/features/pbr-materials.md). Ta opcja nakazuje konwerterowi traktowanie wszystkich materiałów jako [materiałów koloru](../../overview/features/color-materials.md) . Jeśli masz dane, które już zawierają oświetlenie, takie jak modele utworzone za pomocą photogrammetry, ta opcja pozwala szybko wymusić poprawną konwersję dla wszystkich materiałów, bez konieczności [przesłonięcia poszczególnych materiałów](override-materials.md) osobno.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konwertowanie ze starszych formatów FBX z modelem materiałowym podstawowego Phong

* `fbxAssumeMetallic` -Starsze wersje formatu FBX definiują ich materiały przy użyciu modelu materiału podstawowego Phong. Proces konwersji musi wnioskować, jak te materiały są mapowane na [model PBR](../../overview/features/pbr-materials.md)modułu renderowania. Zwykle jest to dobre rozwiązanie, ale niejednoznaczność może powstać, gdy materiał nie ma żadnych tekstur, wysokich wartości odblasków i nieszarego koloru albedo. W takim przypadku konwersja musi wybrać między priorytetyzacją odblasków wartości, Definiowanie wysoce odbijających materiałów metalicznych, gdy kolor albedo jest rozwiązany, lub określić priorytety koloru albedo, definiując takie jak Shiny kolorowe tworzywo sztuczne. Domyślnie proces konwersji zakłada, że wysoce odblasków wartości implikują materiał metaliczny w przypadkach, gdy ma zastosowanie niejednoznaczność. Ten parametr można ustawić, aby `false` przełączyć się na odwrotność.

### <a name="coordinate-system-overriding"></a>Przesłanianie systemu współrzędnych

* `axis` -Aby przesłonić jednostkę układu współrzędnych. Wartości domyślne to `["+x", "+y", "+z"]` . Teoretycznie format FBX ma nagłówek, w którym są zdefiniowane te wektory, a konwersja używa tych informacji do przekształcenia sceny. Format glTF definiuje również stały układ współrzędnych. W ramach tej działalności niektóre elementy zawartości mają nieprawidłowe informacje w nagłówku lub zostały zapisane z inną Konwencją systemu współrzędnych. Ta opcja umożliwia przesłonięcie układu współrzędnych w celu zrekompensowania. Na przykład: `"axis" : ["+x", "+z", "-y"]` program zamieni osi z i oś y i utrzymuje skrętności układu współrzędnych, odwracając kierunek osi y.

### <a name="node-meta-data"></a>Metadane węzła

* `metadataKeys` — Umożliwia określenie kluczy właściwości metadanych węzła, które mają być zachowane w wyniku konwersji. Można określić dokładne klucze lub klucze wieloznaczne. Klucze wieloznaczne mają format "ABC *" i pasują do każdego klucza rozpoczynającego się od "ABC". Obsługiwane typy wartości metadanych to `bool` , `int` , `float` , i `string` .

    Dla plików GLTF te dane pochodzą z [obiektu Extras w węzłach](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Dla plików FBX te dane pochodzą z `Properties70` danych `Model nodes` . Aby uzyskać więcej informacji, zapoznaj się z dokumentacją narzędzia zasobów 3W.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: Formatowanie

Można dostosować :::no-loc text="vertex"::: format siatki do dokładności handlowej w przypadku oszczędności pamięci. Zmniejszenie ilości pamięci umożliwia załadowanie większych modeli lub osiągnięcie lepszej wydajności. Jednak w zależności od danych niewłaściwy format może znacząco wpływać na jakość renderowania.

> [!CAUTION]
> Zmiana :::no-loc text="vertex"::: formatu powinna być ostatnim etapem, gdy modele nie mieszczą się w pamięci lub w przypadku optymalizacji pod kątem najlepszej możliwej wydajności. Zmiany mogą łatwo wprowadzać artefakty renderowania, zarówno oczywiste, jak i delikatne. Nie należy zmieniać ustawień domyślnych, chyba że wiesz, co należy wyszukać.

Możliwe są następujące korekty:

* Określone strumienie danych można jawnie dołączać lub wykluczać.
* Dokładność strumieni danych można zmniejszyć, aby zmniejszyć ilość pamięci.

Następująca `vertex` sekcja w `.json` pliku jest opcjonalna. Dla każdej części, która nie została określona jawnie, usługa konwersji powraca do ustawienia domyślnego.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Wymuszając składnik do `NONE` , jest gwarantowane, że siatka wyjściowa nie ma odpowiedniego strumienia.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Formaty składników na :::no-loc text="vertex"::: strumień

Formaty te są dozwolone dla odpowiednich składników:

| :::no-loc text="Vertex"::: cm6long | Obsługiwane formaty (pogrubienie = domyślne) |
|:-----------------|:------------------|
|pozycja| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, brak |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **Brak**|
|typow| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, brak |
|liczby| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, brak |
|normalny| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, brak |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, brak |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, brak |

#### <a name="supported-component-formats"></a>Obsługiwane formaty składników

Są to następujące pamięci:

| Format | Opis | Bajtów na :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|Pełna precyzja zmiennoprzecinkowa dwóch składników|8
|16_16_FLOAT|dwuskładnikowa precyzja zmiennoprzecinkowa|4
|32_32_32_FLOAT|precyzja pełnej liczby zmiennoprzecinkowej z trzema składnikami|12
|16_16_16_16_FLOAT|Liczba pół zmiennoprzecinkowa z czterema składnikami|8
|8_8_8_8_UNSIGNED_NORMALIZED|bajty z czterema składnikami, znormalizowane do `[0; 1]` zakresu|4
|8_8_8_8_SIGNED_NORMALIZED|bajty z czterema składnikami, znormalizowane do `[-1; 1]` zakresu|4

#### <a name="best-practices-for-component-format-changes"></a>Najlepsze rozwiązania dotyczące zmian w formacie składnika

* `position`: Bardzo rzadko jest wystarczająca dokładność. **16_16_16_16_FLOAT** wprowadza zauważalne artefakty podziału nawet dla małych modeli.
* `normal`, `tangent` , `binormal` : Zwykle te wartości są zmieniane razem. O ile nie istnieją zauważalne artefakty oświetlenia wynikające z normalnego podziałuu, nie ma powodów, aby zwiększyć ich dokładność. W niektórych przypadkach te składniki mogą być ustawione na **Brak**:
  * `normal`, `tangent` , i `binormal` są odpowiednie tylko wtedy, gdy powinien być oświetlony co najmniej jeden materiał w modelu. W odniesieniu do ARR jest to przypadek, gdy [materiał PBR](../../overview/features/pbr-materials.md) jest używany w modelu w dowolnym momencie.
  * `tangent` i `binormal` są potrzebne tylko wtedy, gdy żadne z materiałów oświetlonych używa normalnej tekstury mapy.
* `texcoord0`, `texcoord1` : Współrzędne tekstury mogą korzystać z zmniejszonej dokładności (**16_16_FLOAT**), gdy ich wartości pozostają w `[0; 1]` zakresie, a rozmiary tekstur mają maksymalny rozmiar 2048 x 2048 pikseli. W przypadku przekroczenia tych limitów jakość mapowania tekstury będzie mieć wpływ.

#### <a name="example"></a>Przykład

Załóżmy, że masz model photogrammetry, który ma oświetlenie rozszerzania do tekstury. Wszystko, co jest konieczne do renderowania modelu, to :::no-loc text="vertex"::: położenia i Współrzędne tekstury.

Domyślnie konwerter musi założyć, że w pewnym momencie możesz chcieć używać materiałów PBR w modelu, aby generować `normal` `tangent` dane, i `binormal` . W związku z tym użycie pamięci dla wierzchołków wynosi `position` (12 bajtów) + `texcoord0` (8 bajtów) + `normal` (4 bajty) + `tangent` (4 bajty) + `binormal` (4 bajty) = 32 bajtów. Większe modele tego typu mogą łatwo mieć wiele milionów :::no-loc text="vertices"::: wyników wynikających z modeli, które mogą przyjmować wiele gigabajtów pamięci. Takie duże ilości danych wpłynie na wydajność i nawet zabrakło pamięci.

Wiedząc, że nie potrzebujesz dynamicznego oświetlenia na modelu i wiedzą, że wszystkie Współrzędne tekstury znajdują się w `[0; 1]` zakresie, można ustawić `normal` , `tangent` , i `binormal` do `NONE` i `texcoord0` do połowy precyzji (), co spowoduje, że `16_16_FLOAT` tylko 16 bajtów na :::no-loc text="vertex"::: . Wycinanie danych siatki na pół umożliwia załadowanie większych modeli i zwiększenie wydajności.

## <a name="memory-optimizations"></a>Optymalizacje pamięci

Użycie pamięci przez załadowanej zawartości może stać się wąskim gardłem w systemie renderowania. Jeśli ładunek pamięci stał się zbyt duży, może dojść do naruszenia wydajności renderowania lub spowodować, że model nie zostanie załadowany całkowicie. W tym artykule omówiono niektóre ważne strategie zmniejszania rozmiaru pamięci.

### <a name="instancing"></a>Tworzenie wystąpienia

Tworzenie wystąpień jest koncepcją, w której siatki są ponownie używane dla części z oddzielnymi przekształceniami przestrzennymi, a nie z każdą częścią, która odwołuje się do własnej unikatowej geometrii. Tworzenie wystąpienia ma znaczny wpływ na rozmiary pamięci.
Przykładowe przypadki użycia związane z tworzeniem wystąpień są wkrętami w modelu silnika lub krzesłami w modelu architektury.

> [!NOTE]
> Tworzenie wystąpienia może znacząco poprawić użycie pamięci (i w ten sposób załadować czas), jednak ulepszenia po stronie wydajności renderowania są nieznaczące.

Usługa konwersji uwzględnia tworzenie wystąpień, jeśli części są oznaczone odpowiednio w pliku źródłowym. Jednak konwersja nie wykonuje dodatkowej dokładnej analizy danych siatki w celu zidentyfikowania części wielokrotnego użytku. W ten sposób narzędzie tworzenia zawartości i jego potoku eksportu są decydującymi kryteriami dla właściwej konfiguracji tworzenia wystąpień.

Prosty sposób na przetestowanie, czy informacje o utworzeniu wystąpienia są zachowywane podczas konwersji, mają na celu wyszukanie [statystyk wyjściowych](get-information.md#example-info-file), w tym `numMeshPartsInstanced` elementów członkowskich. Jeśli wartość dla `numMeshPartsInstanced` jest większa od zera, wskazuje, że siatki są współdzielone między wystąpieniami.

#### <a name="example-instancing-setup-in-3ds-max"></a>Przykład: Konfiguracja wystąpienia w programie 3ds Max

W programie [Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) istnieją różne tryby klonowania obiektów o nazwie, **`Copy`** **`Instance`** i **`Reference`** które zachowywać się inaczej w odniesieniu do wystąpienia w wyeksportowanym `.fbx` pliku.

![Klonowanie w tabeli 3ds Max](./media/3dsmax-clone-object.png)

* **`Copy`** : W tym trybie siatka jest klonowana, dlatego nie są używane żadne wystąpienia ( `numMeshPartsInstanced` = 0).
* **`Instance`** : Te dwa obiekty mają tę samą siatkę, więc jest używane Tworzenie wystąpień ( `numMeshPartsInstanced` = 1).
* **`Reference`** : Modyfikatory DISTINCT można zastosować do geometrie, aby eksporter wybierał podejście i nie używa wystąpień ( `numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Tryb kompozycji oparty na głębokości

Jeśli pamięć jest istotna, skonfiguruj moduł renderowania z [trybem kompozycji opartym na głębokości](../../concepts/rendering-modes.md#depthbasedcomposition-mode). W tym trybie ładunek procesora GPU jest dystrybuowany między wieloma procesorami GPU.

### <a name="decrease-vertex-size"></a>Zmniejsz rozmiar wierzchołka

Zgodnie z opisem w sekcji [najlepsze rozwiązania dotyczące zmian w formacie składnika](configure-model-conversion.md#best-practices-for-component-format-changes) dostosowanie formatu wierzchołka może zmniejszyć rozmiar pamięci. Jednak ta opcja powinna być ostatnią możliwością.

### <a name="texture-sizes"></a>Rozmiary tekstury

W zależności od typu scenariusza ilość danych tekstury może być większa niż ilość pamięci używanej przez dane siatki. Modele photogrammetry są kandydatami.
Konfiguracja konwersji nie umożliwia automatycznego skalowania tekstur w dół. W razie potrzeby skalowanie tekstury musi odbywać się jako krok wstępnego przetwarzania po stronie klienta. Krok konwersji pozwala jednak wybrać odpowiedni [Format kompresji tekstury](https://docs.microsoft.com/windows/win32/direct3d11/texture-block-compression-in-direct3d-11):

* `BC1` dla nieprzezroczystych tekstur kolorów
* `BC7` dla tekstur kolorów źródłowych z kanałem alfa

Ponieważ format `BC7` ma dwukrotnie wpływ na pamięć w porównaniu z `BC1` , ważne jest, aby upewnić się, że tekstury wejściowe nie zapewniają niepotrzebnych kanałów alfa.

## <a name="typical-use-cases"></a>Typowe przypadki użycia

Wyszukiwanie dobrych ustawień importu dla danego przypadku użycia może być procesem żmudnym. Z drugiej strony Ustawienia konwersji mogą mieć znaczny wpływ na wydajność środowiska uruchomieniowego.

Istnieją pewne klasy przypadków użycia, które kwalifikują się do określonych optymalizacji. Poniżej przedstawiono kilka przykładów.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Przypadek użycia: wizualizacja architektoniczna/duże mapy na zewnątrz

* Te typy scen mają być statyczne, co oznacza, że nie potrzebują ruchomych części. Odpowiednio `sceneGraphMode` można ustawić wartość `static` lub nawet `none` , co zwiększa wydajność środowiska uruchomieniowego. W `static` trybie z trybem węzeł główny sceny może być nadal przenoszony, obracany i skalowany, na przykład w celu dynamicznego przełączenia między skalą 1:1 (w przypadku pierwszej osoby) i widokiem najwyższego poziomu tabeli.

* Gdy konieczne jest przeniesienie części wokół, zazwyczaj oznacza to, że konieczna jest obsługa raycasts lub innych [zapytań przestrzennych](../../overview/features/spatial-queries.md), dzięki czemu można wybierać te części w pierwszym miejscu. Z drugiej strony, jeśli nie zamierzasz poruszać się wokół siebie, szanse są wysokie, ale nie potrzebujesz jej do uczestniczenia w zapytania przestrzenne, dlatego można wyłączyć `generateCollisionMesh` flagę. Ten przełącznik ma znaczny wpływ na czasy konwersji, czasy ładowania, a także koszty aktualizacji poszczególnych klatek.

* Jeśli aplikacja nie używa [wyciętych płaszczyzn](../../overview/features/cut-planes.md), `opaqueMaterialDefaultSidedness` flaga powinna być wyłączona. Wzrost wydajności jest zwykle 20%-30%. Nadal mogą być używane wycięte płaszczyzny, ale nie będzie można używać ich ponownie podczas wyszukiwania w wewnętrznych częściach obiektów, które wyglądają na intuicyjne. Aby uzyskać więcej informacji, zobacz [ :::no-loc text="single sided"::: renderowanie](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Przypadek użycia: modele photogrammetry

Podczas renderowania modeli photogrammetry zazwyczaj nie ma potrzeby dla wykresu sceny, więc można ustawić `sceneGraphMode` na `none` . Ponieważ te modele rzadko zawierają złożone wykresy sceny, które zaczynają się od, wpływ tej opcji powinien być nieistotny, chociaż.

Ponieważ oświetlenie jest już rozszerzania do tekstury, nie jest wymagana żadna dynamiczna oświetlenie. Zatem:

* Ustaw `unlitMaterials` flagę na, aby `true` przekształcić wszystkie materiały w bez oświetlenia [kolory](../../overview/features/color-materials.md).
* Usuń niepotrzebne dane z formatu wierzchołka. Zapoznaj się z powyższym [przykładem](#example) .

### <a name="use-case-visualization-of-compact-machines-etc"></a>Przypadek użycia: wizualizacja maszyn kompaktowych itp.

W tych przypadkach, modele często mają bardzo duże szczegóły w niewielkim woluminie. Renderowanie jest silnie zoptymalizowane pod kątem obsługi takich przypadków. Jednak większość optymalizacji wymienionych w poprzednim przypadku użycia nie ma zastosowania w tym miejscu:

* Poszczególne części powinny być wybierane i ruchome, więc `sceneGraphMode` musi pozostać do `dynamic` .
* Rzutowania promieniowego są zwykle integralną częścią aplikacji, dlatego siatki kolizji muszą być generowane.
* Wytnij płaszczyzny wyglądają lepiej z `opaqueMaterialDefaultSidedness` włączoną flagą.

## <a name="deprecated-features"></a>Przestarzałe funkcje

Udostępnianie ustawień przy użyciu nazwy pliku niezwiązanego z modelem `conversionSettings.json` jest nadal obsługiwane, ale przestarzałe.
Zamiast tego użyj nazwy pliku specyficznego dla modelu `<modelName>.ConversionSettings.json` .

Użycie `material-override` Ustawienia do identyfikacji [pliku przesłonięcia materiału](override-materials.md) w pliku ustawień konwersji jest nadal obsługiwane, ale przestarzałe. Zamiast tego użyj nazwy pliku specyficznego dla modelu `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](model-conversion.md)
* [Materiały kolorów](../../overview/features/color-materials.md)
* [Materiały PBR](../../overview/features/pbr-materials.md)
* [Zastępowanie materiałów podczas konwersji modelu](override-materials.md)

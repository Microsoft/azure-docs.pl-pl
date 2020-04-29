---
title: Materiały PBR
description: Opisuje typ materiału PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680260"
---
# <a name="pbr-materials"></a>Materiały PBR

*Materiały PBR* to jeden z obsługiwanych [typów materiałów](../../concepts/materials.md) w ramach renderowania zdalnego na platformie Azure. Są one używane w przypadku [siatek](../../concepts/meshes.md) , które powinny otrzymać realistyczne oświetlenie.

Sekcja PBR to **P**hysically **B**ased **R**endering i oznacza, że materiał opisuje wizualizacje właściwości powierzchni w sposób fizyczny, tak że realistyczne wyniki są możliwe w przypadku wszystkich warunków oświetlenia. Większość nowoczesnych aparatów gier i narzędzi do tworzenia zawartości obsługuje te materiały, ponieważ są one uznawane za najlepsze przybliżenie rzeczywistych scenariuszy dla renderowania w czasie rzeczywistym.

![Przykładowy model Kaska glTF renderowany przez ARR](media/helmet.png)

Materiały PBR nie są rozwiązaniem uniwersalnym, chociaż. Istnieją materiały, które odzwierciedlają kolor inaczej, w zależności od kąta wyświetlania. Na przykład niektóre sieci szkieletowe lub farby samochodu. Tego rodzaju materiały nie są obsługiwane przez standardowy model PBR i nie są obecnie obsługiwane przez zdalne renderowanie na platformie Azure. Obejmuje to rozszerzenia PBR, takie jak *cienkie klisze* (powierzchnie wielowarstwowe) i *jasne* (dla farb samochodowych).

## <a name="common-material-properties"></a>Właściwości wspólnego materiału

Te właściwości są wspólne dla wszystkich materiałów:

* **albedoColor:** Ten kolor jest mnożony przez inne kolory, takie jak *albedoMap* lub *kolor wierzchołka*. Jeśli *przezroczystość* jest włączona w materiale, kanał alfa jest używany do dostosowywania nieprzezroczystości, `1` co oznacza całkowite nieprzezroczystość i `0` znaczenie w pełni przezroczyste. Wartość domyślna to biały.

  > [!NOTE]
  > Gdy materiał PBR jest całkowicie przezroczysty, podobnie jak idealnie czysty fragment, nadal odzwierciedla środowisko. Jasne plamy, takie jak słońce, są nadal widoczne w odbiciu. Różni się to w przypadku [materiałów kolorowych](color-materials.md).

* **albedoMap:** [Tekstura 2D](../../concepts/textures.md) dla wartości albedo dla pikseli.

* **alphaClipEnabled** i **AlphaClipThreshold:** Jeśli *alphaClipEnabled* ma wartość true, wszystkie piksele, w których wartość alfa albedo jest niższa niż *alphaClipThreshold* , nie będą rysowane. Przycinanie alfa może być używane nawet bez włączania przezroczystości i jest znacznie szybsze do renderowania. Materiały przycinane alfa są ciągle wolniejsze, aby były renderowane od całkowicie nieprzezroczystych materiałów. Domyślnie przycinanie alfa jest wyłączone.

* **textureCoordinateScale** i **textureCoordinateOffset:** Skala jest mnożona do współrzędnych tekstury UV, przesunięcie jest dodawane do niego. Może służyć do rozciągnięcia i przesunięcia tekstur. Domyślna Skala to (1, 1), a przesunięcie to (0, 0).

* **useVertexColor:** Jeśli siatka zawiera kolory wierzchołków, a ta opcja jest włączona, kolory wierzchołka siatki są mnożone do *albedoColor* i *albedoMap*. Domyślnie kolory wierzchołków są wyłączone.

* **isDoubleSided:** Jeśli jest ustawiona wartość true, Trójkąty z tym materiałem są renderowane, nawet jeśli lampa jest oglądana na ich powierzchni. W przypadku oświetlenia materiałów PBR również jest obliczana prawidłowo dla twarzy z tyłu. Domyślnie ta opcja jest wyłączona. Zobacz również [renderowanie jednostronne](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Właściwości materiału PBR

Podstawowy pomysł renderowania opartego na fizycznie polega na użyciu właściwości *BaseColor*, *metalu*i *chropowatości* do emulowania szerokiego zakresu materiałów rzeczywistych. Szczegółowy opis programu PBR wykracza poza zakres tego artykułu. Aby uzyskać więcej informacji na temat usługi PBR, zobacz [inne źródła](http://www.pbr-book.org). Następujące właściwości są specyficzne dla materiałów PBR:

* **baseColor:** W materiałach PBR *kolor albedo* jest określany jako *kolor podstawowy*. W przypadku renderowania zdalnego na platformie Azure Właściwość *Color albedo* jest już obecna za pomocą wspólnych właściwości materiału, więc nie istnieje dodatkowa Właściwość koloru podstawowego.

* **niesztywność** i **roughnessMap:** sztywność definiuje, jak sztywne lub gładkie powierzchni. Nieprzemieszczone powierzchnie rozpraszają światło w większej liczbie kierunków niż gładkie powierzchnie, co sprawia, że odbicia są rozmyte, a nie ostre. Zakres wartości jest z `0.0` do. `1.0` Gdy `roughness` jest `0.0`równe, odbicia będzie ostro. Gdy `roughness` jest `0.5`równe, odbicia staną się zamazane.

  W przypadku podania zarówno wartości nieilościowej, jak i mapy o niesztywnej wartości końcowej będzie iloczyn dwóch.

* **metalu** i **metalnessMap:** w fizyka, ta właściwość odnosi się do tego, czy powierzchnia jest przeprowadzona czy nieruchoma. Materiały przewodzące mają różne właściwości odbijające i mają być odzwierciedlane bez koloru albedo. W materiałach PBR ta właściwość ma wpływ na to, ile powierzchni odzwierciedla otoczenie otoczenia. Zakres wartości od `0.0` do `1.0`. Gdy metalu jest `0.0`, kolor albedo jest w pełni widoczny i materiał wygląda jak plastikowe lub ceramiczne. Gdy metale jest `0.5`, wygląda na to, że jest to kolor malowany. Gdy metalowy jest `1.0`, powierzchnia niemal całkowicie traci kolor albedo i odzwierciedla tylko otoczenie. `metalness` Na przykład jeśli `1.0` jest i `roughness` jest `0.0` to powierzchnia wygląda podobnie jak w świecie rzeczywistym.

  Jeśli zostanie podana zarówno wartość metalu, jak i mapa metalu, końcowa wartość będzie iloczynem dwóch.

  ![metalowy i niesztywność](./media/metalness-roughness.png)

  Na powyższym rysunku sfera w prawym dolnym rogu wygląda podobnie do rzeczywistego materiału metalowego, po lewej stronie wygląda jak ceramiczna lub plastikowa. Kolor albedo jest zmieniany również w zależności od właściwości fizycznych. Dzięki zwiększeniu sztywności materiał traci ostrość odbicia.

* **normalMap:** Aby symulować szczegółowe informacje, można podać [mapę normalną](https://en.wikipedia.org/wiki/Normal_mapping) .

* **occlusionMap** i **aoScale:** otoczenie [zamknięcia](https://en.wikipedia.org/wiki/Ambient_occlusion) sprawia, że obiekty z crevices wyglądają bardziej realistycznie przez dodanie cieni do obszarów zamknięte. Zamknięcia zakres wartości z `0.0` do `1.0`, gdzie `0.0` oznacza zaciemnienie (zamknięte) `1.0` i oznacza brak Occlusions. Jeśli tekstura 2D jest określona jako mapa zamknięcia, efekt jest włączony i *aoScale* działa jako mnożnik.

  ![Mapa zamknięcia](./media/boom-box-ao2.gif)

* **przezroczysty:** W przypadku materiałów PBR istnieje tylko jedno ustawienie przejrzystości: jest ono włączone lub nie. Nieprzezroczystość jest definiowana przez kanał alfa koloru albedo. Gdy ta funkcja jest włączona, zostanie wywołany bardziej skomplikowany potok renderowania, aby rysować powierzchnie częściowo przezroczyste. Zdalne renderowanie na platformie Azure implementuje prawdziwie [niezależną od kolejności przejrzystości](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT).

  Renderowanie przezroczystej geometrii jest kosztowne. Jeśli potrzebujesz tylko otworów na powierzchni, na przykład dla liści drzewa, lepiej jest użyć przycinania alfa.

  ![Na](./media/transparency.png) powyższym obrazie powinna zostać wyświetlona informacja o przezroczystości, w jaki sposób najbardziej przejrzysty jest zakres z prawej strony, ale odbicie jest nadal widoczne.

  > [!IMPORTANT]
  > Jeśli dowolny materiał ma być przełączany z nieprzezroczystego na przezroczysty w czasie wykonywania, moduł renderowania musi używać [trybu renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* . To ograniczenie nie ma zastosowania do materiałów, które są konwertowane jako przezroczyste materiały, aby zaczynać się od.

## <a name="technical-details"></a>Szczegóły techniczne

Zdalne renderowanie na platformie Azure używa mikroaspektu Cooka-Torrance BRDF z systemem GGX NDF, Schlick wygaszania Fresnela i GGXego okresu widoczności z lambertaą Ten model jest w tej chwili niefaktycznym standardem branżowym. Więcej szczegółowych informacji można znaleźć w tym artykule: [Torrance renderowanie oparte na fizycznie](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Alternatywą dla modelu *"Niesztywności"* używanym podczas renderowania zdalnego na platformie Azure jest model *odblasków-Glossiness* PBR. Ten model może reprezentować szerszy zakres materiałów. Jest to jednak droższe i zwykle nie działa prawidłowo w przypadku przypadków w czasie rzeczywistym.
Nie zawsze jest możliwe przekonwertowanie z *odblasków-Glossiness* na *sztywność* , ponieważ istnieją pary wartości *(rozpraszające, odblasków)* , których nie można przekonwertować na *(BaseColor, metalu)*. Konwersja w innym kierunku jest prostsza i bardziej precyzyjna, ponieważ wszystkie pary *(BaseColor, metale)* odpowiadają dokładnie zdefiniowanym par *(rozpraszaniem, odblasków)* .

## <a name="next-steps"></a>Następne kroki

* [Materiały kolorów](color-materials.md)
* [Tekstury](../../concepts/textures.md)
* [Siatki](../../concepts/meshes.md)

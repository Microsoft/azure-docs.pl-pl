---
title: TexConv — narzędzie konwersji tekstury
description: Podręcznik użytkownika dla narzędzia wiersza polecenia TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680026"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv — narzędzie konwersji tekstury

TexConv to narzędzie wiersza polecenia do przetwarzania tekstur z typowych formatów danych wejściowych, takich jak PNG, TGA, JPEG i DDS, do zoptymalizowanych formatów dla użycia środowiska uruchomieniowego.
Typowym scenariuszem jest konwertowanie pojedynczego pliku wejściowego `A.xxx` do formatu zoptymalizowanego `B.yyy` , ale narzędzie ma wiele dodatkowych opcji do użycia zaawansowanego.

## <a name="command-line-help"></a>Pomoc wiersza polecenia

Uruchomienie TexConv.exe z `--help` parametrem wyświetli wszystkie dostępne opcje. Ponadto TexConv drukuje używane opcje podczas wykonywania, aby pomóc zrozumieć, co robi. Szczegółowe informacje można znaleźć w tych danych wyjściowych.

## <a name="general-usage"></a>Ogólne użycie

TexConv zawsze generuje **dokładnie jeden plik wyjściowy** . Do uzyskania danych wyjściowych z programu może być używana **wiele plików wejściowych** . Dla zestawu wymaga również **mapowania kanału**, które informuje o tym, który kanał (*czerwony, zielony, niebieski* lub *alfa*) ma być pobierany i przenoszony do tego kanału obrazu wyjściowego.

Najbardziej prosty wiersz polecenia jest następujący:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` Określa plik i format danych wyjściowych
- `-in0` Określa pierwszy obraz wejściowy
- `-rgba` informuje o tym, że obraz wyjściowy powinien używać wszystkich czterech kanałów i że powinny być pobierane 1:1 z obrazu wejściowego

## <a name="multiple-input-files"></a>Wiele plików wejściowych

Aby utworzyć dane wyjściowe z wielu plików wejściowych, określ każdy plik wejściowy przy użyciu `-in` opcji o rosnącej liczbie:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Podczas składania mapy sześciennej z tekstur 2D, jeden może również używać `-right` ,,, `-left` , `-top` `-bottom` `-front` , `-back` lub,, `-px` `-nx` ,, `-py` `-ny` `-pz` , `-nz` .

Aby zmapować te dane wejściowe do pliku wyjściowego, konieczne jest odpowiednie mapowanie kanału.

## <a name="channel-mappings"></a>Mapowania kanałów

Opcje mapowania kanału określają, od których dane wejściowe mają być wypełniane przez dane kanały wyjściowe. Dane wejściowe dla każdego kanału można określić osobno:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

W tym miejscu kanały RGB wyjściowe byłyby wypełniane przy użyciu pierwszego obrazu wejściowego, ale kolor czerwony i niebieski zostaną zamienione. Kanał alfa danych wyjściowych będzie wypełniony wartościami z czerwonego kanału drugiego obrazu wejściowego.

Każde mapowanie każdego kanału zapewnia największą elastyczność. Dla wygody można napisać to samo przy użyciu operatorów "przemieniane":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Kanały wyjściowe

Dostępne są następujące opcje mapowania kanału:

- `-r`, `-g` , `-b` , `-a` : Określają przypisania pojedynczego kanału
- `-rg` : Określ przydziały czerwieni i zielonego kanału.
- `-rgb` : Określ przydziały czerwony, zielony i niebieski kanał.
- `-rgba` : Określa wszystkie cztery przypisania kanałów.

Wspominając tylko kanał R, RG lub RGB, instruuje TexConv, aby utworzył plik wyjściowy tylko odpowiednio 1, 2 lub 3 kanały.

### <a name="input-swizzling"></a>Przemieniane wejściowy

Po określeniu, która tekstura danych wejściowych powinna wypełnić kanał wyjściowy, jeden może Swizzle dane wejściowe:

- `-rgba in0` jest równoważne `-rgba in0.rgba`
- `-rgba in0.bgra` będzie Swizzle kanały wejściowe
- `-rgb in0.rrr` duplikuje kanał czerwony do wszystkich kanałów

Jeden może również wypełnić kanały kolorem czarnym lub białym:

- `-rgb in0 -a white` utworzy 4-teksturę wyjściową kanału, ale ustawi alfa na całkowicie nieprzezroczysty
- `-rg black -b white` utworzy całkowicie niebieską teksturę

## <a name="common-options"></a>Typowe opcje

Poniżej przedstawiono najbardziej interesujące opcje. Więcej opcji znajduje się na liście `TexConv --help` .

### <a name="output-type"></a>Typ danych wyjściowych

- `-type 2D` : Dane wyjściowe będą zwykłym obrazem 2D.
- `-type Cubemap` : Dane wyjściowe będą obrazem mapy sześciennej. Obsługiwane tylko dla plików wyjściowych DDS. Gdy ta wartość jest określona, jeden może złożyć mapy sześciennej z 6 zwykłych obrazów wejściowych 2D.

### <a name="image-compression"></a>Kompresja obrazu

- `-compression none` : Obraz wyjściowy zostanie odkompresowany.
- `-compression medium` : Jeśli jest obsługiwana, obraz wyjściowy będzie używać kompresji bez poświęcania zbyt dużej jakości.
- `-compression high` : Jeśli jest obsługiwana, obraz danych wyjściowych będzie używać kompresji i poświęcać jakość na rzecz mniejszego pliku.

### <a name="mipmaps"></a>Mipmapy

Domyślnie TexConv generuje mipmapy, gdy obsługuje go format danych wyjściowych.

- `-mipmaps none` : Mipmapy nie zostanie wygenerowany.
- `-mipmaps Linear` : Jeśli jest obsługiwana, mipmapy zostanie wygenerowane przy użyciu filtra Box.

### <a name="usage-srgb--gamma-correction"></a>Użycie (Korekcja sRGB/gamma)

`-usage`Opcja określa przeznaczenie danych wyjściowych, a tym samym informuje TexConv o tym, czy zastosować korekcję gamma do plików wejściowych i wyjściowych. Użycie ma wpływ tylko na kanały RGB. Kanał alfa jest zawsze uznawany za zawierający wartości "liniowe". Jeśli użycie nie zostanie określone, tryb "automatycznie" podejmie próbę wykrycia użycia z formatu i nazwy pierwszego obrazu wejściowego. Na przykład formaty danych wyjściowych o pojedynczym i podwójnym kanale są zawsze liniowe. Sprawdź dane wyjściowe, aby zobaczyć, jakie decyzje TexConv.

- `-usage Linear` : Obraz wyjściowy zawiera wartości, które nie reprezentują kolorów. Jest to zazwyczaj przypadek dla tekstur metalicznych i niesztywnych, a także wszystkich rodzajów masek.

- `-usage Color` : Obraz wyjściowy reprezentuje kolor, taki jak mapy rozproszone/albedo. Flaga sRGB zostanie ustawiona w nagłówku danych wyjściowych.

- `-usage HDR` : Plik wyjściowy powinien używać więcej niż 8 bitów na piksel do kodowania. W związku z tym wszystkie wartości są przechowywane w przestrzeni liniowej. W przypadku tekstury HDR nie ma znaczenia, czy dane reprezentują kolor lub inne dane.

- `-usage NormalMap` : Obraz wyjściowy reprezentuje mapę normalną przestrzeni stycznej. Wartości zostaną znormalizowane, a obliczenia mipmappingu będą nieznacznie zoptymalizowane.

- `-usage NormalMap_Inverted` : Dane wyjściowe to w postaci zwykłej mapy normalnej ze znakiem Y, wskazując w odwrotnym kierunku niż dane wejściowe.

### <a name="image-rescaling"></a>Ponowne skalowanie obrazu

- `-minRes 64` : Określa minimalną rozdzielczość danych wyjściowych. Jeśli obraz wejściowy jest mniejszy, zostanie on przeskalowany.
- `-maxRes 1024` : Określa maksymalną rozdzielczość danych wyjściowych. Jeśli obraz wejściowy jest większy, zostanie pobrany downscaled.
- `-downscale 1` : Jeśli wartość jest większa niż 0, obrazy wejściowe będą wypadać w rozdzielczości N razy. Służy do zastosowania ogólnej redukcji jakości.

## <a name="examples"></a>Przykłady

### <a name="convert-a-color-texture"></a>Konwersja tekstury koloru

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Konwertowanie mapy normalnej

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Utwórz mapy sześciennej HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Doskonałe źródło dla CubeMaps HDR to [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Tworzenie wiele obrazów w jeden

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Wyodrębnienie pojedynczego kanału

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```

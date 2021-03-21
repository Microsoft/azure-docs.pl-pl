---
title: Zmień rozmiar i Przytnij miniatury obrazów — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Niektóre odpowiedzi z interfejsy API wyszukiwania Bing obejmują adresy URL dla miniaturowych obrazów obsługiwanych przez usługę Bing, których rozmiar i kadrowanie mogą zawierać parametry zapytania.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350487"
---
# <a name="resize-and-crop-thumbnail-images"></a>Zmień rozmiar i Przytnij miniatury obrazów

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Niektóre odpowiedzi z interfejsy API wyszukiwania Bing obejmują adresy URL dla miniaturowych obrazów obsługiwanych przez usługę Bing, których rozmiar i kadrowanie mogą zawierać parametry zapytania. Na przykład:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Jeśli zostanie wyświetlony podzbiór tych miniatur, podaj opcję wyświetlania pozostałych obrazów.

> [!NOTE]
> Należy upewnić się, że obrazy miniatur i zmiany rozmiarów będą zawierać scenariusz wyszukiwania, który szanuje prawa innych firm, zgodnie z wymaganiami Wyszukiwanie Bing interfejsu API [i wyświetla wymagania](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Zmień rozmiar miniatury 

Aby zmienić rozmiar miniatury, `w` w adresie URL miniatury należy określić tylko jedną z parametrów zapytania (szerokość) lub `h` (wysokość). Określenie tylko wysokości lub szerokości umożliwia usłudze Bing zachowanie oryginalnego aspektu obrazu. Określ szerokość i wysokość w pikselach. 

Na przykład jeśli oryginalna miniatura to 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Aby zmniejszyć jego rozmiar, należy ustawić dla `w` parametru nową wartość (na przykład `336` ), a następnie usunąć `h`  parametr:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Jeśli określisz tylko wysokość lub szerokość miniatury, zostanie zachowany oryginalny współczynnik proporcji obrazu. Jeśli określisz oba parametry i współczynnik proporcji nie zostanie zachowany, Bing doda białe dopełnienie do obramowania obrazu.

Na przykład jeśli zmienisz rozmiar obrazu 480x359 na 200x200 bez przycinania, Pełna szerokość będzie zawierać obraz, ale wysokość będzie zawierać 25 pikseli białego wypełnienia u góry i u dołu obrazu. Jeśli obraz został 359x480, lewe i prawe obramowanie będzie zawierać białe uzupełnienie. W przypadku przycinania obrazu białe uzupełnienie nie zostanie dodane.  

Na poniższej ilustracji przedstawiono oryginalny rozmiar obrazu miniatury (480x300).  
  
![Oryginalny obraz krajobrazu](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Na poniższej ilustracji przedstawiono rozmiar obrazu zmieniony na 200x200. Współczynnik proporcji jest zachowywany, a obramowanie górne i dolne jest dopełniane kolorem białym (szare obramowanie jest dołączone, aby pokazać uzupełnienie).  
  
![Obraz o zmienionym rozmiarze](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Jeśli określisz wymiary, które są większe niż oryginalna szerokość i wysokość obrazu, Bing doda białe uzupełnienie do lewej i górnego obramowania.  

## <a name="request-different-thumbnail-sizes"></a>Żądaj różnych rozmiarów miniatur

Aby zażądać innego rozmiaru obrazu miniatury, Usuń wszystkie parametry zapytania z adresu URL miniatury, z `id` wyjątkiem `pid` parametrów i. Następnie należy dodać `&w` parametr zapytania (Width) lub `&h` (Height) o żądanym rozmiarze obrazu w pikselach, ale nie oba. W usłudze Bing zostanie zachowany oryginalny współczynnik proporcji obrazu. 

Aby zwiększyć szerokość obrazu określonego przez powyższy adres URL do 165 pikseli, użyj następującego adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Jeśli zażądasz obrazu, który jest większy niż oryginalny rozmiar obrazu, Bing dodaje białe uzupełnienie obrazu w razie konieczności. Na przykład jeśli oryginalny rozmiar obrazu to 474x316, a wartość `&w` 500, Bing zwróci obraz 500x333. Ten obraz będzie miał 8,5 pikseli białego wypełnienia wzdłuż górnej i dolnej krawędzi oraz 13 pikseli wypełnienia po lewej i prawej krawędzi.

Aby zapobiec dodawaniu bieli przez usługę Bing, jeśli żądany rozmiar jest większy niż oryginalny rozmiar obrazu, ustaw `&p` parametr zapytania na 0. Na przykład jeśli parametr zostanie uwzględniony `&p=0` w powyższym adresie URL, Bing zwróci obraz 474x316 zamiast obrazu 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Jeśli określisz `&w` Parametry i `&h` parametrów zapytania, Bing będzie zachować współczynnik proporcji obrazu i dodaje białe uzupełnienie, zgodnie z wymaganiami. Na przykład jeśli oryginalny rozmiar obrazu to 474x316 i ustawisz parametry width i Height na 200x200 ( `&w=200&h=200` ), Bing zwróci obraz zawierający 33 pikseli białego wypełnienia u góry i u dołu. Jeśli dołączysz `&p` parametr zapytania, Bing zwróci obraz 200x134.

## <a name="crop-a-thumbnail"></a>Kadrowanie miniatury 

Aby przyciąć obraz, należy uwzględnić `c` parametr zapytania (przycinanie). Można użyć następujących wartości:
  
- `4`&mdash;Wskaźnik niewidomy  
- `7`&mdash;Inteligentny współczynnik  

### <a name="smart-ratio-cropping"></a>Inteligentne przecinanie proporcji

Jeśli zażądasz przycinania inteligentnego współczynnika (poprzez ustawienie `c` parametru na `7` ), usługa Bing przywróci obraz z środkowej części swojego regionu na zewnątrz, zachowując współczynnik proporcji obrazu. Regionem zainteresowania jest obszar obrazu, który jest określany przez usługę Bing, zawierający większość części importu. Poniżej przedstawiono przykładowy region zainteresowania.  
  
![Region zainteresowania](./media/resize-crop/bing-resize-crop-regionofinterest.png)

W przypadku zmiany rozmiaru obrazu i zażądania przycinania inteligentnego wskaźnika usługa Bing zmniejsza obraz do żądanego rozmiaru przy zachowaniu współczynnika proporcji. Następnie usługa Bing przycina obraz na podstawie wymiarów o zmienionym rozmiarze. Na przykład, jeśli szerokość o zmienionym rozmiarze jest mniejsza lub równa wysokości, Bing obraz zostanie przycięty do lewej i prawej strony środka zainteresowania. W przeciwnym razie usługi Bing przydzielą ją do góry i u dołu środka zainteresowania.  
  
 
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu inteligentnego przycinania proporcji. Ponieważ Bing mierzy obraz w lewym górnym rogu, Dolna część obrazu jest obcinana. 
  
![Obraz poziomy przycięty do 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu inteligentnego przycinania proporcji. Ponieważ Bing mierzy obraz w lewym górnym rogu, Dolna część obrazu jest obcinana. 
   
![Obraz poziomy przycięty do 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu inteligentnego przycinania proporcji. Ponieważ Bing mierzy obraz z centrum, lewa i prawa część obrazu są przycinane.
  
![Obraz poziomy przycięty do 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Jeśli Bing nie może określić regionu obrazu, usługa będzie używać przycinania Niewidzącego wskaźnika.  

### <a name="blind-ratio-cropping"></a>Nieniewidomy współczynnik przycinania

Jeśli zażądasz przycinania wskaźnika niewidomego (przez ustawienie `c` parametru na `4` ), Bing Przycinanie obrazu przy użyciu poniższych reguł.  
  
- Jeśli `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` obraz jest mierzony od lewego górnego rogu i przycięty u dołu.  
- Jeśli `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` obraz jest mierzony od środka i przycięty do lewej i prawej strony.  

Poniżej przedstawiono obraz w pionie, który jest 225x300.  
  
![Oryginalny obraz słonecznika](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Poniżej przedstawiono obraz zredukowany do 200x200 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od lewego górnego rogu, w dolnej części obrazu, który zostanie przycięty.  
  
![Obraz słonecznika przycięty do 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Poniżej przedstawiono obraz zredukowany do 200x100 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od lewego górnego rogu, w dolnej części obrazu, który zostanie przycięty.  
  
![Obraz słonecznika przycięty do 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Poniżej przedstawiono obraz zredukowany do 100x200 przy użyciu przycinania niewidomych wskaźników. Obraz jest mierzony od środka, co powoduje wycinanie lewej i prawej części obrazu.  
  
![Obraz słonecznika przycięty do 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejsy API wyszukiwania Bing?](bing-api-comparison.md)
* [Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing](use-display-requirements.md)
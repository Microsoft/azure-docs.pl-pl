---
title: Pokaż wyniki wyszukiwania na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wykonać żądanie wyszukiwania za pomocą usługi Microsoft Azure Maps Web SDK i wyświetlić wyniki na mapie.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895277"
---
# <a name="show-search-results-on-the-map"></a>Pokaż wyniki wyszukiwania na mapie

W tym artykule pokazano, jak wyszukiwać interesujące lokalizacje i wyświetlać wyniki wyszukiwania na mapie.

Istnieją dwa sposoby wyszukiwania interesujących lokalizacji. Jednym ze sposobów jest użycie modułu usługi do żądania wyszukiwania. Druga metoda polega na tym, że żądanie wyszukiwania [Azure Maps interfejs API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) za pomocą [interfejsu API pobierania](https://fetch.spec.whatwg.org/). Obie metody omówiono poniżej.

## <a name="make-a-search-request-via-service-module"></a>Utwórz żądanie wyszukiwania za pomocą modułu usługi

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Pokaż wyniki wyszukiwania na mapie (module usługi)</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie, pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy wystąpienie [potoku](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `searchURL`Reprezentuje adres URL służący do Azure Maps operacji [wyszukiwania](/rest/api/maps/search) .

Trzeci blok kodu tworzy obiekt źródła danych przy użyciu klasy [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) i dodaje do niego wyniki wyszukiwania. [Warstwa symboli](/javascript/api/azure-maps-control/atlas.layer.symbollayer) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](/javascript/api/azure-maps-control/atlas.source.datasource) na mapie.  Następnie zostanie utworzona warstwa symboli. Źródło danych jest dodawane do warstwy symboli, która jest następnie dodawana do mapy.

Czwarty blok kodu używa metody [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) w [module usługi](how-to-use-services-module.md). Umożliwia ona wykonywanie bezpłatnych wyszukiwań tekstowych za pośrednictwem [interfejsu API REST usługi Get Search](/rest/api/maps/search/getsearchfuzzy) w celu wyszukiwania punktów zainteresowania. Żądania GET w interfejsie API wyszukiwania rozmytego mogą obsługiwać dowolną kombinację danych wejściowych rozmytych. Kolekcje funkcji GEOJSON z odpowiedzi są następnie wyodrębniane przy użyciu `geojson.getFeatures()` metody i dodawane do źródła danych, co powoduje automatyczne wyniki renderowania danych na mapie za pośrednictwem warstwy symboli.

Ostatni blok kodu dostosowuje granice aparatu dla mapy za pomocą właściwości [Setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Żądanie wyszukiwania, źródło danych, warstwa symboli i granice aparatu znajdują się wewnątrz [odbiornika zdarzeń](/javascript/api/azure-maps-control/atlas.map#events) mapy. Chcemy mieć pewność, że wyniki są wyświetlane po całkowitym załadowaniu mapy.


## <a name="make-a-search-request-via-fetch-api"></a>Utwórz żądanie wyszukiwania za pomocą interfejsu API pobierania

<iframe height='500' scrolling='no' title='Pokaż wyniki wyszukiwania na mapie' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Pokaż wyniki wyszukiwania na mapie</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy adres URL, na który zostanie wysłane żądanie wyszukiwania. Tworzy także dwie tablice do przechowywania granic i kodów PIN dla wyników wyszukiwania.

Trzeci blok kodu używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/). [Interfejs API pobierania](https://fetch.spec.whatwg.org/) służy do żądania [Azure Maps interfejsu API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) w celu wyszukiwania interesujących punktów. Interfejs API wyszukiwania rozmytego może obsługiwać dowolną kombinację danych wejściowych rozmytych. Następnie obsługuje i analizuje odpowiedź wyszukiwania i dodaje numery PIN wyników do tablicy searchPins.

Czwarty blok kodu tworzy obiekt źródła danych przy użyciu klasy [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) . W kodzie dodamy wyniki wyszukiwania do obiektu źródłowego. [Warstwa symboli](/javascript/api/azure-maps-control/atlas.layer.symbollayer) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](/javascript/api/azure-maps-control/atlas.source.datasource) na mapie. Następnie zostanie utworzona warstwa symboli. Źródło danych jest dodawane do warstwy symboli, która jest następnie dodawana do mapy.

Ostatni blok kodu tworzy obiekt [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) . Używa tablicy wyników, a następnie dostosowuje granice aparatu dla mapy za pomocą [Setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)mapy. Następnie renderuje to numery PIN wyników.

Żądanie wyszukiwania, źródło danych, warstwa symboli i granice aparatu są ustawiane w obrębie [odbiornika zdarzeń](/javascript/api/azure-maps-control/atlas.map#events) mapy, aby upewnić się, że wyniki są wyświetlane po całkowitym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

Dowiedz się więcej na temat **wyszukiwania rozmytego**:

> [!div class="nextstepaction"]
> [Azure Maps interfejsu API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](map-route.md)
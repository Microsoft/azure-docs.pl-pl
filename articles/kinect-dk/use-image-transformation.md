---
title: Korzystanie z transformacji obrazu zestawu SDK usługi Azure urządzenia Kinect
description: Dowiedz się, jak używać funkcji transformacji obrazu zestawu SDK czujnika urządzenia Kinect platformy Azure.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, system współrzędnych, kalibracja, projekt, unprojekt, transformacja, RGB-d, Chmura punktów
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277458"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Korzystanie z transformacji obrazu zestawu SDK usługi Azure urządzenia Kinect

Postępuj zgodnie z określonymi funkcjami, aby korzystać z obrazów między skoordynowanymi systemami kamer w usłudze Azure urządzenia Kinect DK i przekształcać je.

## <a name="k4a_transformation-functions"></a>funkcje k4a_transformation

 Wszystkie funkcje z prefiksem *k4a_transformation* działać na całych obrazach. Wymagają one dojścia transformacji [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) uzyskane za pośrednictwem [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) i są nieprzypisane przez [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). Możesz również zapoznać się z [przykładem przekształcenia](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) zestawu SDK, który pokazuje, jak używać trzech funkcji w tym temacie.

Omówione są następujące funkcje:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Omówienie

 Funkcja [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) przekształca mapę głębokości z punktu widzenia aparatu głębokości do punktu widzenia kamery koloru. Ta funkcja została zaprojektowana tak, aby generowała obrazy RGB-D, gdzie D reprezentuje dodatkowy kanał obrazu, który rejestruje wartość głębokości. Jak widać na poniższej ilustracji, obraz koloru i dane wyjściowe [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) wyglądają tak, jakby były wykonane z tego samego punktu widzenia, czyli punktu widzenia aparatu koloru.

   ![Przekształcanie obrazów](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementacja

 Ta funkcja transformacji jest bardziej złożona niż po prostu wywoływanie [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) dla każdego piksela. Powoduje to zniekształcenie trójkąta z geometrii aparatu głębokości do geometrii aparatu kolorów. Trójkątna siatka służy do uniknięcia generowania otworów w obrazie ze przekształconą głębokością. Bufor Z-Buffer gwarantuje, że Occlusions są prawidłowo obsługiwane. Przyspieszenie GPU jest domyślnie włączone dla tej funkcji.

#### <a name="parameters"></a>Parametry

 Parametry wejściowe są uchwytem transformacji i obrazem głębokości. Rozdzielczość obrazu głębokości musi być zgodna z ```depth_mode``` określonym podczas tworzenia dojścia transformacji. Na przykład jeśli uchwyt przekształcenia został utworzony przy użyciu ```K4A_DEPTH_MODE_WFOV_UNBINNED``` trybu 1024x1024, rozdzielczość obrazu głębokości musi wynosić 1024x1024 pikseli. Dane wyjściowe są przekształconym obrazem głębokości, który musi zostać przydzielony przez użytkownika za pośrednictwem wywołania [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozdzielczość przekształconego obrazu głębokości musi być zgodna z ```color_resolution``` określonym podczas tworzenia dojścia transformacji. Na przykład jeśli rozdzielczość koloru została ustawiona na `K4A_COLOR_RESOLUTION_1080P` , rozdzielczość obrazu wyjściowego musi być 1920 x 1080 pikseli. Krok obrazu wyjściowego jest ustawiony na `width * sizeof(uint16_t)` , ponieważ obraz przechowuje 16-bitową wartość głębokości.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Omówienie

 Funkcja [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) przekształca mapę głębokości i obraz niestandardowy z punktu widzenia aparatu głębokości do punktu widzenia aparatu kolorów. Jako rozszerzenie [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514), ta funkcja została zaprojektowana w celu utworzenia odpowiedniego obrazu niestandardowego, dla którego każdy piksel dopasowuje odpowiednie współrzędne pikseli w aparacie kolorów do obrazu z przekształconą głębokością.

#### <a name="implementation"></a>Implementacja

 Ta funkcja przekształcania generuje obraz głębi przekształcony w taki sam sposób jak [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). W celu przekształcenia obrazu niestandardowego ta funkcja udostępnia opcje używania interpolacji liniowej lub najbliższej interpolacji sąsiada. Użycie interpolacji liniowej może utworzyć nowe wartości w przekształconym obrazie niestandardowym. Użycie najbliższej interpolacji sąsiada uniemożliwi wyświetlanie w obrazie wyjściowym wartości, które nie występują w oryginalnym obrazie, ale spowoduje to mniej płynny obraz. Obraz niestandardowy powinien mieć jeden kanał 8-bitowy lub 16-bitowy. Przyspieszenie GPU jest domyślnie włączone dla tej funkcji.

#### <a name="parameters"></a>Parametry

 Parametry wejściowe są uchwytem transformacji, obrazem głębokości, obrazem niestandardowym i typem interpolacji. Obraz głębokości i rozdzielczość obrazu niestandardowego muszą być zgodne z `depth_mode` określonym podczas tworzenia dojścia transformacji. Na przykład jeśli uchwyt przekształcenia został utworzony przy użyciu `K4A_DEPTH_MODE_WFOV_UNBINNED` trybu 1024x1024, rozdzielczość obrazu głębokości i obrazu niestandardowego musi wynosić 1024x1024 pikseli. `interpolation_type`Powinien być albo `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . Dane wyjściowe są przekształconym obrazem głębokości i przekształconym obrazem niestandardowym, który musi zostać przydzielony przez użytkownika za pośrednictwem wywołania [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozdzielczość przekształconego obrazu głębokości i przekształconego obrazu niestandardowego musi być zgodna z `color_resolution` określonym podczas tworzenia dojścia transformacji. Na przykład jeśli rozdzielczość koloru została ustawiona na `K4A_COLOR_RESOLUTION_1080P` , rozdzielczość obrazu wyjściowego musi być 1920 x 1080 pikseli. Krok obrazu głębi wyjściowej jest ustawiony na `width * sizeof(uint16_t)` , ponieważ obraz przechowuje 16-bitowe wartości głębokości. Niestandardowy obraz wejściowy i przekształcony obraz niestandardowy muszą mieć format lub należy `K4A_IMAGE_FORMAT_CUSTOM8` `K4A_IMAGE_FORMAT_CUSTOM16` odpowiednio ustawić odpowiedni krok obrazu. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Omówienie

 Funkcja [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) przekształca obraz koloru z punktu widzenia aparatu kolorów do punktu widzenia aparatu głębokości (patrz rysunek powyżej). Może służyć do generowania obrazów RGB-D.

#### <a name="implementation"></a>Implementacja

 Dla każdego piksela mapy głębokości funkcja używa wartości głębokości pikseli do obliczenia odpowiedniej współrzędnej w pikselach w obrazie koloru. Następnie wyszukamy wartość koloru na tej współrzędnej w obrazie koloru. Interpolacja dwuliniowa jest wykonywana w obrazie koloru, aby uzyskać wartość koloru z dokładnością do pikseli. Piksel, który nie ma skojarzonego odczytania głębokości, jest przypisany do wartości BGRA `[0,0,0,0]` w obrazie danych wyjściowych. Przyspieszenie GPU jest domyślnie włączone dla tej funkcji. Ponieważ ta metoda wytwarza otwory w przekształconym obrazie kolorów i nie obsługuje Occlusions, zalecamy użycie funkcji [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) .

#### <a name="parameters"></a>Parametry

Parametry wejściowe są uchwytem transformacji, obrazem głębokości i obrazem koloru. Rozdzielczości obrazów głębi i kolorów muszą być zgodne depth_mode i color_resolution określone podczas tworzenia uchwytu transformacji. Dane wyjściowe to przekształcony obraz koloru, który musi zostać przydzielony przez użytkownika za pośrednictwem wywołania [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozdzielczość przekształconego obrazu koloru musi być zgodna depth_resolution określona podczas tworzenia dojścia transformacji. Obrazy wyjściowe przechowują wartości 4 8-bitowe reprezentujące BGRA na każdy piksel. W związku z tym krok obrazu to ```width * 4 * sizeof(uint8_t)``` . Kolejność danych jest przeplatana na piksel, czyli niebieska wartość — piksel 0, Zielona wartość — piksel 0, czerwony wartość — piksel 0, wartość alfa — piksel 0, niebieska wartość — piksel 1 i tak dalej.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Omówienie

Funkcja [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) konwertuje mapę głębokości 2D pobraną przez aparat do chmury punktu 3W w systemie współrzędnych tego samego aparatu. Aparat może być w tym przypadku kamerą głębi lub kolorem.

#### <a name="implementation"></a>Implementacja

 Funkcja daje równoważne wyniki do uruchamiania [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) na każdy piksel, ale jest bardziej wydajny. Podczas wywoływania [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)wstępnie obliczamy tak zwaną tabelę przeszukiwania XY, która przechowuje czynniki skali x i y dla każdego piksela obrazu. Gdy wywołując [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70), uzyskujemy trójwymiarową współrzędną x pikseli, mnożąc współczynnik skali x pikseli z współrzędną z pikseli. Analogicznie, Współrzędna Y 3W jest obliczana przez mnożenie ze współczynnikiem skali y. [Przykładowy przykład dla chmurowych punktów](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) SDK pokazuje, jak jest obliczana tabela XY. Użytkownicy mogą skorzystać z przykładowego kodu, aby zaimplementować własną wersję tej funkcji, na przykład w celu przyspieszenia potoku procesora GPU.

#### <a name="parameters"></a>Parametry

 Parametry wejściowe są uchwytem transformacji, specyfikatorem aparatu i obrazem głębokości. Jeśli specyfikator aparatu jest ustawiony na głębokość, rozdzielczość obrazu głębokości musi pasować do depth_mode określonego podczas tworzenia uchwytu transformacji. W przeciwnym razie, jeśli specyfikator jest ustawiony na kamerę kolorów, rozdzielczość musi być zgodna z rozdzielczością wybranego color_resolution. Parametr wyjściowy jest obrazem XYZ, który musi zostać alokowany przez użytkownika za pośrednictwem wywołania [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Rozdzielczość obrazu XYZ musi być zgodna z rozdzielczością mapy głębokości wejściowych. Na każdym pikselu są przechowywane trzy wartości współrzędnych 16-bitowych w milimetrach. W związku z tym krok obrazu XYZ jest ustawiany na `width * 3 * sizeof(int16_t)` . Kolejność danych jest przeplatana z pikselami, czyli współrzędnych X, która jest równa 0, współrzędnej Y (w pikselach 0), współrzędnych: piksel 0, współrzędne X — piksel 1 i tak dalej. Jeśli piksel nie może zostać skonwertowany na 3D, funkcja przypisuje wartości `[0,0,0]` do piksela.

## <a name="samples"></a>Samples

[Przykład transformacji](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak korzystać z funkcji transformacji obrazu zestawu SDK usługi Azure urządzenia Kinect, możesz również poznać

>[!div class="nextstepaction"]
>[Funkcje kalibracji zestawu SDK czujnika usługi Azure urządzenia Kinect](use-calibration-functions.md)

Możesz również przejrzeć

[Układy współrzędnych](coordinate-systems.md)

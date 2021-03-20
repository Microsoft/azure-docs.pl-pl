---
title: Korzystanie z funkcji kalibracji platformy Azure urządzenia Kinect
description: Dowiedz się, jak używać funkcji kalibracji dla platformy Azure urządzenia Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, system współrzędnych, kalibracja, funkcje, aparat, wewnętrzna, zewnętrznych, projekt, unprojekt, transformacja, RGB-d, Chmura punktu
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277494"
---
# <a name="use-azure-kinect-calibration-functions"></a>Korzystanie z funkcji kalibracji platformy Azure urządzenia Kinect

Funkcje kalibracji umożliwiają przekształcanie punktów między systemami współrzędnych każdego czujnika na urządzeniu urządzenia Kinect platformy Azure. Aplikacje wymagające konwersji całych obrazów mogą wykorzystać przyspieszone operacje dostępne w [funkcjach transformacji](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Pobierz dane kalibracji

Aby wykonać przekształcenia systemu współrzędnych, należy pobrać kalibrację urządzenia. Dane kalibracji są przechowywane w [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) typie danych. Jest on uzyskiwany z urządzenia za pośrednictwem funkcji [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). Dane kalibracyjne nie tylko są specyficzne dla każdego urządzenia, ale również do trybu operacyjnego kamer. Dlatego [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) wymaga `depth_mode` parametrów i `color_resolution` jako danych wejściowych.

### <a name="opencv-compatibility"></a>Zgodność OpenCV

Parametry kalibracji są zgodne z [OpenCV](https://opencv.org/). Aby uzyskać więcej informacji na temat parametrów kalibracji poszczególnych aparatów, zobacz również [dokumentację OpenCV](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Zobacz również [przykład OpenCV Compatibility](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) zestawu SDK, który ilustruje konwersję między typem [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) i odpowiednimi strukturami danych OpenCV.

## <a name="coordinate-transformation-functions"></a>Koordynuj funkcje transformacji

Na poniższym rysunku przedstawiono różne systemy współrzędnych platformy Azure urządzenia Kinect oraz funkcje do konwersji między nimi. Pomijamy systemy współrzędnych 3W żyroskop i przyspieszeniomierza, aby utrzymać prostą ilustrację.

   ![Transformacja współrzędnych](./media/how-to-guides/coordinate-transformation.png)

Uwagi dotyczące zniekształcenia obiektywu: współrzędne 2D zawsze odwołują się do zniekształconego obrazu w zestawie SDK. [Przykład niezniekształcenia](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) zestawu SDK pokazuje zniekształcenie obrazu. Ogólnie rzecz biorąc, punkty 3W nigdy nie będą miały wpływ na zniekształcenia obiektywu.

### <a name="convert-between-3d-coordinate-systems"></a>Konwersja między systemami współrzędnych 3W

Funkcja [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) konwertuje punkt 3W źródłowego układu współrzędnych na punkt 3W docelowego układu współrzędnych przy użyciu kalibracji aparatu zewnętrznych. Dla elementów źródłowych i docelowych można ustawić dowolne z czterech systemów współrzędnych 3W, czyli kamerę kolorów, kamerę głębi, żyroskop lub przyspieszeniomierza. Jeśli źródło i cel są identyczne, niemodyfikowany wejściowy punkt 3W jest zwracany jako dane wyjściowe.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Konwertuj między systemami współrzędnych 2D i 3D

Funkcja [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) konwertuje punkt 3W źródłowego układu współrzędnych na współrzędną 2D w aparacie docelowym. Ta funkcja jest często określana jako funkcja projektu. Chociaż źródło można ustawić na dowolne cztery systemy współrzędnych 3W, obiekt docelowy musi być głębokością lub kamerą kolorów. Jeśli źródło i cel są różne, wejściowy punkt 3W jest konwertowany na układ współrzędnych 3W aparatu docelowego przy użyciu [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Gdy punkt 3W zostanie przedstawiony w docelowym układzie współrzędnych kamery, odpowiednie współrzędne w pikselach 2D są obliczane przy użyciu wewnętrznej kalibracji aparatu docelowego. Jeśli punkt 3W znajduje się poza widocznym obszarem docelowego aparatu, prawidłowa wartość jest równa 0.

Funkcja [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) konwertuje współrzędne w pikselach 2D aparatu źródłowego do punktu 3W docelowego układu współrzędnych aparatu. Źródłem musi być kamera koloru lub głębi. Element docelowy można ustawić na jeden z czterech układów współrzędnych 3W. Oprócz współrzędnej 2D pikseli, wartość głębokości piksela (w milimetrach) w obrazie obrazu źródłowego jest wymagana jako dane wejściowe do funkcji, jednym ze sposobów uzyskania wartości głębokości w geometrii aparatu kolorów jest użycie funkcji [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Funkcja oblicza promień 3D wiodący od punktu odniesienia aparatu źródłowego przez określoną współrzędną pikseli przy użyciu wewnętrznej kalibracji aparatu źródłowego. Wartość głębokości służy do znalezienia dokładnej lokalizacji punktu 3W na tym promieniu. Ta operacja jest często określana jako funkcja unproject. Jeśli aparaty źródłowe i docelowe są inne, funkcja przekształca punkt 3W do układu współrzędnych obiektu docelowego za pośrednictwem [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Jeśli Współrzędna pikseli 2D wypada poza widocznym obszarem w aparacie źródłowym, prawidłowa wartość jest równa 0.

### <a name="converting-between-2d-coordinate-systems"></a>Konwertowanie między systemami współrzędnych 2D

Funkcja [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) konwertuje dwuwymiarową współrzędną kamery źródłowej na współrzędną 2D w aparacie docelowym. Źródłowa i docelowa musi być ustawiona na wartość Color lub Camera głębokości. Funkcja wymaga wartości głębi piksela (w milimetrach) w obrazie źródłowego obrazu aparatu jako dane wejściowe, jednym ze sposobów uzyskania wartości głębokości w geometrii aparatu kolorów jest użycie funkcji [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Wywołuje [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) w celu przekonwertowania na punkt 3W systemu źródłowego aparatu fotograficznego. Następnie wywołuje [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) w celu przekonwertowania na współrzędną dwuwymiarową obrazu docelowego aparatu. Prawidłowa wartość jest równa 0, jeśli [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) lub [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) zwróci nieprawidłowy wynik.

## <a name="related-samples"></a>Powiązane przykłady

- [Przykład zgodności OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Przykład niezniekształcenia](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Przykład chmury Fast Point](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Następne kroki

Teraz wiesz o kalibracji aparatu, możesz również dowiedzieć się, jak
>[!div class="nextstepaction"]
>[Synchronizacja urządzeń do przechwytywania](capture-device-synchronization.md)

Możesz również przejrzeć

[Układy współrzędnych](coordinate-systems.md)

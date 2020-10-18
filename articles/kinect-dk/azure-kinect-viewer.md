---
title: Azure Kinect Viewer
description: Dowiedz się, jak wizualizować wszystkie strumienie danych urządzeń za pomocą usługi Azure urządzenia Kinect Viewer.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, czujnik, Podgląd, Wizualizacja, Głębokość, RGB, Color, IMU, audio, mikrofon, Chmura w chmurze
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166109"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect Viewer

Usługa Azure urządzenia Kinect Viewer, dostępna w katalogu narzędzi zainstalowanych jako `k4aviewer.exe` (na przykład `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , gdzie `X.Y.Z` jest zainstalowana wersja zestawu SDK), może służyć do wizualizacji wszystkich strumieni danych urządzeń w celu:

* Sprawdź, czy Czujniki działają prawidłowo.
* Pomoc dotycząca pozycjonowania urządzenia.
* Eksperymentuj z ustawieniami aparatu.
* Odczytaj konfigurację urządzenia.
* Nagrania odtwarzania wykonane za pomocą [usługi Azure urządzenia Kinect Recorder](azure-kinect-recorder.md).

Aby uzyskać więcej informacji na temat usługi Azure urządzenia Kinect Viewer, zobacz [jak korzystać z usługi Azure urządzenia Kinect Video](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

Usługa Azure urządzenia Kinect Viewer jest [Open Source](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) i może służyć jako przykład do korzystania z interfejsów API.

## <a name="use-viewer"></a>Korzystanie z przeglądarki

Przeglądarka może działać w dwóch trybach: z danymi dynamicznymi z czujnika lub z zarejestrowanych danych ([Azure urządzenia Kinect Recorder](azure-kinect-recorder.md)).

### <a name="start-application"></a>Uruchom aplikację

Uruchom aplikację, uruchamiając `k4aviewer.exe` .

![Zrzut ekranu pokazujący uruchomioną aplikację Podgląd.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Korzystanie z przeglądarki z danymi na żywo

1. W sekcji **Otwórz urządzenie** wybierz **numer seryjny** urządzenia do otwarcia. Następnie wybierz pozycję **Odśwież**, jeśli brakuje urządzenia.
2. Wybierz przycisk **Otwórz urządzenie** .
3. Wybierz pozycję **Rozpocznij** , aby rozpocząć przesyłanie strumieniowe danych przy użyciu ustawień domyślnych.

### <a name="use-the-viewer-with-recorded-data"></a>Korzystanie z przeglądarki z zarejestrowanymi danymi

W sekcji **Otwórz nagrywanie** przejdź do zapisanego pliku, a następnie wybierz go.

## <a name="check-device-firmware-version"></a>Sprawdź wersję oprogramowania układowego urządzenia

Uzyskaj dostęp do wersji oprogramowania układowego urządzenia w oknie konfiguracji, jak pokazano na poniższej ilustracji.

![Sprawdzanie wersji oprogramowania układowego urządzenia przy użyciu przeglądarki](./media/how-to-guides/check-firmware-update.png)

Na przykład w tym przypadku usługodawca internetowy z głębokością aparatu jest uruchomiony 1.5.63 zapory.

## <a name="depth-camera"></a>Kamera z pomiarem głębi

W podglądzie zdjęć głębokości zostaną wyświetlone dwa okna:

* Jedna jest nazywana *aktywną jasnością* , która jest obrazem w skali szarości, z jasnością podczerwieni.
* Druga jest określana jako *głębokość*, która ma kolorową reprezentację danych głębokości.

Umieść kursor w pikselach okna głębi, aby zobaczyć wartość czujnika głębokości, jak pokazano poniżej.

![Widok głębokości](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>Aparat RGB

Na poniższej ilustracji przedstawiono widok kamery kolorów.

![Widok RGB](./media/how-to-guides/viewer-rgb-camera.png)

Podczas przesyłania strumieniowego można kontrolować ustawienia kamery RGB z okna konfiguracji.

![Kontrolki kamery RGB](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Jednostka pomiaru bezwładnościowego (IMU)

Okno IMU ma dwa składniki, przyspieszeniomierz i żyroskop.

Górna część to przyspieszeniomierz i pokazuje Przyspieszenie liniowe w licznikach/sekundę<sup>2</sup>.  Obejmuje to przyspieszenie, więc jeśli jest on płaski w tabeli, oś Z prawdopodobnie pokaże się wokół 9,8 m/s<sup>2</sup>.

Dolna część jest częścią żyroskop i pokazuje obrót rotacyjny w radianach na sekundę

![Widok czujnika ruchu](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Wejście mikrofonu

Widok mikrofonu przedstawia reprezentację dźwięku wysłuchanego na każdym z mikrofonów. Jeśli nie ma żadnego dźwięku, wykres jest pokazywany jako pusty, w przeciwnym razie zobaczysz ciemny niebieski dźwięk z górnym niebieskim przebiegiem.

Ciemna fala reprezentuje wartości minimalne i maksymalne obserwowane przez mikrofon w tym wycinku czasu. Lekki sygnalizator reprezentuje pierwiastek średnika wartości obserwowanych przez mikrofon w tym wycinku czasu.

![Widok danych wejściowych mikrofonu](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Wizualizacja w chmurze

Wizualizacja głębi w 3D pozwala na przenoszenie obrazu przy użyciu poinstruujych kluczy.

![Chmura punktów głębokości](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Kontrola synchronizacji

Możesz użyć przeglądarki, aby skonfigurować urządzenie jako autonomiczne (domyślne), główne lub tryb podrzędny podczas konfigurowania synchronizacji wielu urządzeń.
Podczas zmieniania konfiguracji lub wstawiania/usuwania kabla synchronizacji wybierz pozycję **Odśwież** , aby zaktualizować.

![Zewnętrzna kontrola synchronizacji](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Przewodnik konfigurowania synchronizacji zewnętrznej](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)

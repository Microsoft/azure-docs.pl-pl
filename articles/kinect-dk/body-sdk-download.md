---
title: Pobieranie zestawu SDK śledzenia treści usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać każdą wersję zestawu SDK czujnika usługi Azure urządzenia Kinect w systemie Windows lub Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, SDK, Pobierz aktualizację, Najnowsza, dostępna, Zainstaluj, treść, śledzenie
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277582"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Pobierz zestaw SDK śledzenia treści usługi Azure urządzenia Kinect

Ten dokument zawiera linki umożliwiające zainstalowanie każdej wersji zestawu SDK śledzenia treści usługi Azure urządzenia Kinect.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Zawartość zestawu SDK śledzenia treści usługi Azure urządzenia Kinect

- Nagłówki i biblioteki do kompilowania aplikacji do śledzenia treści przy użyciu usługi Azure urządzenia Kinect DK.
- Redystrybucyjne bibliotek DLL wymaganych przez aplikacje do śledzenia treści przy użyciu usługi Azure urządzenia Kinect DK.
- Przykładowe aplikacje do śledzenia treści.

## <a name="windows-download-links"></a>Linki pobierania systemu Windows

Wersja       | Pobierz
--------------|----------
1.0.1 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942)
1.0.0 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848)
0.9.5 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100636)
0.9.4 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100415)
0.9.3 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100307)
0.9.2 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100128)
0.9.1 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100063)
0.9.0 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=58402)

## <a name="linux-installation-instructions"></a>Instrukcje instalacji systemu Linux

Obecnie jedyna obsługiwana dystrybucja to Ubuntu 18,04. Aby zażądać obsługi innych dystrybucji, zobacz [Tę stronę](https://aka.ms/azurekinectfeedback).

Najpierw musisz skonfigurować [repozytorium pakietów firmy Microsoft](https://packages.microsoft.com/), postępując zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Pakiet zawiera nagłówki i pliki CMAKE do skompilowania `libk4abt` .
`libk4abt<major>.<minor>`Pakiet zawiera obiekty udostępnione, które są konieczne do uruchamiania plików wykonywalnych, które są zależne od `libk4abt` programu oraz podgląd przykładu.

Samouczki podstawowe wymagają `libk4abt<major>.<minor>-dev` pakietu. Aby go zainstalować, uruchom polecenie

`sudo apt install libk4abt1.0-dev`

Jeśli polecenie zakończy się pomyślnie, zestaw SDK jest gotowy do użycia.

> [!NOTE]
> Podczas instalowania zestawu SDK Pamiętaj o ścieżce instalacji programu. Na przykład "C:\Program Files\Azure urządzenia Kinect Body śledzący SDK 1.0.0". W tej ścieżce znajdziesz przykłady, do których odwołuje się artykuł.
> Przykłady śledzenia treści znajdują się w folderze " [treść-śledzenie-przykłady](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) " w repozytorium Azure-urządzenia Kinect-Samples. Przykłady, do których odwołuje się artykuł, znajdziesz tutaj.

## <a name="change-log"></a>Dziennik zmian

### <a name="v101"></a>1.0.1 v
* [Poprawka błędu] Rozwiąż problem polegający na tym, że zestaw SDK ulega awarii, jeśli ładowanie onnxruntime.dll ze ścieżki w systemie Windows Build 19025 lub nowszym: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>1.0.0 v
* Ona Dodaj otokę języka C# do instalatora MSI.
* [Poprawka błędu] Rozwiąż problem polegający na tym, że nie można poprawnie wykryć obrotu elementu głównego: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Poprawka błędu] Rozwiąż problem polegający na tym, że użycie procesora CPU spadnie do 100% na komputerze z systemem Linux: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Badan Dodaj dwa próbki do przykładowego repozytorium. Przykład 1 pokazuje, jak przekształcić wyniki śledzenia treści z przestrzeni głębokości na [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)do miejsca koloru; przykład 2 pokazuje, jak wykryć [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) do płaszczyzny podłogowej

### <a name="v095"></a>0.9.5 v
* Ona Obsługa języka C#. Otoka języka C# jest pakowana w pakiecie NuGet.
* Ona Obsługa wieloskładnikowego śledzenia. Tworzenie wielu ścieżek jest dozwolone. Teraz użytkownik może tworzyć wiele śledzących do śledzenia treści z różnych urządzeń urządzenia Kinect platformy Azure.
* Ona Obsługa przetwarzania wielowątkowego w trybie procesora. Podczas uruchamiania w trybie procesora CPU wszystkie rdzenie zostaną użyte do zmaksymalizowania szybkości.
* Ona Dodaj `gpu_device_id` do `k4abt_tracker_configuration_t` struktury. Zezwól użytkownikom na określenie urządzenia GPU, które jest inne niż domyślne, aby uruchomić algorytm śledzenia treści.
* [Poprawka błędu/zmiana nieprzerwana] Naprawianie liter w nazwie. Zmień nazwę wspólną z `K4ABT_JOINT_SPINE_NAVAL` na `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>0.9.4 v
* Ona Obsługa dodawania połączeń ręcznych. Zestaw SDK udostępnia informacje dla trzech dodatkowych połączeń dla każdej z rąk: RĄCZKi, HANDTIP, kciuk.
* Ona Dodaj poziom ufności przewidywania dla każdego wykrytych połączeń.
* Ona Dodawanie obsługi trybu procesora. Zmiana `cpu_only_mode` wartości w `k4abt_tracker_configuration_t` , teraz zestaw SDK może działać w trybie procesora CPU, który nie wymaga, aby użytkownik miał zaawansowaną kartę graficzną.

### <a name="v093"></a>0.9.3 v
* Ona Opublikuj nowy model DNN dnn_model_2_0. Onnx, który znacznie poprawia niezawodność śledzenia treści.
* Ona Domyślnie Wyłącz wygładzanie danych czasowych. Śledzone połączenia będą bardziej reagować.
* Ona Popraw dokładność mapowania indeksu treści.
* [Poprawka błędu] Usuń usterkę, która nie obowiązuje w ustawieniu orientacji czujnika.
* [Poprawka błędu] Zmień typ body_index_map z K4A_IMAGE_FORMAT_CUSTOM na K4A_IMAGE_FORMAT_CUSTOM8.
* [Znany problem] Dwie zamknięte treści mogą być scalane z segmentem jednego wystąpienia.

### <a name="v092"></a>0.9.2 v
* [Istotna zmiana] Aktualizacja programu zależy od najnowszego zestawu SDK usługi Azure urządzenia Kinect sensor 1.2.0.
* [Zmiana interfejsu API] `k4abt_tracker_create` Funkcja zacznie przyjmować `k4abt_tracker_configuration_t` dane wejściowe. 
* [Zmiana interfejsu API] Zmień `k4abt_frame_get_timestamp_usec` interfejs API tak, aby `k4abt_frame_get_device_timestamp_usec` był bardziej szczegółowy i spójny z zestawem SDK czujnika 1.2.0.
* Ona Zezwól użytkownikom na określenie orientacji montażu czujnika podczas tworzenia modułu śledzącego, aby uzyskać dokładniejsze wyniki śledzenia treści przy instalowaniu pod różnymi kątami.
* Ona Podaj nowy interfejs API, `k4abt_tracker_set_temporal_smoothing` Aby zmienić ilość danych czasowych, które użytkownik chce zastosować.
* Ona Dodaj otokę C++ K4ABT. HPP.
* Ona Dodaj nagłówek definicji wersji k4abtversion. h.
* [Poprawka błędu] Usuń usterkę, która spowodowała bardzo wysokie użycie procesora CPU.
* [Poprawka błędu] Usuwanie usterki rejestratora.

### <a name="v091"></a>od 0.9.1 v
* [Poprawka błędu] Napraw przeciek pamięci podczas niszczenia narzędzia do śledzenia
* [Poprawka błędu] Lepsze komunikaty o błędach dotyczące brakujących zależności
* [Poprawka błędu] Niepowodzenie bez awarii podczas tworzenia drugiego wystąpienia modułu śledzącego
* [Poprawka błędu] Zmienne środowiskowe rejestratora działają teraz prawidłowo
* Obsługa systemu Linux

### <a name="v090"></a>0.9.0 v

* [Istotna zmiana] Obniżono zależność zestawu SDK do CUDA 10,0 (od CUDA 10,1). Środowisko uruchomieniowe ONNX jest oficjalnie obsługiwane tylko do CUDA 10,0.
* [Istotna zmiana] Przełączono do środowiska uruchomieniowego ONNX zamiast środowiska uruchomieniowego Tensorflow. Zmniejsza czas uruchamiania pierwszej ramki i użycie pamięci. Zmniejsza również rozmiar binarny zestawu SDK.
* [Zmiana interfejsu API] Zmieniono nazwę `k4abt_tracker_queue_capture()` na `k4abt_tracker_enqueue_capture()`
* [Zmiana interfejsu API] Pozłamane `k4abt_frame_get_body()` dwie osobne funkcje: `k4abt_frame_get_body_skeleton()` i `k4abt_frame_get_body_id()` . Teraz możesz wysyłać zapytania o identyfikator treści bez zawsze kopiowania całej struktury szkieletowej.
* [Zmiana interfejsu API] Dodano  `k4abt_frame_get_timestamp_usec()` funkcję ułatwiającą wykonywanie zapytań dotyczących sygnatury czasowej ramki treści przez użytkowników.
* Ulepszona dokładność algorytmu śledzenia treści i niezawodność śledzenia

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure urządzenia Kinect DK](about-azure-kinect-dk.md)

- [Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Skonfiguruj śledzenie treści usługi Azure urządzenia Kinect](body-sdk-setup.md)

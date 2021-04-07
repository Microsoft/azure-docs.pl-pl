---
title: Pobieranie zestawu SDK śledzenia treści usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać każdą wersję zestawu SDK czujnika usługi Azure urządzenia Kinect w systemie Windows lub Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, SDK, Pobierz aktualizację, Najnowsza, dostępna, Zainstaluj, treść, śledzenie
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654496"
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
1.1.0 | [Pakiet](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942)
1.0.0 | Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848)

## <a name="linux-installation-instructions"></a>Instrukcje instalacji systemu Linux

Obecnie jedyna obsługiwana dystrybucja to Ubuntu 18,04 i 20,04. Aby zażądać obsługi innych dystrybucji, zobacz [Tę stronę](https://aka.ms/azurekinectfeedback).

Najpierw musisz skonfigurować [repozytorium pakietów firmy Microsoft](https://packages.microsoft.com/), postępując zgodnie z instrukcjami znajdującymi się [tutaj](/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Pakiet zawiera nagłówki i pliki CMAKE do skompilowania `libk4abt` .
`libk4abt<major>.<minor>`Pakiet zawiera obiekty udostępnione, które są konieczne do uruchamiania plików wykonywalnych, które są zależne od `libk4abt` programu oraz podgląd przykładu.

Samouczki podstawowe wymagają `libk4abt<major>.<minor>-dev` pakietu. Aby go zainstalować, uruchom polecenie

`sudo apt install libk4abt<major>.<minor>-dev`

Jeśli polecenie zakończy się pomyślnie, zestaw SDK jest gotowy do użycia.

> [!NOTE]
> Podczas instalowania zestawu SDK Pamiętaj o ścieżce instalacji programu. Na przykład "C:\Program Files\Azure urządzenia Kinect Body śledzący SDK 1.0.0". W tej ścieżce znajdziesz przykłady, do których odwołuje się artykuł.
> Przykłady śledzenia treści znajdują się w folderze " [treść-śledzenie-przykłady](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) " w repozytorium Azure-urządzenia Kinect-Samples. Przykłady, do których odwołuje się artykuł, znajdziesz tutaj.

## <a name="change-log"></a>Dziennik zmian

### <a name="v110"></a>1.1.0 v
* Ona Dodaj obsługę DirectML (tylko system Windows) i TensorRT wykonywanie modelu szacowania ułożenia. Zobacz często zadawane pytania dotyczące nowych środowisk wykonywania.
* Ona Dodaj `model_path` do `k4abt_tracker_configuration_t` struktury. Umożliwia użytkownikom określenie nazwy ścieżki dla modelu szacowania ułożenia. Domyślny `dnn_model_2_0_op11.onnx` model szacowania ułożenia w warstwie Standardowa znajduje się w bieżącym katalogu.
* Ona Uwzględnij `dnn_model_2_0_lite_op11.onnx` model szacowania ułożenia w wersji Lite. Ten model handlu ma na celu obniżenie wydajności ~ 5% przyrostu dokładności.
* Ona Zweryfikowane próbki kompilują się z programem Visual Studio 2019 i aktualizujemy przykłady w celu korzystania z tej wersji.
* [Istotna zmiana] Aktualizacja do środowiska uruchomieniowego ONNX 1,6 z obsługą procesora CPU, CUDA 11,1, DirectML (tylko system Windows) i TensorRT 7.2.1 środowiska wykonawczego. Wymaga aktualizacji sterownika NVIDIA do R455 lub lepszej.
* [Istotna zmiana] Brak instalacji NuGet.
* [Poprawka błędu] [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) do obsługi procesora GPU z serii NVIDIA RTX 30xx
* [Poprawka błędu] Dodano obsługę [połączeń](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) AMD i Intel Integrated GPU (tylko system Windows)
* [Poprawka błędu] Aktualizacja do [CUDA 11,1](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Poprawka błędu] [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248) aktualizacji do zestawu czujników SDK 1.4.1

### <a name="v101"></a>1.0.1 v
* [Poprawka błędu] Rozwiąż problem polegający na tym, że zestaw SDK ulega awarii, jeśli ładowanie onnxruntime.dll ze ścieżki w systemie Windows Build 19025 lub nowszym: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>1.0.0 v
* Ona Dodaj otokę języka C# do instalatora MSI.
* [Poprawka błędu] Rozwiąż problem polegający na tym, że nie można poprawnie wykryć obrotu elementu głównego: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Poprawka błędu] Rozwiąż problem polegający na tym, że użycie procesora CPU spadnie do 100% na komputerze z systemem Linux: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Badan Dodaj dwa próbki do przykładowego repozytorium. Przykład 1 pokazuje, jak przekształcić wyniki śledzenia treści z przestrzeni głębokości na [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)do miejsca koloru; przykład 2 pokazuje, jak wykryć [link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) do płaszczyzny podłogowej

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure urządzenia Kinect DK](about-azure-kinect-dk.md)

- [Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Skonfiguruj śledzenie treści usługi Azure urządzenia Kinect](body-sdk-setup.md)
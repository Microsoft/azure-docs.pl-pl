---
title: Znane problemy i rozwiązywanie problemów z platformą Azure urządzenia Kinect
description: Poznaj niektóre znane problemy i porady dotyczące rozwiązywania problemów podczas korzystania z zestawu SDK czujnika z platformą Azure urządzenia Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: Rozwiązywanie problemów, aktualizacja, usterka, urządzenia Kinect, opinie, odzyskiwanie, rejestrowanie, porady
ms.openlocfilehash: 32a86deb0b6ab70e42ae3d659504256baae76202
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654768"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Znane problemy i rozwiązywanie problemów z platformą Azure urządzenia Kinect

Ta strona zawiera znane problemy i wskazówki dotyczące rozwiązywania problemów podczas korzystania z zestawu SDK czujnika z platformą Azure urządzenia Kinect DK. Zobacz też [strony pomocy technicznej](./index.yml) dotyczącej problemów dotyczących sprzętu.

## <a name="known-issues"></a>Znane problemy

- Problemy ze zgodnością z kontrolerami hosta USB ASMedia (na przykład ASM1142 Chipset)
  - Niektóre przypadki użycia sterownika USB firmy Microsoft mogą odblokować
  - Wiele komputerów ma także alternatywną liczbę kontrolerów hosta i zmiana portu USB3 może pomóc

Aby uzyskać więcej informacji dotyczących czujników związanych z zestawem SDK, zobacz temat problemy z usługą [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Zbieranie dzienników

Rejestrowanie K4A.dll jest włączane za poorednictwem zmiennych środowiskowych. Domyślnie rejestrowanie jest wysyłane do stdout i generowane są tylko błędy i komunikaty krytyczne. Te ustawienia można zmienić tak, aby rejestrowanie przechodził do pliku. Poziom szczegółowości można również dostosować w razie konieczności. Poniżej znajduje się przykład dla systemu Windows, włączania rejestrowania do pliku o nazwie k4a. log i przechwytywania ostrzeżeń i komunikatów wyższego poziomu.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Uruchom scenariusz z wiersza polecenia (na przykład Uruchom Podgląd)
4. Przejdź do pliku k4a. log i Udostępnij plik.

Aby uzyskać więcej informacji, zobacz poniższy klip z pliku nagłówkowego:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

Rejestrowanie dla K4ABT.dll zestawu SDK śledzenia treści jest podobne, z tą różnicą, że użytkownicy powinni modyfikować różne zestawy nazw zmiennych środowiskowych:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Urządzenie nie jest wyliczane w Menedżerze urządzeń

- Sprawdź, czy stan jest zgodny z urządzeniem, jeśli jest miganie żółtego problemu z łącznością USB i nie ma wystarczającej mocy. Kabel zasilania powinien być podłączony do podanej karty zasilania. Gdy kabel sieciowy ma podłączony typ USB, urządzenie wymaga więcej mocy niż można dostarczyć portu USB komputera. W związku z tym nie należy łączyć się z nim za pośrednictwem portu lub koncentratora USB.
- Sprawdź, czy kabel sieciowy jest podłączony i czy używany jest port USB3 dla danych.
- Spróbuj zmienić port USB3 dla połączenia danych (zalecane jest użycie portu USB blisko płyty głównej, na przykład z tyłu komputera).
- Sprawdź kabel, uszkodzone lub niższe kable jakości mogą spowodować niezawodne Wyliczenie (urządzenie utrzymuje "miganie" w Menedżerze urządzeń).
- Jeśli nawiązano połączenie z komputerem przenośnym i działa on w trybie baterii, może to spowodować ograniczenie zasilania do portu.
- Uruchom ponownie komputer hosta.
- Jeśli problem będzie nadal występował, może to być problem ze zgodnością.
- Jeśli wystąpił błąd podczas aktualizacji oprogramowania układowego i urządzenie nie zostało odzyskane przez siebie, wykonaj [zresetowanie do ustawień fabrycznych](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Nie można otworzyć podglądu usługi Azure urządzenia Kinect

- Najpierw należy sprawdzić, czy urządzenie wylicza w systemie Windows Device Manager.

    ![Aparaty urządzenia Kinect platformy Azure w Menedżerze urządzeń systemu Windows](./media/resources/viewer-fails.png)

- Sprawdź, czy masz inną aplikację korzystającą z urządzenia (na przykład aplikacja kamery systemu Windows). Tylko jedna aplikacja jednocześnie może uzyskać dostęp do urządzenia.
- Sprawdź dziennik k4aviewer. err w poszukiwaniu komunikatów o błędach.
- Otwórz aplikację Windows Camera i sprawdź, czy działa.
- Przed użyciem urządzenia Zaczekaj, aż urządzenie zostanie wyłączone.
- Uruchom ponownie komputer hosta.
- Upewnij się, że na komputerze są używane najnowsze sterowniki grafiki.
- Jeśli używasz własnej kompilacji zestawu SDK, spróbuj użyć oficjalnie wydanej wersji, jeśli rozwiązanie tego problemu zostanie rozwiązany.
- Jeśli problem będzie się powtarzać, [Zbierz dzienniki](troubleshooting.md#collecting-logs) i informacje o plikach.

## <a name="cannot-find-microphone"></a>Nie można znaleźć mikrofonu

- Najpierw sprawdź, czy tablica mikrofonów jest wyliczona w Device Manager.
- Jeśli urządzenie jest wyliczane i działa prawidłowo w systemie Windows, może to być problem, który po aktualizacji oprogramowania układowego systemu Windows przypisał inny identyfikator kontenera do aparatu.
- Możesz spróbować zresetować go, przechodząc do Device Manager, klikając prawym przyciskiem myszy pozycję "tablica mikrofonów Azure urządzenia Kinect" i wybierając pozycję "Odinstaluj urządzenie". Po zakończeniu odłączania i ponownego dołączania czujnika.

    ![Tablica MIC usługi Azure urządzenia Kinect](./media/resources/mic-not-found.png)

- Po ponownym uruchomieniu programu Azure urządzenia Kinect Viewer i spróbuj ponownie.

## <a name="device-firmware-update-issues"></a>Problemy z aktualizacją oprogramowania układowego urządzenia

- Jeśli po aktualizacji nie zgłoszono poprawnego numeru wersji, może być konieczne włączenie tego urządzenia.
- Jeśli aktualizacja oprogramowania układowego zostanie przerwana, może ulec uszkodzeniu i nie można jej wyliczyć. Odłącz i ponownie podłącz urządzenie, a następnie poczekaj 60 sekund, aby sprawdzić, czy może on zostać odzyskany.
Jeśli nie, wykonaj [zresetowanie do ustawień fabrycznych](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Problemy z jakością obrazu

- Uruchom [usługę Azure urządzenia Kinect Viewer](azure-kinect-viewer.md) i sprawdź pozycjonowanie urządzenia pod kątem zakłóceń lub jeśli czujnik jest zablokowany lub soczewki jest zanieczyszczone.
- Spróbuj użyć innych trybów operacyjnych, aby zawęzić w dół w przypadku wystąpienia problemu w określonym trybie.
- Aby udostępnić problemy z jakością obrazu zespołem, możesz:

1) Wypełnij widok wstrzymania w [usłudze Azure urządzenia Kinect Viewer](azure-kinect-viewer.md) i Zrób zrzut ekranu lub
2) Utwórz nagranie przy użyciu [usługi Azure urządzenia Kinect Recorder](azure-kinect-recorder.md), na przykład `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Niespójne lub nieoczekiwane sygnatury czasowe urządzenia

Wywołanie ```k4a_device_set_color_control``` może tymczasowo wywołać zmiany czasu na urządzeniu, które może potrwać kilka przechwycenia. Należy unikać wywoływania interfejsu API w pętli przechwytywania obrazu, aby uniknąć resetowania wewnętrznego obliczeń czasu dla każdego nowego obrazu. Zamiast tego należy wywołać interfejs API przed uruchomieniem aparatu lub po prostu zmienić wartość w pętli przechwytywania obrazu. W szczególności należy unikać wywoływania ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Zgodność kontrolera hosta USB3

Jeśli urządzenie nie jest wyliczane w Menedżerze urządzeń, może to być spowodowane tym, że jest on podłączony do nieobsługiwanego kontrolera USB3. 

W przypadku platformy Azure urządzenia Kinect DK w systemach **Windows, Intel**, **Texas Instruments (TI)** i **Renesas** są *jedynymi obsługiwanymi kontrolerami hosta*. Zestaw Azure urządzenia Kinect SDK na platformach systemu Windows korzysta z ujednoliconego identyfikatora kontenera i musi obejmować urządzenia USB 2,0 i 3,0, aby zestaw SDK mógł znaleźć głębokość, kolor i urządzenia audio, które znajdują się fizycznie na tym samym urządzeniu. W systemie Linux może być obsługiwane więcej kontrolerów hosta, ponieważ platforma ta jest zależna od identyfikatora kontenera i większej liczby numerów seryjnych urządzeń. 

Temat kontrolerów hosta USB jest jeszcze bardziej skomplikowany, gdy na komputerze jest zainstalowany więcej niż jeden kontroler hosta. Gdy kontrolery hosta są mieszane, użytkownik może napotkać problemy, w których niektóre porty działają prawidłowo i inne nie działają. W zależności od tego, jak porty są połączone z przypadkiem, mogą zostać wyświetlone wszystkie porty frontonu z usługą Azure urządzenia Kinect

**System Windows:** Aby dowiedzieć się, jaki kontroler hosta został otwarty Device Manager

1. Wyświetl > urządzenia według typu 
2. Dzięki połączeniu z usługą Azure urządzenia Kinect wybierz kamerę — >Azure urządzenia Kinect
3. Wyświetlanie > urządzeń według połączeń

![Rozwiązywanie problemów z portem USB](./media/resources/usb-troubleshooting.png)

Aby lepiej zrozumieć, który port USB jest połączony na komputerze, Powtórz te kroki dla każdego portu USB podczas łączenia usługi Azure urządzenia Kinect DK z różnymi portami USB na komputerze.

## <a name="depth-camera-auto-powers-down"></a>Zmniejsz uprawnienia autodostrajania aparatu

Laser używany przez kamerę głębokości do obliczania danych głębi obrazu ma ograniczoną cykl życia. Aby zmaksymalizować okres istnienia laserów, aparat głębokości będzie wykrywał, kiedy dane głębokości nie są używane. Głębokość aparatu w przypadku przesyłania strumieniowego urządzenia przez kilka minut, ale komputer hosta nie odczytuje danych. Ma to również wpływ na synchronizację wielu urządzeń, w której urządzenia podrzędne są uruchamiane w stanie, w którym kamera głębi jest przesyłana strumieniowo i głębokości ramki są aktywnie pomocne podczas oczekiwania na uruchomienie synchronizacji przechwytywania przez urządzenie główne. Aby uniknąć tego problemu w scenariuszach przechwytywania wielu urządzeń, upewnij się, że urządzenie główne jest uruchamiane w ciągu minuty od pierwszego uruchomienia podrzędnego. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Używanie zestawu SDK śledzenia treści z Unreal

Aby użyć zestawu SDK śledzenia treści z Unreal, upewnij się, że dodano `<SDK Installation Path>\tools` do zmiennej środowiskowej `PATH` i skopiowano do `dnn_model_2_0.onnx` `cudnn64_7.dll` `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Korzystanie z usługi Azure urządzenia Kinect w systemie bezobsługowym systemu Linux

Aparat głębokości usługi Azure urządzenia Kinect w systemie Linux używa technologii OpenGL. Oprogramowanie OpenGL wymaga wystąpienia okna, które wymaga połączenia monitora z systemem. Obejście tego problemu jest następujące:

1. Włącz automatyczne logowanie dla konta użytkownika, którego planujesz używać. Zapoznaj się z [tym](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) artykułem, aby uzyskać instrukcje dotyczące włączania automatycznego logowania.
2. Wyłącz system, Odłącz monitor i Włącz system. Automatyczne logowanie wymusza utworzenie sesji x-Server.
3. Nawiązywanie połączenia za pośrednictwem protokołu SSH i Ustawianie zmiennej ENV `export DISPLAY=:0`
4. Uruchom aplikację Azure urządzenia Kinect.

Narzędzie [xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) może być używane do natychmiastowego blokowania ekranu po automatycznym logowaniu. Dodaj następujące polecenie do aplikacji startowej lub usługi systemowej:

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>Brakująca Dokumentacja języka C#

Dokumentacja dotycząca zestawu czujników SDK C# znajduje się [tutaj](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html).

Dokumentacja zestawu SDK śledzenia treści w języku C# znajduje się [tutaj](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html).

## <a name="specifying-onnx-runtime-execution-environment"></a>Określanie środowiska wykonawczego środowiska uruchomieniowego ONNX

Zestaw SDK śledzenia treści obsługuje środowiska CPU, CUDA, DirectML (tylko system Windows) i TensorRT Execution do wnioskowania modelu szacowania ułożenia. `K4ABT_TRACKER_PROCESSING_MODE_GPU`Domyślnie cuda wykonywanie w systemach Linux i DirectML w systemie Windows. Dodano trzy dodatkowe tryby do wybierania określonych środowisk wykonywania: `K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA` , `K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML` i `K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT` .

Środowisko uruchomieniowe ONNX zawiera zmienne środowiskowe do sterowania buforowaniem modelu TensorRT. Zalecane wartości to:
- ORT_TENSORRT_ENGINE_CACHE_ENABLE = 1 
- ORT_TENSORRT_ENGINE_CACHE_PATH = "nazwa_ścieżki"

Przed rozpoczęciem śledzenia treści należy utworzyć folder.

Środowisko wykonawcze TensorRT obsługuje zarówno operacji FP32 (domyślne), jak i FP16. FP16 Handely ~ wzrost wydajności dla minimalnych zmniejszeń dokładności. Aby określić FP16:
- ORT_TENSORRT_FP16_ENABLE = 1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>Wymagane biblioteki DLL dla środowisk wykonywania środowiska uruchomieniowego ONNX

|Tryb      | CUDA 11,1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| Procesor CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc — builtins64_111 |                      |                      |

## <a name="next-steps"></a>Następne kroki

[Więcej informacji pomocy technicznej](support.md)
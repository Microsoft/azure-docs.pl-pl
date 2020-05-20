---
title: Rozwiązywanie problemów
description: Informacje dotyczące rozwiązywania problemów z renderowaniem zdalnym platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b518b2b92ba6d2529ffdefce754a3b29b74fb21b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674291"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Na tej stronie wymieniono typowe problemy zakłócające zdalne renderowanie na platformie Azure i sposoby ich rozwiązywania.

## <a name="client-cant-connect-to-server"></a>Klient nie może nawiązać połączenia z serwerem

Upewnij się, że zapory (na urządzeniu, wewnątrz routerów itp.) nie blokują następujących portów:

* **50051 (TCP)** — wymagane do początkowego połączenia (uzgadnianie http)
* **8266 (TCP + UDP)** — wymagane do transferu danych
* **5000 (TCP)**, **5433 (tcp)**, **8443 (TCP)** — wymagane dla [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Błąd "Rozłączono: VideoFormatNotAvailable"

Sprawdź, czy procesor GPU obsługuje sprzętowe dekodowanie wideo. Zobacz [komputer deweloperski](../overview/system-requirements.md#development-pc).

Jeśli pracujesz na laptopie z dwoma procesorami GPU, istnieje możliwość, że procesor GPU, który jest uruchamiany domyślnie, nie zapewnia funkcji sprzętowego dekodowania wideo. Jeśli tak, spróbuj wymusić, aby aplikacja korzystała z innego procesora GPU. Jest to często możliwe w ustawieniach sterownika procesora GPU.

## <a name="h265-codec-not-available"></a>Koder-dekoder H265 niedostępny

Istnieją dwa powody, dla których serwer może odmówić połączenia z **niedostępnym koderem-dekoder** .

**Koder-dekoder H265 nie jest zainstalowany:**

Najpierw należy zainstalować **rozszerzenia wideo HEVC** , jak wspomniano w sekcji [oprogramowanie](../overview/system-requirements.md#software) wymagań systemowych.

Jeśli nadal występują problemy, upewnij się, że karta graficzna obsługuje H265 i że masz zainstalowany najnowszy sterownik grafiki. Zapoznaj się z sekcją [komputer deweloperski](../overview/system-requirements.md#development-pc) wymagania systemowe dotyczące informacji specyficznych dla dostawcy.

**Koder-dekoder jest zainstalowany, ale nie można go użyć:**

Przyczyną tego problemu jest nieprawidłowe ustawienie zabezpieczeń w bibliotekach DLL. Ten problem nie jest manifestem podczas próby oglądania filmów wideo zakodowanych za pomocą H265. Ponowne zainstalowanie dekodera nie rozwiązuje problemu. Zamiast tego wykonaj następujące czynności:

1. Otwórz **program PowerShell z uprawnieniami administratora** i uruchom

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    To polecenie powinno wyprowadzić `InstallLocation` koder-dekoder, podobnie jak:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Otwórz ten folder w Eksploratorze Windows
1. Powinien istnieć podfolder **x86** i **x64** . Kliknij prawym przyciskiem myszy jeden z folderów i wybierz polecenie **Właściwości**
    1. Wybierz kartę **zabezpieczenia** , a następnie kliknij przycisk Ustawienia **Zaawansowane** .
    1. Kliknij przycisk **Zmień** dla **właściciela**
    1. Wpisz **administratorów** w polu tekstowym
    1. Kliknij przycisk **Sprawdź nazwy** i **OK**
1. Powtórz powyższe kroki dla drugiego folderu
1. Powtórz powyższe kroki dla każdego pliku DLL w obu folderach. Dostępne są cztery biblioteki DLL.

Aby sprawdzić, czy ustawienia są teraz poprawne, zrób to dla każdej z czterech bibliotek DLL:

1. Wybierz **właściwości > zabezpieczenia > Edytuj**
1. Zapoznaj się z listą wszystkich **grup/użytkowników** i upewnij się, że każda z nich ma **& wykonaj** prawo do odczytu (znacznik wyboru w kolumnie **Zezwalaj** musi być ustawiony na osi).

## <a name="low-video-quality"></a>Niska jakość wideo

Jakość wideo może zostać naruszona przez jakość sieci lub brak kodera wideo H265.

* Zapoznaj się z instrukcjami, aby [zidentyfikować problemy z siecią](#unstable-holograms).
* Zapoznaj się z [wymaganiami systemowymi](../overview/system-requirements.md#development-pc) dotyczącymi instalowania najnowszego sterownika grafiki.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Wideo zarejestrowane przy użyciu MRC nie odzwierciedla jakości aktywnego środowiska

Film wideo może być zarejestrowany w usłudze HoloLens za pomocą [funkcji przechwytywania rzeczywistości mieszanej (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Jednak wyniki wideo mają gorszą jakość niż środowisko na żywo z dwóch powodów:
* Szybkość wideo jest ograniczona do 30 Hz, a nie do 60 Hz.
* Obrazy wideo nie przechodzą przez krok przetwarzania zachodzącego [projektu](../overview/features/late-stage-reprojection.md) , więc film wideo wygląda na choppier.

Oba są nieodłącznymi ograniczeniami techniki nagrywania.

## <a name="black-screen-after-successful-model-loading"></a>Czarny ekran po pomyślnym załadowaniu modelu

Jeśli masz połączenie z środowiskiem uruchomieniowym renderowania i pomyślnie załadowano model, ale zobaczysz tylko czarny ekran, może to mieć kilka różnych przyczyn.

Zalecamy przetestowanie następujących rzeczy przed bardziej szczegółowymi analizami:

* Czy jest zainstalowany koder-dekoder H265? Mimo że powinno nastąpić powrót do kodera-dewielokrotna h264ego, występują przypadki, w których ta rezerwa nie działała prawidłowo. Zapoznaj się z [wymaganiami systemowymi](../overview/system-requirements.md#development-pc) dotyczącymi instalowania najnowszego sterownika grafiki.
* W przypadku korzystania z projektu środowiska Unity Zamknij środowisko Unity, Usuń *bibliotekę* tymczasową i foldery *obj* w katalogu projektu i ponownie załaduj/Skompiluj projekt. W niektórych przypadkach dane w pamięci podręcznej spowodowały, że próbka nie działa prawidłowo, bez oczywistych przyczyn.

Jeśli te dwa kroki nie były pomocne, należy sprawdzić, czy ramki wideo są odbierane przez klienta, czy nie. Można to zrobić programowo, jak wyjaśniono w rozdziale [kwerendy wydajności po stronie serwera](../overview/features/performance-queries.md) . `FrameStatistics struct`Zawiera element członkowski, który wskazuje liczbę ramek wideo, które zostały odebrane. Jeśli ta liczba jest większa niż 0 i rośnie wraz z upływem czasu, klient odbiera rzeczywiste ramki wideo z serwera. W związku z tym musi być problemem po stronie klienta.

### <a name="common-client-side-issues"></a>Typowe problemy po stronie klienta

**Model przekracza limity wybranej maszyny wirtualnej, w tym maksymalną liczbę wielokątów:**

Zapoznaj się z określonymi [ograniczeniami rozmiaru maszyny wirtualnej](../reference/limits.md#overall-number-of-polygons).

**Model nie znajduje się w widoku frustum:**

W wielu przypadkach model jest wyświetlany poprawnie, ale znajduje się poza kamerą frustum. Typowym powodem jest to, że model został wyeksportowany z daleko wyśrodkowanego przedziału. Pomaga w programistycznym zbadaniu pola powiązanego z modelem i wizualizowania pola z użyciem aparatu Unity jako pola wiersza lub drukowania jego wartości w dzienniku debugowania.

Ponadto proces konwersji generuje [wyjściowy plik JSON](../how-tos/conversion/get-information.md) wraz ze przekonwertowanym modelem. Aby debugować problemy dotyczące pozycjonowania modelu, warto zajrzeć do `boundingBox` wpisu w [sekcji outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Pole ograniczenia jest opisane jako `min` `max` pozycja i w przestrzeni 3D, w metrach. Dlatego Współrzędna 1000,0 oznacza, że nie jest to 1 Kilometer od początku.

W przypadku tego pola ograniczenia mogą występować dwa problemy, które prowadzą do niewidocznej geometrii:
* **Pole może być daleko od środka**, więc obiekt jest obcinany całkowicie ze względu na przycinanie do odległej płaszczyzny. `boundingBox`Wartości w tym przypadku wyglądać następująco: `min = [-2000, -5,-5], max = [-1990, 5,5]` , przy użyciu dużego przesunięcia na osi x jako przykładu. Aby rozwiązać ten problem, należy włączyć `recenterToOrigin` opcję w [konfiguracji konwersji modelu](../how-tos/conversion/configure-model-conversion.md).
* **Pole może być wyśrodkowane, ale zamówienia wielkości są zbyt duże**. Oznacza to, że podczas gdy aparat zostanie uruchomiony w środku modelu, jego geometria jest obcinana we wszystkich kierunkach. Typowe `boundingBox` wartości w tym przypadku wyglądać następująco: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Przyczyną tego typu problemu jest zwykle niezgodność skali jednostki. Aby skompensować, należy określić [wartość skalowania podczas konwersji](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) lub oznaczyć Model źródłowy odpowiednimi jednostkami. Skalowanie może być również stosowane do węzła głównego podczas ładowania modelu w czasie wykonywania.

**Potok renderowania aparatu Unity nie obejmuje punktów zaczepienia renderowania:**

Renderowanie zdalne na platformie Azure jest podłączane do potoku renderowania aparatu Unity, aby wykonać transkompozycję ramki z filmem wideo i przeprojektować. Aby sprawdzić, czy te punkty zaczepie istnieją, Otwórz *okno menu > analizy > debuger ramek*. Włącz ją i upewnij się, że `HolographicRemotingCallbackPass` w potoku istnieją dwa wpisy:

![Debuger ramki aparatu Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Kod Unity korzystający z interfejsu API renderowania zdalnego nie kompiluje

### <a name="use-debug-when-compiling-for-unity-editor"></a>Użyj debugowania podczas kompilowania dla edytora Unity

Zmień *typ kompilacji* rozwiązania Unity na **Debuguj**. Podczas testowania ARR w edytorze aparatu Unity definicja `UNITY_EDITOR` jest dostępna tylko w kompilacjach "debug". Należy zauważyć, że jest to niepowiązane z typem kompilacji używanym dla [wdrożonych aplikacji](../quickstarts/deploy-to-hololens.md), w którym należy preferować kompilacje "Release".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Błędy kompilacji podczas kompilowania próbek aparatu Unity 2

Wystąpił błąd fałszywe podczas próby skompilowania przykładów aparatu Unity (Szybki Start, ShowCaseApp,..) dla urządzenia HoloLens 2. Program Visual Studio nie może kopiować niektórych plików poza nimi. W przypadku wystąpienia tego problemu:
* Usuń wszystkie pliki tymczasowe aparatu Unity z projektu i spróbuj ponownie. Oznacza to, że Zamknij Unity, Usuń *bibliotekę* tymczasową i foldery *obj* w katalogu projektu i ponownie załaduj/Skompiluj projekt.
* Upewnij się, że projekty znajdują się w katalogu na dysku z odpowiednio krótką ścieżką, ponieważ krok kopiowania czasami wydaje się być przyczyną problemów z długimi nazwami plików.
* Jeśli to nie pomoże, może to być, że usługa MS sens przeszkadza w kroku kopiowania. Aby skonfigurować wyjątek, Uruchom to polecenie rejestru z wiersza polecenia (wymaga uprawnień administratora):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Niestabilne hologramy

W przypadku gdy renderowane obiekty pozornie są przenoszone wraz z przesunięciami, mogą wystąpić problemy związane z *reprojektem późnego etapu* (LSR). Zapoznaj się z sekcją dotyczącą [przemieszczenia na późnym etapie](../overview/features/late-stage-reprojection.md) w celu uzyskania wskazówek dotyczących sposobu podejścia do takiej sytuacji.

Kolejną przyczyną niestabilnych hologramów (Wobbling, zniekształcenia, zakłócenia lub hologramów przeskoków) może być słaba łączność sieciowa, w szczególności niewystarczająca przepustowość sieci lub zbyt wysokie opóźnienia. Dobrym wskaźnikiem jakości połączenia sieciowego jest wartość [Statystyka wydajności](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` . Ponownie używane ramki wskazują sytuacje, w których stara ramka wideo jest wymagana ponownie na stronie klienta, ponieważ nie jest dostępna nowa ramka wideo — na przykład ze względu na utratę pakietów lub z powodu różnic w opóźnieniu sieci. Jeśli `ARRServiceStats.VideoFramesReused` jest często większa od zera, oznacza to problem z siecią.

Inna wartość, która ma być wyszukiwana `ARRServiceStats.LatencyPoseToReceiveAvg` . Powinien on być spójny poniżej 100 ms. Jeśli zobaczysz wyższe wartości, oznacza to, że masz połączenie z centrum danych zbyt daleko.

Aby zapoznać się z listą potencjalnych środków zaradczych, zobacz [wytyczne dotyczące łączności sieciowej](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](../overview/system-requirements.md)
* [Wymagania dotyczące sieci](../reference/network-requirements.md)

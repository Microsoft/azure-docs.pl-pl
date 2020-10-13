---
title: Rozwiązywanie problemów
description: Informacje dotyczące rozwiązywania problemów z renderowaniem zdalnym platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: e8de33e7417ab6421792d341474c320a5f63423b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803827"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Na tej stronie wymieniono typowe problemy zakłócające zdalne renderowanie na platformie Azure i sposoby ich rozwiązywania.

## <a name="cant-link-storage-account-to-arr-account"></a>Nie można połączyć konta magazynu z kontem ARR

Czasami podczas [łączenia konta magazynu](../how-tos/create-an-account.md#link-storage-accounts) nie ma na liście konta renderowania zdalnego. Aby rozwiązać ten problem, przejdź do konta ARR w Azure Portal i wybierz pozycję **tożsamość** w grupie **Ustawienia** po lewej stronie. Upewnij się, że **stan** jest ustawiony na wartość **włączone**.
![Debuger ramki aparatu Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Klient nie może nawiązać połączenia z serwerem

Upewnij się, że zapory (na urządzeniu, wewnątrz routerów itp.) nie blokują portów wymienionych w [wymaganiach systemowych](../overview/system-requirements.md#network-ports).

## <a name="error-disconnected-videoformatnotavailable"></a>Błąd " `Disconnected: VideoFormatNotAvailable` "

Sprawdź, czy procesor GPU obsługuje sprzętowe dekodowanie wideo. Zobacz [komputer deweloperski](../overview/system-requirements.md#development-pc).

Jeśli pracujesz na laptopie z dwoma procesorami GPU, istnieje możliwość, że procesor GPU, który jest uruchamiany domyślnie, nie zapewnia funkcji sprzętowego dekodowania wideo. Jeśli tak, spróbuj wymusić, aby aplikacja korzystała z innego procesora GPU. Jest to często możliwe w ustawieniach sterownika procesora GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Pobieranie stanu sesji/konwersji nie powiodło się

Wysyłanie poleceń interfejsu API REST zbyt często spowoduje, że serwer zostanie ograniczony i ostatecznie nie zwraca błędu. Kod stanu HTTP w przypadku dławienia ma 429 ("zbyt wiele żądań"). Zgodnie z zasadą dla elementu kciuka należy mieć opóźnienie **5-10 sekund między kolejnymi wywołaniami**.

Należy zauważyć, że ten limit nie ma wpływu na wywołania interfejsu API REST, gdy jest wywoływana bezpośrednio, ale także ich odpowiedników w języku C#/C + +, takich jak `Session.GetPropertiesAsync` , `Session.RenewAsync` , lub `Frontend.GetAssetConversionStatusAsync` .

W przypadku korzystania z funkcji ograniczania przepustowości po stronie serwera należy zmienić kod, tak aby wywołania były rzadziej używane. Serwer zresetuje stan ograniczania co minutę, więc można bezpiecznie ponownie uruchomić kod po minucie.

## <a name="h265-codec-not-available"></a>Koder-dekoder H265 niedostępny

Istnieją dwa powody, dla których serwer może odmówić połączenia z `codec not available` błędem.

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

Zobacz określone [limity rozmiaru serwera](../reference/limits.md#overall-number-of-polygons).

**Model nie znajduje się w frustumu aparatu:**

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

Renderowanie zdalne na platformie Azure jest podłączane do potoku renderowania aparatu Unity, aby wykonać transkompozycję ramki z filmem wideo i przeprojektować. Aby sprawdzić, czy te punkty zaczepie istnieją, otwórz menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Włącz ją i upewnij się, że `HolographicRemotingCallbackPass` w potoku istnieją dwa wpisy:

![Potok renderowania aparatu Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Wzór szachownicy jest renderowany po załadowaniu modelu

Jeśli renderowany obraz wygląda następująco: ![ zrzut ekranu pokazuje siatkę czarno-białych kwadratów z menu Narzędzia.](../reference/media/checkerboard.png)
następnie moduł renderujący trafi [limity dla rozmiaru standardowej konfiguracji](../reference/vm-sizes.md). Aby wyeliminować ograniczenia, należy przełączyć się do rozmiaru konfiguracji **Premium** lub zmniejszyć liczbę widocznych wielokątów.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Renderowany obraz w aparacie Unity jest odwrócony do góry

Upewnij się, że postępuj zgodnie z [samouczkiem aparatu Unity: dokładnie Przeglądaj modele zdalne](../tutorials/unity/view-remote-models/view-remote-models.md) . Obraz z góry nogami wskazuje, że środowisko Unity jest wymagane do utworzenia celu renderowania poza ekranem. To zachowanie nie jest obecnie obsługiwane i tworzy ogromny wpływ na wydajność w przypadku urządzeń HoloLens 2.

Przyczyną tego problemu może być MSAA, HDR lub włączenie procesu post. Upewnij się, że profil niskiej jakości został wybrany i ustawiony jako domyślny w aparacie Unity. Aby to zrobić, przejdź do pozycji *Edytuj ustawienia projektu >... >*.

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
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Kompilacje Arm64 dla projektów Unity nie powiodły się, ponieważ brakuje AudioPluginMsHRTF.dll

Obiekt `AudioPluginMsHRTF.dll` for Arm64 został dodany do pakietu *Windows Mixed Reality* *(com. Unity. XR. windowsmr. Metro)* w wersji 3.0.1. Upewnij się, że masz zainstalowaną wersję 3.0.1 lub nowszą za pośrednictwem Menedżera pakietów aparatu Unity. Na pasku menu aparatu Unity przejdź do *okna > Menedżer pakietów* i Wyszukaj pakiet *rzeczywistości mieszanej systemu Windows* .

## <a name="native-c-based-application-does-not-compile"></a>Natywna aplikacja oparta na języku C++ nie kompiluje

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Błąd "nie znaleziono biblioteki" dla aplikacji platformy UWP lub biblioteki DLL

W pakiecie NuGet języka C++ istnieje `microsoft.azure.remoterendering.Cpp.targets` plik pliku, który definiuje, który z elementów binarnych ma być używany. Aby zidentyfikować `UWP` warunki w pliku, należy sprawdzić `ApplicationType == 'Windows Store'` . Dlatego należy upewnić się, że ten typ jest ustawiony w projekcie. Tak jak w przypadku tworzenia aplikacji platformy UWP lub biblioteki DLL za pomocą Kreatora projektu programu Visual Studio.

## <a name="unstable-holograms"></a>Niestabilne hologramy

W przypadku gdy renderowane obiekty pozornie są przenoszone wraz z przesunięciami, mogą wystąpić problemy związane z *reprojektem późnego etapu* (LSR). Zapoznaj się z sekcją dotyczącą [przemieszczenia na późnym etapie](../overview/features/late-stage-reprojection.md) w celu uzyskania wskazówek dotyczących sposobu podejścia do takiej sytuacji.

Kolejną przyczyną niestabilnych hologramów (Wobbling, zniekształcenia, zakłócenia lub hologramów przeskoków) może być słaba łączność sieciowa, w szczególności niewystarczająca przepustowość sieci lub zbyt wysokie opóźnienia. Dobrym wskaźnikiem jakości połączenia sieciowego jest wartość [Statystyka wydajności](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` . Ponownie używane ramki wskazują sytuacje, w których stara ramka wideo jest wymagana ponownie na stronie klienta, ponieważ nie jest dostępna nowa ramka wideo — na przykład ze względu na utratę pakietów lub z powodu różnic w opóźnieniu sieci. Jeśli `ARRServiceStats.VideoFramesReused` jest często większa od zera, oznacza to problem z siecią.

Inna wartość, która ma być wyszukiwana `ARRServiceStats.LatencyPoseToReceiveAvg` . Powinien on być spójny poniżej 100 ms. Wyświetlanie wyższych wartości może wskazywać, że połączenie z centrum danych jest zbyt daleko.

Aby zapoznać się z listą potencjalnych środków zaradczych, zobacz [wytyczne dotyczące łączności sieciowej](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Walka Z

Chociaż ARR oferuje [funkcje ograniczania środków zaradczych](../overview/features/z-fighting-mitigation.md), w przypadku, gdy usługa walki może nadal się pojawić na scenie. Ten przewodnik ma na celu rozwiązywanie problemów związanych z pozostałymi problemami.

### <a name="recommended-steps"></a>Zalecane czynności

Użyj poniższego przepływu pracy, aby ograniczyć walkę z:

1. Przetestuj scenę z domyślnymi ustawieniami ARR (z ograniczeniami do walki z)

1. Wyłącz środki zaradcze dla walki za pośrednictwem [interfejsu API](../overview/features/z-fighting-mitigation.md) 

1. Zmień kamerę blisko zakresu

1. Rozwiązywanie problemów z sceną za pośrednictwem następnej sekcji

### <a name="investigating-remaining-z-fighting"></a>Badanie pozostałej części z-walka

Jeśli powyższe kroki zostały wyczerpane, a pozostała walka z nią nie zostanie zaakceptowana, należy zbadać podstawową przyczynę z walki z. Jak zostało to określone na [stronie funkcji ograniczenia dotyczącej walki z walką z](../overview/features/z-fighting-mitigation.md), istnieją dwa główne przyczyny dotyczące z-walk: utrata dokładności głębokości na dalekiej końcu zakresu głębokości oraz powierzchnie przecinające się podczas współcinania. Utrata dokładności głębokości jest matematyczną możliwością i można ją ograniczyć tylko w kroku 3. Współrzędne powierzchniowe wskazują na wadliwe źródło zasobów i są lepiej naprawione w danych źródłowych.

ARR zawiera funkcję służącą do określenia, czy powierzchnie mogą z-walka: [podświetlanie szachownicy](../overview/features/z-fighting-mitigation.md). Możesz również określić wizualnie, co powoduje przeprowadzenie walki z. Poniższa pierwsza animacja przedstawia przykład utraty dokładności na odległość, a drugi pokazuje przykład niemal widocznych powierzchni:

![Animacja pokazuje przykład zmniejszenia dokładności na odległość.](./media/depth-precision-z-fighting.gif)  ![Animacja pokazuje przykład niemal współrzędnych.](./media/coplanar-z-fighting.gif)

Porównaj te przykłady z walką z, aby określić przyczynę lub opcjonalnie wykonać ten przepływ pracy krok po kroku:

1. Umieść kamerę powyżej powierzchni do przeszukiwania, aby wyglądała bezpośrednio na powierzchni.
1. Wolno przesunąć kamerę do tyłu, poza powierzchnie.
1. Jeśli walka z i jest widoczna przez cały czas, powierzchnie są doskonale współrzędnymi. 
1. Jeśli walka z jest widoczna w większości czasu, powierzchnie są niemal współrzędnymi.
1. Jeśli walka z jest widoczna tylko od razu, powód nie ma dokładności głębokości.

Współrzędne powierzchniowe mogą mieć różne przyczyny:

* Obiekt został zduplikowany przez eksportowanie aplikacji ze względu na błąd lub różne podejścia do przepływu pracy.

    Sprawdź te problemy, korzystając z obsługi odpowiednich aplikacji i aplikacji.

* Powierzchnie są duplikowane i przerzucane, aby pojawiły się podwójnie w modułach renderowania, które używają przedniej lub usuwania z tyłu.

    Importowanie przy użyciu [konwersji modelu](../how-tos/conversion/model-conversion.md) określa główną część modelu. Domyślna wartość jest przyjmowana domyślnie. Powierzchnia będzie renderowana jako cienka ściana z fizycznie prawidłowym oświetleniem z obu stron. Pojedyncze wartości mogą być implikowane przez flagi w źródłowym elemencie zawartości lub jawnie wymuszone podczas [konwersji modelu](../how-tos/conversion/model-conversion.md). Dodatkowo, ale opcjonalnie, [tryb](../overview/features/single-sided-rendering.md) jednostronny można ustawić na "normalny".

* Obiekty przecinają się w zasobach źródłowych.

     Obiekty przekształcone w taki sposób, że niektóre z nich nakładają się również na tworzenie z-walka. Ten problem można także utworzyć, przenosząc części drzewa sceny w zaimportowanej scenie.

* Powierzchnie są celowo całkowicie do dotknięcia, takich jak wyróżnianie licencji lub tekst na ściany.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Artefakty grafiki wykorzystujące wieloprzebiegowe renderowanie stereo w natywnych aplikacjach C++

W niektórych przypadkach niestandardowe natywne aplikacje języka C++, które używają trybu renderowania wieloprzebiegowego dla zawartości lokalnej (renderowanie do lewego i prawego oka w osobnych przebiegach) po wywołaniu [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) może wyzwolić usterkę sterownika. Usterka powoduje niedeterministyczne błędy rasteryzacji, co sprawia, że poszczególne Trójkąty lub części trójkątów zawartości lokalnej są losowo znikane. Ze względu na wydajność zaleca się, aby nie renderować zawartości lokalnej z bardziej nowoczesnym techniką renderowania stereo z wieloma przebiegami, na przykład przy użyciu **SV_RenderTargetArrayIndex**.

## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](../overview/system-requirements.md)
* [Wymagania dotyczące sieci](../reference/network-requirements.md)

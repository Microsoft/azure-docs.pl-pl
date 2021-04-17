---
title: Rozwiązywanie problemów
description: Informacje dotyczące rozwiązywania problemów z Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575347"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Ta strona zawiera listę typowych problemów zakłócających Azure Remote Rendering oraz sposoby ich rozwiązywania.

## <a name="cant-link-storage-account-to-arr-account"></a>Nie można połączyć konta magazynu z kontem usługi ARR

Czasami podczas [łączenia konta magazynu konto](../how-tos/create-an-account.md#link-storage-accounts) Remote Rendering nie jest wyświetlane. Aby rozwiązać ten problem, przejdź do konta ARR w Azure Portal i wybierz pozycję **Tożsamość** w **grupie** Ustawienia po lewej stronie. Upewnij **się, że dla** ustawienia Stan ustawiono **wartość Wł.**.
![Debuger ramek aparatu Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Klient nie może nawiązać połączenia z serwerem

Upewnij się, że zapory (na urządzeniu, wewnątrz routerów itp.) nie blokują portów wymienionych w [wymaganiach systemowych](../overview/system-requirements.md#network-firewall).

## <a name="error-disconnected-videoformatnotavailable"></a>Błąd ' `Disconnected: VideoFormatNotAvailable` '

Sprawdź, czy procesor GPU obsługuje sprzętowe dekodowanie wideo. Zobacz [Development PC](../overview/system-requirements.md#development-pc).

Jeśli pracujesz na laptopie z dwoma procesorami GPU, istnieje możliwość, że procesor GPU, na których jest uruchomiony domyślnie, nie zapewnia funkcji dekodowania sprzętowego wideo. Jeśli tak, spróbuj wymusić na aplikacji użycie innego procesora GPU. Jest to często możliwe w ustawieniach sterownika procesora GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Pobieranie stanu sesji/konwersji kończy się niepowodzeniem

Zbyt częste wysyłanie poleceń interfejsu API REST spowoduje ograniczenie wydajności serwera i zwrócenie awarii. Kod stanu HTTP w przypadku ograniczania przepustowości to 429 ("zbyt wiele żądań"). Z reguły między kolejnymi wywołaniami powinno być opóźnienie o **wartości 5–10 sekund.**

Należy pamiętać, że ten limit ma wpływ nie tylko na wywołania interfejsu API REST, gdy są wywoływane bezpośrednio, ale także na ich odpowiedniki w języku C#/C++, takie `Session.GetPropertiesAsync` jak `Session.RenewAsync` , lub `Frontend.GetAssetConversionStatusAsync` .

Jeśli występuje ograniczanie przepustowości po stronie serwera, zmień kod, aby rzadziej wielokrotnie wykonać wywołania. Serwer będzie resetować stan ograniczania przepustowości co minutę, dzięki czemu można bezpiecznie ponownie uruchomić kod po minucie.

## <a name="h265-codec-not-available"></a>Koder-codec H265 jest niedostępny

Istnieją dwie przyczyny, dla których serwer może odmówić połączenia z powodu `codec not available` błędu.

**Koder-codec H265 nie jest zainstalowany:**

Najpierw należy zainstalować pakiet **Rozszerzenia wideo HEVC** jak wspomniano w sekcji [Oprogramowanie](../overview/system-requirements.md#software) wymagań systemowych.

Jeśli nadal występują problemy, upewnij się, że karta grafiki obsługuje technologię H265 i jest zainstalowany najnowszy sterownik graficzny. Zapoznaj się [z sekcją](../overview/system-requirements.md#development-pc) Development PC w sekcji wymagań systemowych, aby uzyskać informacje specyficzne dla dostawcy.

**Koder-koder-koder jest zainstalowany, ale nie można go używać:**

Przyczyną tego problemu jest nieprawidłowe ustawienie zabezpieczeń bibliotek DLL. Ten problem nie występuje podczas próby obejrzenia filmów wideo zakodowanych za pomocą H265. Ponowna instalacja koderów-koderów nie rozwiąże również problemu. Zamiast tego wykonaj następujące czynności:

1. Otwórz program **PowerShell z uprawnieniami administratora i** uruchom

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    To polecenie powinno zwrócić `InstallLocation` koder koder-koder, na przykład:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Otwórz ten folder w Eksplorator Windows
1. Powinien być **podfolder x86** i **x64.** Kliknij prawym przyciskiem myszy jeden z folderów i wybierz polecenie **Właściwości**
    1. Wybierz **kartę Zabezpieczenia** i kliknij **przycisk Ustawienia** zaawansowane
    1. Kliknij **pozycję Zmień** dla **właściciela**
    1. Wpisz **Administratorzy** w polu tekstowym
    1. Kliknij **pozycję Sprawdź nazwy i** przycisk **OK.**
1. Powtórz powyższe kroki dla innego folderu
1. Powtórz również powyższe kroki dla każdego pliku DLL w obu folderach. Powinny być w całości cztery biblioteki DLL.

Aby sprawdzić, czy ustawienia są teraz poprawne, zrób to dla każdej z czterech bibliotek DLL:

1. Wybierz **pozycję Właściwości > Security > Edytuj**
1. Przejdź przez listę wszystkich **grup/użytkowników** i upewnij  się, że każda z nich ma ustawiony  prawy & Odczyt (znacznik wyboru w kolumnie zezwalaj musi być oznaczany)

## <a name="low-video-quality"></a>Niska jakość wideo

Jakość wideo może zostać naruszona przez jakość sieci lub brakujący koder wideo H265.

* Zapoznaj się z krokami, aby [zidentyfikować problemy z siecią.](#unstable-holograms)
* Zobacz wymagania [systemowe dotyczące](../overview/system-requirements.md#development-pc) instalowania najnowszego sterownika grafiki.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Wideo zarejestrowane za pomocą mrC nie odzwierciedla jakości obsługi na żywo

Wideo można nagrać na urządzeniach HoloLens za [pośrednictwem Przechwytywanie rzeczywistości mieszanej (MRC).](/windows/mixed-reality/mixed-reality-capture-for-developers) Wynikowy film wideo ma jednak niższą jakość niż środowisko na żywo z dwóch powodów:
* Szybkość klatek wideo jest ograniczona do 30 Hz, a nie 60 Hz.
* Obrazy wideo nie przechodziły przez etap przetwarzania ponownego [przetwarzania](../overview/features/late-stage-reprojection.md) na późnym etapie, więc wygląda na to, że wideo jest bardziej ważne.

Oba te ograniczenia są związane z techniką rejestrowania.

## <a name="black-screen-after-successful-model-loading"></a>Czarny ekran po pomyślnym załadowaniu modelu

Jeśli masz połączenie ze środowiskiem uruchomieniowym renderowania i pomyślnie załadowano model, ale później zobaczysz tylko czarny ekran, może to mieć kilka odrębnych przyczyn.

Zalecamy przetestowanie następujących rzeczy przed wykonaniem bardziej szczegółowej analizy:

* Czy jest zainstalowany koder-koder-system H265? Mimo że powinien następować rezerwowy koder-koder H264, widzieliśmy przypadki, w których ten rezerwowy koder nie działał prawidłowo. Zobacz wymagania [systemowe dotyczące](../overview/system-requirements.md#development-pc) instalowania najnowszego sterownika grafiki.
* W przypadku korzystania z projektu aparatu  Unity zamknij unity, usuń tymczasową bibliotekę i *foldery obj* z katalogu projektu, a następnie ponownie załaduj/skompilowaj projekt. W niektórych przypadkach dane buforowane spowodowały, że próbka nie działała prawidłowo bez oczywistej przyczyny.

Jeśli te dwa kroki nie pomogły, konieczne jest określenie, czy ramki wideo są odbierane przez klienta, czy nie. Można to zbadać programowo, jak wyjaśniono w rozdziale zapytania dotyczące wydajności [po stronie](../overview/features/performance-queries.md) serwera. Ma `FrameStatistics struct` on członka, który wskazuje, ile ramek wideo zostało odebranych. Jeśli ta liczba jest większa niż 0 i zwiększa się wraz z czasem, klient odbiera rzeczywiste ramki wideo z serwera. W związku z tym musi to być problem po stronie klienta.

### <a name="common-client-side-issues"></a>Typowe problemy po stronie klienta

**Model przekracza limity wybranej maszyny wirtualnej, a w szczególności maksymalną liczbę wielokątów:**

Zobacz [limity rozmiaru określonego serwera.](../reference/limits.md#overall-number-of-polygons)

**Model nie znajduje się wewnątrz aparatu frustum:**

W wielu przypadkach model jest wyświetlany prawidłowo, ale znajduje się poza frustum aparatu. Częstą przyczyną jest to, że model został wyeksportowany z odsuwem od środka, dzięki czemu jest obcięty przez płaszczyznę przycinania aparatu. Ułatwia ono programowe wykonywanie zapytań o pole granicy modelu i wizualizowanie pola za pomocą aparatu Unity jako pola wiersza lub drukowanie jego wartości w dzienniku debugowania.

Ponadto proces konwersji generuje plik [wyjściowy JSON](../how-tos/conversion/get-information.md) wraz z przekonwertowanym modelem. Aby debugować problemy z pozycjonowaniem modelu, warto zaglądać do wpisu w sekcji `boundingBox` [outputStatistics :](../how-tos/conversion/get-information.md#the-outputstatistics-section)

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

Pole granicy jest opisane jako pozycja `min` i `max` w przestrzeni 3D, w metrach. Zatem współrzędna 1000,0 oznacza, że znajduje się 1 km od źródła.

Mogą być dwa problemy z tym polem granicy, które prowadzą do niewidocznej geometrii:
* **Pole może być daleko od środka,** więc obiekt jest całkowicie obcięty z powodu obcinania płaszczyzny. Wartości w tym przypadku będą wyglądać tak: , przy użyciu dużego przesunięcia na osi `boundingBox` `min = [-2000, -5,-5], max = [-1990, 5,5]` x jako przykładu w tym miejscu. Aby rozwiązać ten typ problemu, włącz `recenterToOrigin` opcję w [konfiguracji konwersji modelu](../how-tos/conversion/configure-model-conversion.md).
* **Pole może być wyśrodkowane, ale jego rządy wielkości są zbyt duże.** Oznacza to, że chociaż aparat zaczyna się na środku modelu, jego geometria jest obcinana we wszystkich kierunkach. Typowe `boundingBox` wartości w tym przypadku będą wyglądać tak: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Przyczyną tego typu problemu jest zazwyczaj niezgodność skali jednostek. Aby skompensować, określ [wartość skalowania podczas konwersji](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) lub oznacz model źródłowy prawidłowymi jednostkami. Skalowanie można również zastosować do węzła głównego podczas ładowania modelu w czasie wykonywania.

**Potok renderowania aparatu Unity nie zawiera zaczepień renderowania:**

Azure Remote Rendering się do potoku renderowania aparatu Unity, aby wykonać kompozycję ramek z wideo i wykonać ponowną reprojection. Aby sprawdzić, czy te elementy zaczepienia istnieją, otwórz menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Włącz ją i upewnij się, że w potoku znajdują się `HolographicRemotingCallbackPass` dwa wpisy:

![Potok renderowania aparatu Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Wzorzec tablicy kontrolnej jest renderowany po załadowaniu modelu

Jeśli renderowany obraz wygląda następująco: Zrzut ekranu przedstawia siatkę czarnych i białych ![ kwadratów z menu Narzędzia.](../reference/media/checkerboard.png)
następnie program renderuje limity [wielokątów dla standardowego rozmiaru konfiguracji](../reference/vm-sizes.md). Aby temu uniknąć, przełącz się na **rozmiar konfiguracji Premium** lub zmniejsz liczbę widocznych wielokątów.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Renderowany obraz w a unity jest do góry do góry

Pamiętaj, aby dokładnie wykonać kroki [samouczka aparatu Unity: wyświetlanie modeli](../tutorials/unity/view-remote-models/view-remote-models.md) zdalnych. Obraz do góry do góry wskazuje, że do utworzenia obiektu docelowego renderowania poza ekranem jest wymagane aparatu Unity. To zachowanie nie jest obecnie obsługiwane i ma ogromny wpływ na wydajność urządzenia HoloLens 2.

Przyczyny tego problemu mogą być następujące: MSAA, DOSTĘP lub włączenie przetwarzania po przetwarzaniu. Upewnij się, że wybrano profil o niskiej jakości i ustawiono go jako domyślny w a aparatu Unity. W tym celu przejdź do > *Ustawienia projektu... > Jakości.*

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Nie można skompilować kodu aparatu Unity przy użyciu interfejsu API Remote Rendering

### <a name="use-debug-when-compiling-for-unity-editor"></a>Używanie debugowania podczas kompilowania dla edytora aparatu Unity

Przełącz typ *kompilacji rozwiązania* Unity na **debugowanie**. Podczas testowania ARR w edytorze aparatu Unity definiowanie `UNITY_EDITOR` jest dostępne tylko w kompilacjach "Debuguj". Należy pamiętać, że nie ma to związku z typem kompilacji używanym dla wdrożonych [aplikacji,](../quickstarts/deploy-to-hololens.md)w którym należy preferować kompilacje "Release".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Błędy kompilacji podczas kompilowania przykładów aparatu Unity dla urządzenia HoloLens 2

Podczas próby skompilowania przykładów aparatu Unity (szybki start, ShowCaseApp, ..) dla urządzenia HoloLens 2 widzieliśmy nieużywalne błędy. Visual Studio się, że nie można skopiować niektórych plików, chociaż są dostępne. Jeśli ten problem zostanie rozwiązany:
* Usuń wszystkie tymczasowe pliki aparatu Unity z projektu i spróbuj ponownie. Oznacza to zamknięcie aparatu Unity, usunięcie biblioteki tymczasowej i *folderów obj* z katalogu projektu i załadowanie/skompilowanie projektu ponownie. 
* Upewnij się, że projekty znajdują się w katalogu na dysku ze względnie krótką ścieżką, ponieważ krok kopiowania czasami wydaje się mieć problemy z długimi nazwami plików.
* Jeśli to nie pomoże, może to być, że usługa MS Sense zakłóca krok kopiowania. Aby skonfigurować wyjątek, uruchom to polecenie rejestru z wiersza polecenia (wymaga uprawnień administratora):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Kompilacje Arm64 dla projektów Unity nie powiodły się, AudioPluginMsHRTF.dll brakujących kompilacji

Dla Arm64 został dodany do `AudioPluginMsHRTF.dll` *pakietu Windows Mixed Reality* *(com.unity.xr.windowsmr.beta)* w wersji 3.0.1. Upewnij się, że masz zainstalowaną wersję 3.0.1 lub nowszą za pośrednictwem interfejsu Menedżer pakietów Unity. Na pasku menu aparatu Unity przejdź do pozycji *Window > Menedżer pakietów* i poszukaj *pakietu Windows Mixed Reality* Unity.

## <a name="native-c-based-application-does-not-compile"></a>Natywna aplikacja oparta na języku C++ nie kompiluje się

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Błąd "Nie znaleziono biblioteki" dla aplikacji platformy uniwersalnej systemu Windows lub biblioteki DLL

Wewnątrz pakietu NuGet języka C++ znajduje się plik, który definiuje, którego z odmian `microsoft.azure.remoterendering.Cpp.targets` binarnych użyć. Aby zidentyfikować `UWP` , warunki w pliku sprawdzają, czy program ma . `ApplicationType == 'Windows Store'` Dlatego należy upewnić się, że ten typ jest ustawiony w projekcie. Powinno to mieć miejsce podczas tworzenia aplikacji platformy uniwersalnej systemu Windows lub biblioteki Dll Visual Studio kreatora projektu platformy UWP.

## <a name="unstable-holograms"></a>Niestabilne hologramy

W przypadku, gdy renderowane obiekty wydają się przenosić wraz z ruchami głowy, mogą wystąpić problemy z *reprojectioną* późnego etapu (LSR). Aby uzyskać wskazówki dotyczące podejścia do takiej sytuacji, zapoznaj się z sekcją ["Reprojection](../overview/features/late-stage-reprojection.md) późnego etapu".

Innym powodem niestabilnych hologramów (chybienia, warowania, zakłóceń lub skoków) może być słaba łączność sieciowa, w szczególności niewystarczająca przepustowość sieci lub zbyt duże opóźnienie. Dobrym wskaźnikiem jakości połączenia sieciowego jest wartość [statystyk wydajności](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused` . Ponownie użyte ramki wskazują sytuacje, w których stara ramka wideo musi zostać ponownie użyta po stronie klienta, ponieważ nie była dostępna nowa ramka wideo — na przykład ze względu na utratę pakietów lub zmiany opóźnienia sieci. Jeśli `ServiceStatistics.VideoFramesReused` wartość jest często większa niż zero, oznacza to problem z siecią.

Inną wartością, na które należy zwrócić uwagę, jest `ServiceStatistics.LatencyPoseToReceiveAvg` . Powinna ona być spójnie poniżej 100 ms. Wyświetlanie wyższych wartości może wskazywać, że masz połączenie z centrum danych, które jest za daleko.

Aby uzyskać listę potencjalnych środków zaradczych, zobacz [wytyczne dotyczące łączności sieciowej](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z- załagodnianie

Podczas gdy funkcja ARR [oferuje funkcję](../overview/features/z-fighting-mitigation.md)ograniczania ryzyka z-wywalczy, z-jących nadal może być wyświetlane w scenie. Ten przewodnik ma na celu rozwiązywanie pozostałych problemów.

### <a name="recommended-steps"></a>Zalecane czynności

Użyj następującego przepływu pracy, aby rozwiązać problemy z ograniczaniem:

1. Przetestuj scenę przy użyciu domyślnych ustawień ARR (środki zaradcze z włączona)

1. Wyłączanie ograniczania ryzyka z-mitigation za pośrednictwem interfejsu [API](../overview/features/z-fighting-mitigation.md) 

1. Zmień kamerę w pobliżu i najdalej na bliższy zakres

1. Rozwiązywanie problemów ze sceną za pośrednictwem następnej sekcji

### <a name="investigating-remaining-z-fighting"></a>Badanie pozostałej części walk z

Jeśli powyższe kroki zostały wyczerpane, a pozostałe czynności z są nieakceptowalne, należy zbadać podstawową przyczynę zrównowag. Jak podano na stronie funkcji ograniczania ryzyka [z,](../overview/features/z-fighting-mitigation.md)istnieją dwa główne powody ograniczania głębokości z: utrata dokładności głębokości na końcu zakresu głębokości i powierzchni, które przecinają się podczas współpłaszczyzowania. Utrata dokładności głębokości to matematyczna możliwość ograniczenia ryzyka tylko przez krok 3 powyżej. Powierzchnie współpłaszczyzowane wskazują na wadę zasobu źródłowego i są lepiej naprawione w danych źródłowych.

Funkcja ARR umożliwia określenie, czy powierzchnie mogą z-wywalczyć: [wyróżnianie tablicy kontrolnej](../overview/features/z-fighting-mitigation.md). Możesz również wizualnie określić, co powoduje zdjęcie z. Następująca pierwsza animacja przedstawia przykład utraty dokładności głębokości w odległości, a druga przedstawia przykład niemal współpłaszczyzowanych powierzchni:

![Animacja pokazuje przykład utraty dokładności głębokości w odległości.](./media/depth-precision-z-fighting.gif)  ![Animacja przedstawia przykład niemal współpłaszczyzowanych powierzchni.](./media/coplanar-z-fighting.gif)

Porównaj te przykłady z walką z, aby określić przyczynę lub opcjonalnie postępuj zgodnie z tym przepływem pracy krok po kroku:

1. Umieść kamerę nad powierzchniami typu z, aby patrzyła bezpośrednio na powierzchnię.
1. Wolno przesuwaj kamerę do tyłu, z dala od powierzchni.
1. Jeśli walkę z jest widoczna przez cały czas, powierzchnie są idealnie współpłaszczyzowane. 
1. Jeśli walkę z jest widoczna przez większość czasu, powierzchnie są niemal współpłaszczyzowane.
1. Jeśli walkę z jest widoczna tylko z bardzo odejmowania, przyczyną jest brak precyzji głębokości.

Powierzchnie współpłaszczyzowane mogą mieć różne przyczyny:

* Obiekt został zduplikowany przez aplikację eksportującą z powodu błędu lub różnych podejść do przepływu pracy.

    Sprawdź te problemy z odpowiednią obsługą aplikacji i aplikacji.

* Powierzchnie są duplikowane i przerzucane w taki sposób, aby pojawiały się dwustronnie w programach renderujących, które używają cullinga twarzy frontowej lub tylnej.

    Importowanie za [pośrednictwem konwersji](../how-tos/conversion/model-conversion.md) modelu określa poboczność podmiotu zabezpieczeń modelu. Jako wartość domyślną przyjmuje się dwustronność. Powierzchnia będzie renderowana jako zuchemna ściany z fizycznie poprawnym oświetleniem z obu stron. Pojedynczość może być implikowana przez flagi w zasobie źródłowym lub jawnie wymuszona podczas [konwersji modelu](../how-tos/conversion/model-conversion.md). Ponadto, ale opcjonalnie, [tryb jednosekwowy](../overview/features/single-sided-rendering.md) można ustawić na "normalny".

* Obiekty przecinają się w źródłowych obiektach.

     Obiekty przekształcone w sposób, w jaki niektóre ich powierzchnie nakładają się również na siebie, tworzą zrębowe. Przekształcanie części drzewa sceny w zaimportowanej scenie w ARR może również tworzyć ten problem.

* Powierzchnie są celowo dotykowe, takie jak dekalogi lub tekst na ścianie.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Artefakty graficzne korzystające z renderowania wielo przebiegów stereo w natywnych aplikacjach C++

W niektórych przypadkach niestandardowe natywne aplikacje języka C++, które używają trybu renderowania wielodaniowego stereo dla zawartości lokalnej (renderowanie do lewego i prawego oka w osobnych przebiegach) po wywołaniu [**funkcji BlitRemoteFrame,**](../concepts/graphics-bindings.md#render-remote-image) mogą wyzwolić usterkę sterownika. Ta usterka powoduje błędy rasteryzacji nie deterministycznej, co powoduje losowe zniknięcie pojedynczych trójkątów lub części trójkątów zawartości lokalnej. Ze względu na wydajność zaleca się renderowanie zawartości lokalnej z bardziej nowoczesną techniką renderowania stereo z jednym przebiegiem, na przykład przy **użyciu SV_RenderTargetArrayIndex**.

## <a name="conversion-file-download-errors"></a>Błędy pobierania pliku konwersji

Usługa konwersji może napotkać błędy podczas pobierania plików z magazynu obiektów blob z powodu ograniczeń długości ścieżki narzuconych przez system Windows i usługę. Ścieżki plików i nazwy plików w magazynie obiektów blob nie mogą przekraczać 178 znaków. Na przykład, jeśli `blobPrefix` znak `models/Assets` ma 13 znaków:

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Usługa konwersji pobierze wszystkie pliki określone w pliku , a nie tylko `blobPrefix` pliki używane podczas konwersji. W takich przypadkach pliki/foldery powodujące problemy mogą być mniej oczywiste, dlatego ważne jest, aby sprawdzić wszystkie elementy znajdujące się na koncie magazynu w obszarze `blobPrefix` . Zobacz przykładowe dane wejściowe poniżej, aby zobaczyć, co zostanie pobrane.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](../overview/system-requirements.md)
* [Wymagania dotyczące sieci](../reference/network-requirements.md)
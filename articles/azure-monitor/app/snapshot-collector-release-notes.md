---
title: Informacje o wersji pakietu NuGet Microsoft. ApplicationInsights. SnapshotCollector — Application Insights
description: Informacje o wersji pakietu NuGet Microsoft. ApplicationInsights. SnapshotCollector używane przez Snapshot Debugger Application Insights.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093264"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Informacje o wersji dla elementu Microsoft. ApplicationInsights. SnapshotCollector

Ten artykuł zawiera informacje o wydaniach dla pakietu NuGet Microsoft. ApplicationInsights. SnapshotCollector dla aplikacji platformy .NET, które są używane przez Snapshot Debugger Application Insights.

[Dowiedz się](./snapshot-debugger.md) więcej na temat Snapshot Debugger Application Insights dla aplikacji .NET.

Aby zapoznać się z raportami o błędach i opiniami, Otwórz problem w usłudze GitHub w witrynie https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Informacje o wersji

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Wydanie punktowe backport poprawkę z 1.4.0 — pre.
### <a name="bug-fixes"></a>Poprawki błędów
- Napraw [ObjectDisposedException przy zamykaniu](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Wersja punktu umożliwiająca rozwiązanie problemu wykrytego w testowaniu scenariusza dołączania bezkodu Azure App Service.
### <a name="changes"></a>Zmiany
- Obiekt docelowy netcoreapp 3.0 jest teraz zależny od Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (poprzednio >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Wersja punktowa, która umożliwia rozwiązanie kilku problemów o dużym wpływie.
### <a name="bug-fixes"></a>Poprawki błędów
- Stałe odnajdywanie PDB w folderze wwwroot/bin, który został przerwany podczas zmiany algorytmu wyszukiwania symboli w 1.3.6.
- Stałe zakłócenia ExtractWasCalledMultipleTimesException w telemetrii.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Zmiany
- Element docelowy netcoreapp 2.0 SnapshotCollector zależy od Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (ponownie). Spowoduje to przywrócenie zachowania przed 1.3.5. Próbowaliśmy uaktualnić go w programie 1.3.6, ale niektóre scenariusze Azure App Service.
### <a name="new-features"></a>Nowe funkcje
- Snapshot Collector odczytuje i analizuje parametry ConnectionString ze zmiennej środowiskowej APPLICATIONINSIGHTS_CONNECTION_STRING lub z TelemetryConfiguration. Służy on głównie do ustawiania punktu końcowego do łączenia się z usługą migawek. Aby uzyskać więcej informacji, zobacz [dokumentację parametrów połączenia](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Poprawki błędów
- Przełączono do korzystania z HttpClient dla wszystkich obiektów docelowych z wyjątkiem net45, ponieważ żądanie Webw niektórych środowiskach kończy się niepowodzeniem z powodu niezgodnego to elementu SecurityProtocol (wymaga protokołu TLS 1,2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Zmiany
- SnapshotCollector teraz zależy od elementu Microsoft. ApplicationInsights >= 2.5.1 dla wszystkich platform docelowych. Może to być istotna zmiana, jeśli aplikacja zależy od starszej wersji zestawu Microsoft. ApplicationInsights SDK.
- Usuń obsługę protokołów TLS 1,0 i 1,1 w migawce obiektu przekazującego.
- Okres skanowania plików PDB domyślnie trwa 24 godziny, a nie 15 minut. Konfigurowalne za pośrednictwem PdbRescanInterval na SnapshotCollectorConfiguration.
- Skanowanie plików PDB przeszukuje tylko foldery najwyższego poziomu, a nie cyklicznie. Może to być istotna zmiana, jeśli symbole znajdują się w podfolderach folderu binarnego.
### <a name="new-features"></a>Nowe funkcje
- Obrót dziennika w SnapshotUploader, aby uniknąć wypełniania folderu logs starymi plikami.
- Obsługa deoptymalizacji (za pośrednictwem ReJIT on Attach) dla aplikacji platformy .NET Core 3,0.
- Dodaj symbole do pakietu NuGet.
- Ustaw dodatkowe metadane podczas przekazywania minizrzutów.
- Dodano zainicjowaną właściwość do SnapshotCollectorTelemetryProcessor. Jest to CancellationToken, który zostanie anulowany, gdy Snapshot Collector zostanie całkowicie zainicjowany i połączony z punktem końcowym usługi.
- Migawki mogą teraz być przechwytywane pod kątem wyjątków w metodach generowanych dynamicznie. Na przykład skompilowane drzewa wyrażeń generowane przez zapytania Entity Framework.
### <a name="bug-fixes"></a>Poprawki błędów
- AmbiguousMatchException — ładowanie Snapshot Collector ze względu na monitor stanu.
- Metoda rozszerzenia GetSnapshotCollector teraz przeszukuje wszystkie TelemetrySinks.
- Nie uruchamiaj migawek obiektu przekazującego na nieobsługiwanych platformach.
- Obsługa InvalidOperationException podczas deoptymalizacji metod dynamicznych (na przykład Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Dodawanie obsługi dla chmur suwerennych (starsze wersje nie działają w przypadku suwerennych chmur)
- Łatwiejsze Dodawanie modułu zbierającego migawki przy użyciu AddSnapshotCollector (). Więcej informacji można znaleźć [tutaj](./snapshot-debugger-appservice.md).
- Użyj ustawienia FISMA MD5 na potrzeby weryfikowania bloków obiektów BLOB. Pozwala to uniknąć domyślnego algorytmu kryptograficznego .NET MD5, który jest niedostępny, gdy system operacyjny jest ustawiony na tryb zgodny ze standardem FIPS.
- Ignoruj ramki .NET Framework podczas deoptymalizacji wywołań funkcji. Takie zachowanie może być kontrolowane przez ustawienie konfiguracji DeoptimizeIgnoredModules.
- Dodanie `DeoptimizeMethodCount` Ustawienia konfiguracji, które pozwala na deoptymalizację więcej niż jednego wywołania funkcji. Więcej informacji można znaleźć tutaj

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Zezwalaj na używanie kluczy Instrumentacji strukturalnej.
- Zwiększ niezawodność SnapshotUploader — Kontynuuj uruchamianie nawet wtedy, gdy nie można przenieść starych dzienników obiektu przekazującego.
- Po ponownym włączeniu zgłaszaj dodatkową telemetrię po natychmiastowym wyjściu SnapshotUploader.exe (została wyłączona w 1.3.3).
- Uprość wewnętrzną telemetrię.
- _Funkcja eksperymentalna_: plany kolekcji punkt przyciągania: Dodaj "snapshotOnFirstOccurence". Więcej informacji można znaleźć [tutaj](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Rozwiązano problem, który spowodował, że SnapshotUploader.exe przestać odpowiadać i nie przekazywać migawek dla aplikacji platformy .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Funkcja eksperymentalna_: punkt przyciągania plany zbierania danych. Więcej informacji można znaleźć [tutaj](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe zakończy się, gdy środowisko uruchomieniowe zwalnia element AppDomain, z którego załadowano SnapshotCollector, zamiast czekać na zakończenie procesu. Pozwala to zwiększyć niezawodność modułu zbierającego w usługach IIS.
- Dodaj konfigurację, aby zezwolić na wiele wystąpień SnapshotCollector, które używają tego samego klucza instrumentacji do udostępniania tego samego procesu SnapshotUploader: ShareUploaderProcess (wartość domyślna to `true` ).
- Zgłoś dodatkową telemetrię, gdy SnapshotUploader.exe natychmiast się kończy.
- Zmniejszenie liczby plików obsługi SnapshotUploader.exe potrzeby zapisywania na dysku.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Usuń obsługę zbierania migawek za pomocą interfejsu API RtlCloneUserProcess i obsługujący interfejs API PssCaptureSnapshots.
- Zwiększ domyślny limit liczby migawek, które można przechwytywać w ciągu 10 minut od 1 do 3.
- Zezwalaj SnapshotUploader.exe na negocjowanie protokołu TLS 1,1 i 1,2
- Zgłoś dodatkową telemetrię, gdy SnapshotUploader rejestruje ostrzeżenie lub błąd
- Zatrzymaj tworzenie migawek, gdy usługa zaplecza zgłosi dzienny limit przydziału (50 migawek dziennie)
- Dodaj dodatkowe ewidencjonowanie SnapshotUploader.exe, aby nie zezwalać na uruchamianie dwóch wystąpień w tym samym czasie.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Zmiany
- W przypadku aplikacji przeznaczonych do .NET Framework, Snapshot Collector obecnie zależy od Microsoft. ApplicationInsights w wersji 2.3.0 lub nowszej.
Została użyta do 2.2.0 lub wyższego.
Uważamy, że nie jest to problem z większością aplikacji, ale informuje nas o tym, czy ta zmiana uniemożliwia korzystanie z najnowszych Snapshot Collector.
- Użyj wykładniczych opóźnień wycofywania w migawce obiektu przekazującego podczas ponawiania nieudanych operacji przekazywania.
- Użyj ServerTelemetryChannel (jeśli jest dostępny), aby uzyskać bardziej niezawodne raportowanie danych telemetrycznych.
- Użyj elementu "SdkInternalOperationsMonitor" podczas początkowego połączenia z usługą Snapshot Debugger, aby był ignorowany przez śledzenie zależności.
- Popraw dane telemetryczne wokół początkowego połączenia z usługą Snapshot Debugger.
- Zgłoś dodatkową telemetrię dla:
  - Wersja Azure App Service.
  - Wystąpienia obliczeniowe platformy Azure.
  - Opakowania.
  - Aplikacja funkcji platformy Azure.
### <a name="bug-fixes"></a>Poprawki błędów
- Gdy interwał resetowania licznika problemów jest ustawiony na 24 dni, interpretuj go jako 24 godziny.
- Rozwiązano problem polegający na tym, że migawka obiektu przekazującego przestanie przetwarzać nowe migawki, jeśli wystąpił wyjątek podczas usuwania migawki.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Naprawianie silnej nazwy przy użyciu plików binarnych obiektu przekazującego migawek.

## <a name="122"></a>[ppkt](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Zmiany
- Pliki wymagające SnapshotUploader (64). exe są teraz osadzane jako zasoby w głównej bibliotece DLL. Oznacza to, że folder SnapshotCollectorFiles nie jest już tworzony, upraszczając tworzenie i wdrażanie oraz zmniejszając bałagan w Eksplorator rozwiązań. Należy zachować ostrożność podczas uaktualniania programu w celu przejrzenia zmian w `.csproj` pliku. `Microsoft.ApplicationInsights.SnapshotCollector.targets`Plik nie jest już wymagany.
- Dane telemetryczne są rejestrowane w zasobie Application Insights, nawet jeśli ProvideAnonymousTelemetry ma wartość false. Dzięki temu możemy zaimplementować funkcję sprawdzania kondycji w Azure Portal. ProvideAnonymousTelemetry ma wpływ tylko na dane telemetryczne wysyłane do firmy Microsoft w celu uzyskania pomocy technicznej i ulepszania produktów.
- Gdy TempFolder lub ShadowCopyFolder są przekierowywane do zmiennych środowiskowych, utrzymywanie modułu zbierającego bezczynne do momentu ustawienia tych zmiennych środowiskowych.
- W przypadku aplikacji, które łączą się z Internetem za pośrednictwem serwera proxy, Snapshot Collector automatycznie wykrywa wszystkie ustawienia serwera proxy i przekazuje je do SnapshotUploader.exe.
- Niższy priorytet procesu SnapshotUplaoder (tam, gdzie to możliwe). Ten priorytet można zastąpić za pomocą opcji IsLowPrioirtySnapshotUploader.
- Dodano metodę rozszerzenia GetSnapshotCollector w TelemetryConfiguration dla scenariuszy, w których chcesz programowo skonfigurować Snapshot Collector.
- Ustaw Application Insights wersję zestawu SDK (zamiast wersji aplikacji) w ramach telemetrii dotyczącej klientów.
- Wyślij pierwsze zdarzenie pulsu po dwie minuty.
### <a name="bug-fixes"></a>Poprawki błędów
- Należy rozwiązać NullReferenceException, gdy wyjątki zawierają wartości null lub niezmienne dane.
- W obiektu przekazującego ponów próbę wykonania operacji PDB odpowiadającej kilku razy, jeśli uzyskamy naruszenie zasad współużytkowania.
- Napraw zduplikowane dane telemetryczne, gdy więcej niż jedno wywołanie wątku w potoku telemetrii podczas uruchamiania.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Zmiany
- Pliki komentarzy dokumentacji XML są teraz zawarte w pakiecie NuGet.
- Dodano metodę rozszerzenia ExcludeFromSnapshotting na `System.Exception` potrzeby scenariuszy, w których wiadomo, że wystąpił wyjątek szumu i chcesz uniknąć tworzenia migawek dla tego programu.
- Dodano właściwość konfiguracji IsEnabledWhenProfiling; wartość domyślna to true. Jest to zmiana z poprzednich wersji, w przypadku których tworzenie migawek zostało tymczasowo wyłączone, jeśli Application Insights Profiler była wykonana Szczegółowa kolekcja. Stare zachowanie można odzyskać przez ustawienie dla tej właściwości wartości false.
### <a name="bug-fixes"></a>Poprawki błędów
- Podpisz SnapshotUploader64.exe prawidłowo.
- Ochrona przed podwójnym inicjalizacją procesora telemetrii.
- Zapobiegaj podwójnemu rejestrowaniu danych telemetrycznych w aplikacjach z wieloma potokami.
- Usuń usterkę z czasem wygaśnięcia planu kolekcji, co może uniemożliwić tworzenie migawek po 24 godzinach.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Największą zmianą w tej wersji (w związku z tym przejście do nowego numeru wersji pomocniczej) jest ponowne zapisanie potoku tworzenia i obsługi migawek. W poprzednich wersjach ta funkcja została zaimplementowana w kodzie natywnym (ProductionBreakpoints *. dll i SnapshotHolder*. exe). Nowa implementacja to cały kod zarządzany z P/Invoke. W przypadku tej pierwszej wersji przy użyciu nowego potoku nie zostało to zrobione z oryginalnego zachowania. Nowa implementacja umożliwia lepsze raportowanie błędów i konfiguruje je na potrzeby przyszłych ulepszeń.

### <a name="other-changes-in-this-version"></a>Inne zmiany w tej wersji
- Zmieniono nazwę MinidumpUploader.exe na SnapshotUploader.exe (lub SnapshotUploader64.exe).
- Dodano dane telemetryczne chronometrażu w celu deoptymalizacji/przeprowadzenia optymalizacji żądań.
- Dodano kompresję gzip dla operacji przekazywania minizrzutu.
- Rozwiązano problem polegający na tym, że plików PDB został zablokowany, uniemożliwiając uaktualnienie lokacji.
- Rejestruj oryginalną nazwę folderu (SnapshotCollectorFiles) podczas kopiowania w tle.
- Dostosuj limity pamięci dla procesów 64-bitowych, aby zapobiec ponownym uruchomieniu lokacji z powodu OOM.
- Rozwiąż problem polegający na tym, że migawki zostały jeszcze zebrane nawet po wyłączeniu.
- Rejestruj zdarzenia pulsu w zasobach AI klienta.
- Zwiększ szybkość migawki, usuwając "Źródło" z identyfikatora problemu.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Zmiany
Rozszerzone dane telemetryczne użycia
- Wykrywanie i raportowanie wersji .NET i systemu operacyjnego
- Wykrywaj i zgłaszaj dodatkowe środowiska platformy Azure (usługa w chmurze, Service Fabric)
- Rejestruj i Raportuj metryki wyjątków (liczba wyjątków 1 i liczba wywołań elementu Trackexception) w danych telemetrycznych pulsu.
### <a name="bug-fixes"></a>Poprawki błędów
- Poprawna obsługa elementu SqlException, w którym nie jest zgłaszany wyjątek wewnętrzny (Win32exception).
- Przytnij spacje końcowe w folderach symboli, co spowodowało nieprawidłowe analizowanie argumentów wiersza polecenia do MinidumpUploader.
- Zapobiegaj nieskończonej ponowieniu nieudanych połączeń z punktem końcowym agenta Snapshot Debugger.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Zmiany
- Dodano ochronę pamięci hosta. Ta funkcja zmniejsza wpływ na pamięć komputera hosta.
- Zwiększ możliwości wyświetlania migawek Azure Portal.
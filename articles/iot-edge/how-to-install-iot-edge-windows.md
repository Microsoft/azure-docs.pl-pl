---
title: Instalowanie Azure IoT Edge w systemie Windows | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge w systemach Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465562"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Install the Azure IoT Edge runtime on Windows (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemie Windows)

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury.

Aby dowiedzieć się więcej na temat środowiska uruchomieniowego IoT Edge, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge w systemie Windows x64 (AMD/Intel) przy użyciu kontenerów systemu Windows.

> [!NOTE]
> Znany problem z systemem operacyjnym Windows zapobiega przejściu do trybu uśpienia i hibernacji w przypadku, gdy są uruchomione moduły IoT Edge (kontenery systemu Windows nano Server w systemie). Ten problem wpływa na czas pracy baterii na urządzeniu.
>
> W celu obejścia tego problemu należy użyć polecenia, `Stop-Service iotedge` Aby zatrzymać wszystkie uruchomione moduły IoT Edge przed użyciem tych stanów.

Korzystanie z kontenerów systemu Linux w systemach Windows nie jest zalecaną lub obsługiwaną konfiguracją produkcyjną dla Azure IoT Edge. Można go jednak używać w celach deweloperskich i testowych. Aby dowiedzieć się więcej, zobacz [używanie IoT Edge w systemie Windows do uruchamiania kontenerów systemu Linux](how-to-install-iot-edge-windows-with-linux.md).

Aby uzyskać informacje o tym, co zawiera Najnowsza wersja IoT Edge, zobacz [wersje Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja służy do sprawdzenia, czy urządzenie z systemem Windows może obsługiwać IoT Edge i przygotować je do aparatu kontenera przed instalacją.

### <a name="supported-windows-versions"></a>Obsługiwane wersje systemu Windows

IoT Edge dla systemu Windows wymaga systemu Windows w wersji 1809/kompilacja 17763, który jest najnowszą [kompilacją długoterminowej pomocy technicznej systemu Windows](https://docs.microsoft.com/windows/release-information/). Aby uzyskać pomoc techniczną dla systemu Windows, zobacz artykuł co jest obsługiwane w zależności od tego, czy przygotowujesz się do scenariuszy produkcyjnych lub scenariuszy projektowania i testowania:

* **Produkcja**: Najnowsze informacje o tym, które systemy operacyjne są obecnie obsługiwane w scenariuszach produkcyjnych, można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md#operating-systems).
* **Opracowywanie i testowanie**: w przypadku scenariuszy projektowania i testowania Azure IoT Edge z kontenerami systemu Windows można zainstalować na dowolnej jednostce SKU (Pro, Enterprise, Server itp.) systemu windows Build 17763, która obsługuje funkcję Containers.

Urządzenia IoT Core muszą zawierać opcjonalną funkcję kontenerów systemu Windows IoT Core do obsługi środowiska uruchomieniowego IoT Edge. Użyj następującego polecenia w [zdalnej sesji programu PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) , aby sprawdzić, czy kontenery systemu Windows są obsługiwane na urządzeniu:

```powershell
Get-Service vmcompute
```

Jeśli usługa jest obecna, należy uzyskać pomyślną odpowiedź ze stanem usługi wymienionym jako **uruchomiony**. Jeśli `vmcompute` Usługa nie zostanie znaleziona, urządzenie nie spełnia wymagań dotyczących IoT Edge. Skontaktuj się z dostawcą sprzętu, aby uzyskać pomoc techniczną dotyczącą tej funkcji.

### <a name="prepare-for-a-container-engine"></a>Przygotowanie do aparatu kontenera

Azure IoT Edge opiera się na aparacie kontenera [zgodnym ze sterownikiem OCI](https://www.opencontainers.org/) . W przypadku scenariuszy produkcyjnych Użyj aparatu Moby zawartego w skrypcie instalacji, aby uruchomić kontenery systemu Windows na urządzeniu z systemem Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalowanie IoT Edge na nowym urządzeniu

>[!NOTE]
>Azure IoT Edge pakiety oprogramowania podlegają postanowieniom licencyjnym znajdującym się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj pakietu.

Skrypt programu PowerShell pobiera i instaluje demona Security Azure IoT Edge. Następnie demon zabezpieczeń uruchamia pierwsze dwa moduły środowiska uruchomieniowego, Agent IoT Edge, który umożliwia zdalne wdrażanie innych modułów.

>[!TIP]
>W przypadku urządzeń z systemem IoT Core zalecamy uruchamianie poleceń instalacji przy użyciu sesji RemotePowerShell. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu PowerShell dla systemu Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

W przypadku instalowania środowiska uruchomieniowego IoT Edge po raz pierwszy na urządzeniu należy zainicjować obsługę administracyjną urządzenia z tożsamością usługi IoT Hub. Pojedyncze urządzenie IoT Edge może być inicjowane ręcznie przy użyciu parametrów połączenia urządzenia dostarczonych przez IoT Hub. Można też użyć usługi Device Provisioning (DPS), aby automatycznie zainicjować obsługę administracyjną urządzeń, co jest przydatne, gdy istnieje wiele urządzeń do skonfigurowania. W zależności od wybranej opcji aprowizacji wybierz odpowiedni skrypt instalacji.

W poniższych sekcjach opisano typowe przypadki użycia i parametry skryptu instalacji IoT Edge na nowym urządzeniu.

### <a name="option-1-install-and-manually-provision"></a>Opcja 1: Instalowanie i Ręczne inicjowanie obsługi administracyjnej

W tej pierwszej opcji podano **Parametry połączenia urządzenia** wygenerowane przez IoT Hub w celu udostępnienia urządzenia.

W tym przykładzie przedstawiono instalację ręczną przy użyciu kontenerów systemu Windows:

1. Jeśli jeszcze tego nie zrobiono, Zarejestruj nowe urządzenie IoT Edge i Pobierz **Parametry połączenia urządzenia**. Skopiuj parametry połączenia, aby użyć ich w dalszej części tej sekcji. Ten krok można wykonać przy użyciu następujących narzędzi:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji AMD64 programu PowerShell, aby zainstalować IoT Edge, a nie PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w kroku 1. Parametry połączenia urządzenia kojarzą urządzenie fizyczne z IDENTYFIKATORem urządzenia w IoT Hub.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Wykonaj kroki opisane w sekcji [Weryfikowanie pomyślnej instalacji](#verify-successful-installation) , aby sprawdzić stan IoT Edge na urządzeniu.

W przypadku ręcznego instalowania i aprowizacji urządzenia można użyć dodatkowych parametrów do zmodyfikowania instalacji, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatora w trybie offline
* Zadeklaruj określony obraz kontenera agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź z wyprzedzeniem, aby dowiedzieć się więcej na temat [wszystkich parametrów instalacji](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opcja 2: Instalacja i Automatyczna obsługa administracyjna

W tej drugiej opcji można zainicjować obsługę administracyjną urządzenia przy użyciu IoT Hub Device Provisioning Service. Podaj **Identyfikator zakresu** z wystąpienia usługi Device Provisioning Service wraz z innymi informacjami specyficznymi dla preferowanego [mechanizmu zaświadczania](../iot-dps/concepts-security.md#attestation-mechanism):

* [Tworzenie i Inicjowanie obsługi symulowanego urządzenia IoT Edge przy użyciu wirtualnego modułu TPM w systemie Windows](how-to-auto-provision-simulated-device-windows.md)
* [Tworzenie i Inicjowanie obsługi symulowanego urządzenia IoT Edge przy użyciu certyfikatów X. 509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu zaświadczania klucza symetrycznego](how-to-auto-provision-symmetric-keys.md)

Podczas automatycznej instalacji i aprowizacji urządzenia można użyć dodatkowych parametrów do zmodyfikowania instalacji, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatora w trybie offline
* Zadeklaruj określony obraz kontenera agenta i podaj poświadczenia, jeśli znajduje się w rejestrze prywatnym

Aby uzyskać więcej informacji na temat tych opcji instalacji, przejdź do tego artykułu lub przejdź do informacji o [wszystkich parametrach instalacji](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalacja w trybie offline lub określona wersja

Podczas instalacji są pobierane trzy pliki:

* Skrypt programu PowerShell, który zawiera instrukcje dotyczące instalacji
* Microsoft Azure IoT Edge CAB, który zawiera demo IoT Edge Security daemon (iotedged), aparat kontenera Moby i interfejs wiersza polecenia Moby
* Instalator pakietu redystrybucyjnego programu Visual C++ (środowisko uruchomieniowe VC)

Jeśli urządzenie będzie w trybie offline w trakcie instalacji lub jeśli chcesz zainstalować określoną wersję IoT Edge, możesz pobrać te pliki przed czasem do urządzenia. Po zakończeniu instalacji wskaż skrypt instalacji znajdujący się w katalogu zawierającym pobrane pliki. Instalator najpierw sprawdza ten katalog, a następnie pobiera tylko składniki, które nie zostały odnalezione. Jeśli wszystkie pliki są dostępne w trybie offline, możesz zainstalować program bez połączenia z Internetem.

Możesz również użyć parametru ścieżka instalacji w trybie offline, aby zaktualizować IoT Edge. Aby uzyskać więcej informacji, zobacz temat [aktualizowanie IoT Edge zabezpieczeń i środowiska uruchomieniowego](how-to-update-iot-edge.md).

1. Najnowsze pliki instalacyjne IoT Edge wraz z poprzednimi wersjami znajdują się w artykule [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Znajdź wersję, którą chcesz zainstalować, i pobierz następujące pliki z sekcji **Assets** informacji o wersji na urządzeniu IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z wersji 1.0.9 lub nowszej lub Microsoft-Azure-IoTEdge.cab z wersji 1.0.8 i starszych.

   Microsoft-Azure-IotEdge-arm32.cab jest również dostępna od 1.0.9 tylko do celów testowych. IoT Edge nie jest obecnie obsługiwana na urządzeniach z systemem Windows ARM32.

   Ważne jest, aby użyć skryptu programu PowerShell z tej samej wersji co używany plik cab, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

3. Jeśli pobrany plik cab ma sufiks architektury, Zmień nazwę pliku na tylko **Microsoft-Azure-IoTEdge.cab**.

4. Opcjonalnie można pobrać Instalatora dla Visual C++ pakiet redystrybucyjny. Na przykład skrypt programu PowerShell używa tej wersji: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Zapisz Instalatora w tym samym folderze na urządzeniu IoT jako pliki IoT Edge.

5. Aby zainstalować program za pomocą składników trybu [offline, należy](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) uzyskać kopię lokalną skryptu programu PowerShell. Następnie użyj `-OfflineInstallationPath` parametru jako części `Deploy-IoTEdge` polecenia i podaj ścieżkę bezwzględną do katalogu plików. Przykład:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Polecenie wdrożenia użyje wszystkich składników znalezionych w lokalnym katalogu plików. Jeśli brakuje pliku cab lub Instalatora Visual C++, spróbuje je pobrać.

6. Uruchom `Initialize-IoTEdge` polecenie, aby zainicjować obsługę administracyjną urządzenia przy użyciu tożsamości w IoT Hub. Podaj parametry połączenia urządzenia do aprowizacji ręcznej lub wybierz jedną z metod opisanych w poprzedniej sekcji [Automatyczne inicjowanie](#option-2-install-and-automatically-provision) obsługi administracyjnej.

   Jeśli urządzenie zostało uruchomione ponownie po uruchomieniu `Deploy-IoTEdge` , przed uruchomieniem Uruchom skrypt programu PowerShell `Initialize-IoTEdge` .

Aby uzyskać więcej informacji na temat opcji instalacji w trybie offline, przejdź dalej, aby poznać [wszystkie parametry instalacji](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Sprawdź stan usługi IoT Edge. Powinien być wymieniony jako uruchomiony.  

```powershell
Get-Service iotedge
```

Przeanalizuj dzienniki usług z ostatnich 5 minut. Po zakończeniu instalowania środowiska uruchomieniowego IoT Edge może zostać wyświetlona lista błędów od czasu między uruchomieniem metody **Deploy-IoTEdge** i **Initialize-IoTEdge**. Te błędy są oczekiwane, ponieważ próba uruchomienia usługi przed jej skonfigurowaniem.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Uruchom [Narzędzie do rozwiązywania problemów](troubleshoot.md#run-the-check-command) , aby sprawdzić najbardziej typowe błędy konfiguracji i sieci.

```powershell
iotedge check
```

Do momentu wdrożenia pierwszego modułu do IoT Edge na urządzeniu moduł **$edgeHub** system nie zostanie wdrożony na urządzeniu. W związku z tym, automatyczne sprawdzanie zwróci błąd `Edge Hub can bind to ports on host` sprawdzania łączności. Ten błąd można zignorować, chyba że występuje po wdrożeniu modułu na urządzeniu.

Na koniec Wyświetl listę uruchomionych modułów:

```powershell
iotedge list
```

Po nowej instalacji jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**. Po [wdrożeniu modułów IoT Edge](how-to-deploy-modules-portal.md) po raz pierwszy na urządzeniu zostanie uruchomiony inny moduł systemowy **edgeHub**.

## <a name="manage-module-containers"></a>Zarządzanie kontenerami modułów

Usługa IoT Edge wymaga aparatu kontenera działającego na urządzeniu. Po wdrożeniu modułu na urządzeniu środowisko uruchomieniowe IoT Edge używa aparatu kontenera do ściągania obrazu kontenera z rejestru w chmurze. Usługa IoT Edge umożliwia korzystanie z modułów i pobieranie dzienników, ale czasami warto używać aparatu kontenera do współpracy z kontenerem.

Aby uzyskać więcej informacji o pojęciach dotyczących modułu, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md).

Jeśli używasz kontenerów systemu Windows na urządzeniu z systemem Windows IoT Edge, wówczas instalacja IoT Edge obejmowała aparat kontenera Moby. Aparat Moby opierał się na tych samych standardach jak Docker i został zaprojektowany do równoległego uruchamiania na tym samym komputerze co program Docker Desktop. Z tego powodu, jeśli chcesz, aby obiekty docelowe były zarządzane przez aparat Moby, konieczne jest przeanalizowanie tego aparatu zamiast platformy Docker.

Na przykład aby wyświetlić listę wszystkich obrazów platformy Docker, użyj następującego polecenia:

```powershell
docker images
```

Aby wyświetlić listę wszystkich obrazów Moby, należy zmodyfikować to samo polecenie ze wskaźnikiem do aparatu Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identyfikator URI aparatu jest wyświetlany w danych wyjściowych skryptu instalacji lub można go znaleźć w sekcji Ustawienia środowiska uruchomieniowego kontenera dla pliku config. YAML.

![Identyfikator URI moby_runtime w pliku config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Aby uzyskać więcej informacji o poleceniach, których można użyć do współdziałania z kontenerami i obrazami działającymi na urządzeniu, zobacz [interfejs wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Windows, użyj następującego polecenia z okna administracyjnego programu PowerShell. To polecenie usuwa środowisko uruchomieniowe IoT Edge wraz z istniejącą konfiguracją i danymi aparatu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Polecenie Uninstall-IoTEdge nie działa w przypadku systemu Windows IoT Core. Aby usunąć IoT Edge z urządzeń z systemem Windows IoT Core, należy ponownie wdrożyć obraz systemu Windows IoT Core.

Aby uzyskać więcej informacji o opcjach odinstalowywania, użyj polecenia `Get-Help Uninstall-IoTEdge -full` .

## <a name="verify-installation-script"></a>Weryfikuj skrypt instalacji

Polecenia instalacji podane w tym artykule używają polecenia cmdlet Invoke-WebRequest, aby zażądać skryptu instalacji z programu `aka.ms/iotedge-win` . Ten link wskazuje `IoTEdgeSecurityDaemon.ps1` skrypt z najnowszej [wersji IoT Edge](https://github.com/Azure/azure-iotedge/releases). Możesz również pobrać ten skrypt lub wersję skryptu z określonej wersji, aby uruchomić polecenia instalacji na urządzeniu IoT Edge.

Podany skrypt jest podpisany, aby zwiększyć bezpieczeństwo. Podpis można sprawdzić, pobierając skrypt na urządzenie, a następnie uruchamiając następujące polecenie programu PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Stan danych wyjściowych jest **prawidłowy** w przypadku zweryfikowania podpisu.

## <a name="all-installation-parameters"></a>Wszystkie parametry instalacji

W poprzednich sekcjach wprowadzono typowe scenariusze instalacji z przykładami użycia parametrów w celu zmodyfikowania skryptu instalacji. Ta sekcja zawiera tabele referencyjne typowych parametrów używanych do instalowania, aktualizowania lub odinstalowywania IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Polecenie Deploy-IoTEdge pobiera i wdraża demona zabezpieczeń IoT Edge i jej zależności. Polecenie wdrożenia akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Deploy-IoTEdge -full` .  

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge polecenie konfiguruje IoT Edge przy użyciu parametrów połączenia urządzenia i szczegółów operacyjnych. Większość informacji generowanych przez to polecenie jest następnie przechowywana w pliku iotedge\config.YAML. Polecenie inicjujące akceptuje te typowe parametry między innymi. Aby uzyskać pełną listę, użyj polecenia `Get-Help Initialize-IoTEdge -full` .

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Ręczne** | Brak | **Parametr przełącznika**. Jeśli typ aprowizacji nie zostanie określony, wartość domyślna to ręczna.<br><br>Deklaruje, że podajesz parametry połączenia urządzenia, aby ręcznie zainicjować obsługę urządzenia |
| **Dokumenty** | Brak | **Parametr przełącznika**. Jeśli typ aprowizacji nie zostanie określony, wartość domyślna to ręczna.<br><br>Deklaruje, że podajesz identyfikator zakresu usługi Device Provisioning Service (DPS) i Identyfikator rejestracji urządzenia, aby zapewnić obsługę administracyjną.  |
| **DeviceConnectionString** | Parametry połączenia z urządzenia IoT Edge zarejestrowane w IoT Hub, w pojedynczym cudzysłowie | **Wymagane** do ręcznego inicjowania obsługi. Jeśli nie podano parametrów połączenia w parametrach skryptu, zostanie wyświetlony monit o podanie jednego z nich. |
| **Identyfikatora zakresu** | Identyfikator zakresu z wystąpienia usługi Device Provisioning skojarzonej z IoT Hubem. | **Wymagane** do APROWIZACJI usługi DPS. Jeśli w parametrach skryptu nie podano identyfikatora zakresu, zostanie wyświetlony monit o podanie jednego z nich. |
| **Identyfikator rejestracji** | Identyfikator rejestracji generowany przez urządzenie | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z modułu TPM lub zaświadczania klucza symetrycznego. **Opcjonalne** w przypadku zaświadczania certyfikatu X. 509. |
| **X509IdentityCertificate** | Ścieżka identyfikatora URI do certyfikatu tożsamości urządzenia X. 509 na urządzeniu. | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z zaświadczania o certyfikatach X. 509. |
| **X509IdentityPrivateKey** | Ścieżka URI klucza certyfikatu tożsamości urządzenia X. 509 na urządzeniu. | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z zaświadczania o certyfikatach X. 509. |
| **SymmetricKey** | Klucz symetryczny używany do inicjowania obsługi tożsamości urządzenia IoT Edge podczas korzystania z usługi DPS | **Wymagane** do APROWIZACJI usługi DPS w przypadku korzystania z zaświadczania klucza symetrycznego. |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono systemu operacyjnego kontenera, system Windows jest wartością domyślną.<br><br>W przypadku kontenerów systemu Windows IoT Edge używa aparatu kontenera Moby dołączonego do instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | Identyfikator URI obrazu agenta IoT Edge | Domyślnie Nowa instalacja IoT Edge używa najnowszego znacznika stopniowego dla obrazu agenta IoT Edge. Użyj tego parametru, aby ustawić określony tag dla wersji obrazu lub podać własny obraz agenta. Aby uzyskać więcej informacji, zobacz [Omówienie tagów IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nazwa użytkownika** | Nazwa użytkownika rejestru kontenerów | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj nazwę użytkownika z dostępem do rejestru. |
| **Hasło** | Bezpieczny ciąg hasła | Tego parametru należy używać tylko w przypadku ustawienia parametru-AgentImage w kontenerze w rejestrze prywatnym. Podaj hasło, aby uzyskać dostęp do rejestru. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **ContainerOs** | **System Windows** lub **Linux** | Jeśli nie określono żadnego systemu operacyjnego kontenera, system Windows jest wartością domyślną. W przypadku kontenerów systemu Windows aparat kontenera zostanie uwzględniony w instalacji. W przypadku kontenerów systemu Linux należy zainstalować aparat kontenera przed rozpoczęciem instalacji. |
| **Proxy** | Adres URL serwera proxy | Należy uwzględnić ten parametr, jeśli urządzenie musi przejść przez serwer proxy, aby połączyć się z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtable parametrów i wartości | Podczas instalacji wykonywane jest kilka żądań sieci Web. To pole służy do ustawiania parametrów dla tych żądań sieci Web. Ten parametr jest przydatny do konfigurowania poświadczeń dla serwerów proxy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ścieżka katalogu | Jeśli ten parametr zostanie uwzględniony, Instalator sprawdzi katalog na wymienionym katalogu dla plików MSI IoT Edge cab i VC Runtime wymaganych do instalacji. Wszystkie pliki, które nie znajdują się w katalogu, są pobierane. Jeśli oba pliki znajdują się w katalogu, można zainstalować IoT Edge bez połączenia z Internetem. Można również użyć tego parametru, aby użyć określonej wersji. |
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi wdrożenia ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametr | Dopuszczalne wartości | Komentarze |
| --------- | --------------- | -------- |
| **Moc** | brak | Ta flaga wymusza dezinstalację w przypadku, gdy poprzednia próba odinstalowania zakończyła się niepowodzeniem.
| **RestartIfNeeded** | brak | Ta flaga umożliwia skryptowi dezinstalacji ponowne uruchomienie maszyny bez monitowania, w razie potrzeby. |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).

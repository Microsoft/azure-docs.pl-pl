---
title: Zainstaluj Azure IoT Edge | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Windows lub Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: 3a02459f5b92aa7d708c29c737ed9428ed14215a
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045690"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Instalowanie lub Odinstalowywanie środowiska uruchomieniowego Azure IoT Edge

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

Aby skonfigurować urządzenie IoT Edge, należy wykonać dwie czynności. Pierwszym krokiem jest zainstalowanie środowiska uruchomieniowego wraz z jego zależnościami, które zostały omówione w tym artykule. Drugim krokiem jest połączenie urządzenia ze swoją tożsamością w chmurze i skonfigurowanie uwierzytelniania przy użyciu IoT Hub. Te kroki znajdują się w następnych artykułach.

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniach z systemem Linux lub Windows. W przypadku urządzeń z systemem Windows możesz skorzystać z dodatkowych opcji korzystania z kontenerów systemu Linux lub kontenerów Windows. Obecnie w scenariuszach produkcyjnych zalecane jest używanie kontenerów systemu Windows w systemie Windows. Kontenery systemu Linux w systemie Windows są przydatne w scenariuszach projektowania i testowania, zwłaszcza w przypadku tworzenia na komputerze z systemem Windows w celu wdrażania ich na urządzeniach z systemem Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Najnowsze informacje o tym, które systemy operacyjne są obecnie obsługiwane w scenariuszach produkcyjnych, można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

Posiadanie urządzenia z systemem Linux lub x64, ARM32 lub ARM64. Firma Microsoft udostępnia pakiety instalacyjne dla systemów operacyjnych Ubuntu Server 16,04, Ubuntu Server 18,04 i raspbian.

>[!NOTE]
>Obsługa urządzeń z ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Przygotuj urządzenie w celu uzyskania dostępu do pakietów instalacyjnych firmy Microsoft.

1. Zainstaluj konfigurację repozytorium zgodną z systemem operacyjnym urządzenia.

   * **Ubuntu Server 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Rozciągnij raspbian**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Skopiuj wytworzoną listę do katalogu sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Zainstaluj klucz publiczny programu Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Wersja systemu Windows

IoT Edge z kontenerami systemu Windows wymaga systemu Windows w wersji 1809/kompilacja 17762, który jest najnowszą [kompilacją długoterminowej pomocy technicznej systemu Windows](/windows/release-information/). W przypadku scenariuszy projektowania i testowania każda jednostka SKU (Pro, Enterprise, serwer itp.), która obsługuje funkcję kontenerów, będzie działała. Należy jednak zapoznać się z listą obsługiwanych systemów przed przejściem do środowiska produkcyjnego.

IoT Edge z kontenerami systemu Linux można uruchomić w dowolnej wersji Windows, która spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Wymagania aparatu kontenera

Azure IoT Edge opiera się na aparacie kontenera [zgodnym ze sterownikiem OCI](https://www.opencontainers.org/) . Upewnij się, że urządzenie może obsługiwać kontenery.

W przypadku instalowania IoT Edge na maszynie wirtualnej należy włączyć wirtualizację zagnieżdżoną i przydzielić co najmniej 2 GB pamięci. W przypadku maszyn wirtualnych funkcji Hyper-V maszyny wirtualne generacji 2 mają domyślnie włączoną wirtualizację zagnieżdżoną. W przypadku oprogramowania VMware istnieje przełącznik umożliwiający włączenie tej funkcji na maszynie wirtualnej.

---

Azure IoT Edge pakiety oprogramowania podlegają postanowieniom licencyjnym znajdującym się w każdym pakiecie ( `usr/share/doc/{package-name}` lub `LICENSE` katalogu). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj tego pakietu.

## <a name="install-a-container-engine"></a>Instalowanie aparatu kontenera

Azure IoT Edge opiera się na zgodnym ze STEROWNIKIem środowiska uruchomieniowego kontenera. W przypadku scenariuszy produkcyjnych zaleca się użycie aparatu opartego na Moby. Aparat Moby jest jedynym aparatem kontenera oficjalnie obsługiwanym przez Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne ze środowiskiem uruchomieniowym Moby.

# <a name="linux"></a>[Linux](#tab/linux)

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

W przypadku wystąpienia błędów podczas instalowania aparatu kontenera Moby należy zweryfikować jądro systemu Linux pod kątem zgodności Moby. Niektórzy producenci urządzeń osadzonych dostarczają obrazy urządzeń, które zawierają niestandardowe jądra systemu Linux bez funkcji wymaganych do zapewnienia zgodności aparatu kontenera. Uruchom następujące polecenie, które używa [skryptu Check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) dostarczonego przez Moby, aby sprawdzić konfigurację jądra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

W danych wyjściowych skryptu Sprawdź, czy wszystkie elementy w obszarze `Generally Necessary` i `Network Drivers` są włączone. Jeśli nie masz żadnych funkcji, włącz je przez przebudowywanie jądra ze źródła i wybranie skojarzonych modułów do uwzględnienia w odpowiednim pliku kernel. config. Podobnie, jeśli używasz generatora konfiguracji jądra, takiego jak `defconfig` lub `menuconfig` , Znajdź i Włącz odpowiednie funkcje i odpowiednio Skompiluj jądro. Po wdrożeniu nowo zmodyfikowanego jądra ponownie uruchom skrypt Check-config, aby sprawdzić, czy wszystkie wymagane funkcje zostały pomyślnie włączone.

# <a name="windows"></a>[Windows](#tab/windows)

W przypadku scenariuszy produkcyjnych należy użyć aparatu opartego na Moby, który jest zawarty w skrypcie instalacji. Nie ma dodatkowych kroków, które należy wykonać, aby zainstalować aparat.

W przypadku IoT Edge z kontenerami systemu Linux należy zapewnić własne środowisko uruchomieniowe kontenera. Zainstaluj program [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) na urządzeniu i skonfiguruj go tak, aby [korzystał z kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) przed kontynuowaniem.

---

## <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń IoT Edge

Demon Security IoT Edge zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany przy każdym rozruchu i uruchamiania urządzenia przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

Kroki opisane w tej sekcji przedstawiają typowy proces instalacji najnowszej wersji na urządzeniu z połączeniem internetowym. Jeśli musisz zainstalować określoną wersję, na przykład wersję wstępną lub zainstalować ją w trybie offline, wykonaj kroki [instalacji w trybie offline lub określonej wersji](#offline-or-specific-version-installation) w następnej sekcji.

# <a name="linux"></a>[Linux](#tab/linux)

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zaznacz, aby zobaczyć, które wersje IoT Edge są dostępne.

   ```bash
   apt list -a iotedge
   ```

Jeśli chcesz zainstalować najnowszą wersję demona zabezpieczeń, użyj następującego polecenia, które powoduje także zainstalowanie najnowszej wersji pakietu **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, określ wersję z listy apt dane wyjściowe. Należy również określić tę samą wersję dla pakietu **libiothsm-STD** , który w przeciwnym razie zainstaluje jego najnowszą wersję. Na przykład następujące polecenie instaluje najnowszą wersję wersji 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Jeśli wersja, którą chcesz zainstalować, nie znajduje się na liście, postępuj zgodnie z krokami [instalacji w trybie offline lub określonym wersją](#offline-or-specific-version-installation) w następnej sekcji. W tej sekcji pokazano, jak określić docelową poprzednią wersję demona IoT Edge Security lub wersja Release Candidate.

# <a name="windows"></a>[Windows](#tab/windows)

1. Uruchom program PowerShell jako administrator.

   Użyj sesji AMD64 programu PowerShell, a nie programu PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Uruchom polecenie [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , które wykonuje następujące zadania:

   * Sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji.
   * Włącza funkcję Containers.
   * Pobiera aparat Moby i środowisko uruchomieniowe IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`Polecenie domyślnie używa kontenerów systemu Windows. Jeśli chcesz używać kontenerów systemu Linux, Dodaj `ContainerOs` parametr:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. W tym momencie dane wyjściowe mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz.

Instalując IoT Edge na urządzeniu, można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatorowi lokalny katalog instalacji w trybie offline.

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge w systemie Windows](reference-windows-scripts.md).

---

Teraz, gdy aparat kontenera i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przejść do następnego kroku, który polega na zarejestrowaniu urządzenia w usłudze IoT Hub i skonfigurowaniu urządzenia przy użyciu tożsamości i informacji o uwierzytelnianiu w chmurze.

Wybierz Następny artykuł w oparciu o typ uwierzytelniania, którego chcesz użyć:

* [Uwierzytelnianie klucza symetrycznego](how-to-manual-provision-symmetric-key.md) jest szybsze, aby rozpocząć pracę.
* [Uwierzytelnianie certyfikatu X. 509](how-to-manual-provision-x509.md) jest bezpieczniejsze w scenariuszach produkcyjnych.

## <a name="offline-or-specific-version-installation"></a>Instalacja w trybie offline lub określona wersja

Kroki opisane w tej sekcji dotyczą scenariuszy nieuwzględnionych w standardowych krokach instalacji. Może to obejmować:

* Zainstaluj IoT Edge w trybie offline
* Zainstaluj wersję Release Candidate
* W systemie Windows Zainstaluj wersję inną niż Najnowsza

# <a name="linux"></a>[Linux](#tab/linux)

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zainstalować konkretną wersję środowiska uruchomieniowego Azure IoT Edge, która nie jest dostępna w programie `apt-get install` . Lista pakietów firmy Microsoft zawiera tylko ograniczony zestaw najnowszych wersji i ich wersje podrzędne, dlatego te kroki są przeznaczone dla wszystkich osób, które chcą zainstalować starszą wersję lub wersję programu Release Candidate.

Korzystając z poleceń zwinięcie, można kierować pliki składników bezpośrednio z repozytorium IoT Edge GitHub.

<!-- TODO: Offline installation? -->

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa.

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. Każde wydanie powinno mieć nowe pliki dla IoT Edge Security daemon i hsmlib. Użyj następujących poleceń, aby zaktualizować te składniki.

   1. Znajdź plik **libiothsm-STD** zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Znajdź plik **iotedge** , który jest zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   4. Aby zainstalować tę wersję demona zabezpieczeń IoT Edge, użyj skopiowanego linku w poniższym poleceniu.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

Podczas instalacji są pobierane trzy pliki:

* Skrypt programu PowerShell, który zawiera instrukcje dotyczące instalacji
* Microsoft Azure IoT Edge CAB, który zawiera demo IoT Edge Security daemon (iotedged), aparat kontenera Moby i interfejs wiersza polecenia Moby
* Instalator pakietu redystrybucyjnego programu Visual C++ (środowisko uruchomieniowe VC)

Jeśli urządzenie będzie w trybie offline w trakcie instalacji lub jeśli chcesz zainstalować określoną wersję IoT Edge, możesz pobrać te pliki przed czasem do urządzenia. Po zakończeniu instalacji wskaż skrypt instalacji znajdujący się w katalogu zawierającym pobrane pliki. Instalator najpierw sprawdza ten katalog, a następnie pobiera tylko składniki, które nie zostały odnalezione. Jeśli wszystkie pliki są dostępne w trybie offline, możesz zainstalować program bez połączenia z Internetem.

1. Najnowsze pliki instalacyjne IoT Edge wraz z poprzednimi wersjami znajdują się w artykule [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Znajdź wersję, którą chcesz zainstalować, i pobierz następujące pliki z sekcji **Assets** informacji o wersji na urządzeniu IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z wersji 1.0.9 lub nowszej lub Microsoft-Azure-IoTEdge.cab z wersji 1.0.8 i starszych.

   Microsoft-Azure-IotEdge-arm32.cab jest również dostępna od 1.0.9 tylko do celów testowych. IoT Edge nie jest obecnie obsługiwana na urządzeniach z systemem Windows ARM32.

   Ważne jest, aby użyć skryptu programu PowerShell z tej samej wersji co używany plik cab, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

3. Jeśli pobrany plik cab ma sufiks architektury, Zmień nazwę pliku na tylko **Microsoft-Azure-IoTEdge.cab**.

4. Opcjonalnie można pobrać Instalatora dla Visual C++ pakiet redystrybucyjny. Na przykład skrypt programu PowerShell używa tej wersji: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Zapisz Instalatora w tym samym folderze na urządzeniu IoT jako pliki IoT Edge.

5. Aby zainstalować program za pomocą składników trybu [offline, należy](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) uzyskać kopię lokalną skryptu programu PowerShell. 

6. Uruchom polecenie [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) z `-OfflineInstallationPath` parametrem. Podaj ścieżkę bezwzględną do katalogu plików. Przykład:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Polecenie wdrożenia użyje wszystkich składników znalezionych w lokalnym katalogu plików. Jeśli brakuje pliku cab lub Instalatora Visual C++, spróbuje je pobrać.

---

Teraz, gdy aparat kontenerów i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przejść do następnego kroku, który polega na [uwierzytelnieniu urządzenia IoT Edge w IoT Hub](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia, użyj następujących poleceń.

# <a name="linux"></a>[Linux](#tab/linux)

Usuń środowisko uruchomieniowe usługi IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po usunięciu środowiska uruchomieniowego IoT Edge wszystkie utworzone kontenery zostaną zatrzymane, ale nadal istnieją na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które pozostaną.

```bash
sudo docker ps -a
```

Usuń kontenery z urządzenia, w tym dwa kontenery środowiska uruchomieniowego.

```bash
sudo docker rm -f <container name>
```

Na koniec Usuń środowisko uruchomieniowe kontenera z urządzenia.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Windows, użyj polecenia [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) w oknie administracyjnym programu PowerShell. To polecenie usuwa środowisko uruchomieniowe IoT Edge wraz z istniejącą konfiguracją i danymi aparatu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Aby uzyskać więcej informacji o opcjach odinstalowywania, użyj polecenia `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu środowiska uruchomieniowego IoT Edge Skonfiguruj urządzenie w celu nawiązania połączenia z IoT Hub. Poniższe artykuły zapoznają się z artykułem rejestrowanie nowego urządzenia w chmurze, a następnie udostępnieniem urządzenia informacji o tożsamości i uwierzytelnianiu.

Wybierz Następny artykuł w oparciu o typ uwierzytelniania, którego chcesz użyć:

* **Klucz symetryczny**: zarówno IoT Hub, jak i urządzenie IoT Edge mają kopię klucza bezpiecznego. Gdy urządzenie łączy się z IoT Hub, sprawdza, czy klucze są zgodne. Ta metoda uwierzytelniania jest szybsza, aby rozpocząć pracę, ale nie jako bezpieczna.

  [Konfigurowanie urządzenia Azure IoT Edge z uwierzytelnianiem przy użyciu klucza symetrycznego](how-to-manual-provision-symmetric-key.md)

* Z **podpisem własnym x. 509**: urządzenie IoT Edge ma certyfikaty tożsamości x. 509, a IoT Hub otrzymuje odcisk palca certyfikatów. Gdy urządzenie łączy się z IoT Hub, porównują certyfikat z odciskiem palca. Ta metoda uwierzytelniania jest bezpieczniejsza i zalecana w scenariuszach produkcyjnych.

  [Konfigurowanie urządzenia Azure IoT Edge przy użyciu uwierzytelniania certyfikatu X. 509](how-to-manual-provision-x509.md)
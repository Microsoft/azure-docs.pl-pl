---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie-IntelliJ IDE — Azure | Microsoft Docs
description: Ten przewodnik przedstawia sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej przy użyciu IntelliJ do testowania i opracowywania.
author: v-krghan
manager: dominicbetts
ms.custom: devx-track-java
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: f7554843db247ade1cddff78902430a5d84debe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319171"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Wdróż lokalnie Akcelerator rozwiązania do monitorowania zdalnego — IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule opisano sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej na potrzeby testowania i programowania. Dowiesz się, jak uruchamiać mikrousługi w IntelliJ. Lokalne wdrożenie mikrousług będzie używać następujących usług w chmurze: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics i Azure Time Series Insights.

Jeśli chcesz uruchomić Akcelerator rozwiązania do zdalnego monitorowania w programie Docker na komputerze lokalnym, zobacz [wdrażanie akceleratora rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć usługi platformy Azure używane przez Akcelerator rozwiązania do monitorowania zdalnego, wymagana jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja komputera

Aby ukończyć lokalne wdrożenie, potrzebne są następujące narzędzia zainstalowane na lokalnym komputerze deweloperskim:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Wtyczka IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Wtyczka IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Wtyczka modułu wykonawczego IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/)

Node.js V8 jest wymaganiem wstępnym dla komputerów z interfejsem wiersza polecenia, które są używane przez skrypty do tworzenia zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> IntelliJ IDE jest dostępny dla systemów Windows i Mac.

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Repozytoria kodu źródłowego zdalnego monitorowania obejmują kod źródłowy i pliki konfiguracji platformy Docker wymagane do uruchomienia obrazów platformy Docker mikrousług.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia, aby przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jeden z następujących zestawów poleceń, aby sklonować repozytorium Java:

* Aby pobrać najnowszą wersję implementacji mikrousług Java, uruchom następujące polecenie:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Aby pobrać najnowsze moduły podrzędne, uruchom następujące polecenia:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Te polecenia umożliwiają pobranie kodu źródłowego dla wszystkich mikrousług oprócz skryptów używanych do lokalnego uruchamiania mikrousług. Nie jest potrzebny kod źródłowy do uruchamiania mikrousług w Docker. Jednak kod źródłowy jest przydatny, jeśli później planujesz zmodyfikować Akcelerator rozwiązania i przetestować zmiany lokalnie.

## <a name="deploy-the-azure-services"></a>Wdrażanie usług platformy Azure

Chociaż w tym artykule przedstawiono sposób lokalnego uruchamiania mikrousług, zależą one od usług platformy Azure działających w chmurze. Użyj poniższego skryptu, aby wdrożyć usługi platformy Azure. W przykładach skryptu założono, że używasz repozytorium Java na komputerze z systemem Windows. Jeśli pracujesz w innym środowisku, Dostosuj odpowiednio ścieżki, rozszerzenia plików i separatory ścieżki.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli nie utworzono jeszcze wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia przejdź do folderu **\services\scripts\local\launch** w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować narzędzie **interfejsu wiersza polecenia i** zalogować się do konta platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom skrypt **Start. cmd** . Skrypt prosi o następujące informacje:

   * Nazwa rozwiązania.
   * Subskrypcja platformy Azure, która ma być używana.
   * Lokalizacja centrum danych platformy Azure do użycia.

   Skrypt tworzy grupę zasobów na platformie Azure, która ma nazwę rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure używane przez Akcelerator rozwiązania. Tę grupę zasobów można usunąć, gdy nie są już potrzebne odpowiednie zasoby.

   Skrypt dodaje również zestaw zmiennych środowiskowych do komputera lokalnego. Każda nazwa zmiennej ma **komputery**z prefiksem. Te zmienne środowiskowe zawierają szczegóły, które umożliwiają zdalne monitorowanie Odczytywanie wartości konfiguracji z zasobu Azure Key Vault.

   > [!TIP]
   > Po zakończeniu działania skryptu zapisuje zmienne środowiskowe do pliku o nazwie ** \<your home folder\> \\ . PCs \\ \<solution name\> . env**. Można ich używać do przyszłych wdrożeń akceleratora rozwiązań. Należy pamiętać, że wszystkie zmienne środowiskowe ustawione na maszynie lokalnej przesłaniają wartości w pliku ** \\ \\ \\ . env skryptów usług** , gdy uruchamiasz **platformę Docker**.

1. Zamknij środowisko wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Korzystanie z istniejących zasobów platformy Azure

Jeśli zostały już utworzone wymagane zasoby platformy Azure, ustaw odpowiednie zmienne środowiskowe na komputerze lokalnym:
* **PCS_KEYVAULT_NAME**: nazwa zasobu Key Vault.
* **PCS_AAD_APPID**: identyfikator aplikacji Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: wpis tajny aplikacji usługi Azure AD.

Wartości konfiguracyjne zostaną odczytane z tego zasobu Key Vault. Te zmienne środowiskowe można zapisać w pliku ** \<your home folder\> \\ PCs \\ \<solution name\> . env** z wdrożenia. Należy pamiętać, że zmienne środowiskowe ustawione na wartości Zastąp na komputerze lokalnym w pliku ** \\ \\ Local \\ . env skryptów usług** po uruchomieniu **platformy Docker — tworzenie**.

Niektóre konfiguracje wymagające mikrousługi są przechowywane w wystąpieniu Key Vault, które zostało utworzone podczas początkowego wdrożenia. Odpowiednie zmienne w magazynie kluczy powinny być modyfikowane w razie konieczności.

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchomiono mikrousługi zdalnego monitorowania. Uruchamiasz:

* Interfejs użytkownika sieci Web w sposób natywny.
* Usługi Azure IoT, uwierzytelniania i Azure Stream Analytics Manager w usłudze Docker.
* Mikrousługi w IntelliJ.

### <a name="run-the-device-simulation-service"></a>Uruchamianie usługi symulacji urządzenia

Otwórz nowe okno wiersza polecenia. Sprawdź, czy masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **Start. cmd** w poprzedniej sekcji.

Uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi symulacji urządzenia. Usługa symuluje urządzenia dla rozwiązania do zdalnego monitorowania.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Uruchamianie usługi uwierzytelniania

Otwórz nowe okno wiersza polecenia, a następnie uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi uwierzytelniania. Za pomocą tej usługi można zarządzać użytkownikami, którzy mają uprawnienia dostępu do rozwiązań IoT platformy Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Uruchamianie usługi Stream Analytics Manager

Otwórz nowe okno wiersza polecenia, a następnie uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi Stream Analytics Manager. Za pomocą tej usługi można zarządzać zadaniami Stream Analytics. Takie zarządzanie obejmuje Ustawianie konfiguracji zadania i uruchamianie, zatrzymywanie i monitorowanie stanu zadania.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Wdróż wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousługi zdalnego monitorowania w IntelliJ.

#### <a name="import-a-project"></a>Importowanie projektu

1. Otwórz środowisko IDE IntelliJ.
1. Wybierz pozycję **Importuj projekt**.
1. Wybierz **Azure-IoT-PCs-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Utwórz konfiguracje uruchomieniowe

1. Wybierz kolejno opcje **Uruchom**  >  **Edytuj konfiguracje**.
1. Wybierz pozycję **Dodaj nowe**  >  **zadanie konfiguracji SBT**.
1. Wprowadź **nazwę**, a następnie wprowadź **zadania** jako **uruchomienia**.
1. Wybierz **katalog roboczy** na podstawie usługi, którą chcesz uruchomić.
1. Wybierz pozycję **Zastosuj**  >  **OK** , aby zapisać wybrane opcje.
1. Utwórz konfiguracje uruchomieniowe dla następujących usług sieci Web:
    * Usługa sieci Web (services\config)
    * Usługa sieci Web (services\device-Telemetry)
    * Usługa sieci Web (services\iothub-Manager)
    * Usługa sieci Web (services\storage-adapter)

Na przykład na poniższej ilustracji przedstawiono sposób dodawania konfiguracji dla usługi:

[![Zrzut ekranu przedstawiający okno konfiguracje uruchamiania/debugowania środowiska IDE IntelliJ z wyróżnioną opcją storageAdapter na liście zadania SBT w okienku po lewej stronie, a następnie wpisów w polach Nazwa, zadania, katalog roboczy i parametry maszyny wirtualnej w okienku po prawej stronie.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Tworzenie konfiguracji złożonej

1. Aby uruchomić wszystkie usługi razem, wybierz pozycję **Dodaj nowy**  >  **związek**konfiguracji.
1. Wprowadź **nazwę**, a następnie wybierz pozycję **Dodaj zadania SBT**.
1. Wybierz pozycję **Zastosuj**  >  **OK** , aby zapisać wybrane opcje.

Na przykład na poniższej ilustracji przedstawiono sposób dodawania wszystkich zadań SBT do jednej konfiguracji:

[![Zrzut ekranu przedstawiający okno konfiguracje uruchamiania/debugowania środowiska IDE IntelliJ z wyróżnioną opcją AllServices w okienku po lewej stronie, a opcja "deviceTelemetry" zadania SBT została wyróżniona w okienku po prawej stronie.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Wybierz pozycję **Uruchom** , aby skompilować i uruchomić usługi sieci Web na komputerze lokalnym.

Każda usługa sieci Web otwiera okno wiersza polecenia i okno przeglądarki sieci Web. W wierszu polecenia są wyświetlane dane wyjściowe z uruchomionej usługi. Okno przeglądarki umożliwia monitorowanie stanu. Nie zamykaj okien wiersza polecenia lub stron sieci Web, ponieważ te akcje zatrzymują usługę internetową.

Aby uzyskać dostęp do stanu usług, przejdź do następujących adresów URL:

* IoT-Hub Manager: `http://localhost:9002/v1/status`
* Dane telemetryczne urządzenia: `http://localhost:9004/v1/status`
* sygnatur `http://localhost:9005/v1/status`
* Magazyn-karta: `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie Stream Analytics:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** utworzonej dla Twojego rozwiązania. Nazwa grupy zasobów jest nazwą wybraną dla rozwiązania po uruchomieniu skryptu **Start. cmd** .
1. Wybierz **zadanie Stream Analytics** na liście zasobów.
1. Na stronie **Przegląd** zadania Stream Analytics wybierz przycisk **Start** , a następnie wybierz polecenie **Uruchom** , aby uruchomić zadanie.

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku zostanie uruchomiony interfejs użytkownika sieci Web. Otwórz nowe okno wiersza polecenia. Sprawdź, czy masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **Start. cmd** . Przejdź do folderu **WebUI** w lokalnej kopii repozytorium, a następnie uruchom następujące polecenia:

```cmd
npm install
npm start
```

Gdy polecenie **Uruchom** zostanie wykonane, przeglądarka wyświetli stronę pod adresem `http://localhost:3000/dashboard` . Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujące czynności.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie Nginx

Skonfiguruj odwrotny serwer proxy, który łączy aplikację sieci Web z mikrousługami działającymi na komputerze lokalnym:

1. Skopiuj plik **Nginx. conf** z folderu **webui\scripts\localhost** w lokalnej kopii repozytorium do katalogu instalacyjnego **nginx\conf** .
1. Uruchom Nginx.

Aby uzyskać więcej informacji na temat uruchamiania programu Nginx, zobacz [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Nawiązywanie połączenia z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego, przejdź do `http://localhost:9000` obszaru w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, Usuń usługi w chmurze z subskrypcji platformy Azure po zakończeniu testowania. Aby usunąć usługi, przejdź do [Azure Portal](https://ms.portal.azure.com)i Usuń grupę zasobów utworzoną przez skrypt **Start. cmd** .

Możesz również usunąć kopię lokalną repozytorium zdalnego monitorowania, które zostało utworzone podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do monitorowania zdalnego, następnym krokiem jest zapoznanie się z [możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).

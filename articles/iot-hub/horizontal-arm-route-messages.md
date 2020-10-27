---
title: Używanie szablonu ARM do publikowania IoT Hub Azure, konta magazynu i komunikatów routingu
description: Używanie szablonu ARM do publikowania IoT Hub Azure, konta magazynu i komunikatów routingu
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 1b9c576ce03d808fe6a4d0cac5196dfcd1b73eab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545484"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Szybki Start: wdrażanie IoT Hub platformy Azure i konta magazynu przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć IoT Hub, który będzie kierować komunikaty do usługi Azure Storage, oraz konto magazynu do przechowywania komunikatów. Po ręcznym dodaniu wirtualnego urządzenia IoT do centrum w celu przesłania komunikatów należy skonfigurować te informacje o połączeniu w aplikacji o nazwie  *ARM-Read-Write* w celu przesłania komunikatów z urządzenia do centrum. Centrum jest skonfigurowane tak, aby komunikaty wysyłane do centrum były automatycznie kierowane do konta magazynu. Na końcu tego przewodnika Szybki Start możesz otworzyć konto magazynu i zobaczyć wysłane komunikaty.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki Start jest wywoływany `101-iothub-auto-route-messages` z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Wdróż szablon i uruchom przykładową aplikację

Ta sekcja zawiera instrukcje wdrażania szablonu, tworzenia urządzenia wirtualnego i uruchamiania aplikacji ARM-Read-Write w celu wysyłania komunikatów.

1. Utwórz zasoby, wdrażając szablon ARM.

    > [!TIP]
    > Wybierz poniższy przycisk, aby rozpocząć wdrażanie szablonu. Gdy jest uruchomiona, skonfiguruj aplikację ARM-Read-Write do uruchomienia.

    [![Wdrażanie na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Pobierz i rozpakuj [przykłady dla środowiska IoT C#](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Otwórz okno polecenia i przejdź do folderu, w którym zostały rozpakowane przykłady dotyczące środowiska IoT w języku C#. Znajdź folder z plikiem ARM-Read-Write. csproj. Zmienne środowiskowe są tworzone w tym oknie poleceń. Zaloguj się do [Azure Portal](https://portal.azure.com) , aby uzyskać klucze. Wybierz pozycję **grupy zasobów** , a następnie wybierz grupę zasobów używaną w tym przewodniku Szybki Start.

   ![Wybierz grupę zasobów](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Zobaczysz IoT Hub i konto magazynu, które zostały utworzone podczas wdrażania szablonu ARM. Poczekaj na całkowite wdrożenie szablonu przed kontynuowaniem. Następnie wybierz grupę zasobów, aby wyświetlić swoje zasoby.

   ![Wyświetlanie zasobów w grupie zasobów](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Potrzebna jest **nazwa centrum** . Wybierz centrum na liście zasobów. Skopiuj nazwę centrum z górnej części sekcji IoT Hub do Schowka systemu Windows.

   ![Kopiuj nazwę centrum](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    W tym poleceniu Zastąp nazwę centrum, a następnie wykonaj to polecenie w oknie polecenia:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   który będzie wyglądać następująco:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. Następna zmienna środowiskowa to klucz urządzenia IoT. Dodaj nowe urządzenie do centrum, wybierając pozycję **urządzenia IoT** z menu IoT Hub centrum.

   ![Wybierz urządzenia IoT](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Po prawej stronie ekranu wybierz pozycję **+ Nowy** , aby dodać nowe urządzenie.

   Wprowadź nową nazwę urządzenia. Ten przewodnik Szybki Start używa nazwy rozpoczynającej się od **firmy Contoso-test-Device** . Zapisz urządzenie, a następnie ponownie otwórz ten ekran, aby pobrać klucz urządzenia. (Klucz jest generowany podczas zamykania okienka). Wybierz klucz podstawowy lub pomocniczy i skopiuj go do Schowka systemu Windows. W oknie polecenia Ustaw polecenie, które ma zostać wykonane, a następnie naciśnij klawisz **Enter** . Polecenie powinno wyglądać podobnie do tego, ale przy użyciu klucza urządzenia wklejonego w:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Ostatnia zmienna środowiskowa to **Identyfikator urządzenia** . W oknie wiersza polecenia Skonfiguruj polecenie i wykonaj je.

   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   który będzie wyglądać następująco:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Aby wyświetlić zdefiniowane zmienne środowiskowe, wpisz SET w wierszu polecenia i naciśnij klawisz **Enter** , a następnie wyszukaj te, zaczynając od **IoT** .

   ![Zobacz zmienne środowiskowe](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Teraz zmienne środowiskowe są ustawione, uruchom aplikację z tego samego okna poleceń. Ponieważ korzystasz z tego samego okna, zmienne będą dostępne w pamięci podczas uruchamiania aplikacji.

1. Aby uruchomić aplikację, wpisz następujące polecenie w oknie wiersza polecenia i naciśnij klawisz **Enter** .

    `dotnet run arm-read-write`

   Aplikacja generuje i wyświetla komunikaty w konsoli w miarę wysyłania poszczególnych komunikatów do centrum IoT Hub. Koncentrator został skonfigurowany w szablonie ARM w celu skonfigurowania automatycznego routingu. Komunikaty zawierające tekst `level = storage` są automatycznie kierowane do konta magazynu. Pozwól, aby aplikacja działała od 10 do 15 minut, a następnie naciśnij klawisz **Enter** jeden raz lub dwa razy, dopóki nie przestanie działać.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz grupę zasobów, a następnie wybierz konto magazynu.

1. Przejdź do szczegółów konta magazynu, aż znajdziesz pliki.

   ![Poszukaj plików konta magazynu](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Wybierz jeden z plików, a następnie wybierz pozycję **Pobierz** i Pobierz plik do lokalizacji, którą można znaleźć w dalszej części. Nazwa ta będzie miała wartość numeryczną, na przykład 47. Dodaj plik _. txt_ na końcu, a następnie kliknij go dwukrotnie, aby go otworzyć.

1. Po otwarciu pliku każdy wiersz jest przeznaczony dla innego komunikatu; treść każdej wiadomości również jest zaszyfrowana. Aby można było wykonywać zapytania względem treści wiadomości, musi to być.

   ![Wyświetlanie wysłanych komunikatów](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Te komunikaty są kodowane w formacie UTF-32 i Base64. Jeśli odczytasz komunikat z powrotem, musisz go zdekodować z formatu base64 i UTF-32, aby można było go odczytać jako ASCII. Jeśli interesują Cię zainteresowania, możesz użyć metody ReadOneRowFromFile w samouczku routingu, aby odczytywać jeden z tych plików wiadomości i dekodować go do ASCII. ReadOneRowFromFile znajduje się w repozytorium przykładów usługi IoT C#, które zostało rozpakowane w ramach tego przewodnika Szybki Start. Poniżej znajduje się ścieżka znajdująca się u góry tego folderu: *./IoT-Hub/Tutorials/Routing/SimulatedDevice/program.cs.* Ustaw wartość logiczną `readTheFile` na true, a następnie umieszczaj ścieżkę do pliku na dysku i zostanie otwarty i przetłumaczy pierwszy wiersz w pliku.

Wdrożono szablon ARM w celu utworzenia IoT Hub i konta magazynu oraz uruchamiania programu w celu wysyłania komunikatów do centrum. Komunikaty są następnie automatycznie przechowywane na koncie magazynu, w którym można je wyświetlić.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć zasoby dodane w ramach tego przewodnika Szybki Start, zaloguj się do [Azure Portal](https://portal.azure.com). Wybierz pozycję **grupy zasobów** , a następnie Znajdź grupę zasobów, która została użyta w tym przewodniku Szybki Start. Wybierz grupę zasobów, a następnie wybierz pozycję *Usuń* . Spowoduje to usunięcie wszystkich zasobów w grupie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

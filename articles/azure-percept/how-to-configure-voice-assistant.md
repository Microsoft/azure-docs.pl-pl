---
title: Konfigurowanie aplikacji asystenta głosowego przy użyciu usługi Azure IoT Hub
description: Konfigurowanie aplikacji asystenta głosowego przy użyciu usługi Azure IoT Hub
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663086"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Konfigurowanie aplikacji asystenta głosowego przy użyciu usługi Azure IoT Hub

W tym artykule opisano sposób konfigurowania aplikacji asystenta głosowego przy użyciu IoT Hub. Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia asystenta głosowego przy użyciu szablonu demonstracyjnego, zobacz [Tworzenie asystenta głosowego bez kodu za pomocą usług Azure Percept Studio i Azure Percept audio](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Aktualizowanie konfiguracji asystenta głosowego

1. Otwórz [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) i wpisz **IoT Hub** na pasku wyszukiwania. Kliknij ikonę, aby otworzyć stronę IoT Hub.

1. Na stronie IoT Hub wybierz IoT Hub, do którego urządzenie zostało zainicjowane.

1. Wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami** w menu nawigacji po lewej stronie, aby wyświetlić wszystkie urządzenia połączone z IoT Hub.

1. Wybierz urządzenie, na którym aplikacja asystenta głosowego została wdrożona.

1. Kliknij pozycję **Ustaw moduły**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Image.":::

1. Sprawdź, czy w sekcji **Container Registry poświadczeń** znajduje się następujący wpis. W razie potrzeby Dodaj poświadczenia.

    |Nazwa|Adres|Nazwa użytkownika|Hasło|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. W sekcji **IoT Edge modułów** wybierz pozycję **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Image.":::

1. Kliknij kartę **Ustawienia modułu** . Sprawdź poniższą konfigurację:

    |Identyfikator URI obrazu|Zasady ponownego uruchamiania|Żądany stan|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |zawsze|uruchomion|

    Jeśli ustawienia nie pasują do siebie, edytuj je i kliknij przycisk **Aktualizuj**.

1. Kliknij kartę **zmienne środowiskowe** . Sprawdź, czy żadne zmienne środowiskowe nie są zdefiniowane.

1. Kliknij kartę **Opcje tworzenia kontenera** . Sprawdź, czy ustawienia **HostConfig** są zgodne z poniższymi poniżej. Jeśli nie są zgodne, zaktualizuj ustawienia.

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. Kliknij kartę **Ustawienia sznurka modułu** . Zaktualizuj sekcję **speechConfigs** w następujący sposób:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Użyte powyżej słowo kluczowe jest domyślnym publicznie dostępnym słowem kluczowym. Jeśli chcesz użyć własnych, możesz dodać własne niestandardowe słowo kluczowe, przekazując utworzony plik tabeli do magazynu obiektów BLOB. Magazyn obiektów BLOB musi być skonfigurowany przy użyciu anonimowego dostępu do kontenera lub anonimowego dostępu do obiektów BLOB.

## <a name="how-to-find-out-appid-key-and-region"></a>Jak znaleźć identyfikator appId, klucz i region

Aby zlokalizować **Identyfikator aplikacji**, **klucz** i **region**, przejdź do pozycji [Speech Studio](https://speech.microsoft.com/):

1. Zaloguj się i wybierz odpowiedni zasób mowy.
1. Na stronie głównej programu **Speech Studio** kliknij przycisk **niestandardowe polecenia** w obszarze **Asystenci głosu**.
1. Wybierz projekt docelowy.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Image.":::

1. Kliknij pozycję **Ustawienia** w panelu menu po lewej stronie.
1. **Identyfikator appid** i **klucz** będą znajdować się na karcie Ustawienia **Ogólne** .

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Image.":::

1. Aby znaleźć **region**, Otwórz kartę **Luis Resources (zasoby** ) w ustawieniach. Wybór **zasobów tworzenia** będzie zawierać informacje o regionie.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Image.":::

1. Po wprowadzeniu informacji o **speechConfigs** kliknij pozycję **Aktualizuj**.

1. Kliknij kartę **trasy** w górnej części strony **Ustaw moduły** . Upewnij się, że masz trasę o następującej wartości:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Dodaj trasę, jeśli nie istnieje.

1. Kliknij pozycję **Przejrzyj i utwórz**.

1. Kliknij pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

Po zaktualizowaniu konfiguracji asystenta głosowego Wróć do pokazu w usłudze Azure Percept Studio, aby móc korzystać z aplikacji.
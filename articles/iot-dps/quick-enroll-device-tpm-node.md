---
title: Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure Device Provisioning przy użyciu Node.js
description: Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu zestawu SDK usługi Node.js. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 184fb4bbf8845b749459e1963bed3c6d9fa64856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323849"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Szybki Start: rejestrowanie urządzenia TPM w celu IoT Hub Device Provisioning Service przy użyciu zestawu SDK usługi Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym przewodniku szybki start można programowo utworzyć rejestrację indywidualną dla urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service przy użyciu zestawu SDK usługi Node.js i przykładowej aplikacji Node.js. Opcjonalnie można zarejestrować symulowane urządzenie TPM w usłudze aprowizacji przy użyciu tego wpisu rejestracji indywidualnej.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org). Ten przewodnik Szybki Start instaluje [ zestaw SDK usługiNode.js](https://github.com/Azure/azure-iot-sdk-node) poniżej.
- Klucz poręczenia (opcjonalnie). Wykonaj kroki opisane w temacie [Tworzenie i udostępnianie symulowanego urządzenia](quick-create-simulated-device.md) do momentu uzyskania klucza. Nie należy tworzyć rejestracji indywidualnej przy użyciu Azure Portal.

## <a name="create-the-individual-enrollment-sample"></a>Tworzenie przykładowej rejestracji indywidualnej 

 
1. Z poziomu okna polecenia w folderze roboczym uruchom następujące polecenie:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Za pomocą edytora tekstu utwórz plik **create_individual_enrollment.js** w folderze roboczym. Dodaj następujący kod do pliku i zapisz go:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Uruchamianie przykładowej rejestracji indywidualnej
  
1. Do uruchomienia przykładu będą potrzebne parametry połączenia usługi aprowizacji. 
    1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę Device Provisioning. 
    2. Wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć jego właściwości. W oknie **Zasady dostępu** skopiuj i zanotuj parametry połączenia klucza podstawowego. 

       ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Wymagany jest również klucz poręczenia dla danego urządzenia. Jeśli w celu utworzenia symulowanego urządzenia TPM wykonano kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md) przewodnika Szybki start, użyj klucza utworzonego dla tego urządzenia. W przeciwnym razie aby utworzyć przykładową rejestrację indywidualną, można użyć następującego klucza poręczenia dostarczonego z [ zestawem SDK usługiNode.js](https://github.com/Azure/azure-iot-sdk-node):

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Aby utworzyć rejestrację indywidualną dla urządzenia TPM, uruchom następujące polecenie (z uwzględnieniem cudzysłowów wokół argumentów polecenia):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Po pomyślnym utworzeniu nowej rejestracji indywidualnej w oknie polecenia zostaną wyświetlone jej właściwości.

    ![Właściwości rejestracji w danych wyjściowych polecenia](./media/quick-enroll-device-tpm-node/output.png) 

4. Sprawdź, czy rejestracja indywidualna została utworzona. W witrynie Azure Portal w bloku podsumowania usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **rejestracje indywidualne** i wybierz nowy wpis rejestracji (*pierwszy*), aby zweryfikować Klucz poręczenia i inne właściwości wpisu.

    ![Właściwości rejestracji w portalu](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Jeśli po utworzeniu rejestracji indywidualnej dla urządzenia TPM chcesz zarejestrować symulowane urządzenie, możesz wykonać pozostałe kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md). Pamiętaj, aby pominąć procedurę tworzenia rejestracji indywidualnej przy użyciu Azure Portal w tym przewodniku Szybki Start.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli planujesz Eksplorowanie przykładów usługi Node.js, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu środowiska Node.js na swojej maszynie.
1. Jeśli utworzono symulowane urządzenie TPM, zamknij okno symulatora modułu TPM.
2. Przejdź do usługi Device Provisioning w Azure Portal wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki Start i naciśnij przycisk **Usuń** w górnej części okienka. 
 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start został utworzony indywidualny wpis rejestracji dla urządzenia TPM i, opcjonalnie, utworzono symulowane urządzenie TPM na komputerze i zostało ono zainicjowane w usłudze IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 
 
> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
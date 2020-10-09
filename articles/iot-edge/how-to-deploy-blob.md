---
title: Wdróż magazyn obiektów BLOB w module na urządzeniu — Azure IoT Edge
description: Wdróż moduł Blob Storage platformy Azure na urządzeniu IoT Edge, aby przechowywać dane na krawędzi.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80804626"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Deploy the Azure Blob Storage on IoT Edge module to your device (Wdrażanie modułu usługi Azure Blob Storage w usłudze IoT Edge na urządzeniu)

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują na platformie Azure Blob Storage w modułach IoT Edge. Dwie najprostsza metoda polega na użyciu szablonów Azure Portal lub Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

- [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
- [Urządzenie IoT Edge](how-to-register-device.md) z zainstalowanym IoT Edge środowiska uruchomieniowego.
- [Visual Studio Code](https://code.visualstudio.com/) i [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) w przypadku wdrażania z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Wdróż z Azure Portal

Azure Portal przeprowadzi Cię przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge.

### <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia. Trzy kroki są zorganizowane na kartach: **moduły**, **trasy**i **Przegląd + Utwórz**.

#### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj** listę rozwijaną i wybierz **moduł IoT Edge** , aby wyświetlić stronę **Dodawanie modułu IoT Edge** .

2. Na karcie **Ustawienia modułu** Podaj nazwę modułu, a następnie określ identyfikator URI obrazu kontenera:

   Przykłady:
  
   - **Nazwa modułu IoT Edge**: `azureblobstorageoniotedge`
   - **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nie zaznaczaj opcji **Dodaj** , dopóki nie zostaną określone wartości na kartach **Ustawienia modułu**, **Opcje tworzenia kontenera**i  **Ustawienia sznurka modułu** , zgodnie z opisem w tej procedurze.

   > [!IMPORTANT]
   > W Azure IoT Edge jest rozróżniana wielkość liter podczas wykonywania wywołań do modułów, a zestaw SDK magazynu jest również domyślnie pisany małymi literami. Mimo że nazwa modułu w [witrynie Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) to **AzureBlobStorageonIoTEdge**, zmiana nazwy na małe jest pomocne, aby upewnić się, że połączenia z usługą Azure Blob Storage na IoT Edge module nie zostaną przerwane.

3. Otwórz kartę **Opcje tworzenia kontenera** .

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Skopiuj i wklej poniższy kod JSON w polu, aby podać informacje o koncie magazynu i instalację magazynu na urządzeniu.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Zaktualizuj kod JSON skopiowany do **opcji tworzenia kontenera** z następującymi informacjami:

   - Zamień `<your storage account name>` na nazwę, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

   - Zamień na `<your storage account key>` 64-bajtowy klucz Base64. Klucz można wygenerować za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

   - Zamień `<storage mount>` według systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) lub ścieżkę bezwzględną do istniejącego katalogu na urządzeniu IoT Edge, w którym moduł obiektów BLOB będzie przechowywał swoje dane. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.

     - W przypadku kontenerów systemu Linux format to ** \<your storage path or volume> :/blobroot**. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows formatem jest ** \<your storage path or volume> : C:/BlobRoot**. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Zamiast korzystać z dysku lokalnego, możesz zmapować lokalizację sieciową SMB, aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w Blob Storage na IoT Edge module. Instalacja magazynu musi być zawsze zakończona **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** dla kontenerów Windows.

5. Na karcie **Ustawienia sznurka modułu** Skopiuj poniższy kod JSON i wklej go w polu.

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab4.png)

   Skonfiguruj każdą właściwość z odpowiednią wartością, jak wskazano symbole zastępcze. Jeśli używasz symulatora IoT Edge, ustaw wartości dla zmiennych środowiskowych związanych z tymi właściwościami, zgodnie z opisem w [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Aby uzyskać informacje na temat konfigurowania deviceToCloudUploadProperties i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edycja sznurka modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat żądanych właściwości, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

6. Wybierz pozycję **Dodaj**.

7. Wybierz pozycję **Dalej: trasy** , aby przejść do sekcji trasy.

#### <a name="specify-routes"></a>Określ trasy

Zachowaj trasy domyślne i wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do sekcji Przegląd.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzednich dwóch sekcjach. Istnieją także dwa moduły zadeklarowane jako niedodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się na [IoT Edge środowisko uruchomieniowe](iot-edge-runtime.md) i są wymagane wartości domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

Po utworzeniu wdrożenia powrócisz do **IoT Edge** stronie Centrum IoT Hub.

1. Wybierz urządzenie IoT Edge, do którego należy wdrożenie, aby otworzyć jego szczegóły.
1. Sprawdź szczegóły urządzenia, aby sprawdzić, czy moduł usługi BLOB Storage jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.

## <a name="deploy-from-visual-studio-code"></a>Wdróż z Visual Studio Code

Azure IoT Edge udostępnia szablony Visual Studio Code, które ułatwiają opracowywanie rozwiązań brzegowych. Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu BLOB Storage i skonfigurować manifest wdrożenia.

1. Wybierz pozycję **Widok**  >  **paleta poleceń**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**.

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na komputerze deweloperskim, aby Visual Studio Code utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejący moduł (wprowadź pełny obraz URL)**. |
   | Podaj nazwę modułu | Wprowadź wszystkie małe nazwy dla modułu, takie jak **azureblobstorageoniotedge**.<br/><br/>Ważne jest używanie małych nazw dla Blob Storage platformy Azure na IoT Edge module. W IoT Edge jest rozróżniana wielkość liter podczas odwoływania się do modułów, a zestaw SDK magazynu jest domyślnie pisany małymi literami. |
   | Podaj obraz platformy Docker dla modułu | Podaj identyfikator URI obrazu: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code pobiera podane informacje, tworzy rozwiązanie IoT Edge, a następnie ładuje je w nowym oknie. Szablon rozwiązania tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu.

1. Otwórz *deployment.template.jsna* nowym obszarze roboczym rozwiązania i Znajdź sekcję **modułów** . Wprowadź następujące zmiany w konfiguracji:

   1. Usuń moduł **SimulatedTemperatureSensor** , ponieważ nie jest to konieczne w przypadku tego wdrożenia.

   1. Skopiuj i wklej następujący kod do `createOptions` pola:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aktualizowanie modułu opcji "Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Zamień `<your storage account name>` na nazwę, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

1. Zamień na `<your storage account key>` 64-bajtowy klucz Base64. Klucz można wygenerować za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

1. Zamień `<storage mount>` według systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) lub ścieżkę bezwzględną do katalogu na urządzeniu IoT Edge, w którym moduł obiektów BLOB ma przechowywać swoje dane. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.  

     - W przypadku kontenerów systemu Linux format to ** \<your storage path or volume> :/blobroot**. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows formatem jest ** \<your storage path or volume> : C:/BlobRoot**. Na przykład
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Zamiast korzystać z dysku lokalnego, można zmapować lokalizację sieciową SMB. Aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w Blob Storage na IoT Edge module. Instalacja magazynu musi być zawsze zakończona **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** dla kontenerów Windows.

1. Skonfiguruj [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) dla modułu, dodając następujący kod JSON do *deployment.template.js* pliku. Skonfiguruj każdą właściwość z odpowiednią wartością i Zapisz plik. Jeśli używasz symulatora IoT Edge, ustaw wartości w powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji wyjaśnienie [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![Ustaw żądane właściwości dla azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Aby uzyskać informacje na temat konfigurowania deviceToCloudUploadProperties i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edycja sznurka modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji o opcjach tworzenia kontenera, zasadach ponownego uruchamiania i żądanym stanie, zobacz [EdgeAgent wymagane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Zapisz plik *deployment.template.json*.

1. Kliknij prawym przyciskiem myszy **deployment.template.jsna** i wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

1. Visual Studio Code pobiera informacje podane w *deployment.template.json* i używa go do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym folderze **konfiguracji** w obszarze roboczym rozwiązania. Po umieszczeniu tego pliku można wykonać kroki opisane w sekcji [wdrażanie modułów Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) lub [wdrożyć moduły Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure 2,0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Wdróż wiele wystąpień modułów

Jeśli chcesz wdrożyć wiele wystąpień Blob Storage platformy Azure w module IoT Edge, musisz podać inną ścieżkę magazynu i zmienić `HostPort` wartość, z którą jest powiązany moduł. Moduły magazynu obiektów BLOB zawsze uwidaczniają port 11002 w kontenerze, ale można zadeklarować, który port jest powiązany z hostem.

Edytuj **Opcje tworzenia kontenera** (w Azure Portal) lub pole " **isoptions** " (w *deployment.template.js* pliku w Visual Studio Code), aby zmienić `HostPort` wartość:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Po nawiązaniu połączenia z dodatkowymi modułami magazynu obiektów BLOB Zmień punkt końcowy tak, aby wskazywał zaktualizowany port hosta.

## <a name="configure-proxy-support"></a>Konfigurowanie obsługi serwera proxy

Jeśli Twoja organizacja korzysta z serwera proxy, konieczne będzie skonfigurowanie obsługi serwera proxy dla modułów edgeAgent i edgeHub środowiska uruchomieniowego. Ten proces obejmuje dwa zadania:

- Skonfiguruj demony środowiska uruchomieniowego i agenta IoT Edge na urządzeniu.
- Ustaw zmienną środowiskową HTTPS_PROXY dla modułów w pliku JSON manifestu wdrożenia.

Ten proces jest opisany w temacie [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

Ponadto moduł magazynu obiektów BLOB wymaga również ustawienia HTTPS_PROXY w pliku wdrożenia manifestu. Możesz bezpośrednio edytować plik manifestu wdrożenia lub użyć Azure Portal.

1. Przejdź do centrum IoT Hub w Azure Portal a następnie wybierz pozycję **IoT Edge** w menu po lewej stronie.

1. Wybierz urządzenie, które ma zostać skonfigurowane przez moduł.

1. Wybierz pozycję **Ustaw moduły**.

1. W sekcji **IoT Edge modułów** na stronie wybierz moduł BLOB Storage.

1. Na stronie **Aktualizowanie modułu IoT Edge** wybierz kartę **zmienne środowiskowe** .

1. Dodaj `HTTPS_PROXY` **nazwę** i adres URL serwera proxy dla **wartości**.

      ![Ustaw zmienną środowiskową HTTPS_PROXY](./media/how-to-deploy-blob/https-proxy-config.png)

1. Kliknij przycisk **Aktualizuj**, a następnie **Przejrzyj + Utwórz**.

1. Zwróć uwagę, że serwer proxy został dodany do modułu w manifeście wdrożenia i wybierz pozycję **Utwórz**.

1. Sprawdź ustawienia, wybierając moduł ze strony Szczegóły urządzenia, a następnie w dolnej części strony **szczegóły modułów IoT Edge** wybierz kartę **zmienne środowiskowe** .

      ![Ustaw zmienną środowiskową HTTPS_PROXY](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Blob Storage w IoT Edge](how-to-store-data-blob.md).

Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

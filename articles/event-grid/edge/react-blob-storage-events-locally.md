---
title: Reagowanie na zdarzenia modułu Blob Storage — Azure Event Grid IoT Edge | Microsoft Docs
description: Reagowanie na zdarzenia modułu Blob Storage
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005065"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Samouczek: reagowanie na zdarzenia Blob Storage w IoT Edge (wersja zapoznawcza)
W tym artykule opisano sposób wdrażania Blob Storage platformy Azure w module IoT, który mógłby pełnić rolę Event Grid wydawcy do wysyłania zdarzeń dotyczących tworzenia obiektów blob i usuwania obiektów BLOB do Event Grid.  

Aby zapoznać się z omówieniem Blob Storage platformy Azure na IoT Edge, zobacz [azure BLOB Storage na IoT Edge](../../iot-edge/how-to-store-data-blob.md) i jej funkcjach.

> [!WARNING]
> Usługa Azure Blob Storage w systemie IoT Edge integrację z usługą Event Grid jest w wersji zapoznawczej

Aby można było ukończyć ten samouczek, potrzebne są:

* **Subskrypcja platformy Azure** — Utwórz [bezpłatne konto](https://azure.microsoft.com/free) , jeśli jeszcze go nie masz. 
* **Azure IoT Hub i IoT Edge Device** — wykonaj kroki opisane w przewodniku szybki start dla [urządzeń](../../iot-edge/quickstart.md) z systemem [Linux](../../iot-edge/quickstart-linux.md) lub Windows, jeśli jeszcze go nie masz.

## <a name="deploy-event-grid-iot-edge-module"></a>Wdróż moduł IoT Edge Event Grid

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują Azure Event Grid w IoT Edge. W tym artykule opisano kroki wdrażania Event Grid na IoT Edge z Azure Portal.

>[!NOTE]
> W tym samouczku zostanie wdrożony moduł Event Grid bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po ponownym wdrożeniu modułu. Aby uzyskać więcej informacji na temat sposobu konfiguracji trwałości, zobacz następujące artykuły: [utrwalanie stanu w systemie Linux](persist-state-linux.md) lub [w stanie utrwalania systemu Windows](persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu Event Grid z trwałością.


### <a name="select-your-iot-edge-device"></a>Wybierz urządzenie IoT Edge

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami** . 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Nie otwieraj strony. Wykonaj kroki opisane w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Składa się z trzech kroków: **Dodawanie modułów**, **Określanie tras** i **przeglądanie wdrożenia**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **moduły wdrażania** wybierz pozycję **Dodaj** .
1. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
1. Podaj nazwę, obraz i opcje tworzenia kontenera:

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Kliknij pozycję **Zapisz**.
 1. Przejdź do następnej sekcji, aby dodać moduł subskrybenta Azure Event Grid przed ich wdrożeniem.

    >[!IMPORTANT]
    > W tym samouczku dowiesz się, jak wdrożyć moduł Event Grid, aby zezwolić na żądania HTTP/HTTPs, a uwierzytelnianie klienta zostało wyłączone. W przypadku obciążeń produkcyjnych zaleca się włączenie tylko żądań HTTPs i subskrybentów z włączonym uwierzytelnianiem klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu Event Grid, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Wdróż moduł IoT Edge subskrybenta Event Grid

W tej sekcji przedstawiono sposób wdrażania innego modułu IoT, który będzie działać jako procedura obsługi zdarzeń, do którego mogą być dostarczane zdarzenia.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **modułów wdrażania** wybierz ponownie przycisk **Dodaj** . 
1. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
1. Podaj nazwę, obraz i opcje tworzenia kontenera:

   * **Nazwa**: subskrybent
   * **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opcje tworzenia kontenera**: brak
1. Kliknij pozycję **Zapisz**.
1. Przejdź do następnej sekcji, aby dodać moduł Blob Storage platformy Azure

## <a name="deploy-azure-blob-storage-module"></a>Wdróż moduł Blob Storage platformy Azure

W tej sekcji pokazano, jak wdrożyć moduł Blob Storage platformy Azure, który będzie pełnić rolę utworzonego i usuniętego obiektu BLOB wydawcy Event Grid.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **moduły wdrażania** wybierz pozycję **Dodaj** .
2. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
3. Podaj nazwę, obraz i opcje tworzenia kontenera:

   * **Nazwa**: azureblobstorageoniotedge
   * **Identyfikator URI obrazu**: MCR.Microsoft.com/Azure-Blob-Storage:Latest
   * **Opcje tworzenia kontenera**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
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

   > [!IMPORTANT]
   > - Moduł Blob Storage umożliwia publikowanie zdarzeń przy użyciu protokołów HTTPS i HTTP. 
   > - W przypadku włączenia uwierzytelniania opartego na kliencie dla EventGrid upewnij się, że Zaktualizowano wartość EVENTGRID_ENDPOINT, aby zezwolić na używanie protokołu HTTPS w następujący sposób: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - Dodaj również kolejną zmienną środowiskową `AllowUnknownCertificateAuthority=true` do powyższego kodu JSON. Podczas rozmowy z EventGrid za pośrednictwem protokołu HTTPS **AllowUnknownCertificateAuthority** umożliwia modułowi magazynu ufanie certyfikatom serwera EventGrid z podpisem własnym.

4. Zaktualizuj skopiowany kod JSON przy użyciu następujących informacji:

   - Zamień `<your storage account name>` na nazwę, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

   - Zamień na `<your storage account key>` 64-bajtowy klucz Base64. Klucz można wygenerować za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

   - Zamień `<event grid module name>` na nazwę modułu Event Grid.
   - Zamień `<storage mount>` według systemu operacyjnego kontenera.
     - W przypadku kontenerów **systemu Linux woluminy my-Volume:/blobroot**
     - W przypadku kontenerów systemu Windows **mój wolumin: C:/BlobRoot**

5. Kliknij pozycję **Zapisz**.
6. Kliknij przycisk **dalej** , aby przejść do sekcji trasy

    > [!NOTE]
    > Jeśli używasz maszyny wirtualnej platformy Azure jako urządzenia brzegowego, Dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na portach hosta używanych w tym samouczku: 4438, 5888, 8080 i 11002. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie wirtualnej](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Skonfiguruj trasy

Zachowaj trasy domyślne, a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd

### <a name="review-deployment"></a>Przegląd wdrożenia

1. Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzedniej sekcji. Upewnij się, że zobaczysz następujące cztery moduły: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** i **azureblobstorageoniotedge** , że wszystkie wdrażane.
2. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

## <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

1. Po przesłaniu wdrożenia wrócisz do IoT Edge stronie Centrum IoT Hub.
2. Wybierz **urządzenie IoT Edge** , do którego należy wdrożenie, aby otworzyć jego szczegóły.
3. Na stronie Szczegóły urządzenia sprawdź, czy moduły eventgridmodule, Subscriber i azureblobstorageoniotedge są wymienione jako **określone we wdrożeniu** i **raportowane przez urządzenie**.

   Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publikuj zdarzenia BlobCreated i BlobDeleted

1. Ten moduł automatycznie tworzy **MicrosoftStorage** tematu. Sprawdź, czy istnieje
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - W przypadku przepływu HTTPS w przypadku włączenia uwierzytelniania klienta za pośrednictwem klucza SAS należy dodać określony wcześniej klucz sygnatury dostępu współdzielonego jako nagłówek. W związku z tym żądanie zwinięcie będzie: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zazwinięcia będzie: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby odebrać każde zdarzenie, musisz utworzyć subskrypcję Event Grid dla tematu **MicrosoftStorage** .
    1. Utwórz blobsubscription.jsprzy użyciu następującej zawartości. Aby uzyskać szczegółowe informacje o ładunku, zapoznaj się z naszą [dokumentacją interfejsu API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Właściwość **EndpointType** określa, że subskrybent jest elementem **webhook**.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odnosi się do wdrożonej wcześniej przykładowej funkcji platformy Azure.

    2. Uruchom następujące polecenie, aby utworzyć subskrypcję tematu. Upewnij się, że został wyświetlony kod stanu HTTP `200 OK` .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - W przypadku przepływu HTTPS w przypadku włączenia uwierzytelniania klienta za pośrednictwem klucza SAS należy dodać określony wcześniej klucz sygnatury dostępu współdzielonego jako nagłówek. W związku z tym żądanie zwinięcie będzie: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zazwinięcia będzie:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Uruchom następujące polecenie, aby sprawdzić, czy subskrypcja została pomyślnie utworzona. Należy zwrócić kod stanu HTTP 200 OK.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Przykładowe dane wyjściowe:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - W przypadku przepływu HTTPS w przypadku włączenia uwierzytelniania klienta za pośrednictwem klucza SAS należy dodać określony wcześniej klucz sygnatury dostępu współdzielonego jako nagłówek. W związku z tym żądanie zwinięcie będzie: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zazwinięcia będzie: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Pobierz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) i [Połącz je z lokalnym magazynem](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

### <a name="verify-blobcreated-event-delivery"></a>Weryfikowanie dostarczania zdarzeń BlobCreated

1. Przekaż pliki jako blokowe obiekty blob do magazynu lokalnego z Eksplorator usługi Azure Storage, a moduł automatycznie opublikuje zdarzenia tworzenia. 
2. Zapoznaj się z dziennikami subskrybentów, aby utworzyć zdarzenie. Postępuj zgodnie z instrukcjami, aby [zweryfikować dostarczenie zdarzenia](pub-sub-events-webhook-local.md#verify-event-delivery)

    Przykładowe dane wyjściowe:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Weryfikowanie dostarczania zdarzeń BlobDeleted

1. Usuwanie obiektów blob z magazynu lokalnego przy użyciu Eksplorator usługi Azure Storage, a moduł automatycznie publikuje zdarzenia Delete. 
2. Zapoznaj się z dziennikami subskrybentów dotyczącymi usuwania zdarzeń. Postępuj zgodnie z instrukcjami, aby [zweryfikować dostarczenie zdarzenia](pub-sub-events-webhook-local.md#verify-event-delivery)

    Przykładowe dane wyjściowe:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Gratulacje! Samouczek został ukończony. Poniższe sekcje zawierają szczegółowe informacje na temat właściwości zdarzenia.

### <a name="event-properties"></a>Właściwości zdarzenia

Poniżej znajduje się lista obsługiwanych właściwości zdarzeń oraz ich typów i opisów. 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia magazynu obiektów BLOB. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | ciąg | Operacja, która wyzwoliła zdarzenie. Może to być jedna z następujących wartości: <ul><li>BlobCreated — dozwolone wartości to: `PutBlob` i `PutBlockList`</li><li>BlobDeleted — dozwolone wartości to `DeleteBlob` , `DeleteAfterUpload` i `AutoDelete` . <p>`DeleteAfterUpload`Zdarzenie jest generowane, gdy obiekt BLOB jest automatycznie usuwany, ponieważ żądana Właściwość deleteAfterUpload ma wartość true. </p><p>`AutoDelete` zdarzenie jest generowane, gdy obiekt BLOB zostanie automatycznie usunięty, ponieważ deleteAfterMinutes żądana wartość właściwości wygasła.</p></li></ul>|
| Identyfikatorem żądania klienta | ciąg | Identyfikator żądania dostarczonego przez klienta dla operacji interfejsu API magazynu. Tego identyfikatora można użyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Client-Request-ID" w dziennikach i można go podać w żądaniach klientów przy użyciu nagłówka "x-MS-Client-Request-ID". Aby uzyskać szczegółowe informacje, zobacz [format dziennika](/rest/api/storageservices/storage-analytics-log-format). |
| IdentyfikatorŻądania | ciąg | Identyfikator żądania wygenerowanego przez usługę dla operacji interfejsu API magazynu. Może służyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Request-ID-Header" w dziennikach i jest zwracana z inicjowania wywołania interfejsu API w nagłówku "x-MS-Request-ID". Zobacz [format dziennika](/rest/api/storageservices/storage-analytics-log-format). |
| Element ETag | ciąg | Wartość, której można użyć do warunkowego wykonywania operacji. |
| contentType | ciąg | Typ zawartości określony dla obiektu BLOB. |
| contentLength | liczba całkowita | Rozmiar obiektu BLOB w bajtach. |
| Obiekt blobtype | ciąg | Typ obiektu BLOB. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| url | ciąg | Ścieżka do obiektu BLOB. <br>Jeśli klient używa interfejsu API REST usługi BLOB, ten adres URL ma następującą strukturę: *\<storage-account-name\> . blob.Core.Windows.NET/ \<container-name\> / \<file-name\>*. <br>Jeśli klient używa interfejsu API REST Data Lake Storage, ten adres URL ma następującą strukturę: *\<storage-account-name\> . DFS.Core.Windows.NET/ \<file-system-name\> / \<file-name\>*. |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami w dokumentacji Blob Storage:

- [Filtrowanie zdarzeń Blob Storage](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Zalecane praktyki związane z zużywaniem Blob Storage zdarzeń](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

W tym samouczku opublikowano zdarzenia przez tworzenie lub usuwanie obiektów BLOB w usłudze Azure Blob Storage. Zapoznaj się z innymi samouczkami, aby dowiedzieć się, jak przekazywać zdarzenia do chmury (Azure Event Hub lub Azure IoT Hub): 

- [Przesyłanie dalej zdarzeń do usługi Azure Event Grid](forward-events-event-grid-cloud.md)
- [Przesyłanie dalej zdarzeń do usługi Azure IoT Hub](forward-events-iothub.md)

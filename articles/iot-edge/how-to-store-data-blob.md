---
title: Przechowuj blokowe obiekty blob na urządzeniach — Azure IoT Edge | Microsoft Docs
description: Informacje o warstwach i funkcjach typu Time-to-Live można znaleźć w temacie obsługiwane operacje usługi BLOB Storage i nawiązać połączenie z kontem usługi BLOB Storage.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ee4e313366bafdd2f6e3bd0e104abd9f11b7776
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108674"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Store data at the edge with Azure Blob Storage on IoT Edge (Przechowywanie danych na urządzeniu brzegowym dzięki usłudze Azure Blob Storage w usłudze IoT Edge)

Usługa Azure Blob Storage w systemie IoT Edge zapewnia [blokowy obiekt BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) i [dołączanie rozwiązania BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Storage. Moduł magazynu obiektów BLOB na urządzeniu IoT Edge zachowuje się jak usługa Azure Blob Service, z tą różnicą, że obiekty blob są przechowywane lokalnie na urządzeniu IoT Edge. Możesz uzyskać dostęp do obiektów BLOB za pomocą tych samych metod zestawu SDK usługi Azure Storage lub wywołań interfejsu API obiektów blob, które są już używane. W tym artykule wyjaśniono koncepcje związane z Blob Storage platformy Azure na IoT Edge kontenera, w którym jest uruchomiona usługa BLOB na urządzeniu IoT Edge.

Ten moduł jest użyteczny w scenariuszach:

* miejsce, w którym dane muszą być przechowywane lokalnie, dopóki nie będzie można ich przetworzyć ani przenieść do chmury. Mogą to być dane wideo, obrazy, dane finansowe, dane szpitalne lub inne dane bez struktury.
* gdy urządzenia znajdują się w miejscu z ograniczoną łącznością.
* gdy chcesz efektywnie przetwarzać dane lokalnie w celu uzyskania dostępu do danych o małym opóźnieniu, tak jak najszybciej możesz odpowiedzieć na sytuacje awaryjne.
* Aby zmniejszyć koszty przepustowości i uniknąć przesyłania terabajtów danych do chmury. Dane można przetwarzać lokalnie i wysyłać tylko dane przetworzone do chmury.

Obejrzyj film wideo, aby uzyskać szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Ten moduł zawiera funkcje **deviceToCloudUpload** i **deviceAutoDelete** .

**deviceToCloudUpload** jest konfigurowalną funkcją. Ta funkcja automatycznie przekazuje dane z lokalnego magazynu obiektów BLOB na platformę Azure, używając sporadycznej obsługi łączności z Internetem. Umożliwia to:

* Włącz/Wyłącz funkcję deviceToCloudUpload.
* Wybierz kolejność kopiowania danych na platformę Azure, np. NewestFirst lub OldestFirst.
* Określ konto usługi Azure Storage, do którego chcesz przekazać dane.
* Określ kontenery, które chcesz przekazać do platformy Azure. Ten moduł pozwala określić źródłową i docelową nazwę kontenera.
* Wybierz możliwość natychmiastowego usunięcia obiektów BLOB po zakończeniu przekazywania do magazynu w chmurze
* Wykonaj pełne przekazywanie obiektów BLOB (przy użyciu `Put Blob` operacji) i przekazywanie na poziomie bloku (przy użyciu `Put Block` `Put Block List` i `Append Block` operacji).

Ten moduł używa przekazywania na poziomie bloku, gdy obiekt BLOB zawiera bloki. Poniżej przedstawiono niektóre typowe scenariusze:

* Aplikacja aktualizuje niektóre bloki poprzednio przekazanego bloku obiektu BLOB lub dołącza nowe bloki do obiektu BLOB dołączania, ten moduł przekazuje tylko zaktualizowane bloki, a nie cały obiekt BLOB.
* Moduł przekazuje obiekt BLOB i połączenie internetowe odchodzi, gdy połączenie zostanie ponownie przekazane, a następnie naładuje tylko pozostałe bloki, a nie cały obiekt BLOB.

Jeśli nieoczekiwane zakończenie procesu (na przykład awaria napięcia) wystąpi podczas przekazywania obiektu BLOB, wszystkie bloki, które były spowodowane przekazaniem, zostaną przekazane ponownie po powrocie modułu do trybu online.

**deviceAutoDelete** jest konfigurowalną funkcją. Ta funkcja automatycznie usuwa obiekty blob z magazynu lokalnego, gdy upłynie określony czas (mierzony w minutach). Umożliwia to:

* Włącz/Wyłącz funkcję deviceAutoDelete.
* Określ czas (w minutach) (deleteAfterMinutes), po którym obiekty blob zostaną automatycznie usunięte.
* Wybierz możliwość zachowania obiektu BLOB podczas przekazywania, gdy wartość deleteAfterMinutes wygaśnie.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Możesz użyć maszyny deweloperskiej lub maszyny wirtualnej jako urządzenia IoT Edge, wykonując czynności opisane w przewodniku szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

* Listę obsługiwanych systemów operacyjnych i architektur można znaleźć w [Azure IoT Edge obsługiwanych systemach](support.md#operating-systems) . Blob Storage platformy Azure w module IoT Edge obsługuje następujące architektury:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (wersja zapoznawcza)

Zasoby w chmurze:

Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Właściwości deviceToCloudUpload i deviceAutoDelete

Użyj odpowiednich właściwości modułu, aby ustawić **deviceToCloudUploadProperties** i **deviceAutoDeleteProperties**. Żądane właściwości można ustawić podczas wdrażania lub zmienić je później, edytując sznurek modułu bez konieczności ponownego wdrażania. Zalecamy sprawdzenie "sznurka modułu" dla `reported configuration` i, `configurationValidation` Aby upewnić się, że wartości są prawidłowo propagowane.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Nazwa tego ustawienia to `deviceToCloudUploadProperties` . Jeśli używasz symulatora IoT Edge, ustaw wartości w powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji wyjaśnienie.

| Właściwość | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| uploadOn | wartość true, false | Domyślnie ustawiona wartość `false` . Jeśli chcesz włączyć tę funkcję, ustaw to pole na `true` . <br><br> Zmienna środowiskowa: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Umożliwia wybranie kolejności, w której dane są kopiowane na platformę Azure. Domyślnie ustawiona wartość `OldestFirst` . Kolejność jest określana według czasu ostatniej modyfikacji obiektu BLOB. <br><br> Zmienna środowiskowa: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` to parametry połączenia, które umożliwiają określenie konta magazynu, do którego chcesz przekazać dane. Określ `Azure Storage Account Name` , `Azure Storage Account Key` , `End point suffix` . Dodaj odpowiednie EndpointSuffix systemu Azure, w przypadku których dane zostaną przekazane, różnią się w zależności od globalnego platformy Azure, platformy Azure dla instytucji rządowych i Microsoft Azure Stack. <br><br> W tym miejscu możesz określić parametry połączenia SAS usługi Azure Storage. Ale należy zaktualizować tę właściwość po jej wygaśnięciu. Uprawnienia dostępu współdzielonego mogą obejmować dostęp do kontenerów oraz tworzyć, zapisywać i dodawać dostęp do obiektów BLOB.  <br><br> Zmienna środowiskowa: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Pozwala określić nazwy kontenerów, które mają zostać przekazane na platformę Azure. Ten moduł pozwala określić źródłową i docelową nazwę kontenera. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypiszesz nazwę kontenera jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . Można utworzyć ciągi szablonów dla docelowej nazwy kontenera, zapoznaj się z kolumną możliwe wartości. <br>*% h-> IoT Hub nazwy (3-50 znaków). <br>*% d-> IoT Edge identyfikator urządzenia (od 1 do 129 znaków). <br>*% m-> Nazwa modułu (od 1 do 64 znaków). <br>*% c-> nazwę kontenera źródłowego (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwy kontenera to 63 znaków, podczas gdy automatyczne przypisywanie nazwy kontenera docelowego, jeśli rozmiar kontenera przekracza 63 znaków, przycinanie każdej sekcji (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) do 15 znaków. <br><br> Zmienna środowiskowa: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | wartość true, false | Domyślnie ustawiona wartość `false` . Po jego ustawieniu usługa `true` automatycznie usunie dane po zakończeniu przekazywania do magazynu w chmurze. <br><br> **Uwaga**: Jeśli używasz dołączanych obiektów blob, to ustawienie spowoduje usunięcie dołączanych obiektów blob z magazynu lokalnego po pomyślnym przekazaniu, a wszelkie przyszłe operacje dołączania bloków do tych obiektów BLOB zakończą się niepowodzeniem. Użyj tego ustawienia z zachowaniem ostrożności, nie należy włączać tej opcji, jeśli aplikacja wykonuje rzadko wykonywane operacje dołączania lub nie obsługuje ciągłych operacji dołączania<br><br> Zmienna środowiskowa: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Nazwa tego ustawienia to `deviceAutoDeleteProperties` . Jeśli używasz symulatora IoT Edge, ustaw wartości w powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji wyjaśnienie.

| Właściwość | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| deleteOn | wartość true, false | Domyślnie ustawiona wartość `false` . Jeśli chcesz włączyć tę funkcję, ustaw to pole na `true` . <br><br> Zmienna środowiskowa: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Określ czas (w minutach). Po wygaśnięciu tej wartości moduł automatycznie usunie obiekty blob z magazynu lokalnego. Bieżąca Maksymalna liczba dozwolonych minut to 35791. <br><br> Zmienna środowiskowa: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | wartość true, false | Domyślnie jest ustawiony na `true` i zachowuje obiekt BLOB podczas przekazywania go do magazynu w chmurze, jeśli deleteAfterMinutes wygasa. Można ją ustawić na `false` i będzie ona usuwać dane natychmiast po wygaśnięciu deleteAfterMinutes. Uwaga: aby ta właściwość działała, uploadOn powinna mieć wartość true.  <br><br> **Uwaga**: Jeśli używasz dołączanych obiektów blob, to ustawienie spowoduje usunięcie dołączanych obiektów blob z magazynu lokalnego, gdy wartość wygaśnie, a wszelkie przyszłe operacje dołączania bloków do tych obiektów BLOB zakończą się niepowodzeniem. Warto upewnić się, że wartość wygaśnięcia jest wystarczająco duża dla oczekiwanej częstotliwości operacji dołączania wykonywanych przez aplikację.<br><br> Zmienna środowiskowa: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Używanie udziału SMB jako magazynu lokalnego

Udział SMB można dostarczyć jako ścieżkę do magazynu lokalnego, podczas wdrażania kontenera systemu Windows tego modułu na hoście z systemem Windows.

Upewnij się, że udział SMB i urządzenie IoT należą do wzajemnie zaufanych domen.

Można uruchomić `New-SmbGlobalMapping` polecenie programu PowerShell, aby mapować udział SMB lokalnie na urządzeniu IoT z systemem Windows.

Poniżej przedstawiono kroki konfiguracji:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Na przykład:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

To polecenie użyje poświadczeń do uwierzytelnienia na zdalnym serwerze SMB. Następnie zmapuj ścieżkę udziału zdalnego na G: litera dysku (może to być jakakolwiek inna dostępna litera dysku). Urządzenie IoT ma teraz wolumin danych zamapowany na ścieżkę na dysku G:.

Upewnij się, że użytkownik w urządzeniu IoT może odczytywać i zapisywać dane w zdalnym udziale SMB.

W przypadku wdrożenia wartość `<storage mount>` może być równa **G:/ContainerData: C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Udzielanie dostępu do katalogu użytkownikowi kontenera w systemie Linux

Jeśli używasz [instalacji woluminu](https://docs.docker.com/storage/volumes/) dla magazynu w opcjach tworzenia dla kontenerów systemu Linux, nie musisz wykonywać żadnych dodatkowych czynności, ale jeśli korzystasz z [instalacji bind](https://docs.docker.com/storage/bind-mounts/) , te kroki są wymagane do poprawnego uruchomienia usługi.

Zgodnie z zasadą najniższych uprawnień, aby ograniczyć dostęp użytkowników do minimalnych uprawnień, których potrzebują do wykonania swojej pracy, ten moduł zawiera użytkownika (Name: Absie, ID: 11000) i grupę użytkowników (Name: Absie, ID: 11000). Jeśli kontener zostanie uruchomiony jako **główny** (domyślny użytkownik jest **katalogiem głównym**), Nasza usługa zostanie uruchomiona jako użytkownik z niskim poziomem uprawnień **Absie** .

Takie zachowanie powoduje, że konfiguracja uprawnień na ścieżka hosta wiąże się z tym, że usługa działa prawidłowo, w przeciwnym razie usługa ulegnie awarii z błędami odmowy dostępu. Ścieżka używana w powiązaniu katalogu musi być dostępna dla użytkownika kontenera (przykład: Absie 11000). Można udzielić użytkownikowi kontenera dostępu do katalogu, wykonując polecenia znajdujące się poniżej na hoście:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Na przykład:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Jeśli potrzebujesz uruchomić usługę jako użytkownik inny niż **Absie**, możesz określić niestandardowy identyfikator użytkownika w obszarze Opcje w obszarze właściwości "użytkownik" w manifeście wdrożenia. W takim przypadku należy użyć domyślnego lub głównego identyfikatora grupy `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Teraz Udziel użytkownikowi kontenera dostępu do katalogu

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurowanie plików dziennika

Aby uzyskać informacje na temat konfigurowania plików dziennika dla modułu, zobacz te [najlepsze rozwiązania](./production-checklist.md#set-up-logs-and-diagnostics)w zakresie produkcji.

## <a name="connect-to-your-blob-storage-module"></a>Nawiązywanie połączenia z modułem magazynu obiektów BLOB

Możesz użyć nazwy konta i klucza konta skonfigurowanego dla modułu, aby uzyskać dostęp do magazynu obiektów BLOB na urządzeniu IoT Edge.

Określ urządzenie IoT Edge jako punkt końcowy obiektu BLOB dla dowolnych żądań magazynu, które wprowadzasz do niego. [Parametry połączenia dla jawnego punktu końcowego magazynu można utworzyć](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu IoT Edge informacji o urządzeniu i skonfigurowanej nazwy konta.

* W przypadku modułów wdrożonych na tym samym urządzeniu, na których jest uruchomiony Blob Storage platformy Azure w module IoT Edge, punkt końcowy obiektu BLOB to: `http://<module name>:11002/<account name>` .
* W przypadku modułów lub aplikacji uruchamianych na innym urządzeniu należy wybrać odpowiedni punkt końcowy dla sieci. W zależności od konfiguracji sieci wybierz format punktu końcowego, który umożliwia dostęp danych z modułu zewnętrznego lub aplikacji do urządzenia z uruchomioną Blob Storage platformy Azure na IoT Edge module. Punkt końcowy obiektu BLOB w tym scenariuszu jest jednym z:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > W Azure IoT Edge jest rozróżniana wielkość liter podczas wykonywania wywołań do modułów, a zestaw SDK magazynu jest również domyślnie pisany małymi literami. Mimo że nazwa modułu w [witrynie Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) to **AzureBlobStorageonIoTEdge**, zmiana nazwy na małe jest pomocne, aby upewnić się, że połączenia z usługą Azure Blob Storage na IoT Edge module nie zostaną przerwane.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Przykłady szybkiego startu platformy Azure Blob Storage

Dokumentacja usługi Azure Blob Storage zawiera przykładowy kod szybkiego startu w kilku językach. Te przykłady można uruchomić w celu przetestowania Blob Storage platformy Azure na IoT Edge przez zmianę punktu końcowego obiektu BLOB w celu nawiązania połączenia z lokalnym modułem magazynu obiektów BLOB.

W poniższych przykładach szybkiego startu są używane Języki, które są również obsługiwane przez IoT Edge, więc można je wdrożyć jako moduły IoT Edge obok modułu BLOB Storage:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Wersje przed V 2.1 zestawu SDK języka Python mają znany problem, w którym moduł nie zwraca czasu utworzenia obiektu BLOB. Z powodu tego problemu niektóre metody, takie jak obiekty blob list, nie działają. Aby obejść ten sposób, jawnie ustaw wersję interfejsu API na kliencie obiektów BLOB na "2017-04-17". Przyklad  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Dołącz przykład obiektu BLOB](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Przejdź](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Nawiązywanie połączenia z magazynem lokalnym za pomocą Eksplorator usługi Azure Storage

Za pomocą [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) można nawiązać połączenie z kontem magazynu lokalnego.

1. Pobieranie i instalowanie Eksploratora usługi Azure Storage

1. Nawiązywanie połączenia z usługą Azure Storage przy użyciu parametrów połączenia

1. Podaj parametry połączenia: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Wykonaj kroki, aby nawiązać połączenie.

1. Utwórz kontener wewnątrz lokalnego konta magazynu

1. Rozpocznij przekazywanie plików jako blokowych obiektów blob lub Dołącz obiekty blob.
   > [!NOTE]
   > Ten moduł nie obsługuje stronicowych obiektów BLOB.

1. Możesz również wybrać opcję łączenia kont usługi Azure Storage w Eksplorator usługi Storage. Ta konfiguracja zapewnia jeden widok zarówno dla konta magazynu lokalnego, jak i konta usługi Azure Storage

## <a name="supported-storage-operations"></a>Obsługiwane operacje magazynu

Moduły magazynu obiektów BLOB na IoT Edge korzystają z zestawów SDK usługi Azure Storage i są spójne z wersją 2017-04-17 interfejsu API usługi Azure Storage dla punktów końcowych blokowych obiektów BLOB.

Ponieważ nie wszystkie operacje Blob Storage platformy Azure są obsługiwane przez usługę Azure Blob Storage w IoT Edge, w tej sekcji przedstawiono stan każdego z nich.

### <a name="account"></a>Konto

Obsługiwane:

* Wyświetlanie listy kontenerów

Ich

* Pobieranie i Ustawianie właściwości usługi BLOB Service
* Żądanie przekierowania obiektu BLOB
* Pobierz statystyki usługi BLOB Service
* Pobierz informacje o koncie

### <a name="containers"></a>Kontenery

Obsługiwane:

* Tworzenie i usuwanie kontenera
* Pobieranie właściwości kontenera i metadanych
* Wyświetlanie listy obiektów blob
* Pobieranie i ustawianie listy ACL kontenerów
* Ustawianie metadanych kontenera

Ich

* Kontener dzierżawy

### <a name="blobs"></a>Obiekty blob

Obsługiwane:

* Umieszczanie, pobieranie i usuwanie obiektu BLOB
* Pobieranie i Ustawianie właściwości obiektów BLOB
* Pobieranie i Ustawianie metadanych obiektów BLOB

Ich

* Obiekt BLOB dzierżawy
* Obiekt BLOB migawek
* Kopiowanie i przerywanie kopiowania obiektu BLOB
* Cofanie usunięcia obiektu BLOB
* Ustawianie warstwy obiektu BLOB

### <a name="block-blobs"></a>Blokowe obiekty blob

Obsługiwane:

* Umieść blok
* Umieszczanie i pobieranie listy zablokowanych

Ich

* Umieść blok z adresu URL

### <a name="append-blobs"></a>Uzupełnialne obiekty blob

Obsługiwane:

* Dołącz blok

Ich

* Dołącz blok z adresu URL

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid na IoT Edge integrację

> [!CAUTION]
> Integracja z Event Grid na IoT Edge jest w wersji zapoznawczej

Ten Blob Storage platformy Azure na IoT Edge module zapewnia teraz integrację z Event Grid na IoT Edge. Aby uzyskać szczegółowe informacje na temat integracji, zapoznaj się z [samouczkiem dotyczącym wdrażania modułów, publikowania zdarzeń i weryfikowania dostarczania zdarzeń](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Informacje o wersji

Poniżej znajdują się [Informacje o wersji usługi Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) dla tego modułu

## <a name="suggestions"></a>Sugestie

Twoja opinia jest ważna dla nas, aby ułatwić korzystanie z tego modułu i jego funkcji. Podziel się swoją opinią i daj nam znać, jak możemy udoskonalić.

Możesz skontaktować się z nami na absiotfeedback@microsoft.com

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrożyć usługę Azure Blob Storage na IoT Edge](how-to-deploy-blob.md)

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniami w [usłudze Azure Blob Storage na blogu IoT Edge](https://aka.ms/abs-iot-blogpost)

---
title: Importowanie/Eksportowanie tożsamości urządzeń IoT Hub platformy Azure | Microsoft Docs
description: Jak używać zestawu SDK usługi Azure IoT do uruchamiania operacji zbiorczych w rejestrze tożsamości w celu importowania i eksportowania tożsamości urządzeń. Operacje importowania umożliwiają zbiorcze tworzenie, aktualizowanie i usuwanie tożsamości urządzeń.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: d53e0cb92ead0d60ae335e95903cd69ae2700140
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142816"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Zbiorcze importowanie i eksportowanie tożsamości urządzeń usługi IoT Hub

Każde Centrum IoT Hub ma rejestr tożsamości, którego można użyć do tworzenia zasobów dla urządzeń w usłudze. Rejestr tożsamości pozwala również kontrolować dostęp do punktów końcowych dostępnych dla urządzenia. W tym artykule opisano sposób importowania i eksportowania tożsamości urządzeń zbiorczo do i z rejestru tożsamości. Aby wyświetlić przykład roboczy w języku C# i dowiedzieć się, jak można użyć tej funkcji podczas klonowania koncentratora do innego regionu, zobacz [Jak sklonować IoT Hub](iot-hub-how-to-clone.md).

> [!NOTE]
> IoT Hub ostatnio dodaliśmy obsługę sieci wirtualnej w ograniczonej liczbie regionów. Ta funkcja zabezpiecza operacje importu i eksportu i eliminuje konieczność przekazywania kluczy na potrzeby uwierzytelniania.  Początkowo obsługa sieci wirtualnej jest dostępna tylko w następujących regionach: *WestUS2*, *Wschodnie*i *SouthCentralUS*. Aby dowiedzieć się więcej na temat obsługi sieci wirtualnych i wywołań interfejsu API w celu ich wdrożenia, zobacz [IoT Hub obsługa sieci wirtualnych](virtual-network-support.md).

Operacje importowania i eksportowania odbywają się w kontekście *zadań* , które umożliwiają wykonywanie operacji usług zbiorczych w usłudze IoT Hub.

Klasa **registrymanager** zawiera metody **ExportDevicesAsync** i **ImportDevicesAsync** , które korzystają z platformy **zadań** . Te metody umożliwiają eksportowanie, importowanie i synchronizację całości rejestru tożsamości Centrum IoT Hub.

W tym temacie omówiono użycie klasy **registrymanager** i systemu **zadań** do wykonywania importu zbiorczego i eksportu urządzeń do i z rejestru tożsamości Centrum IoT Hub. Możesz również użyć IoT Hub Device Provisioning Service platformy Azure, aby włączyć funkcję bezobsługowego udostępniania w czasie just-in-Time do jednego lub kilku centrów IoT, bez konieczności interwencji człowieka. Aby dowiedzieć się więcej, zobacz [dokumentację usługi aprowizacji](../iot-dps/index.yml).

## <a name="what-are-jobs"></a>Co to są zadania?

Operacje rejestru tożsamości używają systemu **zadań** , gdy operacja:

* Ma potencjalnie długi czas wykonywania w porównaniu do standardowych operacji w czasie wykonywania.

* Zwraca do użytkownika dużą ilość danych.

Zamiast jednego wywołania interfejsu API oczekujące lub zablokowane w wyniku operacji, operacja asynchronicznie tworzy **zadanie** dla tego Centrum IoT. Następnie operacja natychmiast zwraca obiekt **JobProperties** .

Poniższy fragment kodu w języku C# pokazuje, jak utworzyć zadanie eksportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Aby użyć klasy **registrymanager** w kodzie C#, Dodaj pakiet NuGet **Microsoft. Azure. Devices** do projektu. Klasa **registrymanager** znajduje się w przestrzeni nazw **Microsoft. Azure. Devices** .

Aby zbadać stan **zadania** za pomocą zwróconych metadanych **JobProperties** , można użyć klasy **registrymanager** . Aby utworzyć wystąpienie klasy **registrymanager** , użyj metody **CreateFromConnectionString** .

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Aby znaleźć parametry połączenia dla Centrum IoT Hub, w Azure Portal:

- Przejdź do centrum IoT Hub.

- Wybierz pozycję **zasady dostępu współdzielonego**.

- Wybierz zasady, uwzględniając wymagane uprawnienia.

- Skopiuj element ConnectionString z panelu po prawej stronie ekranu.

Poniższy fragment kodu w języku C# pokazuje, jak sondować co pięć sekund, aby sprawdzić, czy zadanie zostało zakończone:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Jeśli Twoje konto magazynu ma konfiguracje zapory, które ograniczają łączność IoT Hub, rozważ użycie [wyjątku Microsoft Trusted First](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (dostępnego w wybranych regionach dla centrów IoT z tożsamością usługi zarządzanej).


## <a name="device-importexport-job-limits"></a>Limity zadań importowania/eksportowania urządzeń

Dozwolone jest tylko 1 aktywne zadanie importowania lub eksportowania urządzeń dla wszystkich warstw IoT Hub. IoT Hub ma także limity liczby operacji zadań. Aby dowiedzieć się więcej, zobacz [informacje dotyczące przydziałów i ograniczania przepustowości IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Eksportuj urządzenia

Użyj metody **ExportDevicesAsync** , aby wyeksportować całości rejestru tożsamości Centrum IoT Hub do kontenera obiektów BLOB usługi Azure Storage za pomocą sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

Ta metoda umożliwia tworzenie niezawodnych kopii zapasowych informacji o urządzeniu w kontrolowanym przez Ciebie kontenerze obiektów BLOB.

Metoda **ExportDevicesAsync** wymaga dwóch parametrów:

* *Ciąg* , który zawiera identyfikator URI kontenera obiektów BLOB. Ten identyfikator URI musi zawierać token SAS, który przyznaje dostęp do zapisu w kontenerze. Zadanie tworzy blokowy obiekt BLOB w tym kontenerze do przechowywania serializowanych danych urządzenia eksportu. Token sygnatury dostępu współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* *Wartość logiczna* wskazująca, czy chcesz wykluczyć klucze uwierzytelniania z danych eksportu. W przypadku **wartości false**klucze uwierzytelniania są uwzględniane w danych wyjściowych eksportu. W przeciwnym razie klucze są eksportowane jako **wartości null**.

Poniższy fragment kodu w języku C# pokazuje, jak zainicjować zadanie eksportu obejmujące klucze uwierzytelniania urządzenia w danych eksportu, a następnie przeprowadzić sondowanie pod kątem ukończenia:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Zadanie przechowuje dane wyjściowe w udostępnionym kontenerze obiektów BLOB jako blokowy obiekt BLOB o nazwie **devices.txt**. Dane wyjściowe składają się z serializowanych danych w formacie JSON przy użyciu jednego urządzenia w każdym wierszu.

W poniższym przykładzie przedstawiono dane wyjściowe:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Jeśli urządzenie ma dane z sznurów, dane z sznurka są również eksportowane razem z danymi urządzenia. Poniższy przykład pokazuje ten format. Wszystkie dane z wiersza "twinETag" do momentu zakończenia nie są danymi bliźniaczymi.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Jeśli potrzebujesz dostępu do tych danych w kodzie, możesz łatwo zdeserializować te dane przy użyciu klasy **ExportImportDevice** . Poniższy fragment kodu w języku C# pokazuje, jak odczytywać informacje o urządzeniu, które zostały wcześniej wyeksportowane do blokowego obiektu BLOB:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importuj urządzenia

Metoda **ImportDevicesAsync** klasy **registrymanager** umożliwia wykonywanie operacji importu zbiorczego i synchronizacji w rejestrze tożsamości Centrum IoT Hub. Podobnie jak Metoda **ExportDevicesAsync** , Metoda **ImportDevicesAsync** używa struktury **zadań** .

Należy zachować ostrożność przy użyciu metody **ImportDevicesAsync** , ponieważ oprócz aprowizacji nowych urządzeń w rejestrze tożsamości można także aktualizować i usuwać istniejące urządzenia.

> [!WARNING]
> Operacji importowania nie można cofnąć. Zawsze wykonuj kopie zapasowe istniejących danych przy użyciu metody **ExportDevicesAsync** w innym kontenerze obiektów BLOB przed wprowadzeniem zmian zbiorczych w rejestrze tożsamości.

Metoda **ImportDevicesAsync** przyjmuje dwa parametry:

* *Ciąg* zawierający identyfikator URI kontenera obiektów BLOB [usługi Azure Storage](../storage/index.yml) , który ma być używany jako *dane wejściowe* do zadania. Ten identyfikator URI musi zawierać token SAS, który przyznaje dostęp do odczytu do kontenera. Ten kontener musi zawierać obiekt BLOB o nazwie **devices.txt** zawierający serializowane dane urządzenia do zaimportowania do rejestru tożsamości. Dane importowania muszą zawierać informacje o urządzeniu w tym samym formacie JSON, którego używa zadanie **ExportImportDevice** podczas tworzenia obiektu BLOB **devices.txt** . Token sygnatury dostępu współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* *Ciąg* zawierający identyfikator URI kontenera obiektów BLOB [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) do użycia jako *dane wyjściowe* zadania. Zadanie tworzy blokowy obiekt BLOB w tym kontenerze do przechowywania wszelkich informacji o błędzie z ukończonego **zadania**importu. Token sygnatury dostępu współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Te dwa parametry mogą wskazywać na ten sam kontener obiektów BLOB. Osobne parametry po prostu umożliwiają większą kontrolę nad danymi, ponieważ kontener wyjściowy wymaga dodatkowych uprawnień.

Poniższy fragment kodu w języku C# pokazuje, jak zainicjować zadanie importowania:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tej metody można również użyć do zaimportowania danych dla sznurka urządzenia. Format danych wejściowych jest taki sam jak format wyświetlany w sekcji **ExportDevicesAsync** . W ten sposób można ponownie zaimportować wyeksportowane dane. **$Metadata** jest opcjonalne.

## <a name="import-behavior"></a>Zachowanie importowania

Za pomocą metody **ImportDevicesAsync** można wykonywać następujące operacje zbiorcze w rejestrze tożsamości:

* Rejestracja Zbiorcza nowych urządzeń
* Zbiorcze operacje usuwania istniejących urządzeń
* Zmiany stanu zbiorczego (Włączanie lub wyłączanie urządzeń)
* Przydział zbiorczy nowych kluczy uwierzytelniania urządzenia
* Zbiorcza funkcja autogeneracji kluczy uwierzytelniania urządzenia
* Zbiorcza aktualizacja danych bliźniaczych

Można wykonać dowolną kombinację powyższych operacji w ramach pojedynczego wywołania **ImportDevicesAsync** . Można na przykład rejestrować nowe urządzenia i usuwać lub aktualizować istniejące urządzenia jednocześnie. W przypadku użycia wraz z metodą **ExportDevicesAsync** można całkowicie migrować wszystkie urządzenia z jednego Centrum IoT Hub do innego.

Jeśli plik importu zawiera metadane z przędzą, to spowoduje zastąpienie istniejących metadanych bliźniaczych. Jeśli plik importu nie zawiera metadanych bliźniaczych, tylko `lastUpdateTime` metadane są aktualizowane przy użyciu bieżącego czasu.

Użyj **opcjonalnej** właściwości ImportMode w Importuj dane serializacji dla każdego urządzenia, aby kontrolować proces importowania dla poszczególnych urządzeń. Właściwość **ImportMode** ma następujące opcje:

| portmode | Opis |
| --- | --- |
| **Metodę createorupdate** |Jeśli urządzenie nie istnieje o określonym **identyfikatorze**, jest ono nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje są zastępowane przez podane dane wejściowe bez względu na wartość **ETag** . <br> Użytkownik może opcjonalnie określić dane przędzy wraz z danymi urządzenia. Element ETag o przędze, jeśli jest określony, jest przetwarzany niezależnie od elementu ETag urządzenia. W przypadku niezgodności z elementem ETag istniejącej przędzy w pliku dziennika jest zapisywana błąd. |
| **Create** |Jeśli urządzenie nie istnieje o określonym **identyfikatorze**, jest ono nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, w pliku dziennika zostanie zapisany błąd. <br> Użytkownik może opcjonalnie określić dane przędzy wraz z danymi urządzenia. Element ETag o przędze, jeśli jest określony, jest przetwarzany niezależnie od elementu ETag urządzenia. W przypadku niezgodności z elementem ETag istniejącej przędzy w pliku dziennika jest zapisywana błąd. |
| **update** |Jeśli urządzenie już istnieje o określonym **identyfikatorze**, istniejące informacje są zastępowane dostarczonymi danymi wejściowymi bez względu na wartość **ETag** . <br/>Jeśli urządzenie nie istnieje, w pliku dziennika zostanie zapisany błąd. |
| **updateIfMatchETag** |Jeśli urządzenie już istnieje o określonym **identyfikatorze**, istniejące informacje są zastępowane danymi wejściowymi tylko wtedy, gdy istnieje element **ETag** Match. <br/>Jeśli urządzenie nie istnieje, w pliku dziennika zostanie zapisany błąd. <br/>Jeśli występuje niezgodność elementu **ETag** , w pliku dziennika jest zapisywana błąd. |
| **createOrUpdateIfMatchETag** |Jeśli urządzenie nie istnieje o określonym **identyfikatorze**, jest ono nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje są zastępowane przez podane dane wejściowe tylko wtedy, gdy istnieje element **ETag** Match. <br/>Jeśli występuje niezgodność elementu **ETag** , w pliku dziennika jest zapisywana błąd. <br> Użytkownik może opcjonalnie określić dane przędzy wraz z danymi urządzenia. Element ETag o przędze, jeśli jest określony, jest przetwarzany niezależnie od elementu ETag urządzenia. W przypadku niezgodności z elementem ETag istniejącej przędzy w pliku dziennika jest zapisywana błąd. |
| **delete** |Jeśli urządzenie już istnieje o określonym **identyfikatorze**, zostanie usunięte bez względu na wartość **ETag** . <br/>Jeśli urządzenie nie istnieje, w pliku dziennika zostanie zapisany błąd. |
| **deleteIfMatchETag** |Jeśli istnieje już urządzenie o określonym **identyfikatorze**, jest ono usuwane tylko wtedy, gdy istnieje dopasowanie **ETag** . Jeśli urządzenie nie istnieje, w pliku dziennika zostanie zapisany błąd. <br/>Jeśli występuje niezgodność elementu ETag, w pliku dziennika jest zapisywana błąd. |

> [!NOTE]
> Jeśli dane serializacji nie definiują **jawnie flagi** ImportMode dla urządzenia, domyślnie **metodę createorupdate** podczas operacji importowania.

## <a name="import-devices-example--bulk-device-provisioning"></a>Przykład importowania urządzeń — zbiorcze Inicjowanie obsługi urządzeń

Poniższy przykładowy kod w języku C# ilustruje sposób generowania wielu tożsamości urządzeń, które:

* Uwzględnij klucze uwierzytelniania.
* Zapisz te informacje o urządzeniu w blokowym obiekcie blob.
* Zaimportuj urządzenia do rejestru tożsamości.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Przykład importowania urządzeń — usuwanie zbiorcze

Poniższy przykład kodu pokazuje, jak usunąć urządzenia dodane przy użyciu poprzedniego przykładu kodu:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Pobierz identyfikator URI sygnatury dostępu współdzielonego kontenera

Poniższy przykład kodu pokazuje, jak wygenerować [Identyfikator URI sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md) z uprawnieniami do odczytu, zapisu i usuwania dla kontenera obiektów blob:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wykonywania operacji zbiorczych w rejestrze tożsamości w centrum IoT. Wiele z tych operacji, w tym sposobu przenoszenia urządzeń z jednego koncentratora do drugiego, jest używana w [sekcji Zarządzanie urządzeniami zarejestrowanymi w centrum IoT Hub w artykule Jak sklonować IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Artykuł klonowania zawiera skojarzony z nim przykład roboczy, który znajduje się w przykładach środowiska IoT C# na tej stronie: [przykłady usługi Azure IoT dla języka c#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)z ImportExportDevicesSample projektu. Możesz pobrać próbkę i wypróbować ją. istnieją instrukcje przedstawione w artykule [Jak sklonować IoT Hub](iot-hub-how-to-clone.md) .

Aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub, zapoznaj się z następującymi artykułami:

* [Metryki IoT Hub](iot-hub-metrics.md)
* [Dzienniki IoT Hub](iot-hub-monitor-resource-health.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)

Aby poznać korzystanie z IoT Hub Device Provisioning Service w celu włączenia funkcji bezobsługowego, zainicjowania obsługi just-in-Time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](../iot-dps/index.yml)
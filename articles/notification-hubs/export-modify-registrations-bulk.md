---
title: Wyeksportuj i zaimportuj rejestrację usługi Azure Notification Hubs zbiorczo | Microsoft Docs
description: Dowiedz się, w jaki sposób używać Notification Hubs zbiorczej, aby wykonać dużą liczbę operacji w centrum powiadomień lub wyeksportować wszystkie rejestracje.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 73c19b72c75d9638213f3c813a708a272cbe2a1b
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452343"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Zbiorcze eksportowanie i importowanie rejestracji Notification Hubs platformy Azure

Istnieją scenariusze, w których wymagane jest utworzenie lub zmodyfikowanie dużej liczby rejestracji w centrum powiadomień. Niektóre z tych scenariuszy są aktualizacjami tagów po obliczeniach wsadowych lub migracji istniejącej implementacji wypychania do korzystania z usługi Azure Notification Hubs.

W tym artykule wyjaśniono, jak wykonać dużą liczbę operacji w centrum powiadomień lub wyeksportować wszystkie rejestracje.

## <a name="high-level-flow"></a>Przepływ wysokiego poziomu

Wsparcie wsadowe jest przeznaczone do obsługi długotrwałych zadań obejmujących miliony rejestracji. Aby osiągnąć tę skalę, obsługa usługi Batch używa magazynu Azure do przechowywania informacji o zadaniu i danych wyjściowych. W przypadku operacji aktualizacji zbiorczej użytkownik musi utworzyć plik w kontenerze obiektów blob, którego zawartość jest listą operacji aktualizacji rejestracji. Podczas uruchamiania zadania użytkownik udostępnia adres URL wejściowego obiektu BLOB wraz z adresem URL do katalogu wyjściowego (również w kontenerze obiektów BLOB). Po rozpoczęciu zadania użytkownik może sprawdzić stan, badając lokalizację adresu URL podaną podczas uruchamiania zadania. Określone zadanie może wykonywać tylko operacje o określonym rodzaju (tworzenie, aktualizacje lub usuwanie). Operacje eksportowania są wykonywane analogicznie.

## <a name="import"></a>Import

### <a name="set-up"></a>Konfiguruj

W tej sekcji założono, że masz następujące jednostki:

- Administracyjna centrum powiadomień.
- Kontener obiektów BLOB usługi Azure Storage.
- Odwołania do [pakietu NuGet usługi Azure Storage](https://www.nuget.org/packages/windowsazure.storage/) i [pakietu NuGet Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Utwórz plik wejściowy i Zapisz go w obiekcie blob

Plik wejściowy zawiera listę serializowanych rejestracji w kodzie XML, po jednym dla każdego wiersza. Przy użyciu zestawu Azure SDK Poniższy przykład kodu pokazuje, jak serializować rejestracje i przekazać je do kontenera obiektów blob:

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Poprzedni kod serializacji rejestracje w pamięci, a następnie przekazuje cały strumień do obiektu BLOB. Jeśli przekazano plik więcej niż kilka megabajtów, zapoznaj się ze wskazówkami dotyczącymi obiektów blob platformy Azure dotyczącymi sposobu wykonywania tych kroków. na przykład [Zablokuj obiekty blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Utwórz tokeny adresu URL

Po przekazaniu pliku wejściowego Wygeneruj adresy URL do udostępnienia centrum powiadomień dla pliku wejściowego i katalogu wyjściowego. Można użyć dwóch różnych kontenerów obiektów BLOB dla danych wejściowych i wyjściowych.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Przesyłanie zadania

Przy użyciu dwóch adresów URL danych wejściowych i wyjściowych możesz teraz uruchomić zadanie wsadowe.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Oprócz wejściowych i wyjściowych adresów URL, ten przykład tworzy `NotificationHubJob` obiekt, który zawiera `JobType` obiekt, który może być jednym z następujących typów:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Po zakończeniu wywołania zadanie jest kontynuowane przez centrum powiadomień, a jego stan można sprawdzić za pomocą wywołania do [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync).

Po zakończeniu zadania można sprawdzić wyniki, przeglądając następujące pliki w katalogu wyjściowym:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Te pliki zawierają listę operacji zakończonych powodzeniem i zakończonych niepowodzeniem z usługi Batch. Format pliku to `.cvs` , w którym każdy wiersz ma numer wiersza oryginalnego pliku wejściowego i dane wyjściowe operacji (zazwyczaj utworzony lub zaktualizowany opis rejestracji).

### <a name="full-sample-code"></a>Pełny przykładowy kod

Następujący przykładowy kod importuje rejestracje do centrum powiadomień.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Eksportowanie

Eksportowanie rejestracji jest podobne do importu z następującymi różnicami:

- Potrzebujesz tylko wyjściowego adresu URL.
- Tworzysz NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Przykładowy fragment kodu

Poniżej znajduje się przykładowy fragment kodu dotyczący eksportowania rejestracji w języku Java:

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rejestracji, zobacz następujące artykuły:

- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Tagi do rejestracji](notification-hubs-tags-segment-push-message.md)
- [Rejestracja szablonu](notification-hubs-templates-cross-platform-push-messages.md)

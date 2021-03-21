---
title: Korzystanie z usługi Azure queue storage do monitorowania powiadomień dotyczących zadań Media Services za pomocą platformy .NET | Microsoft Docs
description: Informacje dotyczące monitorowania Media Services powiadomień zadań przy użyciu usługi Azure queue storage. Przykładowy kod jest pisany w języku C# i używa zestawu SDK Media Services dla platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: dc6ab94b3685e55125032b0afa52076eae72ba5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016223"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Używanie usługi Azure queue storage do monitorowania powiadomień dotyczących zadań Media Services za pomocą platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Po uruchomieniu zadań kodowania często potrzebny jest sposób śledzenia postępów zadań. Media Services można skonfigurować w celu dostarczania powiadomień do [usługi Azure queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md). Postęp zadania można monitorować przez otrzymywanie powiadomień z magazynu kolejek. 

Dostęp do komunikatów dostarczonych do usługi queue storage można uzyskać z dowolnego miejsca na świecie. Architektura obsługi komunikatów magazynu kolejki jest niezawodna i wysoce skalowalna. W przypadku korzystania z innych metod zalecane jest przełączenie magazynu kolejki sondowania pod kątem komunikatów.

Typowym scenariuszem nasłuchiwania Media Services powiadomień jest opracowanie systemu zarządzania zawartością, który musi wykonać kilka dodatkowych zadań po zakończeniu zadania kodowania (na przykład, aby wyzwolić następny krok w przepływie pracy lub opublikować zawartość).

W tym artykule przedstawiono sposób pobierania komunikatów powiadomień z usługi queue storage.  

## <a name="considerations"></a>Zagadnienia do rozważenia
Podczas tworzenia aplikacji Media Services korzystających z magazynu kolejek należy wziąć pod uwagę następujące kwestie:

* Usługa queue storage nie gwarantuje, że jest zamówione zamówienie First-In-First-Out (FIFO). Aby uzyskać więcej informacji, zobacz temat [kolejki platformy Azure i kolejki Azure Service Bus w porównaniu z kontrastem](/previous-versions/azure/hh767287(v=azure.100)).
* Usługa queue storage nie jest usługą push. Musisz sondować kolejkę.
* Możesz mieć dowolną liczbę kolejek. Aby uzyskać więcej informacji, zobacz [interfejs API REST usługi Queue](/rest/api/storageservices/queue-service-rest-api).
* W usłudze queue storage istnieją pewne ograniczenia i informacje, które należy znać. Są one opisane w [kolejkach platformy Azure i w Azure Service Busych kolejkach w porównaniu z kontrastem](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

## <a name="net-code-example"></a>Przykład kodu platformy .NET

Przykład kodu w tej sekcji wykonuje następujące czynności:

1. Definiuje klasę **EncodingJobMessage** , która jest mapowana na format wiadomości powiadomienia. Kod deserializacji komunikaty odebrane z kolejki do obiektów typu **EncodingJobMessage** .
2. Ładuje informacje o Media Services i koncie magazynu z pliku app.config. W przykładzie kodu są wykorzystywane te informacje do tworzenia obiektów **CloudMediaContext** i **CloudQueue** .
3. Tworzy kolejkę, która odbiera komunikaty powiadomień o zadaniu kodowania.
4. Tworzy punkt końcowy powiadomienia mapowany do kolejki.
5. Dołącza punkt końcowy powiadomienia do zadania i przesyła zadanie kodowania. Do zadania można dołączyć wiele punktów końcowych powiadomień.
6. Przekazuje **NotificationJobState. FinalStatesOnly** do metody **AddNew** . (W tym przykładzie interesuje tylko Stany końcowe przetwarzania zadania).

    ```csharp
    job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
    ```

7. Jeśli przejdziesz do **NotificationJobState. All**, uzyskasz wszystkie następujące powiadomienia o zmianie stanu: w kolejce, zaplanowane, przetwarzane i zakończone. Jednak jak wspomniano wcześniej, usługa queue storage nie gwarantuje uporządkowanej dostawy. Aby zamówić komunikaty, użyj właściwości **timestamp** (zdefiniowanej w podanym przykładzie typu **EncodingJobMessage** ). Możliwe jest duplikowanie komunikatów. Aby sprawdzić duplikaty, należy użyć **Właściwości ETag** (zdefiniowanej dla typu **EncodingJobMessage** ). Istnieje również możliwość, że niektóre powiadomienia o zmianie stanu zostaną pominięte.
8. Czeka na ukończenie zadania do stanu ukończenia, sprawdzając kolejkę co 10 sekund. Usuwa komunikaty po ich przetworzeniu.
9. Usuwa kolejkę i punkt końcowy powiadomienia.

> [!NOTE]
> Zalecanym sposobem monitorowania stanu zadania jest nasłuchiwanie komunikatów powiadomień, jak pokazano w następującym przykładzie:
>
> Alternatywnie można sprawdzić stan zadania przy użyciu właściwości **IJob. State** .  Komunikat z powiadomieniem o ukończeniu zadania może pojawić **się przed** ustawieniem stan na **IJob** . Właściwość **IJob. State**  odzwierciedla dokładny stan z niewielkim opóźnieniem.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 
2. Utwórz nowy folder (folder może znajdować się w dowolnym miejscu na dysku lokalnym) i skopiuj plik MP4, który ma zostać zakodowany i przesłany strumieniowo lub progresywnie. W tym przykładzie użyto ścieżki "C:\Media".
3. Dodaj odwołanie do biblioteki **System. Runtime. Serialization** .

### <a name="code"></a>Kod

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Poprzedni przykład wygenerował następujące dane wyjściowe: wartości będą się różnić.

```output
Created assetFile BigBuckBunny.mp4
Upload BigBuckBunny.mp4
Done uploading of BigBuckBunny.mp4

EventType: NotificationEndPointRegistration
MessageVersion: 1.0
ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
TimeStamp: 2013-05-14T20:22:37
    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
    State: Registered
    Name: dde957b2-006e-41f2-9869-a978870ac620
    Created: 2013-05-14T20:22:35

EventType: JobStateChange
MessageVersion: 1.0
ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
TimeStamp: 2013-05-14T20:24:40
    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
    JobName: My MP4 to Smooth Streaming encoding job
    NewState: Finished
    OldState: Processing
    AccountName: westeuropewamsaccount
job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
State: Finished
```

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

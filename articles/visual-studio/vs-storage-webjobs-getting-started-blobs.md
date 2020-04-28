---
title: Wprowadzenie do usługi BLOB Storage przy użyciu programu Visual Studio (projekty WebJob)
description: Jak rozpocząć korzystanie z usługi BLOB Storage w projekcie zadań WebJob po nawiązaniu połączenia z usługą Azure Storage przy użyciu usług połączonych programu Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72299978"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (projekty zadań WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
W tym artykule przedstawiono przykłady kodu w języku C#, które pokazują, jak wyzwolić proces podczas tworzenia lub aktualizowania obiektu blob platformy Azure. Przykłady kodu używają [zestawu SDK zadań WebJob](https://github.com/Azure/azure-webjobs-sdk/wiki) w wersji 1. x. Po dodaniu konta magazynu do projektu zadania WebJob przy użyciu okna dialogowego **Dodawanie połączonych usług** programu Visual Studio zostanie zainstalowany odpowiedni pakiet NuGet usługi Azure Storage, odpowiednie odwołania platformy .NET zostaną dodane do projektu, a parametry połączenia dla konta magazynu zostaną zaktualizowane w pliku App. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Jak wyzwolić funkcję podczas tworzenia lub aktualizowania obiektu BLOB
W tej sekcji pokazano, jak używać atrybutu **BlobTrigger** .

 **Uwaga:** Zestaw SDK usługi WebJobs skanuje pliki dziennika, aby obejrzeć nowe lub zmienione obiekty blob. Ten proces jest z natury rzeczy wolno; funkcja może nie zostać wyzwolona do kilku minut lub dłużej po utworzeniu obiektu BLOB.  Jeśli aplikacja wymaga natychmiastowego przetworzenia obiektów blob, zalecaną metodą jest utworzenie komunikatu w kolejce podczas tworzenia obiektu BLOB i użycie atrybutu **QueueTrigger** zamiast atrybutu **BlobTrigger** w funkcji, która przetwarza obiekt BLOB.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Pojedynczy symbol zastępczy dla nazwy obiektu BLOB z rozszerzeniem
Poniższy przykładowy kod kopiuje tekst obiektów blob, które pojawiają się w kontenerze *wejściowym* do kontenera *wyjściowego* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Konstruktor atrybutu przyjmuje parametr ciągu, który określa nazwę kontenera i symbol zastępczy dla nazwy obiektu BLOB. W tym przykładzie, jeśli obiekt BLOB o nazwie *Blob1. txt* jest tworzony w kontenerze *Input* , funkcja tworzy obiekt BLOB o nazwie *Blob1. txt* w kontenerze *danych wyjściowych* .

Możesz określić wzorzec nazwy z symbolem zastępczym nazwy obiektu BLOB, jak pokazano w następującym przykładzie kodu:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ten kod kopiuje tylko obiekty blob o nazwach zaczynających się od "Original-". Na przykład *Original-Blob1. txt* w kontenerze *wejściowym* jest kopiowany do *copy-Blob1. txt* w kontenerze *danych wyjściowych* .

Jeśli musisz określić wzorzec nazw dla nazw obiektów blob, które mają klamrowe nawiasy klamrowe w nazwie, podwójne nawiasy klamrowe. Na przykład, jeśli chcesz znaleźć obiekty blob w kontenerze *obrazy* o takich samych nazwach:

        {20140101}-soundfile.mp3

Użyj tego wzorca:

        images/{{20140101}}-{name}

*W przykładzie wartość symbolu* zastępczego *soundfile. mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Oddzielna nazwa obiektu BLOB i symbole zastępcze rozszerzenia
Poniższy przykład kodu zmienia rozszerzenie pliku, ponieważ kopiuje obiekty blob, które pojawiają się w kontenerze *danych wejściowych* do kontenera *danych wyjściowych* . Kod rejestruje rozszerzenie *wejściowego* obiektu BLOB i ustawia rozszerzenie *wyjściowego* obiektu BLOB na *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typy, które można powiązać z obiektami BLOB
Można użyć atrybutu **BlobTrigger** w następujących typach:

* **ciąg**
* **TextReader**
* **Strumień**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Inne typy deserializowane przez [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Jeśli chcesz bezpośrednio korzystać z konta usługi Azure Storage, możesz również dodać parametr **CloudStorageAccount** do sygnatury metody.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Pobieranie zawartości tekstowej obiektu BLOB przez powiązanie do ciągu
Jeśli oczekiwane są obiekty blob tekstu, **BlobTrigger** można zastosować do parametru **ciągu** . Poniższy przykład kodu wiąże obiekt BLOB Text z parametrem **ciągu** o nazwie **LogMessage**. Funkcja używa tego parametru, aby zapisać zawartość obiektu BLOB na pulpicie nawigacyjnym zestawu SDK zadań WebJob.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Pobieranie serializowanej zawartości obiektu BLOB za pomocą ICloudBlobStreamBinder
Poniższy przykład kodu używa klasy implementującej **ICloudBlobStreamBinder** w celu włączenia atrybutu **BlobTrigger** w celu powiązania obiektu BLOB z typem **obrazu webimage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Kod powiązania **webimage** jest dostarczany w klasie **WebImageBinder** , która pochodzi od **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Jak obsłużyć trujące obiekty blob
Gdy funkcja **BlobTrigger** nie powiedzie się, zestaw SDK wywołuje ją ponownie, na wypadek gdyby błąd był spowodowany błędem przejściowym. Jeśli błąd jest spowodowany przez zawartość obiektu BLOB, funkcja kończy się za każdym razem, gdy próbuje przetworzyć obiekt BLOB. Domyślnie zestaw SDK wywołuje funkcję do 5 razy dla danego obiektu BLOB. Jeśli piąta próba nie powiedzie się, zestaw SDK dodaje komunikat do kolejki o nazwie *WebJobs-blobtrigger-trując*.

Maksymalna liczba ponownych prób można skonfigurować. To samo ustawienie **MaxDequeueCount** jest używane na potrzeby obsługi skażonych obiektów blob i komunikatów trującej kolejki.

Komunikat w kolejce dla trujących obiektów BLOB jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w formacie *{Nazwa zadania*). Obowiązki. *{Funkcja Name}*, na przykład: WebJob1. Functions. CopyBlob)
* Blobtype ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* BlobName
* ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

W poniższym przykładzie kodu funkcja **CopyBlob** ma kod, który powoduje niepowodzenie każdego wywołania. Gdy zestaw SDK wywoła go przez maksymalną liczbę ponownych prób, zostanie utworzony komunikat w kolejce trujących obiektów blob i ten komunikat jest przetwarzany przez funkcję **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Zestaw SDK automatycznie deserializacji komunikat JSON. Oto Klasa **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algorytm sondowania obiektów BLOB
Zestaw SDK zadań WebJob skanuje wszystkie kontenery określone przez atrybuty **BlobTrigger** podczas uruchamiania aplikacji. W przypadku dużego konta magazynu to skanowanie może zająć trochę czasu, więc może to być pewien czas przed znalezieniem nowych obiektów blob i wykonaniem funkcji **BlobTrigger** .

Aby wykryć nowe lub zmienione obiekty blob po uruchomieniu aplikacji, zestaw SDK okresowo odczytuje z dzienników usługi BLOB Storage. Dzienniki obiektów BLOB są buforowane i są dostępne tylko fizycznie, co 10 minut, więc po utworzeniu obiektu BLOB lub zaktualizowaniu go przed wykonaniem odpowiedniej funkcji **BlobTrigger** może wystąpić duże opóźnienie.

Istnieje wyjątek dla obiektów BLOB tworzonych przy użyciu atrybutu **obiektu BLOB** . Po utworzeniu nowego obiektu BLOB przez zestaw SDK zadań WebJob następuje natychmiastowe przekazanie nowego obiektu BLOB do dowolnych pasujących funkcji **BlobTrigger** . W związku z tym, jeśli masz łańcuch danych wejściowych i wyjściowych obiektów blob, zestaw SDK może wydajnie przetwarzać je. Ale jeśli potrzebujesz małych opóźnień uruchamiających funkcje przetwarzania obiektów BLOB dla obiektów BLOB tworzonych lub aktualizowanych za pomocą innych metod, zalecamy użycie **QueueTrigger** zamiast **BlobTrigger**.

### <a name="blob-receipts"></a>Potwierdzenia obiektów BLOB
Zestaw SDK zadań WebJob gwarantuje, że żadna funkcja **BlobTrigger** nie jest wywoływana więcej niż raz dla tego samego nowego lub zaktualizowanego obiektu BLOB. Wykonuje to przez obsługę *potwierdzeń obiektów BLOB* w celu ustalenia, czy dana wersja obiektu BLOB została przetworzona.

Potwierdzenia obiektów BLOB są przechowywane w kontenerze o nazwie *Azure-WebJobs-hosty* na koncie usługi Azure Storage określonym przez parametry połączenia AzureWebJobsStorage. Potwierdzenie obiektu BLOB zawiera następujące informacje:

* Funkcja, która została wywołana dla obiektu BLOB ("*{Nazwa zadania WebJob}*". Obowiązki. *{Function Name}*", na przykład:" WebJob1. Functions. CopyBlob ")
* Nazwa kontenera
* Typ obiektu BLOB ("BlockBlob" lub "PageBlob")
* Nazwa obiektu BLOB
* Element ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetwarzanie obiektu BLOB, można ręcznie usunąć potwierdzenie obiektu BLOB dla tego obiektu BLOB z kontenera *Azure-WebJobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Tematy pokrewne omówione w artykule Queues (kolejki)
Aby uzyskać informacje o sposobie obsługi przetwarzania obiektów BLOB wyzwalanych przez komunikat kolejki lub dla scenariuszy zestawu SDK zadań WebJob, które nie są specyficzne dla przetwarzania obiektów blob, zobacz [jak używać usługi Azure queue storage z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Tematy pokrewne omówione w tym artykule obejmują następujące elementy:

* Funkcje asynchroniczne
* Wiele wystąpień
* Bezpieczne zamykanie
* Używanie atrybutów zestawu SDK zadań WebJob w treści funkcji
* Ustaw parametry połączenia zestawu SDK w kodzie.
* Ustaw wartości parametrów konstruktora zestawu SDK usługi WebJobs w kodzie
* Skonfiguruj **MaxDequeueCount** na potrzeby obsługi skażonych obiektów BLOB.
* Ręcznie Wyzwól funkcję
* Zapisz dzienniki

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z obiektami blob platformy Azure. Więcej informacji o sposobach używania Azure WebJobs i zestawu SDK usługi WebJobs znajduje się w temacie [Azure WebJobs zasoby dokumentacji](https://go.microsoft.com/fwlink/?linkid=390226).


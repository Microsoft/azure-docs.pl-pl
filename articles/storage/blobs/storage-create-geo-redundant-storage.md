---
title: Samouczek - Tworzenie aplikacji o wysokiej dostępności z pamięcią blob
titleSuffix: Azure Storage
description: Użyj magazynu geograficznego dostępu do odczytu, aby dane aplikacji były wysoce dostępne.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: a80504f43a14b7b710fc735ebe269285299b94bc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459206"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Samouczek: Tworzenie aplikacji o wysokiej dostępności z pamięcią blob

Niniejszy samouczek jest pierwszą częścią serii. Z tego samouczka dowiesz się, jak uzyskać wysoką dostępność danych aplikacji na platformie Azure.

Po ukończeniu tego samouczka będziesz mieć aplikację konsolową, która przekazuje obiekt blob na konto magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (RA-GRS) i pobiera go z tego konta.

Działanie magazynu RA-GRS polega na replikowaniu transakcji z regionu podstawowego do pomocniczego. Ten proces replikacji gwarantuje, że dane w regionie pomocniczym ostatecznie uzyskają spójność. Aplikacja korzysta z wzorca [Wyłącznika](/azure/architecture/patterns/circuit-breaker) do określania punktu końcowego, z którym ma się połączyć, i automatycznego przełączania między punktami końcowymi podczas symulacji awarii i odzyskiwania.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Ustawianie parametrów połączenia
> * Uruchamianie aplikacji konsolowej

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

# <a name="net"></a>[.NET](#tab/dotnet)

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z obciążeniem **dewelopera platformy Azure.**

  ![Tworzenie aplikacji na platformie Azure (w Internecie i w chmurze)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Instalowanie [języka Python](https://www.python.org/downloads/)
* Pobieranie i [instalowanie pakietu Azure Storage SDK dla języka Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Zainstaluj [plik Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu zapewnia unikatową przestrzeń nazw do przechowywania i umożliwiania dostępu do obiektów danych usługi Azure Storage.

Wykonaj następujące kroki, aby utworzyć konto magazynu geograficznie nadmiarowego do odczytu:

1. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Wybierz pozycję **Storage** na stronie **Nowe**.
3. Wybierz pozycję **Konto usługi Storage — Blob, File, Table, Queue** w sekcji **Polecane**.
4. Wypełnij formularz konta magazynu przy użyciu następujących informacji zgodnie z ilustracją i wybierz pozycję **Utwórz**:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa** | mystorageaccount | Unikatowa wartość konta magazynu |
   | **Model wdrażania** | Resource Manager  | Usługa Resource Manager oferuje najnowsze funkcje.|
   | **Rodzaj konta** | StorageV2 | Aby uzyskać więcej informacji na temat typów kont, zobacz [Typy kont magazynu](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Wydajność** | Standardowa | Warstwa Standardowa jest wystarczająca na potrzeby przykładowego scenariusza. |
   | **Replikacja**| Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS) | Jest to niezbędne do działania przykładu. |
   |**Subskrypcja** | Twoja subskrypcja |Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.azure.com/Subscriptions). |
   |**Grupa zasobów** | myResourceGroup |Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa). |
   |**Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację. |

![tworzenie konta magazynu](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Pobierz przykład

# <a name="net"></a>[.NET](#tab/dotnet)

[Pobierz przykładowy projekt](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) i wyodrębnij (rozpakuj) plik storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip file. Możesz również użyć narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. Przykładowy projekt zawiera aplikację konsolową.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Pobierz przykładowy projekt](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) i wyodrębnij (rozpakuj) plik storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Możesz również użyć narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. Przykładowy projekt zawiera podstawową aplikację w języku Python.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Pobierz przykładowy projekt](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) i rozpakuj plik. Możesz również użyć narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. Przykładowy projekt zawiera podstawową aplikację Node.js.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

# <a name="net"></a>[.NET](#tab/dotnet)

W aplikacji należy wprowadzić parametry połączenia konta magazynu. Te parametry połączenia można przechowywać w zmiennej środowiskowej na maszynie lokalnej z uruchomioną aplikacją. Postępuj zgodnie z jednym z poniższych przykładów w zależności od używanego systemu operacyjnego, aby utworzyć zmienną środowiskową.

W witrynie Azure Portal przejdź do swojego konta magazynu. Wybierz pozycję **Klucze dostępu** w obszarze **Ustawienia** konta magazynu. Skopiuj **parametry połączenia** z klucza podstawowego lub pomocniczego. Uruchom jedno z następujących poleceń w oparciu \<o system\> operacyjny, zastępując ciąg połączenia rzeczywistym ciągiem połączenia. To polecenie zapisuje zmienną środowiskową na maszynie lokalnej. W systemie Windows zmienna środowiskowa nie jest dostępna do czasu ponownego załadowania używanej powłoki lub **wiersza polecenia**.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

W aplikacji należy podać poświadczenia konta magazynu. Można przechowywać te informacje w zmiennych środowiskowych na komputerze lokalnym z uruchomieniem aplikacji. Postępuj zgodnie z jednym z poniższych przykładów w zależności od systemu operacyjnego, aby utworzyć zmienne środowiskowe.

W witrynie Azure Portal przejdź do swojego konta magazynu. Wybierz pozycję **Klucze dostępu** w obszarze **Ustawienia** konta magazynu. Wklej **nazwę konta magazynu** i wartości **klucza** do \<następujących poleceń, zastępując symbole zastępcze youraccountname\> i \<youraccountkey.\> To polecenie zapisuje zmienne środowiskowe na komputerze lokalnym. W systemie Windows zmienna środowiskowa nie jest dostępna do czasu ponownego załadowania używanej powłoki lub **wiersza polecenia**.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aby uruchomić ten przykład, należy dodać poświadczenia `.env.example` konta magazynu do pliku, a następnie zmienić jego nazwę na `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Informacje te można znaleźć w witrynie Azure portal, przechodząc do konta magazynu i wybierając **klawisze programu Access** w sekcji **Ustawienia.**

Zainstaluj wymagane zależności. Aby to zrobić, otwórz wiersz polecenia, przejdź do `npm install`przykładowego folderu, a następnie wprowadź .

---

## <a name="run-the-console-application"></a>Uruchamianie aplikacji konsolowej

# <a name="net"></a>[.NET](#tab/dotnet)

W programie Visual Studio naciśnij klawisz **F5** lub wybierz pozycję **Uruchom**, aby rozpocząć debugowanie aplikacji. Program Visual Studio automatycznie przywraca brakujące pakiety NuGet, jeśli zostały skonfigurowane. Więcej szczegółów można znaleźć w temacie [Installing and reinstalling packages with package restore (Instalowanie i ponowne instalowanie pakietów przy użyciu funkcji przywracania pakietów)](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Zostanie uruchomione okno konsoli i aplikacja zacznie działać. Aplikacja przekazuje obraz **HelloWorld.png** z rozwiązania na konto magazynu. Aplikacja sprawdza, czy obraz został replikowany do pomocniczego punktu końcowego RA-GRS. Następnie rozpoczyna pobieranie obrazu maksymalnie 999 razy. Każdy odczyt jest reprezentowany przez **P** lub **S**. Gdzie **P** reprezentuje podstawowy punkt końcowy i **S** reprezentuje pomocniczy punkt końcowy.

![Uruchomiona aplikacja konsolowa](media/storage-create-geo-redundant-storage/figure3.png)

W przykładowym kodzie zadanie `RunCircuitBreakerAsync` w pliku `Program.cs` jest używane do pobierania obrazu z konta magazynu przy użyciu metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Przed pobraniem [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) jest zdefiniowany. Kontekst operacji definiuje procedury obsługi zdarzeń wyzwalane po pomyślnym zakończeniu pobierania lub po niepowodzeniu pobierania i podjęciu jego kolejnej próby.

# <a name="python"></a>[Python](#tab/python)

Aby uruchomić aplikację w terminalu lub wierszu polecenia, przejdź do katalogu **circuitbreaker.py** i wprowadź wartość `python circuitbreaker.py`. Aplikacja przekazuje obraz **HelloWorld.png** z rozwiązania na konto magazynu. Aplikacja sprawdza, czy obraz został replikowany do pomocniczego punktu końcowego RA-GRS. Następnie rozpoczyna pobieranie obrazu maksymalnie 999 razy. Każdy odczyt jest reprezentowany przez **P** lub **S**. Gdzie **P** reprezentuje podstawowy punkt końcowy i **S** reprezentuje pomocniczy punkt końcowy.

![Uruchomiona aplikacja konsolowa](media/storage-create-geo-redundant-storage/figure3.png)

W przykładowym kodzie metoda `run_circuit_breaker` w pliku `circuitbreaker.py` jest używana do pobierania obrazu z konta magazynu przy użyciu metody [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

Funkcja ponawiania obiektu usługi Storage została ustawiona na zasady ponawiania liniowego. Funkcja ponawiania określa, czy należy ponowić próbę żądania, oraz wskazuje liczbę sekund oczekiwania przed ponownym podjęciem próby wykonania żądania. Ustaw pozycję **retry\_to\_secondary** na wartość true, jeśli żądanie powinno być ponawiane w punkcie pomocniczym w przypadku niepowodzenia początkowego żądania w punkcie podstawowym. W przykładowej aplikacji niestandardowe zasady ponawiania zostały zdefiniowane w funkcji `retry_callback` obiektu magazynu.

Przed pobraniem zdefiniowano [retry_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) i funkcję [response_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) obiektu Usługi. Te funkcje definiują procedury obsługi zdarzeń wyzwalane po pomyślnym zakończeniu pobierania lub po niepowodzeniu pobierania i podjęciu jego kolejnej próby.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aby uruchomić próbkę, otwórz wiersz polecenia, przejdź do `node index.js`przykładowego folderu, a następnie wprowadź .

Przykład tworzy kontener na koncie magazynu obiektów Blob, przekazuje **HelloWorld.png** do kontenera, a następnie wielokrotnie sprawdza, czy kontener i obraz zostały zreplikowane do regionu pomocniczego. Po replikacji zostanie wyświetlony monit o wprowadzenie **D** lub **Q** (a następnie wpisać ENTER) w celu pobrania lub zamknięcia. Dane wyjściowe powinny wyglądać podobnie do następującego przykładu:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Procedura obsługi zdarzeń ponawiania

Procedura obsługi zdarzeń `OperationContextRetrying` jest wywoływana, jeśli pobieranie obrazu nie powiedzie się i zostało ustawiane na ponawianie. W przypadku osiągnięcia maksymalnej liczby ponownych prób zdefiniowanej w aplikacji tryb [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) żądania zmienia się na `SecondaryOnly`. To ustawienie wymusza na aplikacji podjęcie próby pobrania obrazu z pomocniczego punktu końcowego. Ta konfiguracja ogranicza czas żądania obrazu, ponieważ ponowne próby dotyczące podstawowego punktu końcowego nie są podejmowane bez ograniczeń.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Procedura obsługi zdarzeń dla ukończonego żądania

Procedura obsługi zdarzeń `OperationContextRequestCompleted` jest wywoływana, jeśli pobieranie obrazu powiedzie się. Jeśli aplikacja używa pomocniczego punktu końcowego, będzie używać go maksymalnie 20 razy. Po 20 razach aplikacja ustawi tryb [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) z powrotem na `PrimaryThenSecondary` i ponowi próbę użycia podstawowego punktu końcowego. Jeśli żądanie zakończy się pomyślnie, aplikacja będzie nadal odczytywać dane z podstawowego punktu końcowego.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Procedura obsługi zdarzeń ponawiania

Procedura obsługi zdarzeń `retry_callback` jest wywoływana, jeśli pobieranie obrazu nie powiedzie się i zostało ustawiane na ponawianie. W przypadku osiągnięcia maksymalnej liczby ponownych prób zdefiniowanej w aplikacji tryb [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) żądania zmienia się na `SECONDARY`. To ustawienie wymusza na aplikacji podjęcie próby pobrania obrazu z pomocniczego punktu końcowego. Ta konfiguracja ogranicza czas żądania obrazu, ponieważ ponowne próby dotyczące podstawowego punktu końcowego nie są podejmowane bez ograniczeń.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Procedura obsługi zdarzeń dla ukończonego żądania

Procedura obsługi zdarzeń `response_callback` jest wywoływana, jeśli pobieranie obrazu powiedzie się. Jeśli aplikacja używa pomocniczego punktu końcowego, będzie używać go maksymalnie 20 razy. Po 20 razach aplikacja ustawi tryb [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) z powrotem na `PRIMARY` i ponowi próbę użycia podstawowego punktu końcowego. Jeśli żądanie zakończy się pomyślnie, aplikacja będzie nadal odczytywać dane z podstawowego punktu końcowego.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Z Node.js V10 SDK, programy obsługi wywołania zwrotnego są niepotrzebne. Zamiast tego przykład tworzy potok skonfigurowany z opcjami ponawiania próby i pomocniczego punktu końcowego. Dzięki temu aplikacja automatycznie przełączyć się do potoku pomocniczego, jeśli nie można dotrzeć do danych za pośrednictwem potoku podstawowego.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Następne kroki

W pierwszej części serii omówiono czynności dotyczące uzyskiwania wysokiej dostępności aplikacji przy użyciu kont magazynu RA-GRS.

Przejdź do drugiej części serii, aby dowiedzieć się, jak symulować błąd i wymuszać użycie pomocniczego punktu końcowego RA-GRS w aplikacji.

> [!div class="nextstepaction"]
> [Symulowanie błędu w odczytaniu z regionu podstawowego](storage-simulate-failure-ragrs-account-app.md)

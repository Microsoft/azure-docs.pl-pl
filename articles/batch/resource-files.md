---
title: Tworzenie i używanie plików zasobów
description: Dowiedz się, jak tworzyć pliki zasobów usługi Batch z różnych źródeł danych wejściowych. W tym artykule opisano kilka typowych metod tworzenia i umieszczania ich na maszynie wirtualnej.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 84a5e9780b4fa0abfec5b736e04d385f14716873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109293"
---
# <a name="creating-and-using-resource-files"></a>Tworzenie i używanie plików zasobów

Zadanie Azure Batch często wymaga pewnej formy danych do przetworzenia. Pliki zasobów są sposobem na dostarczenie tych danych do maszyny wirtualnej z usługą Batch za pośrednictwem zadania. Wszystkie typy zadań obsługują pliki zasobów: zadania, zadania uruchamiania, zadania przygotowania zadania, zadania zwolnienia zadań itp. W tym artykule opisano kilka typowych metod tworzenia plików zasobów i umieszczania ich na maszynie wirtualnej.  

Pliki zasobów umieszczają dane na maszynie wirtualnej w usłudze Batch, ale typ danych i sposób ich użycia są elastyczne. Istnieją jednak niektóre typowe przypadki użycia:

1. Udostępnianie typowych plików na każdej maszynie wirtualnej przy użyciu plików zasobów w zadaniu uruchamiania
1. Udostępnianie danych wejściowych do przetworzenia przez zadania

Typowe pliki mogą to być na przykład pliki w zadaniu uruchamiania służące do instalowania aplikacji uruchamianych przez zadania. Dane wejściowe mogą być nieprzetworzonymi obrazami lub danymi wideo albo informacjami, które mają być przetwarzane przez partię.

## <a name="types-of-resource-files"></a>Typy plików zasobów

Istnieje kilka różnych opcji umożliwiających generowanie plików zasobów. Proces tworzenia plików zasobów różni się w zależności od tego, gdzie są przechowywane oryginalne dane.

Opcje tworzenia pliku zasobów:

- [Adres URL kontenera magazynu](#storage-container-url): generuje plik zasobów z dowolnego kontenera magazynu na platformie Azure
- [Nazwa kontenera magazynu](#storage-container-name): generuje plik zasobów na podstawie nazwy kontenera na koncie usługi Azure Storage połączonym z usługą Batch
- [Punkt końcowy sieci Web](#web-endpoint): generuje plik zasobów z dowolnego prawidłowego adresu URL http

### <a name="storage-container-url"></a>Adres URL kontenera magazynu

Użycie adresu URL kontenera magazynu oznacza, że z odpowiednimi uprawnieniami można uzyskać dostęp do plików w dowolnym kontenerze magazynu na platformie Azure. 

W tym przykładzie w języku C# pliki zostały już przekazane do kontenera usługi Azure Storage jako magazynu obiektów BLOB. Aby uzyskać dostęp do danych potrzebnych do utworzenia pliku zasobów, najpierw musimy uzyskać dostęp do kontenera magazynu.

Utwórz identyfikator URI sygnatury dostępu współdzielonego (SAS) z odpowiednimi uprawnieniami dostępu do kontenera magazynu. Ustaw czas wygaśnięcia i uprawnienia dla sygnatury dostępu współdzielonego. W takim przypadku nie zostanie określony czas rozpoczęcia, więc sygnatura dostępu współdzielonego stają obowiązywać natychmiast i wygasa po upływie dwóch godzin od jego wygenerowania.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Aby uzyskać dostęp do kontenera, musisz mieć `Read` zarówno `List` uprawnienia, jak i dostęp do obiektów BLOB `Read` .

Po skonfigurowaniu uprawnień należy utworzyć token sygnatury dostępu współdzielonego i sformatować adres URL sygnatury dostępu współdzielonego, aby uzyskać dostęp do kontenera magazynu. Przy użyciu sformatowanego adresu URL sygnatury dostępu współdzielonego dla kontenera magazynu wygeneruj plik zasobów przy użyciu polecenia [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternatywą dla generowania adresu URL sygnatury dostępu współdzielonego jest włączenie anonimowego, publicznego odczytu do kontenera i jego obiektów BLOB w usłudze Azure Blob Storage. Dzięki temu można przyznać dostęp tylko do odczytu do tych zasobów bez udostępniania klucza konta i bez konieczności używania sygnatury dostępu współdzielonego. Publiczny dostęp do odczytu jest zazwyczaj używany w scenariuszach, w których niektóre obiekty blob mają być zawsze dostępne do anonimowego dostępu do odczytu. Jeśli ten scenariusz odpowiada Twojemu rozwiązaniu, zobacz artykuł [anonimowy dostęp do obiektów BLOB](../storage/blobs/anonymous-read-access-configure.md) , aby dowiedzieć się więcej na temat zarządzania dostępem do danych obiektów BLOB.

### <a name="storage-container-name"></a>Nazwa kontenera magazynu

Zamiast konfigurować i tworzyć adres URL sygnatury dostępu współdzielonego, można użyć nazwy kontenera usługi Azure Storage, aby uzyskać dostęp do danych obiektów BLOB. Używany kontener magazynu musi znajdować się na koncie magazynu platformy Azure połączonym z kontem usługi Batch. To konto magazynu jest nazywane kontem magazynu. Za pomocą kontenera usługi autostorage można ominąć Konfigurowanie i tworzenie adresu URL sygnatury dostępu współdzielonego, aby uzyskać dostęp do kontenera magazynu.

W tym przykładzie przyjęto założenie, że dane, które mają być używane do tworzenia plików zasobów, są już na koncie usługi Azure Storage połączonym z kontem usług Batch. Jeśli nie masz konta usługi autostorage, zapoznaj się z instrukcjami w temacie [Tworzenie konta usługi Batch](batch-account-create-portal.md) , aby uzyskać szczegółowe informacje na temat tworzenia i łączenia konta.

Przy użyciu połączonego konta magazynu nie trzeba tworzyć i konfigurować adresu URL sygnatury dostępu współdzielonego dla kontenera magazynu. Zamiast tego Podaj nazwę kontenera magazynu na połączonym koncie magazynu.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Punkt końcowy sieci Web

Dane, które nie zostały przekazane do usługi Azure Storage, nadal mogą być używane do tworzenia plików zasobów. Można określić dowolny prawidłowy adres URL HTTP zawierający dane wejściowe. Adres URL jest dostarczany do interfejsu API usługi Batch, a następnie dane są używane do tworzenia pliku zasobów.

W poniższym przykładzie w języku C# dane wejściowe są hostowane w fikcyjnym punkcie końcowym usługi GitHub. Interfejs API pobiera plik z prawidłowego punktu końcowego sieci Web i generuje plik zasobów, który ma zostać wykorzystany przez zadanie. W tym scenariuszu nie są potrzebne żadne poświadczenia.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Porady i sugestie

Każde zadanie Azure Batch używa plików w inny sposób, co oznacza, że w usłudze Batch dostępne są opcje zarządzania plikami na zadaniach. Następujące scenariusze nie są przeznaczone do kompleksowego, ale obejmują kilka typowych sytuacji i udostępniają zalecenia.

### <a name="many-resource-files"></a>Wiele plików zasobów

Zadanie wsadowe może zawierać kilka zadań, które używają tych samych, wspólnych plików. Jeśli wspólne pliki zadań są współużytkowane przez wiele zadań, użycie pakietu aplikacji do przechowywania plików zamiast plików zasobów może być lepszym rozwiązaniem. Pakiety aplikacji zapewniają optymalizację dla szybkości pobierania. Ponadto dane w pakietach aplikacji są buforowane między zadaniami, więc jeśli pliki zadań nie zmieniają się często, pakiety aplikacji mogą być dobrze dopasowane do danego rozwiązania. W przypadku pakietów aplikacji nie trzeba ręcznie zarządzać kilkoma plikami zasobów ani generować adresów URL SAS, aby uzyskać dostęp do plików w usłudze Azure Storage. Usługa Batch działa w tle w usłudze Azure Storage, aby przechowywać i wdrażać pakiety aplikacji w węzłach obliczeniowych.

Jeśli każde zadanie ma wiele plików unikatowych dla tego zadania, najlepiej jest użyć plików zasobów, ponieważ zadania, które używają unikatowych plików, często muszą być aktualizowane lub zastępowane, co nie jest łatwe do wykonania w przypadku zawartości pakietów aplikacji. Pliki zasobów zapewniają dodatkową elastyczność aktualizowania, dodawania lub edytowania poszczególnych plików.

### <a name="number-of-resource-files-per-task"></a>Liczba plików zasobów na zadanie

Jeśli w zadaniu określono kilka plików zasobów, partia może odrzucić zadanie jako zbyt duże. Najlepszym rozwiązaniem jest utrzymywanie zadań przez zminimalizowanie liczby plików zasobów w samym zadaniu.

Jeśli nie ma możliwości zminimalizowania liczby plików potrzebnych do wykonania zadania, można zoptymalizować zadanie, tworząc jeden plik zasobu, który odwołuje się do kontenera magazynu plików zasobów. W tym celu należy umieścić pliki zasobów w kontenerze usługi Azure Storage i użyć różnych [metod](/dotnet/api/microsoft.azure.batch.resourcefile#methods) "Container" dla plików zasobów. Użyj opcji prefiksu obiektu BLOB, aby określić kolekcje plików do pobrania dla zadań.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [pakietami aplikacji](batch-application-packages.md) jako alternatywą dla plików zasobów.
- Aby uzyskać więcej informacji o używaniu kontenerów dla plików zasobów, zobacz [obciążenia kontenerów](batch-docker-container-workloads.md).
- Aby dowiedzieć się, jak zbierać i zapisywać dane wyjściowe z zadań, zobacz [utrwalanie zadań i danych wyjściowych zadań](batch-task-output.md).
- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
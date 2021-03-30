---
title: Przykłady usługi Azure Storage korzystające z platformy .NET | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich usługi .NET Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 760fedd94ef682b183eede1215186de973e9a216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359785"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Przykłady usługi Azure Storage korzystające z bibliotek klienckich V12 .NET

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> W tych przykładach użyto najnowszej biblioteki .NET V12 w usłudze Azure Storage. Aby uzyskać starszy kod V11, zobacz [przykłady BLOB Storage platformy Azure dla platformy .NET](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) w repozytorium GitHub.

## <a name="blob-samples"></a>Przykłady obiektów BLOB

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu poświadczeń klucza współużytkowanego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie za pomocą tożsamości platformy Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu tokenu Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anonimowy dostęp do publicznego obiektu BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Przetwarzanie wsadowe

:::row:::
   :::column span="":::
      [Usuń kilka obiektów BLOB w jednym żądaniu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Ustaw kilka warstw dostępu do obiektów BLOB w jednym żądaniu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Szczegółowy formant w żądaniu wsadowym](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Błędy przechwytywania z niezakończonej operacji podrzędnej](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Obiekt blob

:::row:::
   :::column span="":::
      [Przekazywanie pliku do obiektu BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Pobieranie obiektu BLOB do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobierz obraz](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Wyświetlanie listy wszystkich obiektów BLOB w kontenerze](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów
:::row:::
   :::column span="2":::
      [Wyzwól odwracalny błąd przy użyciu klienta kontenera](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Przykłady Data Lake Storage Gen2

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Anonimowy dostęp do pliku publicznego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu poświadczeń klucza współużytkowanego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu sygnatury dostępu współdzielonego (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu tokenu Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>System plików
:::row:::
   :::column span="":::
      [Tworzenie pliku przy użyciu klienta systemu plików](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Pobieranie właściwości pliku i katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Zmiana nazwy pliku i katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Tworzenie katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Tworzenie pliku przy użyciu klienta katalogu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetlanie listy katalogów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Przechodzenie plików i katalogów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Plik
:::row:::
   :::column span="":::
      [Przekazywanie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Przekaż, dołączając do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ustawianie i pobieranie listy kontroli dostępu do plików](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Ustawianie i pobieranie uprawnień do pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów

:::row:::
   :::column span="2":::
      [Wyzwól odwracalny błąd](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Przykłady Azure Files

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu poświadczeń klucza współużytkowanego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Uwierzytelnianie przy użyciu sygnatury dostępu współdzielonego (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Udziały plików

:::row:::
   :::column span="":::
      [Tworzenie udziału i przekazywanie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Przechodzenie plików i katalogów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów

:::row:::
   :::column span="2":::
      [Wyzwól odwracalny błąd przy użyciu klienta udziału](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Przykładowe kolejki

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie za pomocą usługi Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu poświadczeń klucza współużytkowanego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu sygnatury dostępu współdzielonego (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Uwierzytelnianie przy użyciu tokenu Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kolejka

:::row:::
   :::column span="2":::
      [Tworzenie kolejki i Dodawanie komunikatu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Komunikat

:::row:::
   :::column span="":::
      [Odbieranie i przetwarzanie komunikatów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Wgląd w wiadomości](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Odbieraj komunikaty i Aktualizuj limit czasu widoczności](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów 
:::row:::
   :::column span="2":::
      [Wyzwól odwracalny błąd przy użyciu klienta kolejki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Przykłady tabeli (v11)

:::row:::
   :::column span="":::
      [Utwórz tabelę](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Usuń jednostkę/tabelę](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wstaw/Scal/Zamień jednostkę](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Jednostki zapytań](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wykonywanie zapytań dotyczących tabel](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Lista ACL/właściwości tabeli](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aktualizuj jednostkę](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Biblioteki przykładów kodu platformy Azure

Aby wyświetlić kompletne biblioteki przykładowe programu .NET, przejdź do:

* [Przykłady kodu obiektów blob platformy Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Przykłady kodu Azure Data Lake](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Przykłady kodu Azure Files](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Przykłady kodu w kolejce platformy Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Repozytorium GitHub można przeglądać i klonować dla każdej biblioteki.

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service na platformie .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Wprowadzenie z usługą Azure Table Service na platformie .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Wprowadzenie z usługą plików platformy Azure w środowisku .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* Java: [przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* JavaScript/Node.js: [przykłady usługi Azure Storage korzystające z języka JavaScript](storage-samples-javascript.md)
* C++: [przykłady usługi Azure Storage przy użyciu języka c++](storage-samples-c-plus-plus.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)

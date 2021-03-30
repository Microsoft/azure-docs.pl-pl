---
title: Przykłady usługi Azure Storage przy użyciu języka Java | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich magazynu Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 9e3cd6be0da7c6d9401945293283de874c7cd866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359768"
---
# <a name="azure-storage-samples-using-v12-java-client-libraries"></a>Przykłady usługi Azure Storage korzystające z bibliotek klienckich V12 Java

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają najnowszej biblioteki środowiska Java V12 w usłudze Azure Storage. Aby uzyskać starszy kod V8, zobacz [wprowadzenie z usługą Azure Blob Service w języku Java](https://github.com/Azure-Samples/storage-blob-java-getting-started) w repozytorium GitHub.

## <a name="blob-samples"></a>Przykłady obiektów BLOB

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu poświadczeń klucza współużytkowanego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L38)
   :::column-end:::
   :::column span="":::
      [Uwierzytelnianie przy użyciu tożsamości platformy Azure](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/AzureIdentityExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob service

:::row:::
   :::column span="":::
      [Tworzenie klienta usługi BLOB Service](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L49)
   :::column-end:::
   :::column span="":::
      [Wyświetlanie listy kontenerów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L10)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuwanie kontenerów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L52)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Przetwarzanie wsadowe

:::row:::
   :::column span="":::
      [Tworzenie klienta Batch BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Zbiorcze usuwanie obiektów BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Ustawianie warstwy dostępu dla partii obiektów BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L51)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Kontener

:::row:::
   :::column span="":::
      [Tworzenie klienta kontenera](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L57)
   :::column-end:::
   :::column span="":::
      [Tworzenie kontenera](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L64)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetlanie listy obiektów blob](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L112)
   :::column-end:::
   :::column span="":::
      [Usuwanie kontenera](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L123)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Obiekt blob

:::row:::
   :::column span="":::
      [Przekazywanie obiektu blob](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L79)
   :::column-end:::
   :::column span="":::
      [Pobieranie obiektu blob](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Usuwanie obiektu blob](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L118)
   :::column-end:::
   :::column span="":::
      [Przekazywanie obiektu BLOB z dużego pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Pobieranie dużego obiektu BLOB do pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L100)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Rozwiązywanie problemów
:::row:::
   :::column span="2":::
      [Wyzwól odwracalny błąd przy użyciu klienta kontenera](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/StorageErrorHandlingExample.java#L11)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Przykłady Data Lake Storage Gen2

### <a name="data-lake-service"></a>Usługa Data Lake
:::row:::
   :::column span="":::
      [Tworzenie klienta usługi Data Lake](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L48)
   :::column-end:::
   :::column span="":::
      [Tworzenie klienta systemu plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L57)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>System plików
:::row:::
   :::column span="":::
      [Tworzenie systemu plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L64)
   :::column-end:::
   :::column span="":::
      [Tworzenie katalogu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Utwórz plik i podkatalog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L73)
   :::column-end:::
   :::column span="":::
      [Tworzenie klienta pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetl listę ścieżek w systemie plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L131)
   :::column-end:::
   :::column span="":::
      [Usuń system plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L142)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Wyświetlanie listy systemów plików na koncie usługi Azure Storage](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/ListFileSystemsExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Tworzenie klienta katalogu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L31)
   :::column-end:::
   :::column span="":::
      [Tworzenie katalogu nadrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Tworzenie katalogu podrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L44)
   :::column-end:::
   :::column span="":::
      [Utwórz plik w katalogu podrzędnym](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobierz właściwości katalogu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L68)
   :::column-end:::
   :::column span="":::
      [Usuwanie katalogu podrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuwanie folderu nadrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Plik

:::row:::
   :::column span="":::
      [Tworzenie pliku przy użyciu klienta plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L93)
   :::column-end:::
   :::column span="":::
      [Usuwanie pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L137)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ustawianie kontroli dostępu dla pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L82)
   :::column-end:::
   :::column span="":::
      [Pobieranie kontroli dostępu do pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L104)
   :::column-end:::
:::row-end:::


## <a name="azure-file-samples"></a>Przykłady plików platformy Azure

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="2":::
      [Uwierzytelnianie przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L27)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>Usługa plików

:::row:::
   :::column span="":::
      [Tworzenie udziałów plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L31)
   :::column-end:::
   :::column span="":::
      [Pobierz właściwości](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wyświetl listę udziałów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
   :::column span="":::
      [Usuń udziały](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>Udział plików

:::row:::
   :::column span="":::
      [Tworzenie klienta udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L29)
   :::column-end:::
   :::column span="":::
      [Tworzenie udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Tworzenie migawki udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L55)
   :::column-end:::
   :::column span="":::
      [Tworzenie katalogu przy użyciu klienta udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L63)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobierz właściwości udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L72)
   :::column-end:::
   :::column span="":::
      [Pobierz katalog główny i katalogi list](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuwanie udziału](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L151)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Tworzenie katalogu nadrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L35)
   :::column-end:::
   :::column span="":::
      [Tworzenie katalogu podrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L42)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Utwórz plik w katalogu podrzędnym](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L50)
   :::column-end:::
   :::column span="":::
      [Wyświetlanie listy katalogów i plików](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Usuń folder podrzędny](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L90)
   :::column-end:::
   :::column span="":::
      [Usuwanie folderu nadrzędnego](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L97)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Plik

:::row:::
   :::column span="":::
      [Tworzenie klienta pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L45)
   :::column-end:::
   :::column span="":::
      [Przekazywanie pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L100)
   :::column-end:::
   :::column span="":::
      [Pobieranie właściwości pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L120)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Usuwanie pliku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L128)
   :::column-end:::
:::row-end:::


## <a name="queue-samples"></a>Przykładowe kolejki

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="2":::
      [Uwierzytelnianie przy użyciu tokenu SAS](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L17)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>usługa kolejki

:::row:::
   :::column span="":::
      [Tworzenie kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L20)
   :::column-end:::
   :::column span="":::
      [Wyświetl kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L22)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuń kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kolejka

:::row:::
   :::column span="":::
      [Tworzenie klienta kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L25)
   :::column-end:::
   :::column span="":::
      [Dodawanie komunikatów do kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Komunikat

:::row:::
   :::column span="":::
      [Pobierz liczbę komunikatów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L34)
   :::column-end:::
   :::column span="":::
      [Wgląd w wiadomości](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Odbieranie komunikatów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Aktualizowanie wiadomości](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Usuń pierwszy komunikat](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L50)
   :::column-end:::
   :::column span="":::
      [Wyczyść wszystkie komunikaty](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Usuwanie kolejki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L64)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Przykłady tabeli (v11)

:::row:::
   :::column span="":::
      [Utwórz tabelę](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L50)
   :::column-end:::
   :::column span="":::
      [Usuń jednostkę/tabelę](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L109)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wstaw/Scal/Zamień jednostkę](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L195)
   :::column-end:::
   :::column span="":::
      [Jednostki zapytań](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L234)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Wykonywanie zapytań dotyczących tabel](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L262)
   :::column-end:::
   :::column span="":::
      [Lista ACL/właściwości tabeli](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aktualizuj jednostkę](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L76)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Biblioteki przykładów kodu platformy Azure

Aby wyświetlić kompletne biblioteki przykładowe języka Java, przejdź do:

* [Przykłady kodu obiektów blob platformy Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)
* [Przykłady kodu Azure Data Lake](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake)
* [Przykłady kodu Azure Files](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share)
* [Przykłady kodu w kolejce platformy Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)

Repozytorium GitHub można przeglądać i klonować dla każdej biblioteki.

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service w języku Java](../blobs/storage-quickstart-blobs-java.md)
* [Wprowadzenie z usługą Azure Queue Service w języku Java](../queues/storage-quickstart-queues-java.md)
* [Wprowadzenie do usługi Azure Table Service w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Wprowadzenie z usługą plików platformy Azure w języku Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* .NET: [przykłady usługi Azure Storage korzystające z platformy .NET](storage-samples-dotnet.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* JavaScript/Node.js: [przykłady usługi Azure Storage korzystające z języka JavaScript](storage-samples-javascript.md)
* C++: [przykłady usługi Azure Storage przy użyciu języka c++](storage-samples-c-plus-plus.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)

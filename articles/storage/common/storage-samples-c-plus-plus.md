---
title: Przykłady usługi Azure Storage korzystające z języka C++ | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich magazynu C++.
author: twooley
ms.author: twooley
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: d82b31cde0e8df5db2d073abcec8ea44f13bd0f0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276675"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>Przykłady usługi Azure Storage korzystające z bibliotek klienckich V12 C++

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają najnowszej biblioteki V12 C++ usługi Azure Storage.

## <a name="blob-samples"></a>Przykłady obiektów BLOB

:::row:::
   :::column:::
        [Uwierzytelnianie przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [Tworzenie kontenera obiektów blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Uzyskiwanie klienta BLOB](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Przekazywanie obiektu blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Ustawianie metadanych w obiekcie blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [Pobierz właściwości obiektu BLOB](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [Pobieranie obiektu blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Przykłady Data Lake Storage Gen2

:::row:::
   :::column:::
        [Tworzenie klienta usługi przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [Tworzenie klienta systemu plików przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Tworzenie systemu plików](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Tworzenie katalogu](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Utwórz plik](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [Dołącz dane do pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Opróżnianie danych pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [Odczytywanie pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Wyświetl listę wszystkich systemów plików](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [Usuń system plików](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Przykłady Azure Files

:::row:::
    :::column:::
        [Tworzenie klienta udziału przy użyciu parametrów połączenia](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [Tworzenie udziału plików](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Pobierz klienta plików](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [Przekazywanie pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Ustawianie metadanych w pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [Pobieranie właściwości pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [Pobieranie pliku](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Biblioteki przykładów kodu platformy Azure

Aby wyświetlić kompletne biblioteki przykładowe języka C++, przejdź do:

* [Przykłady kodu obiektów blob platformy Azure](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
* [Przykłady kodu Azure Data Lake](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/sample)
* [Przykłady kodu Azure Files](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/sample)

Repozytorium GitHub można przeglądać i klonować dla każdej biblioteki.

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Szybki Start: Biblioteka usługi Azure Blob Storage V12 — C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* .NET: [przykłady usługi Azure Storage korzystające z platformy .NET](storage-samples-dotnet.md)
* Java: [przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* JavaScript/Node.js: [przykłady usługi Azure Storage korzystające z języka JavaScript](storage-samples-javascript.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)

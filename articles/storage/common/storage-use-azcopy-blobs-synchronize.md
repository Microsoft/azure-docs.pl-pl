---
title: Synchronizuj z usługą Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które ułatwiają synchronizację z usługą Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ab1da88899ba2b90e303da107631e3878b3a8b58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635879"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Synchronizacja z usługą Azure Blob Storage za pomocą AzCopy v10

Magazyn lokalny można zsynchronizować z usługą Azure Blob Storage za pomocą narzędzia wiersza polecenia AzCopy v10. 

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów BLOB. Możesz również synchronizować kontenery i katalogi wirtualne ze sobą. Synchronizacja jest jednym ze sposobów. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera. Przykłady przedstawione w tej sekcji działają również z kontami, które mają hierarchiczną przestrzeń nazw. 

> [!NOTE]
> Bieżąca wersja AzCopy nie jest synchronizowana między innymi źródłami i miejscami docelowymi (na przykład: Magazyn plików lub Amazon Web Services (AWS).

Aby zobaczyć przykłady dla innych typów zadań, takich jak przekazywanie plików, pobieranie obiektów blob lub kopiowanie obiektów BLOB między kontami, zobacz linki przedstawione w sekcji [następne kroki](#next-steps) tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE] 
> W przykładach w tym artykule przyjęto założenie, że poświadczenia autoryzacji zostały podane przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Wytyczne

- Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw `--delete-destination` opcjonalną flagę na wartość `true` lub, `prompt` Aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym.

- Jeśli ustawisz `--delete-destination` flagę na `true` , AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, ustaw `--delete-destination` flagę na `prompt` .

- Aby zapobiec przypadkowemu usunięciu, upewnij się, że funkcja [usuwania nietrwałego](../blobs/soft-delete-blob-overview.md) została włączona przed użyciem `--delete-destination=prompt|true` flagi.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W tym przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem. 

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików ze zmianami w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Przykład** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera ze zmianami w innym kontenerze

Pierwszym kontenerem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym kontenerze

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójne cudzysłowy ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Synchronizuj z opcjonalnymi flagami

Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Określ, jak ściśle skróty MD5 mają być weryfikowane podczas pobierania.|**--check-MD5** = \[ NOCHECK \| FailIfDifferent logowanie \| \| FailIfDifferentOrMissing\]|
|Wyklucz pliki na podstawie wzorca.|**--Exclude-Path**|
|Określ, jak Szczegółowa ma być liczba wpisów dziennika związanych z synchronizacją.|**--poziom dziennika** = \[ Ostrzeżenie \| \| Brak informacji o błędzie \|\]|

Aby uzyskać pełną listę, zobacz [Opcje](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w następujących artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Kopiowanie między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
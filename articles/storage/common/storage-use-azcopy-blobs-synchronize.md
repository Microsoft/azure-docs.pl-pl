---
title: Synchronizowanie z usługą Azure Blob Storage przy użyciu narzędzia AzCopy w wersji 10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń narzędzia AzCopy, które ułatwiają synchronizowanie z usługą Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502933"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Synchronizowanie z usługą Azure Blob Storage przy użyciu narzędzia AzCopy

Magazyn lokalny można zsynchronizować z usługą Azure Blob Storage za pomocą narzędzia wiersza polecenia AzCopy w wersji 10. 

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów blob. Można również synchronizować między sobą kontenery i katalogi wirtualne. Synchronizacja jest jednym ze sposobów. Innymi słowy, wybierasz, który z tych dwóch punktów końcowych jest źródłem, a który jest miejscem docelowym. Synchronizacja korzysta również z interfejsów API między serwerami. Przykłady przedstawione w tej sekcji działają również z kontami, które mają hierarchiczną przestrzeń nazw. 

> [!NOTE]
> Bieżąca wersja programu AzCopy nie synchronizuje się między innymi źródłami i miejscami docelowymi (na przykład: Magazyn plików lub zasobniki Amazon Web Services (AWS) S3).

Aby wyświetlić przykłady innych typów zadań, takich jak przekazywanie plików, pobieranie obiektów blob lub kopiowanie [](#next-steps) obiektów blob między kontami, zobacz linki w sekcji Następne kroki tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i poznać sposoby podania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE] 
> W przykładach w tym artykule założono, że podano poświadczenia autoryzacji przy użyciu Azure Active Directory (Azure AD).
>
> Jeśli wolisz użyć tokenu SAS do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu programu AzCopy. Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Wytyczne

- Polecenie [synchronizacji](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio zmodyfikowane znaczniki czasu. Ustaw opcjonalną flagę na wartość lub , aby usunąć pliki w katalogu docelowym, jeśli te pliki już nie `--delete-destination` `true` istnieją w katalogu `prompt` źródłowym.

- Jeśli ustawisz `--delete-destination` flagę `true` na wartość , program AzCopy usunie pliki bez monitowania. Jeśli chcesz, aby przed usunięciem pliku przez program AzCopy był wyświetlany monit, ustaw `--delete-destination` flagę na `prompt` wartość .

- Jeśli planujesz ustawić flagę na lub , rozważ użycie polecenia copy zamiast polecenia synchronizacji i ustawienie `--delete-destination` `prompt` `false` parametru na [](storage-ref-azcopy-copy.md) [](storage-ref-azcopy-sync.md) `--overwrite` `ifSourceNewer` . Polecenie [kopiowania](storage-ref-azcopy-copy.md) zużywa mniej pamięci i wiąże się z mniejszymi kosztami rozliczeń, ponieważ operacja kopiowania nie musi indeksować źródła ani miejsca docelowego przed przeniesieniem plików. 

- Aby zapobiec przypadkowemu usunięciu, pamiętaj o włączeniu funkcji usuwania nie [softowego](../blobs/soft-delete-blob-overview.md) przed użyciem `--delete-destination=prompt|true` flagi .

- Maszyna, na której uruchamiasz polecenie synchronizacji, powinna mieć dokładny zegar systemowy, ponieważ czasy ostatniej modyfikacji mają kluczowe znaczenie dla określenia, czy plik ma zostać przeniesiony. Jeśli system ma znaczne nieschyć zegara, należy unikać modyfikowania plików w miejscu docelowym zbyt blisko czasu, w przypadku którego planujesz uruchomić polecenie synchronizacji.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera za pomocą zmian w lokalnym systemie plików

W tym przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem. 

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Przykład**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików ze zmianami w kontenerze

W tym przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera za pomocą zmian w innym kontenerze

Pierwszym kontenerem widocznym w tym poleceniu jest źródło. Drugi to miejsce docelowe.

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w cudzysłów (""), a nie pojedynczy cudzysłów ("").

**Składnia**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aktualizowanie katalogu za pomocą zmian w katalogu w innym kontenerze

Pierwszy katalog wyświetlany w tym poleceniu jest źródłem. Drugi to miejsce docelowe.

> [!TIP]
> W tym przykładzie argumenty ścieżki są ujęte w a cudzysłów pojedynczy (""). Używaj a cudzysłowów pojedynczych we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), argumenty ścieżki należy ująć w podwójny cudzysłów ("") zamiast cudzysłowów pojedynczych ("").

**Składnia**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Przykład**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Synchronizuj z opcjonalnymi flagami

Możesz dostosować operację synchronizacji przy użyciu opcjonalnych flag. Oto kilka przykładów.

|Scenariusz|Flaga|
|---|---|
|Określ sposób weryfikacji wyłącznie skrótów MD5 podczas pobierania.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIf Wymuś \| FailIf WymuśOrMissing\]|
|Wykluczanie plików na podstawie wzorca.|**--exclude-path**|
|Określ, jak szczegółowe mają być wpisy dziennika związane z synchronizacją.|**--poziom dziennika** = \[ OSTRZEŻENIE \| O \| BŁĘDZIE: \| BRAK\]|

Aby uzyskać pełną listę flag, zobacz [opcje](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> Flaga `--recursive` jest domyślnie `true` ustawiona na wartość . Flagi `--exclude-pattern` i dotyczą tylko nazw `--include-pattern` plików, a nie innych części ścieżki pliku. 

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można znaleźć w tych artykułach:

- [Przykłady: Upload](storage-use-azcopy-blobs-upload.md)
- [Przykłady: Pobierz](storage-use-azcopy-blobs-download.md)
- [Przykłady: Kopiowanie między kontami](storage-use-azcopy-blobs-copy.md)
- [Przykłady: Zasobniki usługi Amazon S3](storage-use-azcopy-s3.md)
- [Przykłady: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Przykłady: Azure Files](storage-use-azcopy-files.md)
- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zapoznaj się z tymi artykułami, aby skonfigurować ustawienia, zoptymalizować wydajność i rozwiązać problemy:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)


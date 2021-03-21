---
title: Macierz obsługi dla tworzenia kopii zapasowych obiektów blob platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych obiektów blob platformy Azure (w wersji zapoznawczej)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746507"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Macierz obsługi dla tworzenia kopii zapasowych obiektów blob platformy Azure (w wersji zapoznawczej)

Ten artykuł zawiera podsumowanie regionalnej dostępności, obsługiwanych scenariuszy i ograniczeń operacyjnych kopii zapasowych obiektów BLOB.

## <a name="supported-regions"></a>Obsługiwane regiony

Operacyjna kopia zapasowa dla obiektów BLOB jest obecnie dostępna w następujących regionach: Australia Środkowa, Australia Wschodnia, Brazylia Południowa, Kanada środkowa, Indie Środkowe, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy Środkowo-Zachodnie, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Ameryka Północna, Południowe, Południowe, Południowe Zjednoczone Królestwo Szwajcaria Północna Azja Wschodnia Południowo-środkowe stany USA, Europa Zachodnia , Zachodnie stany USA, zachodnie stany USA 2

## <a name="limitations"></a>Ograniczenia

Operacyjna kopia zapasowa obiektów BLOB używa przywracania do punktu w czasie obiektów blob, przechowywania wersji obiektów blob, nietrwałego usuwania dla obiektów blob, zmiany kanału informacyjnego dla obiektów blob i blokowania, aby zapewnić lokalne rozwiązanie do tworzenia kopii zapasowych. Ograniczenia dotyczące tych możliwości również dotyczą operacyjnej kopii zapasowej.

**Obsługiwane scenariusze:** Operacyjna kopia zapasowa obsługuje blokowe obiekty blob w standardowych kontach magazynu ogólnego przeznaczenia w wersji 2. ADLS Gen2 konta nie są obsługiwane. Ponadto zostaną przywrócone wszystkie stronicowe obiekty blob, dołączanie obiektów blob i Premium obiektów BLOB na koncie magazynu.

**Inne ograniczenia:**

- Jeśli kontener został usunięty w okresie przechowywania, ten kontener nie zostanie przywrócony z operacją przywracania do punktu w czasie. Jeśli podjęto próbę przywrócenia zakresu obiektów blob, które zawierają obiekty blob w usuniętym kontenerze, operacja przywracania do punktu w czasie zakończy się niepowodzeniem. Więcej informacji o ochronie kontenerów można znaleźć w temacie [usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview).
- Jeśli obiekt BLOB został przeniesiony między warstwami gorąca i chłodna w okresie między obecnym chwilą a punktem przywracania, obiekt BLOB zostanie przywrócony do poprzedniej warstwy. Przywracanie blokowych obiektów BLOB w warstwie archiwum nie jest obsługiwane. Na przykład jeśli obiekt BLOB w warstwie gorąca został przeniesiony do warstwy archiwum dwa dni temu, a operacja przywracania zostanie przywrócona do punktu w ciągu trzech dni temu, obiekt BLOB nie zostanie przywrócony do warstwy gorąca. Aby przywrócić zarchiwizowany obiekt BLOB, najpierw przenieś go z warstwy archiwum. Aby uzyskać więcej informacji, zobacz informacje o [rehydratacji danych obiektów blob z warstwy archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration).
- Blok, który został przekazany za pośrednictwem bloku [Put](https://docs.microsoft.com/rest/api/storageservices/put-block) lub [Put z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url), ale nie został przekazany za pośrednictwem [listy bloków Put](https://docs.microsoft.com/rest/api/storageservices/put-block-list), nie jest częścią obiektu BLOB i dlatego nie został przywrócony jako część operacji przywracania.
- Nie można przywrócić obiektu BLOB z aktywną dzierżawą. Jeśli obiekt BLOB z aktywną dzierżawą zostanie uwzględniony w zakresie obiektów BLOB do przywrócenia, operacja przywracania zakończy się niepowodzeniem automatycznie. Przerwij wszystkie aktywne dzierżawy przed rozpoczęciem operacji przywracania.
- Migawki nie są tworzone ani usuwane w ramach operacji przywracania. Tylko podstawowy obiekt BLOB zostanie przywrócony do poprzedniego stanu.

## <a name="next-steps"></a>Następne kroki

- [Przegląd operacyjnej kopii zapasowej dla obiektów blob platformy Azure (w wersji zapoznawczej)](blob-backup-overview.md)

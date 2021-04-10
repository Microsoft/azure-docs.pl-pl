---
title: Microsoft Azure Data Box Gateway przypadków użycia | Microsoft Docs
description: Opisuje przypadki użycia Azure Data Box Gateway, rozwiązanie do magazynowania urządzeń wirtualnych, które umożliwia przesyłanie danych do platformy Azure,
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786616"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Przypadki użycia dla Azure Data Box Gateway

Azure Data Box Gateway to urządzenie bramy magazynu w chmurze, które znajduje się w Twoim miejscu i wysyła obraz, Multimedia i inne dane na platformę Azure. Ta brama magazynu w chmurze jest maszyną wirtualną zainicjowaną w funkcji hypervisor. Dane są zapisywane na tym urządzeniu wirtualnym przy użyciu protokołów NFS i SMB, które następnie wysyła do platformy Azure. Ten artykuł zawiera szczegółowy opis scenariuszy, w których można wdrożyć to urządzenie.

Użyj Data Box Gateway w następujących scenariuszach:

- Ciągłe pozyskiwanie dużych ilości danych.
- Archiwizowanie danych w chmurze w bezpieczny i wydajny sposób.
- W przypadku przyrostowego transferu danych przez sieć po zakończeniu wstępnego transferu zbiorczego za pomocą urządzenie Data Box.

Każdy z tych scenariuszy został szczegółowo opisany w kolejnych sekcjach.


## <a name="continuous-data-ingestion"></a>Ciągłe pozyskiwanie danych

Jedną z głównych zalet Data Box Gateway jest możliwość ciągłego pozyskiwania danych do urządzenia w celu kopiowania ich do chmury, niezależnie od rozmiaru danych.

Gdy dane są zapisywane na urządzeniu bramy, urządzenie przekazuje dane do usługi Azure Storage. Urządzenie automatycznie zarządza magazynem przez usunięcie plików lokalnie podczas zachowywania metadanych po osiągnięciu określonego progu. Przechowywanie lokalnej kopii metadanych umożliwia urządzeniu bramy przekazywanie zmian tylko wtedy, gdy plik zostanie zaktualizowany. Dane przekazane do urządzenia bramy powinny być zgodne z wytycznymi dotyczącymi [zastrzeżenia przekazywania danych](data-box-gateway-limits.md#data-upload-caveats).

Gdy urządzenie jest wypełniane danymi, zaczyna ograniczać szybkość transferu danych przychodzących (w razie potrzeby), aby odpowiadały szybkości, z jaką dane są przekazywane do chmury. Do monitorowania ciągłego pozyskiwania na urządzeniu należy używać alertów. Te alerty są wywoływane po rozpoczęciu ograniczania i są usuwane po zatrzymaniu ograniczania.

## <a name="cloud-archival-of-data"></a>Archiwizowanie danych w chmurze

Użyj Data Box Gateway, jeśli chcesz przechowywać dane przez długi czas w chmurze. Możesz użyć warstwy archiwum magazynu do długoterminowego przechowywania.

Warstwa archiwum jest zoptymalizowana pod kątem przechowywania rzadko używanych danych przez co najmniej 180 dni. Warstwa archiwum zapewnia najniższe koszty magazynowania, ale ma największe koszty dostępu. Aby uzyskać więcej informacji, przejdź do [warstwy dostępu archiwizowanie](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Przenoszenie danych do warstwy Archiwum

Przed rozpoczęciem upewnij się, że masz uruchomione urządzenie Data Box Gateway. Wykonaj kroki opisane w [samouczku: przygotowanie do wdrożenia Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i dalsze przechodzenie do następnego samouczka, dopóki nie będziesz mieć urządzenia operacyjnego.

- Za pomocą urządzenia Data Box Gateway Przekaż dane na platformę Azure, postępując zgodnie z opisem w temacie [transfer danych za pośrednictwem Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Po przekazaniu danych należy przenieść je do warstwy archiwum. Warstwę obiektów BLOB można ustawić na dwa sposoby: za pomocą skryptu Azure PowerShell lub zasad zarządzania cyklem życia usługi Azure Storage.  
    - W przypadku używania Azure PowerShell wykonaj następujące [kroki](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) , aby przenieść dane do warstwy archiwum.
    - W przypadku korzystania z zarządzania cyklem życia platformy Azure wykonaj następujące kroki, aby przenieść dane do warstwy archiwum.
        - [Zarejestruj](../storage/blobs/storage-lifecycle-management-concepts.md) się, aby uzyskać podgląd usługi zarządzania cyklem życia obiektów BLOB do korzystania z warstwy archiwum.
        - Użyj następujących zasad, aby [zarchiwizować dane dotyczące](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest)pozyskiwania.
- Gdy obiekty blob są oznaczone jako archiwalne, nie mogą być już modyfikowane przez bramę, chyba że są przenoszone do warstwy gorąca lub zimna. Jeśli plik znajduje się w magazynie lokalnym, wszelkie zmiany wprowadzone w lokalnej kopii (w tym usunięcia) nie są przekazywane do warstwy archiwum.
- Aby odczytywać dane w magazynie archiwalnym, należy je zmienić, zmieniając warstwę obiektów BLOB na gorącą lub chłodną. [Odświeżanie udziału](data-box-gateway-manage-shares.md#refresh-shares) w bramie nie powoduje odświeżenia obiektu BLOB.

Aby uzyskać więcej informacji, Dowiedz się więcej na temat [zarządzania cyklem życia usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Początkowy transfer zbiorczy, po którym następuje transfer przyrostowy

Używaj urządzenie Data Box i Data Box Gateway razem, gdy chcesz wykonać zbiorcze przekazywanie dużej ilości danych, a następnie Przyrostowe transfery. Użyj urządzenie Data Box do transferu zbiorczego w trybie offline (początkowy inicjator) i Data Box Gateway dla transferów przyrostowych (ciągłe źródło danych) za pośrednictwem sieci.

### <a name="seed-the-data-with-data-box"></a>Wypełnianie danych za pomocą urządzenie Data Box

Wykonaj następujące kroki, aby skopiować dane do urządzenie Data Box i przekazać je do usługi Azure Storage.

1. [Zamów urządzenie Data Box](../databox/data-box-deploy-ordered.md).
2. [Skonfiguruj urządzenie Data Box](../databox/data-box-deploy-set-up.md).
3. [Skopiuj dane do urządzenie Data Box za pośrednictwem protokołu SMB](../databox/data-box-deploy-copy-data.md).
4. [Zwróć urządzenie Data Box, zweryfikuj przekazywanie danych na platformę Azure](../databox/data-box-deploy-picked-up.md).
5. Po zakończeniu przekazywania danych do platformy Azure wszystkie dane powinny znajdować się w kontenerach usługi Azure Storage. Na koncie magazynu dla urządzenie Data Box przejdź do kontenera obiektów BLOB (i plików), aby upewnić się, że wszystkie dane zostały skopiowane. Zanotuj nazwę kontenera, ponieważ będziesz używać tej nazwy później. Na przykład na poniższym zrzucie ekranu `databox` kontener będzie używany do transferu przyrostowego.

    ![Kontener z danymi na urządzenie Data Box](media/data-box-gateway-use-cases/data-container.png)

Ten transfer zbiorczy kończy początkową fazę wypełniania.

### <a name="ongoing-feed-with-data-box-gateway"></a>Ciągłe źródło danych za pomocą Data Box Gateway

Wykonaj następujące kroki, aby uzyskać bieżące pozyskiwanie przez Data Box Gateway. 

1. Utwórz udział w chmurze na Data Box Gateway. Ten udział automatycznie przekazuje wszystkie dane do konta usługi Azure Storage. Przejdź do pozycji **udziały** w zasobie Data Box Gateway i kliknij pozycję **+ Dodaj udział**.

    ![Kliknij pozycję + Dodaj udział](media/data-box-gateway-use-cases/add-share.png)

2. Upewnij się, że ten udział jest mapowany do kontenera zawierającego dane z wypełnieniem. W obszarze **Wybieranie kontenera obiektów BLOB** wybierz pozycję **Użyj istniejącego** i przejdź do kontenera, w którym dane z urządzenie Data Box zostały przeniesione.

    ![Ustawienia udostępniania](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Po utworzeniu udziału Odśwież udział. Ta operacja odświeża udział lokalny przy użyciu zawartości z platformy Azure.

    ![Odśwież udział](media/data-box-gateway-use-cases/refresh-share.png)

    Po zsynchronizowaniu udziału Data Box Gateway przeniesie przyrostowe zmiany, jeśli pliki zostały zmodyfikowane na komputerze klienckim.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Gateway](data-box-gateway-limits.md).
- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.
---
title: Użyj Eksplorator usługi Azure Storage z Azure Data Lake Storage Gen2
description: Użyj Eksplorator usługi Azure Storage do zarządzania katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652279"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Użyj Eksplorator usługi Azure Storage do zarządzania katalogami i plikami w programie Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do tworzenia katalogów i plików oraz zarządzania nimi na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.

- Eksplorator usługi Azure Storage zainstalowany na komputerze lokalnym. Aby zainstalować Eksplorator usługi Azure Storage dla systemu Windows, Macintosh lub Linux, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Eksplorator usługi Storage korzysta z [punktów końcowych](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) & Data Lake Storage Gen2 (DFS) w czasie pracy z Azure Data Lake Storage Gen2. Jeśli dostęp do Azure Data Lake Storage Gen2 jest skonfigurowany za pomocą prywatnych punktów końcowych, upewnij się, że dla konta magazynu utworzono dwa prywatne punkty końcowe: jeden z docelowym podzasobem, `blob` a drugi z docelowym zasobem podrzędnym `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Zaloguj się do Eksplorator usługi Storage

Przy pierwszym uruchomieniu Eksploratora usługi Storage jest wyświetlane okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Chociaż Eksplorator usługi Storage oferuje kilka sposobów łączenia się z kontem magazynu, tylko jeden ze sposobów jest obecnie obsługiwany na potrzeby zarządzania listami kontroli dostępu.

|Zadanie|Przeznaczenie|
|---|---|
|Dodawanie konta platformy Azure | Przekierowuje użytkownika do strony logowania w organizacji w celu uwierzytelnienia na platformie Azure. Obecnie jest to jedyna obsługiwana metoda uwierzytelniania, jeśli chcesz ustawić listy kontroli dostępu i zarządzać nimi.|
|Używanie parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego | Umożliwia bezpośredni dostęp do kontenera lub konta magazynu za pomocą tokenu sygnatury dostępu współdzielonego lub udostępnionych parametrów połączenia. |
|Używanie nazwy i klucza konta magazynu| Użyj nazwy i klucza konta magazynu do nawiązania połączenia z magazynem Azure Storage.|

Wybierz pozycję **Dodaj konto platformy Azure** , a następnie kliknij pozycję **Zaloguj się.**.. Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się do konta platformy Azure.

![Zrzut ekranu, który pokazuje Eksplorator usługi Microsoft Azure Storage i podświetla opcję Dodaj konto platformy Azure i przycisk Zaloguj.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po nawiązaniu połączenia Eksplorator usługi Azure Storage zostanie załadowany z wyświetloną kartą **Eksplorator**. Ten widok zawiera szczegółowe informacje na temat wszystkich kont usługi Azure Storage, a także lokalnych magazynów skonfigurowanych za pomocą [emulatora magazynu azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), kont [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub środowisk [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

![Okno Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener zawiera katalogi i pliki. Aby go utworzyć, rozwiń konto magazynu utworzone w kroku dalszej pracy. Wybierz pozycję **kontenery obiektów BLOB**, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz kontener obiektów BLOB**. Wprowadź nazwę kontenera. Zapoznaj się z sekcją [Tworzenie kontenera](storage-quickstart-blobs-dotnet.md#create-a-container) , aby zapoznać się z listą reguł i ograniczeń dotyczących nazewnictwa kontenerów. Po zakończeniu naciśnij klawisz **Enter** , aby utworzyć kontener. Po pomyślnym utworzeniu kontenera zostanie on wyświetlony w folderze **kontenery obiektów BLOB** dla wybranego konta magazynu.

![Eksplorator usługi Microsoft Azure Storage — Tworzenie kontenera](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Tworzenie katalogu

Aby utworzyć katalog, wybierz kontener, który został utworzony w kroku kontynuował. Na Wstążce kontenera wybierz przycisk **Nowy folder** . Wprowadź nazwę katalogu. Po zakończeniu naciśnij klawisz **Enter** , aby utworzyć katalog. Po pomyślnym utworzeniu katalogu pojawia się on w oknie edytora.

![Eksplorator usługi Microsoft Azure Storage — Tworzenie katalogu](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Przekazywanie obiektów blob do katalogu

Na Wstążce katalogu wybierz przycisk **Przekaż** . Ta operacja udostępnia opcję przekazania folderu lub pliku.

Wybierz pliki lub folder do przekazania.

![Eksplorator usługi Microsoft Azure Storage — przekazywanie obiektu blob](media/data-lake-storage-explorer/upload-file.png)

Po wybraniu przycisku **OK** zaznaczone pliki zostaną umieszczone w kolejce do przekazania, a następnie przekazane. Po zakończeniu przekazywania wyniki zostaną wyświetlone w oknie **Działania**.

## <a name="view-blobs-in-a-directory"></a>Wyświetlanie obiektów blob w katalogu

W aplikacji **Eksplorator usługi Azure Storage** wybierz katalog w ramach konta magazynu. W okienku głównym wyświetlana jest lista obiektów blob w wybranym katalogu.

![Eksplorator usługi Microsoft Azure Storage — wyświetlanie listy obiektów blob w katalogu](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

Aby pobrać pliki przy użyciu **Eksplorator usługi Azure Storage**, po wybraniu pliku wybierz pozycję **Pobierz** na Wstążce. Zostanie wyświetlone okno dialogowe, w którym możliwe jest wprowadzenie nazwy pliku. Wybierz pozycję **Zapisz** , aby rozpocząć pobieranie pliku do lokalizacji lokalnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać uprawnieniami plików i katalogów przez ustawianie list kontroli dostępu (ACL)

> [!div class="nextstepaction"]
> [Użyj Eksplorator usługi Azure Storage do zarządzania listami ACL w programie Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)

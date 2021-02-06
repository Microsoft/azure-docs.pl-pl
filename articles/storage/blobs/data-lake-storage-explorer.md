---
title: Użyj Eksplorator usługi Azure Storage z Azure Data Lake Storage Gen2
description: Użyj Eksplorator usługi Azure Storage do zarządzania katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5ccef241a37e63467b681d5fd12c65072cb92e58
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626479"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami, plikami i listami ACL w usłudze Azure Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage

W tym artykule pokazano, jak używać [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do tworzenia katalogów, plików i list kontroli dostępu (ACL) oraz zarządzania nimi na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.

- Eksplorator usługi Azure Storage zainstalowany na komputerze lokalnym. Aby zainstalować Eksplorator usługi Azure Storage dla systemu Windows, Macintosh lub Linux, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

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

<a id="managing-access"></a>

## <a name="manage-acls"></a>Zarządzanie listami ACL

Kliknij prawym przyciskiem myszy kontener, katalog lub plik, a następnie kliknij polecenie **Zarządzaj listami Access Control**.  Poniższy zrzut ekranu przedstawia menu, które pojawia się po kliknięciu prawym przyciskiem myszy katalogu.

> [!div class="mx-imgBorder"]
> ![Kliknij prawym przyciskiem myszy katalog w Eksplorator usługi Azure Storage](./media/data-lake-storage-explorer/manage-access-control-list-option.png)

Okno dialogowe **Zarządzanie dostępem** umożliwia zarządzanie uprawnieniami właściciela i grupy Właściciele. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

> [!div class="mx-imgBorder"]
> ![Okno dialogowe Zarządzanie dostępem](./media/data-lake-storage-explorer/manage-access-dialog-box.png)

Aby dodać nowego użytkownika lub grupę do listy kontroli dostępu, wybierz przycisk **Dodaj** . Następnie wprowadź odpowiedni wpis Azure Active Directory (AAD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.  Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:**, co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepsze rozwiązanie i zalecenie jest takie, aby utworzyć grupę zabezpieczeń w usłudze AAD i obsługiwać uprawnienia dla grupy, a nie dla poszczególnych użytkowników. Aby uzyskać szczegółowe informacje na temat tego zalecenia, a także inne najlepsze rozwiązania, zobacz [model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

Użyj kontrolek pola wyboru, aby ustawić dostęp i domyślne listy ACL. Aby dowiedzieć się więcej na temat różnic między tymi typami list kontroli dostępu, zobacz [typy list ACL](data-lake-storage-access-control.md#types-of-acls).

<a id="apply-acls-recursively"></a>

## <a name="apply-acls-recursively"></a>Stosuj rekursywnie listy ACL

Wpisy listy ACL można zastosować cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego.

Aby zastosować elementy listy ACL cyklicznie, kliknij prawym przyciskiem myszy kontener lub katalog, a następnie kliknij pozycję **propaguj Access Control List**.  Poniższy zrzut ekranu przedstawia menu, które pojawia się po kliknięciu prawym przyciskiem myszy katalogu.

> [!div class="mx-imgBorder"]
> ![Kliknij prawym przyciskiem myszy katalog i wybierz ustawienie Propaguj kontrolę dostępu](./media/data-lake-storage-explorer/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat list kontroli dostępu w Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)

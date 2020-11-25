---
title: Użyj Eksplorator usługi Azure Storage z Azure Data Lake Storage Gen2
description: Użyj Eksplorator usługi Azure Storage do zarządzania katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e46bb87788de27916860720284087643db7153dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913406"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami, plikami i listami ACL w usłudze Azure Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage

W tym artykule przedstawiono sposób użycia [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz zarządzania nimi.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.
> * Eksplorator usługi Azure Storage zainstalowany na komputerze lokalnym. Aby zainstalować Eksplorator usługi Azure Storage dla systemu Windows, Macintosh lub Linux, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

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

Kontener zawiera katalogi i pliki. Aby go utworzyć, rozwiń konto magazynu utworzone w kroku dalszej pracy. Wybierz pozycję **Kontenery obiektów blob**, rozwiń ją, a następnie wybierz pozycję **Utwórz kontener obiektów blob**. Wprowadź nazwę kontenera. Zapoznaj się z sekcją [Tworzenie kontenera](storage-quickstart-blobs-dotnet.md#create-a-container) , aby zapoznać się z listą reguł i ograniczeń dotyczących nazewnictwa kontenerów. Po zakończeniu naciśnij klawisz **Enter** , aby utworzyć kontener. Po pomyślnym utworzeniu kontenera zostanie on wyświetlony w folderze **kontenery obiektów BLOB** dla wybranego konta magazynu.

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

## <a name="managing-access"></a>Zarządzanie dostępem

Możesz ustawić uprawnienia w katalogu głównym kontenera. Aby to zrobić, należy zalogować się do Eksplorator usługi Azure Storage przy użyciu indywidualnego konta z uprawnieniami do tego celu (w przeciwieństwie do parametrów połączenia). Kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Zarządzaj uprawnieniami**, a następnie okno dialogowe **Zarządzanie uprawnieniami** .

![Eksplorator usługi Microsoft Azure Storage — zarządzanie dostępem do katalogów](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Okno dialogowe **Zarządzanie uprawnieniami** służy do zarządzania uprawnieniami właściciela i grupy właściciela. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

Aby dodać do listy kontroli dostępu nowego użytkownika lub nową grupę, wybierz pole **Dodaj użytkownika lub grupę**.

Wprowadź odpowiedni wpis usługi Azure Active Directory (AAD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.

Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:**, co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepsze rozwiązanie i zalecenie jest takie, aby utworzyć grupę zabezpieczeń w usłudze AAD i obsługiwać uprawnienia dla grupy, a nie dla poszczególnych użytkowników. Szczegółowe informacje na temat tego zalecenia oraz inne najlepsze rozwiązania można znaleźć w [najlepszych rozwiązaniach dotyczących usługi Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Istnieją dwie kategorie uprawnień, które można przypisać: listy ACL dostępu i domyślne listy ACL.

* **Dostęp**: listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

* **Domyślnie**: szablon list ACL skojarzonych z katalogiem, który określa listy ACL dostępu dla wszelkich elementów podrzędnych, które są tworzone w tym katalogu. Pliki nie mają domyślnych list ACL.

W ramach obu tych kategorii istnieją trzy uprawnienia, które można następnie przypisać do plików lub katalogów: **Odczyt**, **zapis** i **wykonywanie**.

>[!NOTE]
> Dokonanie wyboru w tym miejscu nie spowoduje ustawienie uprawnień dla wszystkich elementów znajdujących się obecnie w katalogu. Jeśli dany plik już istnieje, należy przejść do poszczególnych elementów i ustawić uprawnienia ręcznie.

Można zarządzać uprawnieniami dla poszczególnych katalogów, a także dla pojedynczych plików, co umożliwia szczegółową kontrolę dostępu. Proces zarządzania uprawnieniami dla plików i katalogów jest taki sam, jak opisano powyżej. Kliknij prawym przyciskiem myszy plik lub katalog, dla którego chcesz zarządzać uprawnieniami, i wykonaj ten sam proces.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat list kontroli dostępu w Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
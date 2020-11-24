---
title: Szybki Start — Tworzenie obiektu BLOB za pomocą Eksplorator usługi Azure Storage
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą Eksplorator usługi Azure Storage utworzyć kontener i obiekt BLOB, pobrać obiekt BLOB na komputer lokalny i wyświetlić wszystkie obiekty blob w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 2477107105b6dbcab96db8d44ac982554dc0c48d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543174"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Szybki Start: Tworzenie obiektu BLOB za pomocą Eksplorator usługi Azure Storage

W tym przewodniku szybki start dowiesz się, jak za pomocą [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) utworzyć kontener i obiekt BLOB. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać wszystkie obiekty blob w kontenerze. Dowiesz się również, jak utworzyć migawkę obiektu blob, zarządzać zasadami dostępu do kontenera i utworzyć sygnaturę dostępu współdzielonego.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

W przypadku tego przewodnika Szybki start wymagane jest zainstalowanie Eksploratora usługi Azure Storage. Aby zainstalować Eksplorator usługi Azure Storage dla systemu Windows, Macintosh lub Linux, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Logowanie się w Eksploratorze usługi Storage

Podczas pierwszego uruchomienia wyświetlane jest okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. W poniższej tabeli przedstawiono różne sposoby nawiązywania połączenia:

|Zadanie|Przeznaczenie|
|---|---|
|Dodawanie konta platformy Azure | Przekierowuje użytkownika do strony logowania w organizacji w celu uwierzytelnienia na platformie Azure. |
|Używanie parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego | Umożliwia bezpośredni dostęp do kontenera lub konta magazynu za pomocą tokenu sygnatury dostępu współdzielonego lub udostępnionych parametrów połączenia. |
|Używanie nazwy i klucza konta magazynu| Użyj nazwy i klucza konta magazynu do nawiązania połączenia z magazynem Azure Storage.|

Wybierz pozycję **Dodaj konto platformy Azure** , a następnie kliknij pozycję **Zaloguj się.**.. Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się do konta platformy Azure.

![Zrzut ekranu przedstawiający okno Eksplorator usługi Microsoft Azure Storage-Connect.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po nawiązaniu połączenia Eksplorator usługi Azure Storage zostanie załadowany z wyświetloną kartą **Eksplorator**. Ten widok zawiera szczegółowe informacje na temat wszystkich kont usługi Azure Storage, a także lokalnych magazynów skonfigurowanych za pomocą [emulatora magazynu azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), kont [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub środowisk [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

![Okno Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Umożliwia to organizowanie grup obiektów blob w sposób podobny do organizowania plików w folderach na komputerze.

Aby utworzyć kontener, rozwiń konto magazynu utworzone w poprzednim kroku. Wybierz pozycję **Kontenery obiektów blob**, rozwiń ją, a następnie wybierz pozycję **Utwórz kontener obiektów blob**. Wprowadź nazwę kontenera obiektów blob. Zapoznaj się z sekcją [Tworzenie kontenera](storage-quickstart-blobs-dotnet.md#create-a-container) , aby zapoznać się z listą reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów BLOB. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob. Po pomyślnym utworzeniu kontener obiektów blob zostanie wyświetlony w folderze **Kontenery obiektów blob** dla wybranego konta magazynu.

## <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Pliki VHD używane do tworzenia kopii maszyn wirtualnych IaaS są stronicowymi obiektami blob. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość plików przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

Na wstążce kontenera wybierz pozycję **Przekaż**. Ta operacja udostępnia opcję przekazania folderu lub pliku.

Wybierz pliki lub folder do przekazania. Wybierz **typ obiektu blob**. Dopuszczalne wartości to **uzupełniany**, **stronicowy** lub **blokowy** obiekt blob.

W przypadku przekazywania pliku VHD lub VHDX wybierz pozycję **Przekazuj pliki VHD lub VHDX jako stronicowe obiekty blob (zalecane)**.

W polu **Przekaż do folderu (opcjonalnie)** wprowadź nazwę folderu do przechowywania plików lub folderów w folderze w ramach kontenera. Jeśli nie zostanie wybrany żaden folder, pliki zostaną przekazane bezpośrednio w ramach kontenera.

![Eksplorator usługi Microsoft Azure Storage — przekazywanie obiektu blob](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Po wybraniu przycisku **OK** zaznaczone pliki zostaną umieszczone w kolejce do przekazania, a następnie przekazane. Po zakończeniu przekazywania wyniki zostaną wyświetlone w oknie **Działania**.

## <a name="view-blobs-in-a-container"></a>Wyświetlanie obiektów blob w kontenerze

W **Eksploratorze usługi Azure Storage** wybierz kontener w ramach konta magazynu. W okienku głównym wyświetlana jest lista obiektów blob w wybranym kontenerze.

![Zrzut ekranu pokazujący, gdzie wybierasz kontener w Eksplorator usługi Microsoft Azure Storage.](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

Aby pobrać obiekty blob przy użyciu **Eksploratora usługi Azure Storage**, zaznacz obiekt blob, a następnie wybierz pozycję **Pobierz** ze wstążki. Zostanie wyświetlone okno dialogowe, w którym możliwe jest wprowadzenie nazwy pliku. Wybierz pozycję **Zapisz**, aby rozpocząć pobieranie obiektu blob do lokalizacji lokalnej.

## <a name="manage-snapshots"></a>Zarządzanie migawkami

Eksplorator usługi Azure Storage umożliwia tworzenie [migawek](./snapshots-overview.md) obiektów blob i zarządzanie nimi. Aby utworzyć migawkę obiektu blob, kliknij go prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz migawkę**. Aby wyświetlić migawkę obiektu blob, kliknij go prawym przyciskiem myszy, a następnie wybierz pozycję **Zarządzaj migawkami**. Lista migawek dla danego obiektu blob jest wyświetlana w bieżącej karcie.

![Zrzut ekranu przedstawiający listę obiektów BLOB w Eksplorator usługi Microsoft Azure Storage.](media/storage-quickstart-blobs-storage-explorer/snapshots.png)

## <a name="manage-access-policies"></a>Zarządzanie zasadami dostępu

Eksplorator usługi Storage umożliwia zarządzanie zasadami dostępu dla kontenerów w ramach ich interfejsu użytkownika. Istnieją dwa typy zasad bezpiecznego dostępu (sygnatury dostępu współdzielonego): na poziomie usługi i na poziomie konta. Sygnatury dostępu współdzielonego na poziomie konta dotyczą konta magazynu i mogą zostać zastosowane względem wielu usług i zasobów. Sygnatury dostępu współdzielonego na poziomie usługi są definiowane dla zasobu w ramach określonej usługi. Aby wygenerować sygnaturę dostępu współdzielonego na poziomie usługi, kliknij prawym przyciskiem myszy dowolny kontener i wybierz polecenie **Zarządzaj zasadami zabezpieczeń.**... Aby wygenerować sygnaturę dostępu współdzielonego na poziomie konta, kliknij prawym przyciskiem myszy konto magazynu.

Wybierz pozycję **Dodaj**, aby dodać nowe zasady dostępu oraz zdefiniować uprawnienia dla tych zasad. Po zakończeniu wybierz pozycję **Zapisz**, aby zapisać zasady dostępu. Te zasady są teraz dostępne do użycia podczas konfigurowania sygnatury dostępu współdzielonego.

## <a name="work-with-shared-access-signatures"></a>Praca z sygnaturami dostępu współdzielonego

Sygnatury dostępu współdzielonego można pobierać za pomocą Eksploratora usługi Storage. Kliknij prawym przyciskiem myszy konto magazynu, kontener lub obiekt BLOB, a następnie wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego...**. Wybierz czas rozpoczęcia i wygaśnięcia oraz uprawnienia dla adresu URL sygnatury dostępu współdzielonego, a następnie wybierz pozycję **Utwórz**. Zostanie udostępniony pełny adres URL z ciągiem zapytania oraz sam ciąg zapytania — możliwe jest skopiowanie tych elementów z następnego ekranu.

![Eksplorator usługi Microsoft Azure Storage — wyświetlanie listy obiektów blob w kontenerze](media/storage-quickstart-blobs-storage-explorer/sharedaccesssignature.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu **Eksploratora usługi Azure Storage**. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](./storage-quickstart-blobs-powershell.md)
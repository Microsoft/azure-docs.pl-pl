---
title: Zarządzanie zasobami usługi Azure Blob Storage przy użyciu Eksplorator usługi Storage | Microsoft Docs
description: Zarządzaj zasobami usługi Azure Blob Storage przy użyciu Eksplorator usługi Storage. Tworzenie kontenera obiektów blob, wyświetlanie zawartości kontenera obiektów blob, usuwanie lub kopiowanie kontenera obiektów blob oraz inne elementy.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95531971"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

[Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) to usługa do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, do których można uzyskiwać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub https.
Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie. W tym artykule dowiesz się, jak używać Eksplorator usługi Storage do pracy z kontenerami obiektów blob i obiektami BLOB.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule konieczne jest wykonanie kroków znajdujących się w następujących artykułach:

* [Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)
* [Nawiązywanie połączenia z kontem lub usługą Azure Storage](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Wszystkie obiekty blob muszą znajdować się w kontenerze obiektów blob, który jest po prostu logiczną grupą obiektów BLOB. Konto może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może przechowywać nieograniczoną liczbę obiektów BLOB.

Poniższe kroki ilustrują sposób tworzenia kontenera obiektów BLOB w ramach Eksplorator usługi Storage.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy **kontenery obiektów BLOB**, a następnie z menu kontekstowego wybierz pozycję **Utwórz kontener obiektów BLOB**.

   ![Menu kontekstowe — tworzenie kontenerów obiektów blob][0]
4. Zostanie wyświetlone pole tekstowe poniżej folderu **kontenerów obiektów BLOB** . Wprowadź nazwę kontenera obiektów blob. Zobacz [Tworzenie kontenera](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) , aby uzyskać informacje na temat reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów BLOB.

   ![Pole tekstowe tworzenia kontenerów obiektów BLOB][1]
5. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob, lub klawisz **Esc**, aby anulować. Po pomyślnym utworzeniu kontenera obiektów BLOB zostanie on wyświetlony w folderze **kontenery obiektów BLOB** dla wybranego konta magazynu.

   ![Utworzono kontener obiektów BLOB][2]

## <a name="view-a-blob-containers-contents"></a>Wyświetlanie zawartości kontenera obiektów BLOB

Kontenery obiektów BLOB zawierają obiekty blob i foldery (które mogą również zawierać obiekty blob).

Poniższe kroki ilustrują sposób wyświetlania zawartości kontenera obiektów BLOB w ramach Eksplorator usługi Storage:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz wyświetlić.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz wyświetlić, a następnie z menu kontekstowego wybierz pozycję **Otwórz Edytor kontenera obiektów BLOB**.
   Możesz również kliknąć dwukrotnie kontener obiektów blob, który chcesz wyświetlić.

   ![Otwórz menu kontekstowe edytora kontenera obiektów BLOB][19]
5. W okienku głównym zostanie wyświetlona zawartość kontenera obiektów BLOB.

   ![Edytor kontenera obiektów BLOB][3]

## <a name="delete-a-blob-container"></a>Usuwanie kontenera obiektów BLOB

Kontenery obiektów BLOB można łatwo tworzyć i usuwać w razie konieczności. (Aby dowiedzieć się, jak usunąć poszczególne obiekty blob, zapoznaj się z sekcją [Zarządzanie obiektami BLOB w kontenerze obiektów BLOB](#managing-blobs-in-a-blob-container)).

Poniższe kroki ilustrują sposób usuwania kontenera obiektów BLOB w ramach Eksplorator usługi Storage:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz wyświetlić.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz usunąć, a następnie z menu kontekstowego wybierz pozycję **Usuń**.
   Możesz również nacisnąć klawisz **delete** , aby usunąć aktualnie wybrany kontener obiektów BLOB.

   ![Menu kontekstowe usuwania kontenera obiektów BLOB][4]
5. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

   ![Potwierdzenie usunięcia kontenera obiektów BLOB][5]

## <a name="copy-a-blob-container"></a>Kopiowanie kontenera obiektów BLOB

Eksplorator usługi Storage umożliwia skopiowanie kontenera obiektów BLOB do schowka, a następnie wklejenie tego kontenera obiektów BLOB do innego konta magazynu. (Aby dowiedzieć się, jak skopiować pojedyncze obiekty blob, zapoznaj się z sekcją [Zarządzanie obiektami BLOB w kontenerze obiektów BLOB](#managing-blobs-in-a-blob-container)).

Poniższe kroki ilustrują sposób kopiowania kontenera obiektów blob z jednego konta magazynu do innego.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz skopiować.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz skopiować, a następnie z menu kontekstowego wybierz polecenie **Kopiuj kontener obiektów BLOB**.

   ![Menu kontekstowe kopiowania kontenera obiektów BLOB][6]
5. Kliknij prawym przyciskiem myszy żądane konto magazynu "target", do którego chcesz wkleić kontener obiektów blob, a następnie z menu kontekstowego wybierz polecenie **Wklej kontener obiektów BLOB**.

   ![Wklej menu kontekstowe kontenera obiektów BLOB][7]

## <a name="get-the-sas-for-a-blob-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów blob

[Sygnatura dostępu współdzielonego (SAS, shared access signature)](./storage/common/storage-sas-overview.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu.
Oznacza to, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów w ramach konta magazynu na określony czas i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do konta.

Poniższe kroki ilustrują sposób tworzenia sygnatury dostępu współdzielonego dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, dla którego chcesz uzyskać sygnaturę dostępu współdzielonego.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Kliknij prawym przyciskiem myszy żądany kontener obiektów blob, a następnie z menu kontekstowego wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**.

   ![Pobierz menu kontekstowe SAS][8]
5. W oknie dialogowym **Sygnatura dostępu współdzielonego** określ zasady, daty rozpoczęcia i wygaśnięcia, strefę czasową oraz poziomy dostępu dla zasobu.

   ![Pobierz opcje SAS][9]
6. Po zakończeniu określania opcji sygnatury dostępu współdzielonego wybierz pozycję **Utwórz**.
7. Zostanie wyświetlone drugie okno dialogowe **sygnatura dostępu współdzielonego** , które wyświetla listę kontenerów obiektów BLOB wraz z adresem URL i QueryString, których można użyć w celu uzyskania dostępu do zasobu magazynu.
   Wybierz pozycję **Kopiuj** obok adres URL, który chcesz skopiować do schowka.

   ![Kopiuj adresy URL SAS][10]
8. Po zakończeniu wybierz pozycję **Zamknij**.

## <a name="manage-access-policies-for-a-blob-container"></a>Zarządzanie zasadami dostępu dla kontenera obiektów BLOB

Poniższe kroki ilustrują sposób zarządzania (Dodawanie i usuwanie) zasad dostępu dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, którego zasady dostępu chcesz zarządzać.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Wybierz żądany kontener obiektów blob, a następnie z menu kontekstowego wybierz pozycję **Zarządzaj zasadami dostępu**.

   ![Menu kontekstowe Zarządzanie zasadami dostępu][11]
5. W oknie dialogowym **zasady dostępu** zostaną wyświetlone wszystkie zasady dostępu, które zostały już utworzone dla wybranego kontenera obiektów BLOB.

   ![Opcje zasad dostępu][12]
6. Wykonaj następujące kroki w zależności od zadania zarządzania zasadami dostępu:

   * **Dodawanie nowych zasad dostępu** — wybierz pozycję **Dodaj**. Po wygenerowaniu w oknie dialogowym **Zasady dostępu** będą wyświetlane nowo dodane zasady dostępu (przy użyciu ustawień domyślnych).
   * **Edytuj zasady dostępu** — wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.
   * **Usuwanie zasad dostępu** — wybierz pozycję **Usuń** obok zasad dostępu, które chcesz usunąć.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ustawianie publicznego poziomu dostępu dla kontenera obiektów BLOB

Domyślnie każdy kontener obiektów BLOB jest ustawiony na wartość "Brak dostępu publicznego".

Poniższe kroki ilustrują sposób określania publicznego poziomu dostępu dla kontenera obiektów BLOB.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, którego zasady dostępu chcesz zarządzać.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Wybierz żądany kontener obiektów blob, a następnie z menu kontekstowego wybierz pozycję **Ustaw poziom dostępu publicznego**.

   ![Ustaw menu kontekstowe poziomu dostępu publicznego][13]
5. W oknie dialogowym **Ustawianie poziomu dostępu publicznego kontenera** Określ żądany poziom dostępu.

   ![Ustawianie opcji poziomu dostępu publicznego][14]
6. Wybierz przycisk **Zastosuj**.

## <a name="managing-blobs-in-a-blob-container"></a>Zarządzanie obiektami BLOB w kontenerze obiektów BLOB

Po utworzeniu kontenera obiektów BLOB możesz przekazać obiekt BLOB do tego kontenera obiektów blob, pobrać obiekt BLOB na komputer lokalny, otworzyć obiekt BLOB na komputerze lokalnym i wiele innych.

Poniższe kroki ilustrują sposób zarządzania obiektami BLOB (i folderami) w kontenerze obiektów BLOB.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontener obiektów blob, którym chcesz zarządzać.
3. Rozwiń **kontenery obiektów BLOB** konta magazynu.
4. Kliknij dwukrotnie kontener obiektów blob, który chcesz wyświetlić.
5. W okienku głównym zostanie wyświetlona zawartość kontenera obiektów BLOB.

   ![Wyświetlanie kontenera obiektów BLOB][3]
6. W okienku głównym zostanie wyświetlona zawartość kontenera obiektów BLOB.
7. Wykonaj następujące kroki w zależności od zadania, które chcesz wykonać:

   * **Przekazywanie plików do kontenera obiektów BLOB**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż pliki**.

        ![Menu Przekaż pliki][15]
     2. W oknie dialogowym **Przekazywanie plików** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Pliki**, aby wybrać pliki do przekazania.

        ![Opcje przekazywania plików][16]
     3. Określ typ **typu obiektu BLOB**. Aby uzyskać więcej informacji [, zobacz Tworzenie kontenera](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Opcjonalnie określ folder docelowy, do którego zostaną przekazane wybrane pliki. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Przekazywanie folderu do kontenera obiektów BLOB**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie **Przekaż folder** z menu rozwijanego.

        ![Menu Przekaż folder][17]
     2. W oknie dialogowym **Przekazywanie folderu** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Folder**, aby wybrać folder, którego zawartość chcesz przekazać.

        ![Opcje przekazywania folderów][18]
     3. Określ typ **typu obiektu BLOB**. Aby uzyskać więcej informacji [, zobacz Tworzenie kontenera](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Opcjonalnie określ folder docelowy, do którego zawartość wybranego folderu zostanie przekazana. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Pobieranie obiektu BLOB do komputera lokalnego**

     1. Wybierz obiekt BLOB, który chcesz pobrać.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Pobierz**.
     3. W oknie dialogowym **określ miejsce zapisania pobranego obiektu BLOB** Określ lokalizację, w której ma zostać pobrany obiekt BLOB, oraz nazwę, którą chcesz nadać.  
     4. Wybierz pozycję **Zapisz**.
   * **Otwieranie obiektu BLOB na komputerze lokalnym**

     1. Wybierz obiekt BLOB, który chcesz otworzyć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Otwórz**.
     3. Obiekt BLOB zostanie pobrany i otwarty przy użyciu aplikacji skojarzonej z typem pliku źródłowego obiektu BLOB.
   * **Kopiowanie obiektu BLOB do schowka**

     1. Wybierz obiekt BLOB, który chcesz skopiować.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Kopiuj**.
     3. W lewym okienku przejdź do innego kontenera obiektów blob, a następnie kliknij go dwukrotnie, aby wyświetlić go w okienku głównym.
     4. Na pasku narzędzi okienka głównego wybierz pozycję **Wklej** , aby utworzyć kopię obiektu BLOB.
   * **Usuwanie obiektu blob**

     1. Wybierz obiekt BLOB, który chcesz usunąć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Usuń**.
     3. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

## <a name="next-steps"></a>Następne kroki

* Zobacz [najnowsze informacje o wersji i wideo dotyczące Eksploratora usługi Storage](https://www.storageexplorer.com).
* Dowiedz się, jak [tworzyć aplikacje przy użyciu obiektów Blob, tabel, kolejek i plików platformy Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
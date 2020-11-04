---
title: Przenoszenie danych magazynu obiektów BLOB za pomocą procesu nauki o danych Eksplorator usługi Azure Storage w zespole
description: Dowiedz się, jak za pomocą Eksplorator usługi Azure Storage przekazywać i pobierać dane z usługi Azure Blob Storage.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5750132280d69c946ce180c51e4de6507549fcb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318184"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Przenoszenie danych do i z usługi Azure Blob Storage przy użyciu Eksplorator usługi Azure Storage
Eksplorator usługi Azure Storage to bezpłatne narzędzie firmy Microsoft, które umożliwia współdziałanie z danymi usługi Azure Storage w systemach Windows, macOS i Linux. W tym temacie opisano, jak używać go do przekazywania i pobierania danych z usługi Azure Blob Storage. Narzędzie można pobrać z [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> W przypadku korzystania z maszyny wirtualnej, która została skonfigurowana przy użyciu skryptów dostarczonych przez [maszyny wirtualne do analizy danych na platformie Azure](../data-science-virtual-machine/overview.md), Eksplorator usługi Azure Storage jest już zainstalowana na maszynie wirtualnej.
> 
> [!NOTE]
> Aby uzyskać pełne wprowadzenie do usługi Azure Blob Storage, zobacz [podstawowe informacje](../../storage/blobs/storage-quickstart-blobs-dotnet.md) o usłudze Azure BLOB i [usłudze Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konto magazynu i odpowiedni klucz magazynu dla tego konta. Przed przekazaniem/pobraniem danych należy znać nazwę konta i klucz konta usługi Azure Storage. 

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia konta magazynu i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md). Zanotuj klucz dostępu do konta magazynu, który będzie potrzebny do łączenia się z kontem przy użyciu narzędzia Eksplorator usługi Azure Storage.
* Narzędzie Eksplorator usługi Azure Storage można pobrać z [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Zaakceptuj wartości domyślne podczas instalacji.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Korzystanie z Eksploratora usługi Azure Storage
Poniższe kroki przedstawiają sposób przekazywania/pobierania danych przy użyciu Eksplorator usługi Azure Storage. 

1. Uruchom Eksplorator usługi Microsoft Azure Storage.
2. Aby wyświetlić kreatora **logowania do konta...** , wybierz ikonę **Ustawienia konta platformy Azure** , a następnie **Dodaj konto** i wprowadź poświadczenia. 
![Dodawanie konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Aby wyświetlić Kreatora łączenia z usługą **Azure Storage** , wybierz ikonę **Połącz z usługą Azure Storage** . ![Kliknij pozycję "Połącz z usługą Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Wprowadź klucz dostępu z konta usługi Azure Storage w kreatorze **łączenia z usługą Azure Storage** , a następnie kliknij przycisk **dalej**. ![Wprowadź klucz dostępu z konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Wprowadź nazwę konta magazynu w polu **nazwa konta** , a następnie wybierz przycisk **dalej**. ![Dołącz magazyn zewnętrzny](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Dodane konto magazynu powinno być teraz wyświetlane. Aby utworzyć kontener obiektów BLOB na koncie magazynu, kliknij prawym przyciskiem myszy węzeł **kontenery obiektów BLOB** w ramach tego konta, wybierz pozycję **Utwórz kontener obiektów BLOB** , a następnie wprowadź nazwę.
7. Aby przekazać dane do kontenera, wybierz kontener docelowy, a następnie kliknij przycisk **Przekaż** .
![Konta magazynu](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kliknij przycisk **...** z prawej strony pola **pliki** , wybierz co najmniej jeden plik do przekazania z systemu plików, a następnie kliknij przycisk **Przekaż** , aby rozpocząć przekazywanie plików. ![ Przekaż pliki](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Aby pobrać dane, wybierz obiekt BLOB w odpowiednim kontenerze do pobrania, a następnie kliknij pozycję **Pobierz**. ![Pobieranie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
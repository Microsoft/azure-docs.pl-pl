---
title: 'Eksplorator usługi Storage: Ustaw listy ACL w Azure Data Lake Storage Gen2'
description: Użyj Eksplorator usługi Azure Storage do zarządzania listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654341"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Użyj Eksplorator usługi Azure Storage do zarządzania listami ACL w programie Azure Data Lake Storage Gen2

W tym artykule pokazano, jak za pomocą [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zarządzać listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).

Za pomocą Eksplorator usługi Storage można wyświetlać i aktualizować listy kontroli dostępu katalogów i plików. Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Jednak można również zastosować ustawienia listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. 

W tym artykule opisano sposób modyfikowania listy ACL pliku lub katalogu oraz sposób cyklicznego stosowania ustawień listy ACL do katalogów podrzędnych.

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

## <a name="manage-an-acl"></a>Zarządzanie listą ACL

Kliknij prawym przyciskiem myszy kontener, katalog lub plik, a następnie kliknij polecenie **Zarządzaj listami Access Control**.  Poniższy zrzut ekranu przedstawia menu, które pojawia się po kliknięciu prawym przyciskiem myszy katalogu.

> [!div class="mx-imgBorder"]
> ![Kliknij prawym przyciskiem myszy katalog w Eksplorator usługi Azure Storage](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

Okno dialogowe **Zarządzanie dostępem** umożliwia zarządzanie uprawnieniami właściciela i grupy Właściciele. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

> [!div class="mx-imgBorder"]
> ![Okno dialogowe Zarządzanie dostępem](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Aby dodać nowego użytkownika lub grupę do listy kontroli dostępu, wybierz przycisk **Dodaj** . Następnie wprowadź odpowiedni wpis Azure Active Directory (Azure AD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.  Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:**, co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepszym rozwiązaniem jest, aby utworzyć grupę zabezpieczeń w usłudze Azure AD i zachować uprawnienia do grupy, a nie poszczególnych użytkowników. Aby uzyskać szczegółowe informacje na temat tego zalecenia, a także inne najlepsze rozwiązania, zobacz [model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Użyj kontrolek pola wyboru, aby ustawić dostęp i domyślne listy ACL. Aby dowiedzieć się więcej na temat różnic między tymi typami list kontroli dostępu, zobacz [typy list ACL](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Stosuj rekursywnie listy ACL

Wpisy listy ACL można zastosować cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego.

Aby zastosować elementy listy ACL cyklicznie, kliknij prawym przyciskiem myszy kontener lub katalog, a następnie kliknij pozycję **propaguj Access Control List**.  Poniższy zrzut ekranu przedstawia menu, które pojawia się po kliknięciu prawym przyciskiem myszy katalogu.

> [!div class="mx-imgBorder"]
> ![Kliknij prawym przyciskiem myszy katalog i wybierz ustawienie Propaguj kontrolę dostępu](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o modelu uprawnień Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Model kontroli dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)

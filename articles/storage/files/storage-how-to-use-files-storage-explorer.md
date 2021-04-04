---
title: Zarządzanie udziałami plików platformy Azure przy użyciu Eksplorator usługi Azure Storage
description: W tym przewodniku Szybki start dowiesz się, jak zarządzać usługą Azure Files za pomocą Eksploratora usługi Azure Storage.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629788"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Szybki start: tworzenie udziałów plików platformy Azure i zarządzanie nimi za pomocą Eksploratora usługi Azure Storage
W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z [udziałami plików platformy Azure](storage-files-introduction.md) przy użyciu Eksploratora usługi Azure Storage. Udziały plików platformy Azure są podobne do innych udziałów plików, ale są przechowywane w chmurze i obsługiwane przez platformę Azure. Udziały plików platformy Azure obsługują standardowy w branży protokół SMB i umożliwiają udostępnianie plików między wieloma maszynami, aplikacjami i wystąpieniami. 

Eksplorator usługi Azure Storage to popularne narzędzie klienckie dostępne dla systemów Windows, macOS i Linux. Eksplorator usługi Storage służy do zarządzania udziałami plików platformy Azure i innymi zasobami magazynu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start wymaga zainstalowanego Eksploratora usługi Azure Storage. Aby pobrać i zainstalować go, przejdź na stronę [Eksploratora usługi Storage Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Nie możesz używać Eksploratora usługi Storage do tworzenia nowych zasobów. Na potrzeby tego pokazu utwórz konto magazynu w [witrynie Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Łączenie Eksploratora usługi Storage z zasobami platformy Azure
Przy pierwszym uruchomieniu Eksploratora usługi Storage jest wyświetlane okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu: 

- **Logowanie za pomocą konta platformy Azure**: możesz zalogować się przy użyciu poświadczeń użytkownika organizacji lub konta Microsoft. 
- **Łączenie z określonym kontem magazynu za pomocą parametrów połączenia lub tokenu sygnatury dostępu współdzielonego**: parametry połączenia to specjalny ciąg zawierający nazwę konta magazynu oraz klucz konta magazynu lub token sygnatury dostępu współdzielonego. Dzięki tokenowi Eksplorator usługi Storage uzyskuje dostęp do konta magazynu bezpośrednio (zamiast wyświetlania wszystkich kont magazynu na koncie platformy Azure). Aby dowiedzieć się więcej na temat parametrów połączenia, zobacz [Konfiguracja parametrów połączenia usługi Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Nawiązywanie połączenia z konkretnym kontem magazynu za pomocą nazwy i klucza konta magazynu**: użyj nazwy i klucza konta magazynu do nawiązania połączenia z usługą Azure Storage.

Na potrzeby tego przewodnika Szybki start zaloguj się przy użyciu konta platformy Azure. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie wybierz pozycję **Zaloguj się**. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

![Zrzut ekranu okna Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Aby utworzyć Twój pierwszy udział plików platformy Azure w ramach konta magazynu `storageacct<random number>`, wykonaj następujące czynności:

1. Rozwiń element utworzonego konta magazynu.
2. Kliknij prawym przyciskiem myszy pozycję **Udziały plików**, a następnie wybierz polecenie **Utwórz udział plików**.  
    ![Zrzut ekranu przedstawiający folder udziałów plików i menu kontekstowe w kontekście](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Dla udziału plików podaj wartość *myshare*, a następnie naciśnij klawisz Enter.

Nazwy udziałów mogą zawierać tylko małe litery, cyfry i pojedyncze łączniki (ale nie mogą zaczynać się łącznikiem). Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Po utworzeniu udziału plików w okienku po prawej zostanie otwarta karta dla udziału plików. 

## <a name="use-your-azure-file-share"></a>Korzystanie z udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Z udziałem plików platformy Azure możesz też pracować przy użyciu Eksploratora usługi Azure Storage. Zaletą korzystania z Eksploratora usługi Azure Storage w porównaniu z instalowaniem udziału plików przy użyciu protokołu SMB jest to, że wszystkie żądania wprowadzone w ramach Eksploratora usługi Azure Storage są wykonywane za pomocą interfejsu API REST usługi Files. Interfejs API REST usługi Files służy do tworzenia, modyfikowania i usuwania plików i katalogów na klientach bez dostępu przez protokół SMB.

### <a name="create-a-directory"></a>Tworzenie katalogu
Dodanie katalogu zapewnia strukturę hierarchiczną na potrzeby zarządzania udziałem plików. W katalogu możesz utworzyć wiele poziomów. Jednak przed utworzeniem podkatalogów musisz upewnić się, że katalogi nadrzędne istnieją. Na przykład w przypadku ścieżki myDirectory/mySubDirectory należy najpierw utworzyć katalog *myDirectory*. Dopiero wtedy można utworzyć katalog *mySubDirectory*. 

1. Na karcie udziału plików w górnym menu wybierz przycisk **Nowy folder**. Zostanie otwarte okienko **Tworzenie nowego katalogu**.
    ![Zrzut ekranu przedstawiający przycisk Nowy folder w kontekście](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Podaj nazwę katalogu — wpisz ciąg *myDirectory*, a następnie wybierz pozycję **OK**. 

Katalog *myDirectory* zostanie wyświetlony na karcie udziału plików *myshare*.

### <a name="upload-a-file"></a>Przekazywanie pliku 
Istnieje możliwość przekazania pliku z maszyny lokalnej do nowego katalogu w udziale plików. Przekazywanie może obejmować cały folder lub pojedynczy plik.

1. Wybierz z górnego menu polecenie **Przekaż**. Ta operacja udostępnia opcję przekazania folderu lub pliku.
2. Wybierz pozycję **Przekaż plik**, a następnie wybierz plik do przekazania z maszyny lokalnej.
3. W polu **Przekaż do katalogu** wpisz ciąg *myDirectory*, a następnie wybierz pozycję **Przekaż**. 

Gdy skończysz, plik zostanie wyświetlony na liście w okienku *myDirectory*.

### <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać kopię pliku z udziału plików, kliknij prawym przyciskiem myszy plik, a następnie wybierz polecenie **Download** (Pobierz). Wybierz, gdzie chcesz umieścić plik na maszynie lokalnej, a następnie wybierz polecenie **Zapisz**.

Postęp pobierania będzie wyświetlany w okienku **Działania** na dole okna.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Nie możesz użyć Eksploratora usługi Storage do usunięcia zasobów. Aby wyczyścić wyniki działań wykonanych w ramach tego przewodnika Szybki start, użyj [witryny Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest usługa Azure Files?](storage-files-introduction.md)
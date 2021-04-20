---
title: Przewodnik Szybki start dotyczący zarządzania udziałami plików platformy Azure za pomocą witryny Azure Portal
description: Zobacz, jak tworzyć udziały plików platformy Azure i zarządzać nimi w Azure Portal. Utwórz konto magazynu, utwórz udział plików platformy Azure i użyj udziału plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717840"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Szybki start: tworzenie udziałów plików platformy Azure i zarządzanie nimi za pomocą witryny Azure Portal 
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w systemach Windows, Linux i macOS. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu witryny [Azure Portal](https://portal.azure.com/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Aby utworzyć udział plików platformy Azure, wykonaj następujące czynności:

1. Wybierz konto magazynu na pulpicie nawigacyjnym.
1. Na stronie konta magazynu w sekcji **Usługi** wybierz pozycję **Pliki**.
    
    ![Zrzut ekranu przedstawiający sekcję magazynu danych konta magazynu; wybierz udziały plików.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. W menu w górnej części strony **Usługa plików** kliknij pozycję **Udział plików**. Zostanie rozwinięta strona **Nowy udział plików**.
1. W **nazwa** wpisz *myshare*, wprowadź kuquoate i pozostaw **transakcji zoptymalizowane** wybrane dla **warstw**.
1. Wybierz **pozycję Utwórz,** aby utworzyć udział plików platformy Azure.

Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

## <a name="use-your-azure-file-share"></a>Korzystanie z udziału plików platformy Azure
Azure Files udostępnia trzy metody pracy z plikami i folderami w ramach udziału plików platformy Azure: standardowy w branży protokół bloku komunikatów serwera [(SMB),](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)protokół sieciowego systemu plików (NFS) (wersja zapoznawcza) i protokół [REST](/rest/api/storageservices/file-service-rest-api)plików . 

Aby zainstalować udział plików za pomocą protokołu SMB, zobacz następujący dokument zgodny z używanym systemem operacyjnym:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Korzystanie z udziału plików platformy Azure w witrynie Azure Portal
Wszystkie żądania przekazywane za pośrednictwem witryny Azure Portal są przekazywane przy użyciu interfejsu API REST plików, co umożliwia tworzenie, modyfikowanie oraz usuwanie plików i katalogów na klientach bez dostępu do protokołu SMB. Istnieje możliwość bezpośredniej pracy z protokołem REST usługi File (czyli ręcznego tworzenia wywołań HTTP REST), ale najczęstszym sposobem używania protokołu REST usługi File (poza używaniem interfejsu Azure Portal) jest użycie modułu [Azure PowerShell](storage-how-to-use-files-powershell.md), interfejsu wiersza polecenia platformy [Azure](storage-how-to-use-files-cli.md)lub zestawu SDK usługi Azure Storage, które zapewniają ładną otokę protokołu REST usługi File w języku skryptowym/programowania. 

Oczekujemy, że większość użytkowników usługi Azure Files będzie chciała pracować z ich udziałami plików platformy Azure za pośrednictwem protokołu SMB, ponieważ umożliwia im to korzystanie z istniejących aplikacji i narzędzi, z których będą mogli korzystać, ale istnieje kilka powodów, dla których korzystne jest korzystanie z interfejsu API REST usługi File, a nie protokołu SMB, takich jak:

- W trakcie podróży musisz szybko wprowadzić zmianę w udziale plików platformy Azure, korzystając z laptopa bez dostępu do protokołu SMB, tabletu lub urządzenia przenośnego.
- Musisz wykonać skrypt lub uruchomić aplikację z poziomu klienta, który nie może instalować udziału SMB. Mogą to być na przykład klienci lokalni bez odblokowanego portu 445.
- Korzystasz z zasobów bezserwerowych, takich jak [usługa Azure Functions](../../azure-functions/functions-overview.md). 

W poniższych przykładach przedstawiono, jak używać witryny Azure Portal do obsługi udziałów plików platformy Azure przy użyciu protokołu REST usługi Files. 

Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Pracę z udziałem plików platformy Azure umożliwia też witryna Azure Portal. 

#### <a name="create-a-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure:

1. Na stronie **Usługa plików** wybierz udział plików **myshare**. Zostanie otwarta strona udziału plików.
1. W menu w górnej części strony wybierz pozycję **+ Dodaj katalog**. Zostanie rozwinięta strona **Nowy katalog**.
1. Wpisz nazwę *myDirectory*, a następnie kliknij przycisk **OK**.

#### <a name="upload-a-file"></a>Przekazywanie pliku 
Aby można było zademonstrować przekazywanie pliku, najpierw musisz utworzyć lub wybrać plik do przekazania. Możesz to zrobić w dowolny sposób. Po wybraniu pliku do przekazania:

1. Kliknij katalog **myDirectory**. Zostanie otwarty panel **myDirectory**.
1. W menu u góry wybierz pozycję **Przekaż**. Zostanie otwarty panel **Przekazywanie plików**.  
    
    ![Zrzut ekranu panelu przekazywania plików](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Kliknij ikonę folderu, aby otworzyć okno w celu przeglądania plików lokalnych. 
1. Wybierz plik, a następnie kliknij pozycję **Otwórz**. 
1. Na stronie **Przekazywanie plików** sprawdź nazwę pliku, a następnie kliknij pozycję **Przekaż**.
1. Po zakończeniu plik powinien być widoczny na liście na stronie **myDirectory**.

#### <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać kopię przekazanego pliku, kliknij prawym przyciskiem myszy ten plik. Szczegóły środowiska wyświetlanego po kliknięciu przycisku pobierania będą zależeć od używanego systemu operacyjnego i używanej przeglądarki.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest usługa Azure Files?](storage-files-introduction.md)
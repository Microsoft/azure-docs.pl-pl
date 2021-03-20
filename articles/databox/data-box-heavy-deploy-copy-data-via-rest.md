---
title: 'Samouczek: kopiowanie danych do Azure Data Box magazynu obiektów BLOB za pośrednictwem interfejsów API REST'
description: W tym samouczku dowiesz się, jak nawiązać połączenie z usługą Azure Data Box BLOB Storage, używając interfejsów API REST za pośrednictwem protokołu HTTP lub https, a następnie skopiuj dane z Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: e2fc174147b99b7b952c6d10084cfc969dacf5a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949143"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Samouczek: kopiowanie danych do Azure Data Box magazynu obiektów BLOB za pośrednictwem interfejsów API REST  

W tym samouczku opisano procedury łączenia się z magazynem obiektów blob usługi Azure Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS* przy użyciu interfejsów API REST. Po nawiązaniu połączenia należy wykonać kroki wymagane do skopiowania danych do urządzenie Data Box magazynu obiektów BLOB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Łączenie się z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*
> * Kopiowanie danych na urządzenie Data Box Heavy

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie urządzenia Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Znasz [wymagania systemowe magazynu obiektów blob usługi Data Box](data-box-system-requirements-rest.md) oraz obsługiwane wersje interfejsów API, zestawów SDK i narzędzi.
4. Masz dostęp do komputera-hosta zawierającego dane, do których chcesz skopiować Data Box Heavy. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł). 
5. [Pobierz narzędzie AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) na komputer hosta. Użyjesz narzędzia AzCopy do skopiowania danych z komputera hosta do magazynu obiektów blob usługi Azure Data Box.


## <a name="connect-via-http-or-https"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTP lub https

Możesz nawiązać połączenie z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*.

- *Https* to bezpieczny i zalecany sposób nawiązywania połączenia z usługą urządzenie Data Box BLOB Storage.
- Protokół *HTTP* jest używany, gdy połączenie jest nawiązywane za pośrednictwem zaufanych sieci.

Procedura łączenia się z usługą urządzenie Data Box BLOB Storage za pośrednictwem *protokołu HTTP* lub *https*.

## <a name="connect-via-http"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTP

Aby nawiązać połączenie z interfejsami API REST magazynu obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP*, należy wykonać następujące czynności:

- Dodawanie adresu IP urządzenia i punktu końcowego usługi obiektów blob do hosta zdalnego
- Konfigurowanie oprogramowania innych firm i weryfikowanie połączenia

Wszystkie wymienione kroki zostały opisane poniżej.

> [!IMPORTANT]
> Aby uzyskać Data Box Heavy, należy powtórzyć wszystkie instrukcje połączenia w celu nawiązania połączenia z drugim węzłem.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Dodaj adres IP urządzenia i punkt końcowy usługi BLOB Service

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Konfigurowanie oprogramowania partnerów i weryfikowanie połączenia

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTPS

Aby nawiązać połączenie z interfejsami API REST usługi Azure Blob Storage za pośrednictwem protokołu HTTPS, należy wykonać następujące czynności:

- Pobieranie certyfikatu z witryny Azure Portal
- Zaimportuj certyfikat na kliencie lub hoście zdalnym
- Dodawanie adresu IP urządzenia i punktu końcowego usługi BLOB do klienta lub hosta zdalnego
- Konfigurowanie oprogramowania innych firm i weryfikowanie połączenia

Wszystkie wymienione kroki zostały opisane poniżej.

> [!IMPORTANT]
> Aby uzyskać Data Box Heavy, należy powtórzyć wszystkie instrukcje połączenia w celu nawiązania połączenia z drugim węzłem.

### <a name="download-certificate"></a>Pobieranie certyfikatu

Pobierz certyfikat z witryny Azure Portal.

1. Zaloguj się do witryny Azure Portal.
2. Przejdź do zamówienia na urządzenie Data Box, a następnie wybierz pozycję **Ogólne > Szczegóły urządzenia**.
3. W obszarze **Poświadczenia urządzenia** przejdź do sekcji **Dostęp za pomocą interfejsu API**. Kliknij pozycję **Pobierz**. Ta akcja powoduje pobranie pliku certyfikatu **\<your order name> CER** . **Zapisz** ten plik. Ten certyfikat zainstalujesz na komputerze klienta lub hosta, którego będziesz używać do nawiązania połączenia z urządzeniem.

    ![Pobieranie certyfikatu z witryny Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Importowanie certyfikatu 

Dostęp do urządzenie Data Box usługi BLOB Storage za pośrednictwem protokołu HTTPS wymaga certyfikatu TLS/SSL dla urządzenia. Sposób, w jaki ten certyfikat jest dostępny dla aplikacji klienckiej, różni się od aplikacji do aplikacji i między systemami operacyjnymi i dystrybucjami. Niektóre aplikacje mogą uzyskać dostęp do certyfikatu po jego zaimportowaniu do magazynu certyfikatów systemu, podczas gdy inne aplikacje nie korzystają z tego mechanizmu.

Określone informacje dotyczące niektórych aplikacji zostały wymienione w tej sekcji. Aby uzyskać więcej informacji o innych aplikacjach, zapoznaj się z dokumentacją aplikacji i systemu operacyjnego.

Wykonaj następujące kroki, aby zaimportować `.cer` plik do magazynu głównego klienta systemu Windows lub Linux. W systemie Windows można użyć programu Windows PowerShell lub interfejsu użytkownika systemu Windows Server do zaimportowania i zainstalowania certyfikatu w systemie.

#### <a name="use-windows-powershell"></a>Użycie środowiska Windows PowerShell

1. Uruchom sesję programu Windows PowerShell jako administrator.
2. W wierszu polecenia wpisz polecenie:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Użyj interfejsu użytkownika systemu Windows Server

1.  Kliknij plik prawym przyciskiem myszy `.cer` , a następnie wybierz pozycję **Zainstaluj certyfikat**. Ta akcja powoduje uruchomienie Kreatora importowania certyfikatów.
2.  W polu **Lokalizacja magazynu** wybierz pozycję **Maszyna lokalna**, a następnie kliknij przycisk **Dalej**.

    ![Importowanie certyfikatu przy użyciu programu PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do głównego magazynu hosta zdalnego, a następnie kliknij przycisk **Dalej**.

    ![Importowanie certyfikatu przy użyciu programu PowerShell 2](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Kliknij przycisk **Finish** (Zakończ). Pojawi się komunikat informujący, że importowanie zakończyło się pomyślnie.

    ![Importowanie certyfikatu przy użyciu programu PowerShell 3](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Korzystanie z systemu Linux

Metoda importowania certyfikatu zależy od dystrybucji.

> [!IMPORTANT]
> Aby uzyskać Data Box Heavy, należy powtórzyć wszystkie instrukcje połączenia w celu nawiązania połączenia z drugim węzłem.

Kilka, takich jak Ubuntu i Debian, można użyć `update-ca-certificates` polecenia.  

- Zmień nazwę pliku certyfikatu zakodowanego algorytmem Base64, aby miał `.crt` rozszerzenie i skopiować je do programu `/usr/local/share/ca-certificates directory` .
- Uruchom polecenie `update-ca-certificates`.

Najnowsze wersje RHEL, Fedora i CentOS używają `update-ca-trust` polecenia.

- Skopiuj plik certyfikatu do `/etc/pki/ca-trust/source/anchors` katalogu.
- Uruchom polecenie `update-ca-trust`.

Szczegóły można znaleźć w dokumentacji dotyczącej dystrybucji.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Dodaj adres IP urządzenia i punkt końcowy usługi BLOB Service 

Wykonaj te same kroki, aby [dodać adres IP urządzenia i punkt końcowy usługi BLOB Service podczas nawiązywania połączenia za pośrednictwem *protokołu HTTP*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurowanie oprogramowania partnerów i weryfikowanie połączenia

Postępuj zgodnie z instrukcjami, aby [skonfigurować oprogramowanie partnerskie używane podczas nawiązywania połączenia za pośrednictwem *protokołu HTTP*](#configure-partner-software-and-verify-connection). Jedyną różnicą jest to, że należy pozostawić pole *Użyj protokołu HTTP* niezaznaczone.

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z magazynem obiektów blob usługi Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z limitami rozmiaru opisanymi w [limitach usługi Azure Storage i Data Box Heavy](data-box-limits.md).
- Jeśli dane, które są przekazywane przez Data Box Heavy, są przesyłane współbieżnie przez inne aplikacje poza Data Box Heavy, co może skutkować błędami zadań przekazywania i uszkodzeniem danych.

W tym samouczku użyjesz narzędzia AzCopy do skopiowania danych do magazynu obiektów blob usługi Data Box. Możesz również skopiować dane za pomocą Eksploratora usługi Azure Storage (jeśli wolisz użyć narzędzia z graficznym interfejsem użytkownika) lub oprogramowania partnera.

Procedura kopiowania obejmuje następujące czynności:

- Tworzenie kontenera
- Przekazywanie zawartości folderu do magazynu obiektów blob usługi Data Box
- Przekazywanie zmodyfikowanych plików do magazynu obiektów blob usługi Data Box


Wszystkie wymienione kroki zostały szczegółowo opisane poniżej.

> [!IMPORTANT]
> W przypadku Data Box Heavy należy powtórzyć wszystkie instrukcje kopiowania, aby skopiować dane do drugiego węzła.

### <a name="create-a-container"></a>Tworzenie kontenera

Pierwszym krokiem jest utworzenie kontenera, ponieważ obiekty blob są zawsze przekazywane do kontenera. Kontenery umożliwiają organizowanie grup obiektów blob w sposób podobny do organizowania plików w folderach na komputerze. Wykonaj następujące czynności, aby utworzyć kontener obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy pozycję **Kontenery obiektów Blob** i wybierz pozycję **Utwórz kontener obiektów Blob** z menu kontekstowego.

   ![Menu kontekstowe — tworzenie kontenerów obiektów blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Poniżej folderu **Kontenery obiektów Blob** będzie widoczne pole tekstowe. Wprowadź nazwę kontenera obiektów blob. Informacje na temat reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów blob znajdują się w sekcji [Tworzenie kontenera i ustawianie uprawnień](../storage/blobs/storage-quickstart-blobs-dotnet.md).
5. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob, lub klawisz **Esc**, aby anulować. Po pomyślnym utworzeniu kontener obiektów blob zostanie wyświetlony w folderze **Kontenery obiektów blob** na wybranym koncie magazynu.

   ![Utworzony kontener obiektów blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Przekazywanie zawartości folderu do magazynu obiektów blob usługi Data Box

Użyj narzędzia AzCopy, aby przekazać wszystkie pliki w folderze do magazynu obiektów blob w systemie Windows lub Linux. Aby przekazać wszystkie obiekty blob w folderze, wprowadź następujące polecenie narzędzia AzCopy:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

Zastąp wartość `<key>` własnym kluczem konta. Aby znaleźć klucz konta, w witrynie Azure Portal przejdź do swojego konta magazynu. Przejdź do pozycji **Ustawienia > Klucze dostępu**, a następnie zaznacz klucz i wklej go do polecenia narzędzia AzCopy.

Jeśli określony kontener docelowy nie istnieje, narzędzie AzCopy utworzy go i przekaże do niego plik. Zmień ścieżkę źródłową na Twój katalog danych i zastąp wartość `data-box-storage-account-name` w docelowym adresie URL nazwą konta magazynu skojarzonego z Twoją usługą Data Box.

Aby przekazać zawartość określonego katalogu do magazynu obiektów blob w sposób rekursywny, określ opcję `--recursive` (system Linux) lub `/S` (system Windows). Po uruchomieniu narzędzia AzCopy z jedną z tych opcji wszystkie podfoldery i znajdujące się w nich pliki również zostaną przekazane.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Przekazywanie zmodyfikowanych plików do magazynu obiektów blob usługi Data Box

Użyj narzędzia AzCopy do przekazania plików na podstawie daty ich ostatniej modyfikacji. Aby z tego skorzystać, zmodyfikuj pliki lub utwórz nowe pliki w katalogu źródłowym do celów testowych. Aby przekazać tylko zaktualizowane lub nowe pliki, do polecenia narzędzia AzCopy dodaj parametr `--exclude-older` (system Linux) lub `/XO` (system Windows).

Jeśli chcesz skopiować tylko zasoby źródłowe, które nie istnieją w miejscu docelowym, określ zarówno parametry `--exclude-older` i `--exclude-newer` (system Linux) lub `/XO` i `/XN` (system Windows) w poleceniu narzędzia AzCopy. Narzędzie AzCopy przekazuje tylko zaktualizowane dane na podstawie ich sygnatury czasowej.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Jeśli wystąpią błędy podczas operacji łączenia lub kopiowania, zobacz [Rozwiązywanie problemów z usługą urządzenie Data Box BLOB Storage](data-box-troubleshoot-rest.md).

Następnym krokiem jest przygotowanie urządzenia do wysłania.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Łączenie się z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*
> * Kopiowanie danych na urządzenie Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

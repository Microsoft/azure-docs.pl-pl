---
title: Azure Data Box Rozwiązywanie problemów z korzystaniem z interfejsu REST | Microsoft Docs
description: Opisuje sposób rozwiązywania problemów występujących w Azure Data Box, gdy kopiowanie danych odbywa się za pośrednictwem interfejsu REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 17b8d6de198746a79a50c4fbda805b364212e3c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98796049"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Rozwiązywanie problemów związanych z usługą Azure Data Box BLOB Storage

Ten artykuł zawiera szczegółowe informacje dotyczące rozwiązywania problemów, które mogą wystąpić podczas korzystania z usługi urządzenie Data Box BLOB Storage za pośrednictwem interfejsu REST na urządzenie Data Box do kopiowania danych. Te problemy występują podczas korzystania z usługi urządzenie Data Box BLOB Storage z innymi aplikacjami lub bibliotekami klienckimi, takimi jak Eksplorator usługi Azure Storage, AzCopy lub biblioteka usługi Azure Storage dla języka Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Błędy występujące w Eksplorator usługi Azure Storage

W tej sekcji przedstawiono niektóre problemy związane z używaniem Eksplorator usługi Azure Storage z usługą urządzenie Data Box BLOB Storage.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Nie można pobrać zasobów podrzędnych. Wartość jednego z nagłówków HTTP nie ma poprawnego formatu.|Z menu **Edycja** wybierz pozycję **docelowa Azure Stack interfejsy API**. <br>Uruchom ponownie Eksplorator usługi Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w tej ścieżce: <li>`C:\Windows\System32\drivers\etc\hosts` w systemie Windows lub </li><li> `/etc/hosts` w systemie Linux.</li>|
|Nie można pobrać zasobów podrzędnych. <br>Szczegóły: certyfikat z podpisem własnym |Zaimportuj certyfikat TLS/SSL dla urządzenia do Eksplorator usługi Azure Storage: <li>Pobierz certyfikat z Azure Portal. Aby uzyskać więcej informacji, przejdź do pozycji [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Z menu **Edycja** wybierz pozycję **Certyfikaty SSL** , a następnie wybierz pozycję **Importuj certyfikaty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Błędy widoczne w AzCopy dla systemu Windows

W tej sekcji przedstawiono niektóre problemy związane z korzystaniem z programu AzCopy dla systemu Windows z magazynem obiektów BLOB urządzenie Data Box.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|AzCopy polecenie wydaje się przestać odpowiadać przez minutę przed wyświetleniem tego błędu: <br>Nie można wyliczyć https://katalogu... Nie można rozpoznać nazwy zdalnej `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w lokalizacji: `C:\Windows\System32\drivers\etc\hosts` .|
|AzCopy polecenie wydaje się przestać odpowiadać przez minutę przed wyświetleniem tego błędu: <br>Wystąpił błąd podczas analizowania lokalizacji źródłowej. Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.|Zaimportuj certyfikat TLS/SSL dla urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, przejdź do pozycji [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Błędy widoczne w AzCopy dla systemu Linux

W tej sekcji przedstawiono niektóre problemy związane z korzystaniem z AzCopy dla systemu Linux z magazynem obiektów BLOB urządzenie Data Box.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu: <br>Wystąpił błąd podczas analizowania lokalizacji źródłowej `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . Nie ma takiego urządzenia lub adresu|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w lokalizacji: `/etc/hosts` .|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu: <br>Wystąpił błąd podczas analizowania lokalizacji źródłowej... Nie można nawiązać połączenia SSL.|Zaimportuj certyfikat TLS/SSL dla urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, przejdź do pozycji [Pobierz certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Błędy widoczne w bibliotece usługi Azure Storage dla języka Python

W tej sekcji przedstawiono niektóre najczęstsze problemy związane z wdrażaniem Data Box Disk podczas korzystania z klienta systemu Linux do kopiowania danych.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Wartość jednego z nagłówków HTTP nie ma poprawnego formatu. |Zainstalowana wersja biblioteki Microsoft Azure Storage dla języka Python nie jest obsługiwana przez urządzenie Data Box. Zobacz Azure Data Box wymagania dotyczące magazynu obiektów BLOB dla obsługiwanych wersji.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Przed uruchomieniem języka Python należy ustawić zmienną środowiskową REQUESTS_CA_BUNDLE na ścieżkę pliku certyfikatu TLS zakodowanego algorytmem Base64 (Zobacz artykuł jak [pobrać certyfikat](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Na przykład:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternatywnie Dodaj certyfikat do magazynu certyfikatów systemu, a następnie ustaw tę zmienną środowiskową na ścieżkę do tego magazynu. <br> Na przykład na platformie Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Typowe błędy

Te błędy nie są specyficzne dla żadnej aplikacji.

|Komunikat o błędzie  |Zalecana akcja |
|---------|---------|
|Upłynął limit czasu połączenia. |Zaloguj się do urządzenia urządzenie Data Box i sprawdź, czy jest on odblokowany. Za każdym razem, gdy urządzenie zostanie ponownie uruchomione, pozostaje ono zablokowane, dopóki ktoś się nie zaloguje.|
|Uwierzytelnianie interfejsu API REST kończy się niepowodzeniem z powodu błędu: serwer nie może uwierzytelnić żądania. Upewnij się, że wartość nagłówka autoryzacji jest poprawnie sformułowana, łącznie z sygnaturą. ErrorCode: AuthenticationFailed. |Jedną z przyczyn tego problemu jest to, że czas urządzenia nie jest synchronizowany z platformą Azure. W przypadku dużego pochylenia czasu uwierzytelnianie interfejsu API REST zostanie przerwane podczas próby skopiowania danych do urządzenie Data Box za pośrednictwem interfejsu API REST. W takiej sytuacji można otworzyć port wychodzący UDP 123, aby umożliwić dostęp do programu `time.windows.com` . Po zsynchronizowaniu czasu urządzenia z platformą Azure uwierzytelnianie powinno się powieść. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wymaganiach systemowych magazynu obiektów Blob urządzenie Data Box](data-box-system-requirements-rest.md).

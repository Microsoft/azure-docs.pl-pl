---
title: Śledzenie i rejestrowanie Azure Data Box, Azure Data Box Heavy zdarzeń dla zamówienia importu | Microsoft Docs
description: Opisuje sposób śledzenia i rejestrowania zdarzeń na różnych etapach Azure Data Box i Azure Data Box Heavy kolejności importu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147940"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Śledzenie i rejestrowanie zdarzeń dla Azure Data Box i Azure Data Box Heavy zamówienie importu

Urządzenie Data Box lub Data Box Heavy zamówienia importu przechodzą przez następujące kroki: kolejność, konfiguracja, kopiowanie danych, zwracanie, przekazywanie na platformę Azure oraz sprawdzanie i wymazywanie danych. Dla każdego kroku w kolejności można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

W poniższej tabeli przedstawiono podsumowanie urządzenie Data Box lub Data Box Heavy czynności związanych z kolejnością importu oraz narzędzi dostępnych do śledzenia i inspekcji kolejności w każdym kroku.

| urządzenie Data Box etap importowania kolejności       | Narzędzie do śledzenia i inspekcji                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Tworzenie zamówienia               | [Konfigurowanie kontroli dostępu w kolejności za pośrednictwem usługi Azure RBAC](#set-up-access-control-on-the-order)                                                    |
| Przetworzone zamówienie            | [Śledź kolejność](#track-the-order) <ul><li> Azure Portal </li><li> Witryna sieci Web firmy kurierskiej </li><li>Powiadomienia e-mail</ul> |
| Konfigurowanie urządzenia              | Poświadczenia urządzenia dostęp do zalogowanych [dzienników aktywności](#query-activity-logs-during-setup)                                              |
| Kopiowanie danych na urządzenie        | [Wyświetl *error.xml* pliki](#view-error-log-during-data-copy) do skopiowania danych                                                             |
| Przygotowanie do wysłania            | [Inspekcja plików BOM](#inspect-bom-during-prepare-to-ship) lub plików manifestu na urządzeniu                                      |
| Przekazywanie danych na platformę Azure       | [Przeglądanie dzienników kopiowania](#review-copy-log-during-upload-to-azure) pod kątem błędów podczas przekazywania danych w centrum dane platformy Azure                         |
| Dane wymazywane z urządzenia   | [Wyświetlanie łańcucha dzienników opieki](#get-chain-of-custody-logs-after-data-erasure) , w tym dzienników inspekcji i historii kolejności                |

W tym artykule opisano szczegółowo różne mechanizmy lub narzędzia dostępne do śledzenia i inspekcji urządzenie Data Box lub Data Box Heavy kolejności importu. Informacje przedstawione w tym artykule mają zastosowanie do urządzenie Data Box i Data Box Heavy zamówienia importu. W kolejnych sekcjach wszystkie odwołania do urządzenie Data Box dotyczą również Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Konfigurowanie kontroli dostępu w kolejności

Możesz kontrolować, kto może uzyskiwać dostęp do zamówienia podczas pierwszego tworzenia zamówienia. Skonfiguruj role platformy Azure w różnych zakresach, aby kontrolować dostęp do kolejności urządzenie Data Box. Rola platformy Azure określa typ dostępu — do odczytu i zapisu, tylko do odczytu, do odczytu i zapisu do podzbioru operacji.

Dwie role, które można zdefiniować dla usługi Azure Data Box są następujące:

- **Urządzenie Data Box Reader** — dostęp tylko do odczytu do zamówień określonych przez zakres. Mogą jedynie wyświetlać szczegóły zamówienia. Nie mogą oni uzyskać dostępu do żadnych innych szczegółów związanych z kontami magazynu ani edytować szczegółów zamówienia, takich jak adres i tak dalej.
- **Współautor urządzenie Data Box** — można utworzyć tylko zamówienie, aby przesłać dane do danego konta magazynu, *Jeśli mają już dostęp do zapisu do konta magazynu*. Jeśli nie mają dostępu do konta magazynu, nie można nawet utworzyć zamówienia urządzenie Data Box, aby skopiować dane na konto. Ta rola nie definiuje żadnych uprawnień związanych z kontem magazynu ani nie udziela dostępu do kont magazynu.  

Aby ograniczyć dostęp do zamówienia, możesz:

- Przypisywanie roli na poziomie zamówienia. Użytkownik ma tylko te uprawnienia zdefiniowane przez role do współdziałania z tym konkretną urządzenie Data Box kolejnością i nic innego.
- Przypisz rolę na poziomie grupy zasobów, użytkownik ma dostęp do wszystkich zamówień urządzenie Data Box w grupie zasobów.

Aby uzyskać więcej informacji na temat sugerowanego użycia RBAC platformy Azure, zobacz [najlepsze rozwiązania dotyczące kontroli RBAC platformy Azure](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Śledzenie zamówienia

Zamówienie można śledzić za pomocą Azure Portal oraz za pomocą witryny sieci Web firmy Carrier transport. Następujące mechanizmy są stosowane do śledzenia kolejności urządzenie Data Box w dowolnym momencie:

- Aby śledzić kolejność, gdy urządzenie znajduje się w centrum danych platformy Azure lub Twoim środowisku lokalnym, przejdź do **urządzenie Data Box zamówienia > Omówienie** w Azure Portal.

    ![Wyświetl stan zamówienia i numer śledzenia](media/data-box-logs/overview-view-status-1.png)

- Aby śledzić kolejność, gdy urządzenie jest w trakcie przesyłania, przejdź do witryny internetowej przewoźnika regionalnego, na przykład w przypadku witryny UPS w USA. Podaj numer śledzenia skojarzony z zamówieniem.
- Urządzenie Data Box wysyła również powiadomienia e-mail, gdy stan zamówienia zostanie zmieniony na podstawie wiadomości e-mail dostarczonych podczas tworzenia zamówienia. Aby uzyskać listę wszystkich stanów zamówień urządzenie Data Box, zobacz [Wyświetlanie stanu zamówienia](data-box-portal-admin.md#view-order-status). Aby zmienić ustawienia powiadomień skojarzone z kolejnością, zobacz [Edytowanie szczegółów powiadomień](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Badaj dzienniki aktywności podczas instalacji

- Twoje urządzenie Data Box docierają do Twoich miejsc w stanie zablokowanym. Możesz użyć poświadczeń urządzenia dostępnych w Azure Portal zamówienia.  

    Po skonfigurowaniu urządzenie Data Box może być konieczna informacja o tym, kto ma dostęp do poświadczeń urządzenia. Aby ustalić, kto uzyskał dostęp do bloku **poświadczenia urządzenia** , można wykonać zapytanie dotyczące dzienników aktywności.  Wszystkie akcje dotyczące uzyskiwania dostępu do **szczegółów urządzenia > bloku poświadczenia** są rejestrowane w dziennikach aktywności jako `ListCredentials` Akcja.

    ![Wykonywanie zapytań dotyczących dzienników aktywności](media/data-box-logs/query-activity-log-1.png)

- Każdy Zaloguj się do urządzenie Data Box jest rejestrowany w czasie rzeczywistym. Jednak te informacje są dostępne tylko w [dziennikach inspekcji](#audit-logs) po pomyślnym zakończeniu kolejności.

## <a name="view-error-log-during-data-copy"></a>Wyświetl dziennik błędów podczas kopiowania danych

Podczas kopiowania danych do urządzenie Data Box lub Data Box Heavy, generowany jest plik błędu w przypadku problemów z kopiowanymi danymi.

### <a name="errorxml-file"></a>Plik Error.xml

Upewnij się, że zadania kopiowania zakończyły się bez błędów. Jeśli wystąpią błędy podczas procesu kopiowania, Pobierz dzienniki ze strony **Połącz i Kopiuj** .

- Jeśli skopiowano plik, który nie jest 512 bajtów wyrównany do folderu dysku zarządzanego na urządzenie Data Box, plik nie zostanie przekazany jako obiekt BLOB strony do konta magazynu tymczasowego. W dziennikach zostanie wyświetlony komunikat o błędzie. Usuń plik i skopiuj plik o 512 bajtów wyrównanych.
- W przypadku skopiowania dysku VHDX lub dynamicznego dysku VHD lub różnicowego dysku VHD (te pliki nie są obsługiwane) w dziennikach zostanie wyświetlony komunikat o błędzie.

Oto przykład *error.xml* różnych błędów podczas kopiowania na dyski zarządzane.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Oto przykład *error.xml* różnych błędów podczas kopiowania do stronicowych obiektów BLOB.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Oto przykład *error.xml* różnych błędów podczas kopiowania do blokowych obiektów BLOB.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Oto przykład *error.xml* różnych błędów podczas kopiowania do Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

W każdym z powyższych przypadków Usuń błędy przed przejściem do następnego kroku. Aby uzyskać więcej informacji na temat błędów otrzymywanych podczas kopiowania danych do urządzenie Data Box za pośrednictwem protokołów SMB lub NFS, przejdź do [rozwiązywania problemów z urządzenie Data Box i Data Box Heavy](data-box-troubleshoot.md). Aby uzyskać informacje dotyczące błędów otrzymywanych podczas kopiowania danych na urządzenie Data Box za pośrednictwem usługi REST, przejdź do obszaru [Rozwiązywanie problemów z urządzenie Data Box magazynu obiektów BLOB](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Zbadaj BOM podczas przygotowywania do wysłania

Podczas przygotowywania do wysłania należy utworzyć listę plików znanych jako BOM lub plik manifestu.

- Użyj tego pliku, aby sprawdzić poprawność nazw i liczbę plików, które zostały skopiowane do urządzenie Data Box.
- Użyj tego pliku, aby zweryfikować rzeczywiste rozmiary plików.
- Sprawdź, czy *crc64* odpowiada ciągowi o wartości innej niż zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Aby uzyskać więcej informacji na temat błędów otrzymywanych podczas przygotowywania do wysłania, przejdź do [rozwiązywania problemów urządzenie Data Box i Data Box Heavy](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM lub plik manifestu

Plik BOM lub manifestu zawiera listę wszystkich plików, które są kopiowane na urządzenie urządzenie Data Box. Plik BOM zawiera nazwy plików i odpowiednie rozmiary oraz sumę kontrolną. Tworzony jest osobny plik BOM dla blokowych obiektów blob, stronicowych obiektów blob, Azure Files, do kopiowania za pośrednictwem interfejsów API REST oraz kopiowania do dysków zarządzanych na urządzenie Data Box. Pliki BOM można pobrać z lokalnego interfejsu użytkownika sieci Web urządzenia podczas przygotowywania do wysłania.

Te pliki znajdują się również na urządzeniu urządzenie Data Box i są przekazywane do skojarzonego konta magazynu w centrum danych platformy Azure.

### <a name="bom-file-format"></a>Format pliku BOM

Plik BOM lub manifestu ma następujący format ogólny:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Poniżej znajduje się przykład manifestu generowanego, gdy dane zostały skopiowane do udziału blokowego obiektu BLOB na urządzenie Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Pliki BOM lub manifestów są również kopiowane do konta usługi Azure Storage. Możesz użyć pliku lub plików manifestu, aby sprawdzić, czy pliki przekazane do platformy Azure pasują do danych, które zostały skopiowane do urządzenie Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Przejrzyj dziennik kopiowania podczas przekazywania na platformę Azure

Podczas przekazywania danych na platformę Azure jest tworzony dziennik kopii.

### <a name="copy-log"></a>Kopiuj dziennik

Dla każdego przetwarzanego zamówienia usługa urządzenie Data Box tworzy dziennik kopiowania na skojarzonym koncie magazynu. Dziennik kopiowania zawiera łączną liczbę plików, które zostały przekazane, oraz liczbę plików, które wystąpiły podczas kopiowania danych z urządzenie Data Box na konto usługi Azure Storage.

Obliczenia cyklicznej kontroli nadmiarowości (CRC) są wykonywane podczas przekazywania do platformy Azure. CRCs z kopii danych i po przekazaniu danych. Niezgodność CRC wskazuje, że nie powiodło się przekazanie odpowiednich plików.

Domyślnie dzienniki są zapisywane do kontenera o nazwie `copylog` . Dzienniki są przechowywane z następującą konwencją nazewnictwa:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Ścieżka dziennika kopiowania jest również wyświetlana w bloku **Przegląd** dla portalu.

![Ścieżka do kopiowania bloku dziennika przeglądu po zakończeniu](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Przekazywanie zostało ukończone pomyślnie 

W poniższym przykładzie opisano ogólny format dziennika kopiowania dla urządzenie Data Box przekazywania, który zakończył się pomyślnie:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Przekazywanie zakończone z błędami 

Przekazywanie na platformę Azure może także zakończyć się z błędami.

![Ścieżka do kopiowania bloku dziennika przeglądu po zakończeniu z błędami](media/data-box-logs/copy-log-path-2.png)

Oto przykład dziennika kopiowania, w którym zakończono przekazywanie z błędami:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Przekazywanie zostało ukończone z ostrzeżeniami

Przekazanie na platformę Azure kończy się z ostrzeżeniami, jeśli dane mają nazwy kontenera/obiektów BLOB/plików niezgodne z konwencjami nazewnictwa platformy Azure i nazwy zostały zmodyfikowane w celu przekazania danych na platformę Azure.

![Ścieżka do kopiowania bloku dziennika przeglądu po zakończeniu z ostrzeżeniami](media/data-box-logs/copy-log-path-3.png)

Oto przykład dziennika kopiowania, w którym nazwy kontenerów, które nie są zgodne z konwencjami nazewnictwa platformy Azure, zostały zmienione podczas przekazywania danych na platformę Azure.

Nowe unikatowe nazwy kontenerów są w formacie `DataBox-GUID` , a dane dla kontenera są umieszczane w nowym kontenerze. W dzienniku kopiowania określono starą i nową nazwę kontenera dla kontenera.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Poniżej znajduje się przykład dziennika kopiowania, w którym zmieniono nazwy obiektów blob lub plików, które nie są zgodne z konwencjami nazewnictwa platformy Azure, podczas przekazywania danych na platformę Azure. Nowe obiekty blob lub nazwy plików są konwertowane na SHA256 Digest ścieżki względnej do kontenera i są przekazywane do ścieżki na podstawie typu docelowego. Miejscem docelowym może być blokowe obiekty blob, stronicowe obiekty blob lub Azure Files.

`copylog`Określa stary i nowy obiekt BLOB lub nazwę pliku oraz ścieżkę na platformie Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Pobierz łańcuch dzienników opieki po usunięciu danych

Po wymazaniu danych z urządzenie Data Box dysków zgodnie z wytycznymi dla programu NIST SP 800-88 z poprawkami 1 jest dostępny łańcuch dzienników opieki. Te dzienniki obejmują dzienniki inspekcji i historię kolejności. Pliki BOM lub manifestów są również kopiowane z dziennikami inspekcji.

### <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji zawierają informacje dotyczące włączania i uzyskiwania dostępu do udziałów na urządzenie Data Box lub Data Box Heavy, gdy nie znajduje się on poza centrum danych platformy Azure. Te dzienniki znajdują się w: `storage-account/azuredatabox-chainofcustodylogs`

Oto przykład dziennika inspekcji z urządzenie Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Pobieranie historii zamówienia

Historia zamówień jest dostępna w Azure Portal. Jeśli zamówienie zostało zakończone, a oczyszczanie urządzenia (dane wymazywane z dysków) zostało zakończone, przejdź do kolejności urządzenia i przejdź do **szczegółów zamówienia**. Opcja **Pobieranie historii zamówienia** jest dostępna. Aby uzyskać więcej informacji, zobacz [Pobieranie historii kolejności](data-box-portal-admin.md#download-order-history).

Jeśli przewiniesz historię kolejności, zobaczysz:

- Informacje o śledzeniu nośnika dla Twojego urządzenia.
- Zdarzenia z działaniem *SecureErase* . Te zdarzenia odpowiadają za wymazywanie danych na dysku.
- Urządzenie Data Box linki dzienników. Wyświetlane są ścieżki *dzienników inspekcji*, *kopii dzienników* i plików *BOM* .

Oto przykład dziennika historii kolejności z Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak rozwiązywać problemy dotyczące urządzenie Data Box i Data Box Heavy](data-box-troubleshoot.md).

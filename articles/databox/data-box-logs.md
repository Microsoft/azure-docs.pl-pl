---
title: Śledzenie i rejestrowanie zdarzeń azure data box, azure data box heavy; Dokumenty firmy Microsoft
description: W tym artykule opisano sposób śledzenia i rejestrowania zdarzeń na różnych etapach zamówienia azure data box i azure data box heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676408"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Śledzenie i rejestrowanie zdarzeń dla usługi Azure Data Box i Azure Data Box Heavy

Zamówienie data box lub Data Box Heavy przechodzi przez następujące kroki: kolejność, konfiguracja, kopiowanie danych, zwracanie, przekazywanie na platformę Azure i weryfikowanie oraz usuwanie danych. Odpowiadający każdemu krokowi w kolejności można podjąć wiele akcji, aby kontrolować dostęp do zamówienia, kontrolować zdarzenia, śledzić kolejność i interpretować różne generowane dzienniki.

W poniższej tabeli przedstawiono podsumowanie kroków zamówienia Data Box lub Data Box Heavy oraz narzędzia dostępne do śledzenia i inspekcji zamówienia podczas każdego kroku.

| Etap zamówienia pola danych       | Narzędzie do śledzenia i audytu                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Tworzenie zamówienia               | [Konfigurowanie kontroli dostępu do zamówienia za pośrednictwem RBAC](#set-up-access-control-on-the-order)                                                    |
| Zamówienie przetworzone            | [Śledzenie zamówienia](#track-the-order) za pośrednictwem <ul><li> Azure Portal </li><li> Witryna internetowa firmy spedycyjnej </li><li>Powiadomienia e-mail</ul> |
| Konfigurowanie urządzenia              | Dostęp do poświadczeń urządzenia zalogowanych w [dziennikach aktywności](#query-activity-logs-during-setup)                                              |
| Kopiowanie danych na urządzenie        | [Wyświetlanie plików *error.xml* ](#view-error-log-during-data-copy) dla kopiowania danych                                                             |
| Przygotowanie do wysłania            | [Sprawdzanie plików BOM](#inspect-bom-during-prepare-to-ship) lub plików manifestu na urządzeniu                                      |
| Przekazywanie danych na platformę Azure       | [Przeglądanie dzienników kopiowania](#review-copy-log-during-upload-to-azure) w poszukiwaniu błędów podczas przekazywania danych w centrum danych platformy Azure                         |
| Usuwanie danych z urządzenia   | [Wyświetlanie łańcucha dzienników opieki,](#get-chain-of-custody-logs-after-data-erasure) w tym dzienników inspekcji i historii zamówień                |

W tym artykule opisano szczegółowo różne mechanizmy lub narzędzia dostępne do śledzenia i inspekcji data box lub data box heavy zamówienia. Informacje zawarte w tym artykule dotyczą zarówno data box i Data Box Heavy. W kolejnych sekcjach wszelkie odwołania do pola danych mają również zastosowanie do data box heavy.

## <a name="set-up-access-control-on-the-order"></a>Konfigurowanie kontroli dostępu w zamówieniu

Możesz kontrolować, kto może uzyskać dostęp do twojego zamówienia podczas pierwszego utworzenia zamówienia. Skonfiguruj role kontroli dostępu opartej na rolach (RBAC) w różnych zakresach, aby kontrolować dostęp do kolejności data box. Rola RBAC określa typ dostępu — odczytu i zapisu, tylko do odczytu i zapisu do podzbioru operacji.

Dwie role, które można zdefiniować dla usługi Azure Data Box to:

- **Data Box Reader** - mają dostęp tylko do odczytu do zamówień zdefiniowanych przez zakres. Mogą wyświetlać tylko szczegóły zamówienia. Nie mogą uzyskać dostępu do innych szczegółów związanych z kontami magazynu ani edytować szczegółów zamówienia, takich jak adres i tak dalej.
- **Data Box Contributor** - może utworzyć zlecenie przesyłania danych do danego konta magazynu *tylko wtedy, gdy ma już dostęp do zapisu konta magazynu.* Jeśli nie mają dostępu do konta magazynu, nie mogą nawet utworzyć zamówienia w polu danych, aby skopiować dane na konto. Ta rola nie definiuje żadnych uprawnień związanych z kontem magazynu ani nie udziela dostępu do kont magazynu.  

Aby ograniczyć dostęp do zamówienia, można:

- Przypisz rolę na poziomie zamówienia. Użytkownik ma tylko te uprawnienia zdefiniowane przez role do interakcji z tej konkretnej kolejności pola danych tylko i nic więcej.
- Przypisz rolę na poziomie grupy zasobów, użytkownik ma dostęp do wszystkich zamówień pola danych w grupie zasobów.

Aby uzyskać więcej informacji na temat sugerowanego użycia funkcji RBAC, zobacz [Najważniejsze wskazówki dotyczące usługi Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Śledzenie zamówienia

Możesz śledzić swoje zamówienie za pośrednictwem witryny Azure portal i za pośrednictwem witryny internetowej przewoźnika. W dowolnym momencie obowiązują następujące mechanizmy śledzenia kolejności data box:

- Aby śledzić kolejność, gdy urządzenie znajduje się w centrum danych platformy Azure lub w twoim lokalu, przejdź do przeglądu > kolejności pól danych w **witrynie** Azure portal.

    ![Wyświetlanie stanu zamówienia i śledzenie nie](media/data-box-logs/overview-view-status-1.png)

- Aby śledzić zamówienie podczas przesyłania urządzenia, przejdź do witryny internetowej operatora regionalnego, na przykład witryny UPS w USA. Podaj numer śledzenia skojarzony z zamówieniem.
- Data Box wysyła również powiadomienia e-mail w każdej chwili zmiany statusu zamówienia na podstawie wiadomości e-mail podanych podczas tworzenia zamówienia. Aby uzyskać listę wszystkich stanów zamówień pola danych, zobacz [Wyświetlanie stanu zamówienia](data-box-portal-admin.md#view-order-status). Aby zmienić ustawienia powiadomień skojarzone z zamówieniem, zobacz [Edytowanie szczegółów powiadomień](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Dzienniki aktywności kwerend podczas instalacji

- Pole danych jest przesyłane do twojego lokalu w stanie zablokowanym. Poświadczenia urządzenia można użyć dostępnych w witrynie Azure portal dla zamówienia.  

    Po skonfigurowaniu pola danych może być konieczne poznanie, kto uzyskał dostęp do poświadczeń urządzenia. Aby dowiedzieć się, kto uzyskał dostęp do **bloku poświadczenia urządzenia,** można zbadać dzienniki aktywności.  Każda akcja, która obejmuje dostęp do **szczegółów urządzenia > przyciemnianie** `ListCredentials` poświadczenia jest zalogowany do dzienników działań jako akcja.

    ![Wykonywanie zapytań dotyczących dzienników aktywności](media/data-box-logs/query-activity-log-1.png)

- Każdy znak w polu danych jest rejestrowany w czasie rzeczywistym. Jednak te informacje są dostępne tylko w [dziennikach inspekcji](#audit-logs) po pomyślnym zakończeniu zamówienia.

## <a name="view-error-log-during-data-copy"></a>Wyświetlanie dziennika błędów podczas kopiowania danych

Podczas kopiowania danych do pola danych lub pliku Data Box Heavy plik błędu jest generowany w przypadku jakichkolwiek problemów z kopiowanymi danymi.

### <a name="errorxml-file"></a>Plik error.xml

Upewnij się, że zadania kopiowania zostały zakończone bez błędów. Jeśli podczas procesu kopiowania występują błędy, pobierz dzienniki ze strony **Połącz i skopuj.**

- Jeśli plik, który nie jest 512 bajtów wyrównane do folderu dysku zarządzanego w polu danych, plik nie jest przekazyany jako obiekt blob strony do konta magazynu przemieszczania. Zostanie wyświetlony błąd w dziennikach. Usuń plik i skopiuj plik, który jest wyrównany do 512 bajtów.
- Jeśli skopiowano VHDX, dynamiczny dysk VHD lub różnicowy dysk VHD (te pliki nie są obsługiwane), w dziennikach pojawi się błąd.

Oto przykład pliku *error.xml* dla różnych błędów podczas kopiowania do dysków zarządzanych.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Oto przykład pliku *error.xml* dla różnych błędów podczas kopiowania do obiektów blob strony.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Oto przykład pliku *error.xml* dla różnych błędów podczas kopiowania w celu zablokowania obiektów blob.

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

Oto przykład pliku *error.xml* dla różnych błędów podczas kopiowania do usługi Azure Files.

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

W każdym z powyższych przypadków rozwiąż błędy przed przejściem do następnego kroku. Aby uzyskać więcej informacji na temat błędów otrzymanych podczas kopiowania danych do data box za pośrednictwem protokołów SMB lub NFS, przejdź do [rozwiązywania problemów z polem danych i dużymi problemami z polem danych](data-box-troubleshoot.md). Aby uzyskać informacje na temat błędów otrzymanych podczas kopiowania danych do data box za pośrednictwem REST, przejdź do [rozwiązywania problemów z przechowywaniem obiektów Blob pola danych](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspekcja BOM podczas przygotowań do wysyłki

Podczas przygotowywania do wysyłki tworzona jest lista plików znanych jako BOM (BOM) lub plik manifestu.

- Ten plik służy do weryfikacji rzeczywistych nazw i liczby plików skopiowanych do pola danych.
- Ten plik służy do weryfikacji rzeczywistych rozmiarów plików.
- Sprawdź, czy *crc64* odpowiada ciągowi niezerowe. <!--A null value for crc64 indicates that there was a reparse point error)-->

Aby uzyskać więcej informacji na temat błędów otrzymanych podczas przygotowywania do wysyłki, przejdź do [rozwiązywania problemów z polem danych i dużymi problemami z polem danych](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM lub plik manifestu

Plik BOM lub manifest zawiera listę wszystkich plików, które są kopiowane do urządzenia Data Box. Plik BOM ma nazwy plików i odpowiednie rozmiary, a także sumę kontrolną. Oddzielny plik BOM jest tworzony dla bloków blob, stronicowych obiektów blob, plików Platformy Azure, do kopiowania za pośrednictwem interfejsów API REST i dla kopiowania do dysków zarządzanych w polu danych. Pliki BOM można pobrać z lokalnego interfejsu użytkownika sieci Web urządzenia podczas przygotowywania do wysyłki.

Pliki te znajdują się również na urządzeniu Data Box i są przekazywane do skojarzonego konta magazynu w centrum danych platformy Azure.

### <a name="bom-file-format"></a>Format pliku BOM

BOM lub plik manifestu ma następujący ogólny format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Oto przykład manifestu wygenerowany, gdy dane zostały skopiowane do udziału bloku obiektu blob w polu danych.

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

Pliki BOM lub manifestu są również kopiowane na konto magazynu platformy Azure. Za pomocą bom lub plików manifestu można sprawdzić, czy pliki przekazane na platformę Azure są zgodne z danymi skopiowanymi do pola danych.

## <a name="review-copy-log-during-upload-to-azure"></a>Przeglądanie dziennika kopiowania podczas przekazywania na platformę Azure

Podczas przekazywania danych na platformę Azure tworzony jest dziennik kopii.

### <a name="copy-log"></a>Dziennik kopiowania

Dla każdego zamówienia, które jest przetwarzane, usługa Data Box tworzy dziennik kopiowania na skojarzonym koncie magazynu. Dziennik kopiowania zawiera całkowitą liczbę plików, które zostały przekazane i liczbę plików, które wystąpiły podczas kopiowania danych z pola danych na konto magazynu platformy Azure.

Obliczenia cyklicznego sprawdzania nadmiarowości (CRC) są wykonywane podczas przekazywania na platformę Azure. Crc z kopii danych i po przekazaniu danych są porównywane. Niezgodność CRC wskazuje, że nie można przekazać odpowiednich plików.

Domyślnie dzienniki są zapisywane w `copylog`kontenerze o nazwie . Dzienniki są przechowywane z następującą konwencją nazewnictwa:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Ścieżka dziennika kopiowania jest również wyświetlana w **bloku Przegląd** dla portalu.

![Ścieżka do kopiowania dziennika w bloku Przegląd po zakończeniu](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Przekazywanie zakończyło się pomyślnie 

W poniższym przykładzie opisano ogólny format dziennika kopii dla przekazywania pola danych, który zakończył się pomyślnie:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Przekazywanie zakończone z błędami 

Przekaż na platformę Azure może również wraz z błędami.

![Ścieżka do kopiowania bloku przegląd po zakończeniu z błędami](media/data-box-logs/copy-log-path-2.png)

Oto przykład dziennika kopiowania, w którym przekazywanie zostało zakończone z błędami:

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
### <a name="upload-completed-with-warnings"></a>Przekazywanie zakończone z ostrzeżeniami

Przekaż na platformę Azure wraz z ostrzeżeniami, jeśli dane miały nazwy kontenerów/obiektów blob/plików, które nie były zgodne z konwencjami nazewnictwa platformy Azure, a nazwy zostały zmodyfikowane w celu przekazania danych na platformę Azure.

![Ścieżka do kopiowania bloku przegląd po zakończeniu z ostrzeżeniami](media/data-box-logs/copy-log-path-3.png)

Oto przykład dziennika kopiowania, w którym kontenery, które nie były zgodne z konwencjami nazewnictwa platformy Azure zostały zmienione podczas przekazywania danych na platformę Azure.

Nowe unikatowe nazwy kontenerów `DataBox-GUID` są w formacie i dane dla kontenera są umieszczane w nowym kontenerze o zmienionej nazwie. Dziennik kopiowania określa starą i nową nazwę kontenera dla kontenera.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Oto przykład dziennika kopiowania, w którym obiekty BLOB lub pliki, które nie były zgodne z konwencjami nazewnictwa platformy Azure, zostały zmienione podczas przekazywania danych na platformę Azure. Nowe nazwy obiektów blob lub plików są konwertowane na sha256 skrót ścieżki względnej do kontenera i są przekazywane do ścieżki na podstawie typu docelowego. Miejscem docelowym mogą być blokowe obiekty BLOB, obiekty blob stron lub usługi Azure Files.

Określa `copylog` stare i nowe gołąb lub nazwę pliku i ścieżkę na platformie Azure.

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Pobierz dzienniki łańcucha opieki po usunięciu danych

Po usunięciu danych z dysków pola danych zgodnie z wytycznymi NIST SP 800-88 Revision 1 dostępne są dzienniki łańcucha opieki. Dzienniki te obejmują dzienniki inspekcji i historię zamówień. Pliki BOM lub manifestu są również kopiowane za pomocą dzienników inspekcji.

### <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji zawierają informacje dotyczące włączania i uzyskiwania dostępu do udziałów w polu danych lub danych— dużo danych, gdy znajdują się poza centrum danych platformy Azure. Te dzienniki znajdują się pod adresem:`storage-account/azuredatabox-chainofcustodylogs`

Oto przykład dziennika inspekcji z pola danych:

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

Historia zamówień jest dostępna w witrynie Azure portal. Jeśli zamówienie zostanie zakończone, a oczyszczanie urządzenia (usuwanie danych z dysków) zostanie zakończone, przejdź do zamówienia urządzenia i przejdź do **pozycji Szczegóły zamówienia**. Opcja **Pobieranie historii zamówienia** jest dostępna. Aby uzyskać więcej informacji, zobacz [Historia zamówień pobierania](data-box-portal-admin.md#download-order-history).

Po przewinięciu historii zamówień zobaczysz:

- Informacje o śledzeniu operatora dla Twojego urządzenia.
- Zdarzenia z aktywnością *SecureErase.* Zdarzenia te odpowiadają wymazaniu danych na dysku.
- Łącza dziennika pola danych. Ścieżki dla *dzienników inspekcji, dzienników* *kopiowania*i plików *BOM* są prezentowane.

Oto przykład dziennika historii zamówień z witryny Azure portal:

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

- Dowiedz się, jak [rozwiązywać problemy z danymi w witrynie Data Box i Data Box Heavy](data-box-troubleshoot.md).

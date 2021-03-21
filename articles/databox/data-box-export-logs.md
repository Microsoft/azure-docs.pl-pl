---
title: Śledź i Rejestruj Azure Data Box, Azure Data Box Heavy zdarzenia dla zamówienia eksportu | Microsoft Docs
description: Opisuje sposób śledzenia i rejestrowania zdarzeń na różnych etapach Azure Data Box i Azure Data Box Heavy kolejności eksportu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337512"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Śledzenie i rejestrowanie zdarzeń dla Azure Data Box i Azure Data Box Heavy zamówień eksportu

Zamówienie urządzenie Data Box lub Data Box Heavy eksportu przechodzą przez następujące kroki: kolejność, konfiguracja, kopiowanie danych, zwracanie i wymazywanie danych. Dla każdego kroku w kolejności można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

W tym artykule opisano szczegółowo różne mechanizmy lub narzędzia dostępne do śledzenia i inspekcji zamówień eksportu dla urządzenie Data Box lub Data Box Heavy. Informacje przedstawione w tym artykule dotyczą obu programów, urządzenie Data Box i Data Box Heavy. W kolejnych sekcjach wszystkie odwołania do urządzenie Data Box dotyczą również Data Box Heavy.

W poniższej tabeli przedstawiono podsumowanie kroków kolejności eksportu urządzenie Data Box i dostępnych narzędzi do śledzenia i inspekcji kolejności w każdym kroku.

| urządzenie Data Box etap eksportu kolejności       | Narzędzie do śledzenia i inspekcji                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Tworzenie zamówienia               | [Konfigurowanie kontroli dostępu w kolejności za pośrednictwem usługi Azure RBAC](#set-up-access-control-on-the-order) <br> [Włącz pełny dziennik w kolejności](#enable-verbose-log-in-the-order)                                                    |
| Przetworzone zamówienie            | [Śledź kolejność](#track-the-order) <ul><li> Azure Portal </li><li> Witryna sieci Web firmy kurierskiej </li><li>Powiadomienia e-mail</ul> |
| Konfigurowanie urządzenia              | Poświadczenia urządzenia dostęp do zalogowanych [dzienników aktywności](#query-activity-logs-during-setup)              |
| Kopiowanie danych z urządzenia        | [Przejrzyj dzienniki kopiowania](#copy-log) <br> Zapoznaj się z [pełnymi dziennikami](#verbose-log) przed skopiowaniem danych            |
| Dane wymazywane z urządzenia   | [Wyświetlanie łańcucha dzienników opieki](#get-chain-of-custody-logs-after-data-erasure) , w tym dzienników inspekcji i historii kolejności                |


## <a name="set-up-access-control-on-the-order"></a>Konfigurowanie kontroli dostępu w kolejności

Możesz kontrolować, kto może uzyskiwać dostęp do zamówienia podczas pierwszego tworzenia zamówienia. Skonfiguruj role platformy Azure w różnych zakresach, aby kontrolować dostęp do kolejności urządzenie Data Box. Rola platformy Azure określa typ dostępu — do odczytu i zapisu, tylko do odczytu, do odczytu i zapisu do podzbioru operacji.

Dwie role, które można zdefiniować dla usługi Azure Data Box są następujące:

- **Urządzenie Data Box Reader** — dostęp tylko do odczytu do zamówień określonych przez zakres. Mogą jedynie wyświetlać szczegóły zamówienia. Nie mogą oni uzyskać dostępu do żadnych innych szczegółów związanych z kontami magazynu ani edytować szczegółów zamówienia, takich jak adres i tak dalej.
- **Współautor urządzenie Data Box** — można utworzyć tylko zamówienie, aby przesłać dane do danego konta magazynu, *Jeśli mają już dostęp do zapisu do konta magazynu*. Jeśli nie mają dostępu do konta magazynu, nie można nawet utworzyć zamówienia urządzenie Data Box, aby skopiować dane na konto. Ta rola nie definiuje żadnych uprawnień związanych z kontem magazynu ani nie udziela dostępu do kont magazynu.  

Aby ograniczyć dostęp do zamówienia, możesz:

- Przypisywanie roli na poziomie zamówienia. Użytkownik ma tylko te uprawnienia zdefiniowane przez role do współdziałania z tym konkretną urządzenie Data Box kolejnością i nic innego.
- Przypisz rolę na poziomie grupy zasobów, użytkownik ma dostęp do wszystkich zamówień urządzenie Data Box w grupie zasobów.

Aby uzyskać więcej informacji na temat sugerowanego użycia RBAC platformy Azure, zobacz [najlepsze rozwiązania dotyczące kontroli RBAC platformy Azure](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Włącz pełny dziennik w kolejności

Podczas umieszczania zamówienia eksportu dla urządzenie Data Box można włączyć zbieranie pełnych dzienników. Oto ekran Order, w którym można włączyć pełny dziennik:

![Wybierz opcję eksportu](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Po wybraniu opcji **Dołącz dziennik pełny** zostanie wygenerowany pełny plik dziennika podczas kopiowania danych z konta usługi Azure Storage. Ten dziennik zawiera listę wszystkich plików, które zostały pomyślnie wyeksportowane.

Aby uzyskać więcej informacji na temat kolejności eksportu, zobacz [Tworzenie zamówienia eksportowego dla urządzenie Data Box](data-box-deploy-export-ordered.md)

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

- Każdy Zaloguj się do urządzenie Data Box jest rejestrowany w czasie rzeczywistym. Jednak te informacje są dostępne tylko w [łańcuchu inspekcji nadzoru](#chain-of-custody-audit-logs) po pomyślnym zakończeniu zamówienia.

## <a name="view-logs-during-data-copy"></a>Wyświetlanie dzienników podczas kopiowania danych

Przed skopiowaniem danych z urządzenie Data Box można pobrać i przejrzeć *Dziennik kopiowania* oraz *pełny dziennik* dla danych, które zostały skopiowane do urządzenie Data Box. Te dzienniki są generowane, gdy dane są kopiowane z konta magazynu na platformie Azure do urządzenie Data Box. 

### <a name="copy-log"></a>Kopiuj dziennik

Przed skopiowaniem danych z urządzenie Data Box Pobierz dziennik kopiowania ze strony **Połącz i Kopiuj** .

Oto przykładowe dane wyjściowe *dziennika kopiowania* , gdy nie wystąpiły żadne błędy i wszystkie pliki zostały skopiowane podczas kopiowania danych z platformy Azure do urządzenie Data Box urządzenia.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Oto przykładowe dane wyjściowe, gdy *Dziennik kopiowania* zawiera błędy, a niektóre pliki nie zostały skopiowane z platformy Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Wyeksportować te pliki można na następujące sposoby: 

- Pliki, których nie można skopiować za pośrednictwem sieci, można przenieść. 
- Jeśli rozmiar danych był większy niż możliwa do użycia pojemność urządzenia, miało miejsce kopiowanie częściowe i wszystkie pliki, które nie zostały skopiowane, są wskazane w tym dzienniku. Możesz użyć tego dziennika jako danych wejściowych XML do utworzenia nowego zamówienia na usługę Data Box, a następnie skopiować te pliki.

### <a name="verbose-log"></a>Pełny dziennik

*Pełny dziennik* zawiera listę wszystkich plików pomyślnie wyeksportowanych z konta usługi Azure Storage. Dziennik zawiera również rozmiar pliku i obliczenia sumy kontrolnej.

Pełny dziennik zawiera informacje w następującym formacie:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Oto przykładowe dane wyjściowe dziennika pełnego. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Pełne dzienniki są również kopiowane do konta usługi Azure Storage. Domyślnie dzienniki są zapisywane do kontenera o nazwie `copylog` . Dzienniki są przechowywane z następującą konwencją nazewnictwa:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Ścieżka dziennika kopiowania jest również wyświetlana w bloku **Przegląd** dla portalu.

<!-- add a screenshot-->

Za pomocą tych dzienników można sprawdzić, czy pliki skopiowane z platformy Azure są zgodne z danymi, które zostały skopiowane na serwer lokalny. 

Użyj pełnego pliku dziennika:

- Aby sprawdzić poprawność nazw i liczby plików, które zostały skopiowane z urządzenie Data Box.
- Aby sprawdzić, czy nie są rzeczywiste rozmiary plików.
- Aby sprawdzić, czy *crc64* odpowiada ciągowi o wartości innej niż zero. Obliczenia cyklicznej kontroli nadmiarowości (CRC) są wykonywane podczas eksportowania z platformy Azure. CRCs z eksportu i po zakończeniu kopiowania danych z urządzenie Data Box na serwer lokalny można porównać. Niezgodność CRC wskazuje, że odpowiednie pliki nie zostały poprawnie skopiowane.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Pobierz łańcuch dzienników opieki po usunięciu danych

Po wymazaniu danych z urządzenie Data Box dysków zgodnie z wytycznymi dla programu NIST SP 800-88 z poprawkami 1 jest dostępny łańcuch dzienników opieki. Te dzienniki obejmują łańcuch inspekcji nadzoru i historię kolejności. Pliki BOM lub manifestów są również kopiowane z dziennikami inspekcji.

### <a name="chain-of-custody-audit-logs"></a>Łańcuch dzienników inspekcji opieki

Łańcuch dzienników inspekcji nadzoru zawiera informacje na temat włączania i uzyskiwania dostępu do udziałów na urządzenie Data Box lub Data Box Heavy, gdy znajdują się one poza centrum danych platformy Azure. Te dzienniki znajdują się w: `storage-account/azuredatabox-chainofcustodylogs`

Oto przykład dziennika inspekcji z urządzenie Data Box:

```output
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

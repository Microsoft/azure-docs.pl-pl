---
title: Administrowanie Azure Data Box/Azure Data Box Heavy przy użyciu lokalnego interfejsu użytkownika sieci Web
description: Opisuje sposób używania lokalnego interfejsu użytkownika sieci Web do administrowania urządzenie Data Box i Data Box Heavy urządzeń
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/07/2020
ms.author: alkohli
ms.openlocfilehash: 859eb6abd138f0660407618bba4550daf9158ab9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855009"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Użyj lokalnego interfejsu użytkownika sieci Web do administrowania urządzenie Data Box i Data Box Heavy

W tym artykule opisano niektóre zadania związane z konfiguracją i zarządzaniem wykonywane na urządzeniach urządzenie Data Box i Data Box Heavy. Urządzeniami urządzenie Data Box i Data Box Heavy można zarządzać za pomocą interfejsu użytkownika Azure Portal oraz lokalnego interfejsu użytkownika sieci Web dla urządzenia. Ten artykuł koncentruje się na zadaniach wykonywanych przy użyciu lokalnego interfejsu użytkownika sieci Web.

Lokalny interfejs użytkownika sieci Web dla urządzenie Data Box i dla Data Box Heavy służy do wstępnej konfiguracji urządzenia. Możesz również użyć lokalnego interfejsu użytkownika sieci Web, aby zamknąć lub ponownie uruchomić urządzenie, uruchomić testy diagnostyczne, zaktualizować oprogramowanie, wyświetlić kopie dzienników, wymazać dane lokalne z urządzenia i wygenerować pakiet pomocy technicznej dla pomoc techniczna firmy Microsoft. Na urządzeniu Data Box Heavy z dwoma niezależnymi węzłami możesz uzyskać dostęp do dwóch oddzielnych lokalnych interfejsów użytkownika sieci Web odpowiadających poszczególnym węzłom urządzenia.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>Generowanie pakietu dla pomocy technicznej

Jeśli napotkasz jakiekolwiek problemy z urządzeniem, możesz utworzyć pakiet dla pomocy technicznej z dzienników systemu. Pomoc techniczna firmy Microsoft korzysta z tego pakietu podczas rozwiązywania problemów.

Aby wygenerować pakiet pomocy technicznej, wykonaj następujące czynności:

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **skontaktuj się z pomocą techniczną**. Opcjonalnie zaznacz pole wyboru **Uwzględnij zrzuty pamięci**. Następnie wybierz pozycję **Utwórz pakiet pomocy technicznej**.

    Zrzut pamięci to zawartość pamięci urządzenia, zapisana po awarii systemu.

    Nie należy wybierać opcji **Dołącz zrzuty pamięci** , chyba że zostanie zażądana obsługa. Zebranie pakietu dla pomocy technicznej zawierającej zrzuty pamięci może zająć dużo czasu, a dane poufne są uwzględniane.

    ![Tworzenie pakietu dla pomocy technicznej 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Następuje zbieranie danych do pakietu dla pomocy technicznej. Ta operacja potrwa kilka minut, jeśli uwzględniono tylko dzienniki systemowe. Jeśli dołączysz zrzuty pamięci, zajmuje dużo dłużej.

    ![Tworzenie pakietu dla pomocy technicznej 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Po zakończeniu tworzenia pakietu pomocy technicznej wybierz pozycję **Pobierz pakiet pomocy technicznej**.

    ![Tworzenie pakietu dla pomocy technicznej 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Przejrzyj i wybierz lokalizację pobierania. Otwórz folder, aby wyświetlić jego zawartość.

    ![Tworzenie pakietu dla pomocy technicznej 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Wymazywanie danych lokalnych z urządzenia

Możesz użyć lokalnego interfejsu użytkownika sieci Web do wymazania danych lokalnych z urządzenia przed zwróceniem go do centrum danych platformy Azure.

> [!IMPORTANT]
> Nie można wycofać wymazania danych. Przed usunięciem danych lokalnych z urządzenia należy wykonać kopię zapasową plików.

Aby wymazać dane lokalne z urządzenia, wykonaj następujące kroki:

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **dane Wymaż**.
2. Wprowadź hasło urządzenia i wybierz polecenie **Wymaż dane**.

    ![Opcja wymazania danych dla urządzenia](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby kontynuować. Wymazywanie danych może zająć do 50 minut.

   Przed usunięciem z urządzenia należy wykonać kopię zapasową danych lokalnych. Nie można wycofać wymazania danych.

    ![Monit o potwierdzenie wymazania danych](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Zamykanie lub ponowne uruchamianie urządzenia

Możesz zamknąć lub ponownie uruchomić urządzenie przy użyciu lokalnego interfejsu użytkownika sieci Web. Zalecamy, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na hoście, a następnie na urządzeniu. Takie działanie minimalizuje możliwość uszkodzenia danych. Upewnij się, że kopiowanie danych nie jest w toku podczas zamykania urządzenia.

Aby zamknąć urządzenie, wykonaj następujące czynności.

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.

2. Wybierz pozycję **Zamknij**.

    ![Zamykanie urządzenia Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **OK** , aby kontynuować.

    ![Zamykanie urządzenia Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Aby ponownie włączyć wyłączone urządzenie, użyj przycisku zasilania na panelu przednim.

Aby ponownie uruchomić urządzenie Data Box, wykonaj następujące czynności.

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
2. Wybierz pozycję **Uruchom ponownie**.

    ![Ponowne uruchamianie urządzenia Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **OK** , aby kontynuować.

   Urządzenie zostaje wyłączone i uruchomione ponownie.

## <a name="download-bom-or-manifest-files"></a>Pobieranie zestawienia komponentów lub plików manifestu

Plik BOM lub manifest zawiera listę plików, które są kopiowane do urządzenie Data Box lub Data Box Heavy. Te pliki są generowane dla zamówienia importu podczas przygotowywania urządzenia do wysłania.

Przed rozpoczęciem wykonaj następujące kroki, aby pobrać pliki BOM lub manifestów dla zamówienia importu:

1. Przejdź do lokalnego interfejsu użytkownika sieci Web dla Twojego urządzenia. Sprawdź, czy urządzenie ukończyło krok **przygotowanie do wysłania** . Po zakończeniu przygotowywania urządzenia stan urządzenia ma wartość **Gotowe do wysyłki**.

    ![Urządzenie gotowe do wysyłki](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Wybierz pozycję **Pobierz listę plików** , aby pobrać listę plików, które zostały skopiowane do urządzenie Data Box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. W Eksploratorze plików są generowane oddzielne listy plików w zależności od protokołu używanego do nawiązywania połączenia z urządzeniem i używanego typu magazynu platformy Azure.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Pliki dla typu magazynu i protokołu połączenia](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   Poniższa tabela zawiera mapowanie nazw plików na typ magazynu usługi Azure Storage i użyty protokół połączenia.

    |Nazwa pliku  |Typ magazynu usługi Azure Storage  |Użyty protokół połączenia |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blokowe obiekty blob         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Stronicowe obiekty blob         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Stronicowe obiekty blob         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blokowe obiekty blob         |REST         |

Ta lista umożliwia weryfikację plików przekazanych do konta usługi Azure Storage po powrocie urządzenia Data Box do centrum danych platformy Azure. Poniżej przedstawiono przykładowy plik manifestu.

> [!NOTE]
> Na Data Box Heavy są obecne dwa zestawy list plików (pliki BOM) odpowiadające dwóm węzłom na urządzeniu.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Ten plik zawiera listę wszystkich plików, które zostały skopiowane na urządzenie Data Box lub Data Box Heavy. W tym pliku wartość *crc64* odnosi się do sumy kontrolnej wygenerowanej dla odpowiadającego jej pliku.

## <a name="view-available-capacity-of-the-device"></a>Wyświetlanie dostępnej pojemności urządzenia

Aby wyświetlić dostępną i używaną pojemność urządzenia, można użyć jego pulpitu nawigacyjnego.

1. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Wyświetl pulpit nawigacyjny**.
2. W obszarze **Połącz i skopiuj** jest wyświetlane wolne i zajęte miejsce na urządzeniu.

    ![Wyświetlanie dostępnej pojemności](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Pomijanie weryfikacji sumy kontrolnej

Suma kontrolna jest generowana domyślnie dla danych podczas przygotowywania do wysłania. W niektórych rzadkich przypadkach, w zależności od typu danych (małe rozmiary plików), wydajność może zostać zmniejszona. W takich przypadkach można pominąć generowanie sumy kontrolnej.

Obliczenia sum kontrolnych podczas przygotowywania do wysłania są wykonywane tylko w przypadku zamówień importu, a nie dla zamówień eksportu.

Zdecydowanie zalecamy, aby nie wyłączać sum kontrolnych, chyba że wydajność jest poważnie naruszona.

1. W prawym górnym rogu lokalnego interfejsu użytkownika sieci Web urządzenia przejdź do pozycji **Ustawienia**.

    ![Wyłączanie sumy kontrolnej](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Wyłącz** weryfikację sumy kontrolnej.
3. Wybierz przycisk **Zastosuj**.

> [!NOTE]
> Opcja obliczenia pominięcia sum kontrolnych jest dostępna tylko wtedy, gdy Azure Data Box jest odblokowany. Nie zobaczysz tej opcji, gdy urządzenie jest zablokowane.

## <a name="enable-smb-signing"></a>Włączanie podpisywania protokołu SMB

Podpisywanie bloku komunikatów serwera (SMB) to funkcja, za pomocą której komunikacja przy użyciu protokołu SMB może być podpisana cyfrowo na poziomie pakietu. Podpisywanie zapobiega atakom, które modyfikują pakiety SMB podczas przesyłania.

Aby uzyskać więcej informacji dotyczących podpisywania protokołu SMB, zobacz [Omówienie podpisywania bloku komunikatów serwera](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Aby włączyć podpisywanie protokołu SMB na urządzeniu platformy Azure:

1. W prawym górnym rogu lokalnego interfejsu użytkownika sieci Web urządzenia wybierz pozycję **Ustawienia**.

    ![Otwórz ustawienia](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Włącz** Podpisywanie SMB.

    ![Włączanie podpisywania protokołu SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Wybierz przycisk **Zastosuj**.
4. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
5. Wybierz pozycję **Uruchom ponownie**.

## <a name="enable-backup-operator-privileges"></a>Włącz uprawnienia operatora kopii zapasowych

Użytkownicy interfejsu użytkownika sieci Web domyślnie mają uprawnienia operatora kopii zapasowych w udziałach SMB. Jeśli nie chcesz tego zrobić, użyj **uprawnień operatora Włącz** , aby wyłączyć lub włączyć uprawnienia.

Aby uzyskać więcej informacji, zobacz Operatory kopii zapasowych w [Active Directory grupy zabezpieczeń](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Aby włączyć uprawnienia operatorów kopii zapasowych na urządzeniu platformy Azure:

1. W prawym górnym rogu lokalnego interfejsu użytkownika sieci Web urządzenia wybierz pozycję **Ustawienia**.

   ![Otwórz ustawienia urządzenie Data Box-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Włącz** Uprawnienia operatora kopii zapasowych.

   ![Włącz uprawnienia operatora kopii zapasowych](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Wybierz pozycję Zastosuj**.
4. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
5. Wybierz pozycję **Uruchom ponownie**.

## <a name="enable-acls-for-azure-files"></a>Włącz listy ACL dla Azure Files

Metadane w plikach są domyślnie transferowane, gdy użytkownik przekaże dane za pośrednictwem protokołu SMB do urządzenie Data Box. Metadane obejmują listy kontroli dostępu (ACL), atrybuty plików i sygnatury czasowe. Jeśli nie chcesz tego zrobić, użyj **list ACL dla usługi Azure Files** , aby wyłączyć lub włączyć tę funkcję.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Aby przesłać metadane z plikami, musisz być operatorem kopii zapasowych. W przypadku korzystania z tej funkcji upewnij się, że użytkownicy lokalni interfejsu użytkownika sieci Web są operatorami kopii zapasowych. Zobacz [Włączanie uprawnień operatora kopii zapasowych](#enable-backup-operator-privileges).

Aby włączyć transfer list ACL dla usługi Azure Files:

1. W prawym górnym rogu lokalnego interfejsu użytkownika sieci Web urządzenia wybierz pozycję **Ustawienia**.

    ![Otwórz ustawienia urządzenie Data Box-2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Włącz** Listy ACL dla plików platformy Azure.

     ![Włączanie list ACL dla usługi Azure Files](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Wybierz przycisk **Zastosuj**.
4. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
5. Wybierz pozycję **Uruchom ponownie**.

## <a name="enable-tls-11"></a>Włącz protokół TLS 1,1

Domyślnie Azure Data Box używa Transport Layer Security (TLS) 1,2 do szyfrowania, ponieważ jest bezpieczniejsze niż TSL 1,1. Jednak w przypadku korzystania z przeglądarki w celu uzyskania dostępu do danych, które nie obsługują protokołu TLS 1,2, można włączyć protokół TLS 1,1.

Aby uzyskać więcej informacji dotyczących protokołu TLS, zobacz [Azure Data Box Gateway zabezpieczenia](../databox-gateway/data-box-gateway-security.md).

Aby włączyć protokół TLS 1,1 na urządzeniu platformy Azure:

1. W prawym górnym rogu lokalnego interfejsu użytkownika sieci Web urządzenia wybierz pozycję **Ustawienia**.

    ![Otwórz ustawienia urządzenie Data Box-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Włącz** TLS 1,1.

    ![Włącz protokół TLS 1,1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Wybierz przycisk **Zastosuj**.
4. W lokalnym internetowym interfejsie użytkownika przejdź do pozycji **Zamknij lub uruchom ponownie**.
5. Wybierz pozycję **Uruchom ponownie**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać urządzenie Data Box i Data Box Heavy za pośrednictwem Azure Portal](data-box-portal-admin.md).

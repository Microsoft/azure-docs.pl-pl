---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183271"
---
> [!NOTE]
> Podczas wprowadzania zmian w konfiguracji SPZ karty StorSimple dla programu SharePoint należy zalogować się przy użyciu konta użytkownika należącego do grupy Administratorzy domeny. Ponadto należy uzyskać dostęp do strony konfiguracji z przeglądarki działającej na tym samym hoście co Administracja centralna.
> 
> 

#### <a name="to-configure-rbs"></a>Aby skonfigurować strukturę SPZ
1. Otwórz stronę administracji centralnej programu SharePoint i przejdź do **ustawień systemowych**. 
2. W sekcji **Azure StorSimple** kliknij pozycję **Konfiguruj StorSimple adapter**.
   
    ![Konfigurowanie karty StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na stronie **Konfigurowanie karty StorSimple** :
   
   1. Upewnij się, że pole wyboru **Włącz ścieżkę edytowania** jest zaznaczone.
   2. W polu tekstowym wpisz ścieżkę Universal Naming Convention (UNC) magazynu obiektów BLOB.
      
      > [!NOTE]
      > Wolumin magazynu obiektów BLOB musi być hostowany na woluminie iSCSI skonfigurowanym na urządzeniu StorSimple.

   3. Kliknij przycisk **Włącz** poniżej każdej z baz danych zawartości, które chcesz skonfigurować dla magazynu zdalnego.
      
      > [!NOTE]
      > Magazyn obiektów BLOB musi być udostępniony i dostępny dla wszystkich serwerów frontonu sieci Web (WFE), a konto użytkownika skonfigurowane dla farmy serwerów programu SharePoint musi mieć dostęp do udziału.
      
      ![Włączanie dostawcy SPZ](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Po włączeniu lub wyłączeniu SPZ zostanie również wyświetlony następujący komunikat.
      
      ![Skonfiguruj opcję Włącz wyłączanie karty StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kliknij przycisk **Aktualizuj** , aby zastosować konfigurację. Po kliknięciu przycisku **Aktualizuj** stan konfiguracji SPZ zostanie zaktualizowany na wszystkich serwerach WFE, a cała Farma będzie włączona. Zostanie wyświetlony następujący komunikat.
      
      ![Komunikat konfiguracji karty](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Jeśli konfigurujesz strukturę SPZ farmy programu SharePoint z bardzo dużą liczbą baz danych (ponad 200), Strona sieci Web Administracja centralna programu SharePoint może przekroczyć limit czasu. W takim przypadku należy odświeżyć stronę. Nie ma to wpływu na proces konfiguracji.

4. Sprawdź konfigurację:
   
   1. Zaloguj się do witryny administracji centralnej programu SharePoint i przejdź do strony **Konfigurowanie karty StorSimple** .
   2. Sprawdź szczegóły konfiguracji, aby upewnić się, że są one zgodne z wprowadzonymi ustawieniami. 
5. Sprawdź, czy struktura SPZ działa prawidłowo:
   
   1. Przekaż dokument do programu SharePoint. 
   2. Przejdź do skonfigurowanej ścieżki UNC. Upewnij się, że struktura katalogów SPZ została utworzona i że zawiera przekazany obiekt.
6. Obowiązkowe `Migrate()` Do migracji istniejącej zawartości obiektu BLOB do urządzenia StorSimple można użyć polecenia cmdlet programu PowerShell Microsoft SPZ dołączonego do programu SharePoint. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ w programie SharePoint 2013][6] lub [Migrowanie zawartości do lub z SPZ (SharePoint Foundation 2010)][7].
7. Obowiązkowe W przypadku instalacji testowych można sprawdzić, czy obiekty blob zostały przeniesione z bazy danych zawartości w następujący sposób: 
   
   1. Uruchom Management Studio SQL.
   2. Uruchom kwerendę ListBlobsInDB_2010. SQL lub ListBlobsInDB_2013. SQL w następujący sposób.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Jeśli struktura SPZ została prawidłowo skonfigurowana, w kolumnie SizeOfContentInDB powinna zostać wyświetlona wartość NULL dla każdego obiektu, który został przekazany i pomyślnie zewnętrzny przy użyciu SPZ.
8. Obowiązkowe Po skonfigurowaniu SPZ i przeniesieniu całej zawartości obiektu BLOB do urządzenia StorSimple można przenieść bazę danych zawartości na urządzenie. Jeśli zdecydujesz się przenieść bazę danych zawartości, zalecamy skonfigurowanie magazynu bazy danych zawartości na urządzeniu jako woluminu podstawowego. Następnie należy użyć ustalonych SQL Server najlepszych rozwiązań do migracji bazy danych zawartości na urządzenie StorSimple. 
   
   > [!NOTE]
   > Przeniesienie bazy danych zawartości na urządzenie jest obsługiwane tylko dla serii StorSimple 8000 (nie jest to obsługiwane w przypadku serii 5000 lub 7000).
   
   Jeśli obiekty blob i baza danych zawartości są przechowywane na oddzielnych woluminach na urządzeniu StorSimple, zalecamy ich skonfigurowanie w tym samym kontenerze woluminów. Dzięki temu kopie zapasowe będą tworzone razem.
   
   > [!WARNING]
   > Jeśli nie włączono SPZ, nie zalecamy przeniesienia bazy danych zawartości na urządzenie StorSimple. To jest Nietestowana konfiguracja.
   
9. Przejdź do następnego kroku: [Konfigurowanie wyrzucania elementów bezużytecznych](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx

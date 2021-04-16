---
title: Przywracanie plików do systemu Windows Server przy użyciu agenta MARS
description: W tym artykule dowiesz się, jak przywrócić dane przechowywane na platformie Azure na serwer z systemem Windows lub komputer z systemem Windows przy użyciu agenta Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 7ca0787ec38e1bc22b62e756c7ee56c5c9e93493
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517338"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Przywracanie plików do systemu Windows Server przy użyciu agenta MARS

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, należy użyć Kreatora odzyskiwania danych w agencie Microsoft Azure Recovery Services (MARS). Oto co możesz zrobić:

* Przywróć dane na tę samą maszynę, z której zostały wykonane kopie zapasowe.
* Przywracanie danych do alternatywnej maszyny.

Użyj funkcji natychmiastowego przywracania, aby zainstalować zapisywalny migawkę punktu odzyskiwania jako wolumin odzyskiwania. Następnie można eksplorować wolumin odzyskiwania i kopiować pliki na komputer lokalny, w ten sposób selektywnie przywracając pliki.

> [!NOTE]
> Jeśli chcesz przywrócić dane za pomocą funkcji natychmiastowego przywracania, wymagana jest aktualizacja Azure Backup ze stycznia [2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) r. Ponadto dane kopii zapasowej muszą być chronione w magazynach w lokalizacjach regionalnych wymienionych w artykule pomocy technicznej. Zapoznaj się z aktualizacją ze [stycznia 2017 Azure Backup,](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) aby uzyskać najnowszą listę lokalizacji regionalnych, które obsługują natychmiastowe przywracanie.
>

Używaj natychmiastowego przywracania z magazynami usługi Recovery Services w Azure Portal. Jeśli dane są przechowywane w magazynach usługi Backup, zostały one przekonwertowane na magazyny usługi Recovery Services. Jeśli chcesz użyć natychmiastowego przywracania, pobierz aktualizację usługi MARS i postępuj zgodnie z procedurami, które wspominają o błyskawicznym przywracaniu.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Używanie natychmiastowego przywracania do odzyskiwania danych na tę samą maszynę

Jeśli przypadkowo usunięto plik i chcesz przywrócić go na tym samym komputerze (z którego generuje się kopię zapasową), następujące kroki pomogą Ci odzyskać dane.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie została zainstalowana przystawka, wyszukaj na komputerze lub serwerze **Microsoft Azure Backup**.

    Aplikacja desktop powinna pojawić się w wynikach wyszukiwania.

2. Wybierz **pozycję Odzyskaj** dane, aby uruchomić kreatora.

    ![Zrzut ekranu Azure Backup z wyróżnionem daniem Odzyskaj dane (przywróć na tej samej maszynie)](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Wprowadzenie,** aby przywrócić dane na tym samym serwerze lub komputerze, wybierz pozycję **Ten serwer ( `<server name>` )**  >  **Dalej.**

    ![Zrzut ekranu przedstawiający stronę kreatora odzyskiwania Wprowadzenie danych (przywracanie na tę samą maszynę)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na stronie **Wybieranie trybu odzyskiwania** wybierz pozycję Pojedyncze pliki i **foldery** > **Dalej.**

    ![Zrzut ekranu przedstawiający stronę Kreator odzyskiwania danych Wybieranie trybu odzyskiwania (przywracanie na tę samą maszynę)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Opcja przywracania poszczególnych plików i folderów wymaga .NET Framework 4.5.2 lub nowszej. Jeśli nie widzisz opcji  Pojedyncze pliki i foldery, musisz uaktualnić program .NET Framework do wersji 4.5.2 lub nowszej, a następnie spróbuj ponownie.

   > [!TIP]
   > Opcja **Pojedyncze pliki i foldery** umożliwia szybki dostęp do danych punktu odzyskiwania. Jest odpowiednia do odzyskiwania pojedynczych plików i jest zalecana dla całkowitego rozmiaru mniejszego niż 80 GB. Oferuje szybkość transferu lub kopiowania do 6 MB/s podczas odzyskiwania. Opcja **Wolumin** umożliwia odzyskanie wszystkich kopii zapasowej danych na określonym woluminie. Ta opcja zapewnia większą szybkość transferu (do 40 MB/s) i jest zalecana do odzyskiwania dużych ilości danych lub całych woluminów.

5. Na stronie **Wybierz wolumin i datę** wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubiona** data wskazuje dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty jest dostępnych wiele punktów  odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego Godzina.

    ![Zrzut ekranu przedstawiający stronę Wybieranie woluminu i daty w Kreatorze odzyskiwania danych (przywracanie na tę samą maszynę)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po wybraniu punktu odzyskiwania do przywrócenia wybierz pozycję **Zainstaluj**.

    Azure Backup zainstaluje lokalny punkt odzyskiwania i użyje go jako woluminu odzyskiwania.

7. Na stronie **Przeglądaj i odzyskaj pliki** wybierz pozycję Przeglądaj, aby otworzyć Eksplorator Windows i znaleźć odpowiednie pliki i foldery. 

    ![Zrzut ekranu przedstawiający stronę Przeglądanie i odzyskiwanie plików w Kreatorze odzyskiwania danych (przywracanie na tę samą maszynę)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. W Eksplorator Windows skopiuj pliki i foldery, które chcesz przywrócić, a następnie wklej je do dowolnej lokalizacji lokalnej na serwerze lub komputerze. Pliki można otwierać lub przesyłać strumieniowo bezpośrednio z woluminu odzyskiwania i sprawdzać, czy są odzyskiwane prawidłowe wersje.

    ![Zrzut ekranu przedstawiający Eksplorator Windows z wyróżnionem kopiowaniem (przywracanie na tę samą maszynę)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po zakończeniu na stronie Przeglądaj i odzyskaj **pliki** wybierz pozycję **Odinstaluj**. Następnie wybierz **pozycję Tak,** aby potwierdzić, że chcesz odinstalować wolumin.

    ![Zrzut ekranu przedstawiający stronę Przeglądanie i odzyskiwanie plików w Kreatorze odzyskiwania danych (przywracanie na tym samym komputerze) — potwierdzanie odinstalowania woluminu odzyskiwania](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz opcji Odinstaluj, wolumin odzyskiwania pozostanie zainstalowany przez 6 godzin od czasu, gdy został zainstalowany. Jednak czas instalacji jest wydłużony do maksymalnie 24 godzin w przypadku trwającego kopiowania plików. Podczas zainstalowanych woluminów nie będą uruchamiane żadne operacje tworzenia kopii zapasowej. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin jest zainstalowany, będzie uruchamiana po odinstalowyniu woluminu odzyskiwania.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Używanie natychmiastowego przywracania do przywracania danych na inną maszynę

Jeśli cały serwer zostanie utracony, nadal możesz odzyskać dane z Azure Backup na inną maszynę. Poniższe kroki ilustrują przepływ pracy.

Te kroki obejmują następującą terminologię:

* *Maszyna źródłowa* — oryginalna maszyna, z której została podjęta kopia zapasowa, a która jest obecnie niedostępna.
* *Maszyna docelowa* — maszyna, do której są odzyskiwane dane.
* *Przykładowy magazyn* — magazyn usługi Recovery Services, w którym zarejestrowano maszynę źródłową i docelową.

> [!NOTE]
> Kopii zapasowych nie można przywrócić na maszynie docelowej, na których działa wcześniejsza wersja systemu operacyjnego. Na przykład kopię zapasową z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 7 (lub nowszym). Nie można przywrócić kopii zapasowej Windows 10 komputera z systemem Windows 7.
>
>

1. Otwórz **Microsoft Azure Backup** przystawki na maszynie docelowej.

2. Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowane w tym samym magazynie usługi Recovery Services.

3. Wybierz **pozycję Odzyskaj** dane, aby otworzyć **Kreatora odzyskiwania danych.**

    ![Zrzut ekranu przedstawiający Azure Backup z wyróżnionem daniem Odzyskaj dane (przywróć do maszyny alternatywnej)](./media/backup-azure-restore-windows-server/recover.png)

4. Na stronie **Wprowadzenie** wybierz pozycję **Inny serwer.**

    ![Zrzut ekranu przedstawiający stronę kreatora odzyskiwania Wprowadzenie danych (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń magazynu odpowiadający przykładowemu magazynowi, a następnie wybierz pozycję **Dalej.**

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub [wygasł),](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) pobierz nowy plik poświadczeń magazynu z przykładowego magazynu w Azure Portal. Po poświadczeniu prawidłowego magazynu zostanie wyświetlona nazwa odpowiedniego magazynu kopii zapasowych.

6. Na stronie **Wybieranie serwera kopii** zapasowej wybierz maszynę źródłową z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający stronę Kreator odzyskiwania danych Wybieranie serwera kopii zapasowej (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję Pojedyncze pliki i **foldery**  >  **Dalej.**

    ![Zrzut ekranu przedstawiający stronę Kreator odzyskiwania danych Wybieranie trybu odzyskiwania (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na stronie **Wybierz wolumin i datę** wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. Daty **pogrubione** wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w ciągu jednej daty jest dostępnych wiele punktów  odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego Godzina.

    ![Zrzut ekranu przedstawiający stronę Wybieranie woluminu i daty w Kreatorze odzyskiwania danych (przywracanie na maszynę alternatywną)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Wybierz **pozycję Zainstaluj,** aby lokalnie zainstalować punkt odzyskiwania jako wolumin odzyskiwania na maszynie docelowej.

10. Na stronie **Przeglądaj i odzyskaj pliki** wybierz pozycję Przeglądaj, aby otworzyć Eksplorator Windows i znaleźć odpowiednie pliki i foldery. 

    ![Zrzut ekranu przedstawiający stronę Przeglądanie i odzyskiwanie plików w Kreatorze odzyskiwania danych (przywracanie na inną maszynę)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksplorator Windows skopiuj pliki i foldery z woluminu odzyskiwania i wklej je do lokalizacji maszyny docelowej. Pliki można otwierać lub przesyłać strumieniowo bezpośrednio z woluminu odzyskiwania i sprawdzać, czy poprawne wersje zostały odzyskane.

    ![Zrzut ekranu przedstawiający Eksplorator Windows z wyróżnionem kopiowaniem (przywracanie do alternatywnej maszyny)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu na stronie Przeglądaj i odzyskaj **pliki** wybierz pozycję **Odinstaluj**. Następnie wybierz **pozycję Tak,** aby potwierdzić, że chcesz odinstalować wolumin.

    ![Odinstaluj wolumin (przywróć do alternatywnej maszyny)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz opcji Odinstaluj , wolumin odzyskiwania pozostanie zainstalowany przez 6 godzin od czasu, w którym został zainstalowany. Jednak czas instalacji jest wydłużony do maksymalnie 24 godzin w przypadku trwającego kopiowania plików. Podczas zainstalowanych woluminów nie będą uruchamiane żadne operacje tworzenia kopii zapasowej. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin jest zainstalowany, będzie uruchamiana po odinstalowyniu woluminu odzyskiwania.
    >

## <a name="next-steps"></a>Następne kroki

* Teraz, po odzyskaniu plików i folderów, możesz [zarządzać kopiami zapasami](backup-azure-manage-windows-server.md).

* Znajdź [często zadawane pytania dotyczące kopii zapasowej plików i folderów.](backup-azure-file-folder-backup-faq.yml)

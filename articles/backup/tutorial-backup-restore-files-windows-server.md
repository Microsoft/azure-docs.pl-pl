---
title: 'Samouczek: odzyskiwanie elementów do systemu Windows Server'
description: W tym samouczku dowiesz się, jak używać agenta Microsoft Azure Recovery Services Agent (MARS) do odzyskiwania elementów z platformy Azure do systemu Windows Server.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: c9258b7f95337330e4f1de36e389f6b8f2276976
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78672948"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Odzyskiwanie plików z platformy Azure na serwer z systemem Windows

Usługa Azure Backup umożliwia odzyskiwanie wybranych elementów z kopii zapasowych serwera z systemem Windows. Odzyskiwanie pojedynczych plików to przydatny sposób szybkiego przywracania przypadkowo usuniętych plików. W tym samouczku opisano, jak przy użyciu agenta usługi Microsoft Azure Recovery Services (MARS) odzyskać elementy z kopii zapasowych utworzonych uprzednio na platformie Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Rozpoczynanie odzyskiwana pojedynczych plików
> * Wybieranie punktu odzyskiwania
> * Przywracanie elementów z punktu odzyskiwania

W tym samouczku założono, że wykonano już czynności opisane w temacie [Tworzenie kopii zapasowej serwerów z systemem Windows na platformie Azure](backup-windows-with-mars-agent.md) i utworzono co najmniej jedną kopię zapasową plików z serwera z systemem Windows na platformie Azure.

## <a name="initiate-recovery-of-individual-items"></a>Rozpoczynanie odzyskiwana pojedynczych plików

Wraz z agentem usługi Microsoft Azure Recovery Services (MARS) jest instalowany przydatny kreator Microsoft Azure Backup oparty na interfejsie użytkownika. Kreator Microsoft Azure Backup współdziała z agentem usługi MARS w celu pobrania danych kopii zapasowej z punktów odzyskiwania przechowywanych na platformie Azure. Przy użyciu kreatora Microsoft Azure Backup możesz zidentyfikować pliki lub foldery, które chcesz przywrócić na serwer z systemem Windows.

1. Otwórz przystawkę **Microsoft Azure Backup**. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. Po otwarciu kreatora kliknij pozycję **Odzyskaj dane** w **okienku akcji** konsoli kreatora, aby uruchomić **Kreatora odzyskiwania danych**.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na stronie **Wprowadzenie** wybierz pozycję **Ten serwer (nazwa serwera)** i kliknij przycisk **Dalej**.

4. Na stronie **Wybierz tryb odzyskiwania** wybierz pozycję **Pojedyncze pliki i foldery**, a następnie kliknij przycisk **Dalej**, aby rozpocząć proces wyboru punktu odzyskiwania.

5. Na stronie **Wybierz wolumin i datę** wybierz wolumin zawierający pliki lub foldery, które chcesz przywrócić, a następnie kliknij przycisk **Zainstaluj**. Wybierz datę, a następnie wybierz z menu rozwijanego godzinę odpowiadającą wybranemu punktowi odzyskiwania. **Pogrubioną czcionką** są wyróżnione daty, dla których jest dostępny co najmniej jeden punkt odzyskiwania.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Po kliknięciu przycisku **Zainstaluj** usługa Azure Backup udostępnia punkt odzyskiwania jako dysk. Możesz przeglądać pliki na dysku i odzyskiwać je.

## <a name="restore-items-from-a-recovery-point"></a>Przywracanie elementów z punktu odzyskiwania

1. Po zainstalowaniu woluminu odzyskiwania kliknij przycisk **Przeglądaj**, aby otworzyć Eksploratora Windows i znaleźć pliki i foldery, które chcesz odzyskać.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Możesz otworzyć pliki bezpośrednio z poziomu woluminu odzyskiwania, aby je zweryfikować.

2. W Eksploratorze Windows skopiuj pliki i/lub foldery, które chcesz przywrócić, a następnie wklej je w wybranej lokalizacji na serwerze.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Po zakończeniu przywracania plików i/lub folderów kliknij przycisk **Odinstaluj** na stronie **Przeglądanie i odzyskiwanie plików** w **Kreatorze odzyskiwania danych**.

    ![Oczekiwanie na kopię zapasową](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Kliknij przycisk **Tak**, aby potwierdzić, że chcesz odinstalować wolumin.

    Po odinstalowaniu migawki zobaczysz komunikat **Ukończono zadanie** w okienku **Zadania** w konsoli agenta.

## <a name="next-steps"></a>Następne kroki

To ostatni samouczek dotyczący tworzenia kopii zapasowej i przywracania danych serwera z systemem Windows na platformie Azure. Aby uzyskać więcej informacji na temat usługi Azure Backup, zobacz przykładowy skrypt programu PowerShell umożliwiający tworzenie kopii zapasowej zaszyfrowanych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Back up encrypted VM (Tworzenie kopii zapasowej zaszyfrowanej maszyny wirtualnej)](./scripts/backup-powershell-sample-backup-encrypted-vm.md)

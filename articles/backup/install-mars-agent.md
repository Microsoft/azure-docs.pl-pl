---
title: Zainstaluj agenta Microsoft Azure Recovery Services (MARS)
description: Dowiedz się, jak zainstalować agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: fb59c245c469791233ce973b00426a127b116535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975305"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalowanie agenta Azure Backup MARS

W tym artykule wyjaśniono, jak zainstalować agenta Microsoft Azure Recovery Services (MARS). Usługa MARS jest również znana jako agent Azure Backup.

## <a name="about-the-mars-agent"></a>Informacje o agencie MARS

Azure Backup używa agenta MARS do tworzenia kopii zapasowych plików, folderów i stanu systemu z maszyn lokalnych i maszyn wirtualnych platformy Azure. Te kopie zapasowe są przechowywane w magazynie Recovery Services na platformie Azure. Można uruchomić agenta:

* Bezpośrednio na maszynach lokalnych z systemem Windows. Te maszyny mogą tworzyć kopie zapasowe bezpośrednio do magazynu Recovery Services na platformie Azure.
* Na maszynach wirtualnych platformy Azure, które korzystają z systemu Windows obok rozszerzenia kopii zapasowej maszyny wirtualnej platformy Azure. Agent tworzy kopie zapasowe określonych plików i folderów na maszynie wirtualnej.
* Na wystąpieniu serwera Microsoft Azure Backup (serwera usługi MAB) lub na serwerze programu System Center Data Protection Manager (DPM). W tym scenariuszu maszyny i obciążenia wykonują kopie zapasowe do serwera usługi MAB lub Data Protection Manager. Następnie serwera usługi MAB lub Data Protection Manager używa agenta MARS do tworzenia kopii zapasowych w magazynie na platformie Azure.

Dane, które są dostępne dla kopii zapasowej, zależą od tego, gdzie jest zainstalowany agent programu.

> [!NOTE]
> Ogólnie rzecz biorąc, należy wykonać kopię zapasową maszyny wirtualnej platformy Azure przy użyciu rozszerzenia Azure Backup na maszynie wirtualnej. Ta metoda tworzy kopię zapasową całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej, zainstaluj i Użyj agenta MARS obok rozszerzenia. Aby uzyskać więcej informacji, zobacz [Architektura wbudowanej kopii zapasowej maszyny wirtualnej platformy Azure](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się [, jak Azure Backup używa agenta Mars do tworzenia kopii zapasowych maszyn z systemem Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Dowiedz się więcej o [architekturze tworzenia kopii zapasowych](backup-architecture.md#architecture-back-up-to-dpmmabs) , w której jest uruchomiony agent Mars na pomocniczym serwerze serwera usługi mab lub Data Protection Manager.
* Zapoznaj się z [obsługiwanymi informacjami i możliwościami tworzenia kopii zapasowych](backup-support-matrix-mars-agent.md) przez agenta Mars.
* Upewnij się, że masz konto platformy Azure, jeśli musisz utworzyć kopię zapasową serwera lub klienta na platformie Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatny jeden](https://azure.microsoft.com/free/) w zaledwie kilka minut.
* Sprawdź dostęp do Internetu na maszynach, dla których chcesz utworzyć kopię zapasową.
* Upewnij się, że użytkownik wykonujący instalację i Konfiguracja agenta MARS ma uprawnienia administratora lokalnego na serwerze, który ma być chroniony.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.
* Aby zmniejszyć koszty usługi Azure Storage, można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) .

Aby zmodyfikować typ replikacji magazynu:

1. W nowym magazynie wybierz pozycję **Właściwości** w sekcji **Ustawienia** .

1. Na stronie **Właściwości** w obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

    ![Aktualizacja konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.
>

### <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na następujące adresy URL i adresy IP:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Korzystanie z usługi Azure ExpressRoute

Można utworzyć kopię zapasową danych za pośrednictwem usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

Aby korzystać z publicznej komunikacji równorzędnej, należy najpierw zapewnić dostęp do następujących domen i adresów:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Aby użyć komunikacji równorzędnej firmy Microsoft, wybierz następujące usługi, regiony i odpowiednie wartości społeczności:

* Azure Active Directory (12076:5060)
* Region świadczenia usługi Azure, zgodnie z lokalizacją magazynu Recovery Services
* Azure Storage, zgodnie z lokalizacją magazynu Recovery Services

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md).

> [!NOTE]
> Publiczna Komunikacja równorzędna jest przestarzała dla nowych obwodów.

Wszystkie poprzednie adresy URL i adresy IP używają protokołu HTTPS na porcie 443.

### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Pobieranie agenta MARS

Pobierz agenta MARS, aby można było go zainstalować na maszynach, dla których chcesz utworzyć kopię zapasową.

Jeśli Agent został już zainstalowany na wszystkich komputerach, upewnij się, że korzystasz z najnowszej wersji agenta. Znajdź najnowszą wersję w portalu lub przejdź bezpośrednio do [pobierania](https://aka.ms/azurebackup_agent).

1. W magazynie w obszarze **wprowadzenie**wybierz pozycję **kopia zapasowa**.

    ![Otwórz cel kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. W obszarze **gdzie działa Twoje obciążenie?** wybierz pozycję **lokalnie**. Zaznacz tę opcję, nawet jeśli chcesz zainstalować agenta MARS na maszynie wirtualnej platformy Azure.
1. W obszarze **co chcesz utworzyć kopię zapasową?** wybierz pozycję **pliki i foldery**. Możesz również wybrać pozycję **stan systemu**. Dostępnych jest wiele innych opcji, ale te opcje są obsługiwane tylko wtedy, gdy jest używany pomocniczy serwer zapasowy. Wybierz pozycję **Przygotuj infrastrukturę**.

    ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. W obszarze **Przygotowanie infrastruktury**w obszarze **Zainstaluj Recovery Services agenta**Pobierz agenta Mars.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. W menu Pobierz wybierz pozycję **Zapisz**. Domyślnie plik *MARSagentinstaller.exe* jest zapisywany w folderze Pobrane.

1. Wybierz opcję **już Pobierz lub Użyj najnowszego agenta Recovery Services**, a następnie Pobierz poświadczenia magazynu.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Wybierz pozycję **Zapisz**. Plik zostanie pobrany do folderu pobierania. Nie można otworzyć pliku poświadczeń magazynu.

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Uruchom plik *MARSagentinstaller.exe* na maszynach, dla których chcesz utworzyć kopię zapasową.
1. W Kreatorze instalacji agenta MARS wybierz pozycję **ustawienia instalacji**. W tym miejscu wybierz miejsce instalacji agenta i wybierz lokalizację pamięci podręcznej. Następnie wybierz pozycję **Dalej**.
   * Azure Backup używa pamięci podręcznej do przechowywania migawek danych przed wysłaniem ich do platformy Azure.
   * W lokalizacji pamięci podręcznej powinna występować ilość wolnego miejsca równa co najmniej 5% rozmiaru danych, których kopia zapasowa ma zostać wykonana.

    ![Wybieranie ustawień instalacji w Kreatorze instalacji agenta MARS](./media/backup-configure-vault/mars1.png)

1. W obszarze **Konfiguracja serwera proxy**Określ, w jaki sposób Agent uruchomiony na komputerze z systemem Windows będzie łączył się z Internetem. Następnie wybierz pozycję **Dalej**.

   * Jeśli używasz niestandardowego serwera proxy, określ wymagane ustawienia serwera proxy i poświadczenia.
   * Należy pamiętać, że Agent musi mieć dostęp do [określonych adresów URL](#before-you-start).

    ![Konfigurowanie dostępu do Internetu w Kreatorze MARS](./media/backup-configure-vault/mars2.png)

1. Na potrzeby **instalacji**Przejrzyj wymagania wstępne i wybierz pozycję **Zainstaluj**.
1. Po zainstalowaniu agenta wybierz pozycję przechodzenie **do rejestracji**.
1. W oknie **Rejestrowanie**  >  **magazynu**kreatora, Wyszukaj i wybierz pobrany plik poświadczeń. Następnie wybierz pozycję **Dalej**.

    ![Dodawanie poświadczeń magazynu za pomocą Kreatora rejestrowania serwera](./media/backup-configure-vault/register1.png)

1. Na stronie **ustawienie szyfrowania** określ hasło, które będzie używane do szyfrowania i odszyfrowywania kopii zapasowych na komputerze. [Zobacz tutaj](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) , aby uzyskać więcej informacji na temat dozwolonych znaków hasła.

    * Zapisz hasło w bezpiecznej lokalizacji. Jest on potrzebny do przywrócenia kopii zapasowej.
    * Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie będzie mogła odzyskać danych kopii zapasowej.

1. Wybierz pozycję **Zakończ**. Agent jest teraz zainstalowany, a komputer jest zarejestrowany w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="next-steps"></a>Następne kroki

Informacje na temat [tworzenia kopii zapasowych maszyn z systemem Windows przy użyciu agenta Azure Backup Mars](backup-windows-with-mars-agent.md)

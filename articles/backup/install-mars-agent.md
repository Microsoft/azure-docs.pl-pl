---
title: Instalowanie agenta Microsoft Azure Recovery Services (MARS)
description: Dowiedz się, jak zainstalować agenta Microsoft Azure Recovery Services (MARS) w celu instalowania kopii zapasowej maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519225"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalowanie agenta Azure Backup MARS

W tym artykule wyjaśniono, jak zainstalować agenta Microsoft Azure Recovery Services (MARS). Mars jest również nazywany agentem Azure Backup agenta.

## <a name="about-the-mars-agent"></a>Informacje o agencie MARS

Azure Backup używa agenta MARS do obsługi kopii zapasowej plików, folderów i stanu systemu z maszyn lokalnych i maszyn wirtualnych platformy Azure. Te kopie zapasowe są przechowywane w magazynie usługi Recovery Services na platformie Azure. Możesz uruchomić agenta:

* Bezpośrednio na lokalnych maszynach z systemem Windows. Te maszyny mogą wrócić do kopii zapasowej bezpośrednio w magazynie usługi Recovery Services na platformie Azure.
* Na maszynach wirtualnych platformy Azure z systemem Windows obok rozszerzenia kopii zapasowej maszyny wirtualnej platformy Azure. Agent kopii zapasowej określonych plików i folderów na maszynie wirtualnej.
* Na serwerze Microsoft Azure Backup (MABS) lub serwerze System Center Data Protection Manager (DPM). W tym scenariuszu maszyny i obciążenia są back up to MABS or Data Protection Manager. Następnie usługa MABS Data Protection Manager używa agenta MARS do kopii zapasowej w magazynie na platformie Azure.

Dane dostępne do tworzenia kopii zapasowej zależą od tego, gdzie agent jest zainstalowany.

> [!NOTE]
> Ogólnie rzecz biorąc, kopię zapasową maszyny wirtualnej platformy Azure można wrócić przy użyciu Azure Backup na maszynie wirtualnej. Ta metoda służy do tworzenia kopii zapasowej całej maszyny wirtualnej. Jeśli chcesz instalować kopię zapasową określonych plików i folderów na maszynie wirtualnej, zainstaluj agenta MARS i użyj go razem z rozszerzeniem. Aby uzyskać więcej informacji, zobacz Architecture of a built-in Azure VM backup (Architektura wbudowanej [kopii zapasowej maszyny wirtualnej platformy Azure).](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się, [Azure Backup agent MARS jest używany do obsługi kopii zapasowej maszyn z systemem Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Dowiedz się więcej o [architekturze kopii zapasowej,](backup-architecture.md#architecture-back-up-to-dpmmabs) która uruchamia agenta MARS na pomocniczym serwerze USŁUGI MABS Data Protection Manager serwera.
* Zapoznaj [się z obsługiwanymi i kopiami, których kopię zapasową](backup-support-matrix-mars-agent.md) można utworzyć za pomocą agenta MARS.
* Upewnij się, że masz konto platformy Azure, jeśli chcesz wykonać kopię zapasową serwera lub klienta na platformie Azure. Jeśli nie masz konta, możesz utworzyć [](https://azure.microsoft.com/free/) bezpłatne konto w zaledwie kilka minut.
* Sprawdź dostęp do Internetu na maszynach, których kopię zapasową chcesz wrócić.
* Upewnij się, że użytkownik wykonujący instalację i konfigurację agenta MARS ma uprawnienia administratora lokalnego na serwerze, który ma być chroniony.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny używają magazynu [geograficznie nadmiarowego (GRS).](../storage/common/storage-redundancy.md#geo-redundant-storage)

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie magazynu GRS.
* Możesz użyć magazynu [lokalnie nadmiarowego (LRS),](../storage/common/storage-redundancy.md#locally-redundant-storage) aby zmniejszyć koszty magazynu platformy Azure.

Aby zmodyfikować typ replikacji magazynu:

1. W nowym magazynie wybierz pozycję **Właściwości** w **sekcji** Ustawienia.

1. Na stronie **Właściwości** w obszarze **Konfiguracja kopii zapasowej** wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu, a następnie wybierz pozycję **Zapisz.**

    ![Aktualizowanie konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, musisz ponownie utworzyć magazyn.
>

### <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na maszynie lub serwerze proxy zezwalają na następujące adresy URL i IP:

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

### <a name="use-azure-expressroute"></a>Korzystanie z Azure ExpressRoute

Możesz utworzyć kopię zapasową danych za pośrednictwem Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

Aby korzystać z publicznej komunikacji równorzędnej, najpierw upewnij się, że masz dostęp do następujących domen i adresów:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Aby korzystać z komunikacji równorzędnej firmy Microsoft, wybierz następujące usługi, regiony i odpowiednie wartości społeczności:

* Azure Active Directory (12076:5060)
* Region świadczenia usługi Azure zgodnie z lokalizacją magazynu usługi Recovery Services
* Usługa Azure Storage zgodnie z lokalizacją magazynu usługi Recovery Services

Aby uzyskać więcej informacji, zobacz [ExpressRoute routing requirements (Wymagania dotyczące routingu usługi ExpressRoute).](../expressroute/expressroute-routing.md)

> [!NOTE]
> Publiczna komunikacja równorzędna jest przestarzała dla nowych obwodów.

Wszystkie powyższe adresy URL i IP używają protokołu HTTPS na porcie 443.

### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Pobieranie agenta MARS

Pobierz agenta MARS, aby można go było zainstalować na maszynach, dla których chcesz utworzyć kopię zapasową.

Jeśli agent został już zainstalowany na dowolnym komputerze, upewnij się, że używasz najnowszej wersji agenta. Znajdź najnowszą wersję w portalu lub przejdź bezpośrednio do [strony pobierania.](https://aka.ms/azurebackup_agent)

1. W magazynie w obszarze **Wprowadzenie** wybierz pozycję Kopia **zapasowa**.

    ![Otwieranie celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. W **obszarze Gdzie jest uruchomione Twoje obciążenie?** wybierz pozycję **Lokalna.** Wybierz tę opcję, nawet jeśli chcesz zainstalować agenta MARS na maszynie wirtualnej platformy Azure.
1. W **obszarze Co ma być kopią zapasową?** wybierz pozycję Pliki i **foldery.** Możesz również wybrać pozycję **Stan systemu.** Dostępnych jest wiele innych opcji, ale są one obsługiwane tylko w przypadku uruchamiania pomocniczego serwera kopii zapasowej. Wybierz **pozycję Prepare Infrastructure (Przygotuj infrastrukturę).**

    ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. W **obszarze Przygotowywanie infrastruktury** w obszarze Instalowanie agenta usługi Recovery **Services** pobierz agenta MARS.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. W menu pobierania wybierz pozycję **Zapisz.** Domyślnie plik *MARSagentinstaller.exe* jest zapisywany w folderze Pobrane.

1. Wybierz **pozycję Pobierz już lub za pomocą najnowszego agenta usługi Recovery Services,** a następnie pobierz poświadczenia magazynu.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Wybierz pozycję **Zapisz**. Plik zostanie pobrany do folderu Pobrane. Nie można otworzyć pliku poświadczeń magazynu.

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Uruchom plik *MARSagentinstaller.exe* na maszynach, dla których chcesz wrócić do kopii zapasowej.
1. Na stronie agentów MARS Kreator instalacji pozycję **Ustawienia instalacji.** W tym miejscu wybierz miejsce instalacji agenta, a następnie wybierz lokalizację pamięci podręcznej. Następnie wybierz przycisk **Dalej**.
   * Azure Backup używa pamięci podręcznej do przechowywania migawek danych przed wysłaniem ich na platformę Azure.
   * Lokalizacja pamięci podręcznej powinna mieć wolne miejsce równe co najmniej 5% rozmiaru danych, których kopię zapasową chcesz wrócić.

    ![Wybierz ustawienia instalacji w instrukcje agenta MARS Kreator instalacji](./media/backup-configure-vault/mars1.png)

1. W **przypadku konfiguracji serwera** proxy określ, jak agent uruchomiony na maszynie z systemem Windows będzie łączyć się z Internetem. Następnie wybierz przycisk **Dalej**.

   * Jeśli używasz niestandardowego serwera proxy, określ wszelkie niezbędne ustawienia i poświadczenia serwera proxy.
   * Należy pamiętać, że agent musi mieć dostęp do [określonych adresów URL.](#before-you-start)

    ![Konfigurowanie dostępu do Internetu w kreatorze MARS](./media/backup-configure-vault/mars2.png)

1. W **przypadku opcji** Instalacja przejrzyj wymagania wstępne i wybierz pozycję **Zainstaluj.**
1. Po zainstalowaniu agenta wybierz pozycję **Przejdź do rejestracji.**
1. W **chmurze Register Server Wizard** Vault Identification (Identyfikacja magazynu Kreatora rejestrowania serwera) przejdź do pobranego pliku  >  poświadczeń i wybierz go. Następnie wybierz przycisk **Dalej**.

    ![Dodawanie poświadczeń magazynu za pomocą Kreatora rejestrowania serwera](./media/backup-configure-vault/register1.png)

1. Na stronie **Ustawienie szyfrowania** określ hasło, które będzie używane do szyfrowania i odszyfrowywania kopii zapasowych dla maszyny. [Zobacz tutaj,](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) aby uzyskać więcej informacji na temat dozwolonych znaków hasła.

    * Zapisz hasło w bezpiecznej lokalizacji. Jest on potrzebny do przywrócenia kopii zapasowej.
    * Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie pomoże Ci odzyskać danych kopii zapasowej.

1. Wybierz pozycję **Zakończ**. Agent jest teraz zainstalowany, a maszyna jest zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wrócić do kopii zapasowej maszyn z](backup-windows-with-mars-agent.md) systemem Windows przy użyciu Azure Backup MARS

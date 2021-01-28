---
title: Przygotuj serwer programu DPM do tworzenia kopii zapasowych obciążeń
description: W tym artykule dowiesz się, jak przygotować się do tworzenia kopii zapasowych programu System Center Data Protection Manager (DPM) na platformie Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 823b23d99959df5f2eed20cf4136254e1702fe89
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985635"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Przygotowanie do tworzenia kopii zapasowych obciążeń na platformie Azure przy użyciu programu System Center DPM

W tym artykule wyjaśniono, jak przygotować się do tworzenia kopii zapasowych programu System Center Data Protection Manager (DPM) na platformie Azure przy użyciu usługi Azure Backup.

Artykuł zawiera następujące informacje:

- Omówienie wdrażania programu DPM z Azure Backup.
- Wymagania wstępne i ograniczenia dotyczące używania Azure Backup z programem DPM.
- Kroki przygotowujące platformę Azure, w tym Konfigurowanie magazynu kopii zapasowych Recovery Services i opcjonalnie modyfikowanie typu usługi Azure Storage dla magazynu.
- Procedura przygotowywania serwera programu DPM, w tym pobierania poświadczeń magazynu, instalowania agenta Azure Backup i rejestrowania serwera DPM w magazynie.
- Wskazówki dotyczące rozwiązywania problemów z typowymi błędami.

## <a name="why-back-up-dpm-to-azure"></a>Dlaczego należy utworzyć kopię zapasową programu DPM na platformie Azure?

[Program System Center DPM](/system-center/dpm/dpm-overview) tworzy kopię zapasową danych plików i aplikacji. Program DPM współdziała z Azure Backup w następujący sposób:

- **Program DPM działający na serwerze fizycznym lub na lokalnej maszynie wirtualnej** — można utworzyć kopię zapasową danych w magazynie kopii zapasowych na platformie Azure, a także utworzyć kopię zapasową dysku i taśmy.
- **Program DPM działający na maszynie wirtualnej platformy Azure** — w programie System Center 2012 R2 z aktualizacją Update 3 lub nowszą można wdrożyć program DPM na maszynie wirtualnej platformy Azure. Można utworzyć kopię zapasową danych na dyskach platformy Azure dołączonych do maszyny wirtualnej lub użyć Azure Backup, aby utworzyć kopię zapasową danych w magazynie kopii zapasowych.

Korzyści biznesowe tworzenia kopii zapasowych serwerów programu DPM na platformie Azure obejmują:

- W przypadku lokalnego programu DPM Azure Backup stanowi alternatywę dla długoterminowego wdrażania na taśmie.
- W przypadku programu DPM uruchomionego na maszynie wirtualnej platformy Azure Azure Backup umożliwia odciążenie magazynu z dysku platformy Azure. Przechowywanie starszych danych w magazynie kopii zapasowych umożliwia skalowanie swojej firmy przez przechowywanie nowych danych na dysku.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

**Ustawienie** | **Wymaganie**
--- | ---
Program DPM na maszynie wirtualnej platformy Azure | System Center 2012 R2 z programem DPM 2012 R2 z pakietem zbiorczym aktualizacji 3 lub nowszym.
Program DPM na serwerze fizycznym | System Center 2012 z dodatkiem SP1 lub nowszy; System Center 2012 R2.
Program DPM na maszynie wirtualnej funkcji Hyper-V | System Center 2012 z dodatkiem SP1 lub nowszy; System Center 2012 R2.
Program DPM na maszynie wirtualnej VMware | System Center 2012 R2 z pakietem zbiorczym aktualizacji 5 lub nowszym.
Składniki | Na serwerze DPM powinien być zainstalowany program Windows PowerShell i .NET Framework 4,5.
Obsługiwane aplikacje | [Dowiedz się,](/system-center/dpm/dpm-protection-matrix) dla jakich elementów można wykonywać kopie zapasowe w programie DPM.
Obsługiwane typy plików | Kopie zapasowe tych typów plików można wykonać przy użyciu Azure Backup:<br> <li>Zaszyfrowane (tylko pełne kopie zapasowe)<li> Skompresowane (przyrostowe kopie zapasowe) <li> Rozrzedzony (obsługiwane przyrostowe kopie zapasowe)<li> Skompresowane i rozrzedzone (traktowane jako rozrzedzone)
Nieobsługiwane typy plików | <li>Serwery w systemach plików z uwzględnieniem wielkości liter<li> twarde linki (pomijane)<li> punkty ponownej analizy (pominięte)<li> zaszyfrowane i skompresowane (pomijane)<li> zaszyfrowane i rozrzedzone (pomijane)<li> Strumień skompresowany<li> analizowanie strumienia
Magazyn lokalny | Każdy komputer, którego kopia zapasowa ma zostać utworzona, musi mieć lokalne wolne miejsce, co najmniej 5% rozmiaru danych, których kopia zapasowa jest tworzona. Na przykład kopia zapasowa 100 GB danych wymaga co najmniej 5 GB wolnego miejsca w lokalizacji tymczasowej.
Magazyn magazynu | Nie ma limitu ilości danych, do których można utworzyć kopię zapasową w magazynie Azure Backup, ale rozmiar źródła danych (na przykład maszyny wirtualnej lub bazy danych) nie powinien przekraczać 54 400 GB.
Azure ExpressRoute | Możesz tworzyć kopie zapasowe danych za pośrednictwem usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępne dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.<br/><br/> **Przy użyciu publicznej komunikacji równorzędnej**: Upewnij się, że dostęp do następujących domen/adresów:<br/><br/> Adresy<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>Adresy IP<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/>**W przypadku komunikacji równorzędnej firmy Microsoft** wybierz następujące usługi/regiony i odpowiednie wartości społeczności:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure region (zgodnie z lokalizacją magazynu Recovery Services)<br/><br/>— Azure Storage (zgodnie z lokalizacją magazynu Recovery Services)<br/><br/>Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md).<br/><br/>**Uwaga**: publiczna Komunikacja równorzędna jest przestarzała dla nowych obwodów.
Agent usługi Azure Backup | Jeśli program DPM jest uruchomiony w programie System Center 2012 SP1, zainstaluj pakiet zbiorczy 2 lub nowszy dla programu DPM z dodatkiem SP1. Jest to wymagane na potrzeby instalacji agenta.<br/><br/> W tym artykule opisano sposób wdrażania najnowszej wersji agenta Azure Backup, znanego również jako Agent usługi odzyskiwania Microsoft Azure (MARS). Jeśli wdrożono wcześniejszą wersję, należy ją zaktualizować do najnowszej wersji, aby upewnić się, że kopia zapasowa działa zgodnie z oczekiwaniami.

Przed rozpoczęciem musisz mieć konto platformy Azure z włączoną funkcją Azure Backup. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Przeczytaj o [cenach Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modyfikowanie ustawień magazynu

Można wybrać magazyn Geograficznie nadmiarowy i Magazyn lokalnie nadmiarowy.

- Domyślnie magazyn jest nadmiarowy geograficznie.
- Jeśli magazyn jest podstawową kopią zapasową, pozostaw opcję ustawioną na magazyn Geograficznie nadmiarowy. Jeśli potrzebujesz tańszej opcji, która nie jest całkowicie trwała, wykonaj czynności opisane w poniższej procedurze, aby skonfigurować magazyn lokalnie nadmiarowy.
- Dowiedz się więcej na temat [usługi Azure Storage](../storage/common/storage-redundancy.md)oraz opcji magazynu [geograficznie](../storage/common/storage-redundancy.md#geo-redundant-storage) [nadmiarowego i](../storage/common/storage-redundancy.md#locally-redundant-storage) [strefy](../storage/common/storage-redundancy.md#zone-redundant-storage) nadmiarowej.
- Zmodyfikuj ustawienia magazynu przed początkową kopią zapasową. Jeśli wykonano już kopię zapasową elementu, Zatrzymaj jego kopię zapasową w magazynie przed zmodyfikowaniem ustawień magazynu.

Aby edytować ustawienia replikacji magazynu:

1. Otwórz pulpit nawigacyjny magazynu.

2. W obszarze **Zarządzanie** wybierz pozycję **infrastruktura kopii zapasowych**.

3. W menu **Konfiguracja kopii zapasowej** wybierz opcję magazynu dla magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Pobieranie poświadczeń magazynu

Poświadczenia magazynu są używane podczas rejestrowania serwera programu DPM w magazynie.

- Plik poświadczeń magazynu jest to certyfikat wygenerowany przez portal dla każdego magazynu kopii zapasowych.
- Portal przekazuje następnie klucz publiczny do usługi Access Control Service (ACS).
- W trakcie przepływu pracy rejestracji komputera klucz prywatny certyfikatu jest udostępniany użytkownikowi, który uwierzytelnia maszynę.
- Na podstawie uwierzytelniania usługa Azure Backup wysyła dane do określonego magazynu.

### <a name="best-practices-for-vault-credentials"></a>Najlepsze rozwiązania dotyczące poświadczeń magazynu

Aby uzyskać poświadczenia, Pobierz plik poświadczeń magazynu za pośrednictwem bezpiecznego kanału z Azure Portal:

- Poświadczenia magazynu są używane tylko podczas przepływu pracy rejestracji.
- Odpowiedzialność za zapewnienie, że plik poświadczeń magazynu jest bezpieczny i nie został naruszony.
  - Jeśli kontrola poświadczeń zostanie utracona, poświadczenia magazynu mogą służyć do rejestrowania innych maszyn w magazynie.
  - Jednak dane kopii zapasowej są szyfrowane przy użyciu hasła, które należy do Ciebie, dlatego nie można złamać istniejących danych kopii zapasowej.
- Upewnij się, że plik jest zapisany w lokalizacji, do której można uzyskać dostęp z serwera DPM. Jeśli jest przechowywany w udziale plików/SMB, Sprawdź uprawnienia dostępu.
- Poświadczenia magazynu wygasają po upływie 48 godzin. Nowe poświadczenia magazynu można pobrać dowolną liczbę razy. Jednak podczas przepływu pracy rejestracji można używać tylko najnowszego pliku poświadczeń magazynu.
- Usługa Azure Backup nie ma informacji o kluczu prywatnym certyfikatu, a klucz prywatny nie jest dostępny w portalu lub usłudze.

Pobierz plik poświadczeń magazynu na komputer lokalny w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz magazyn, w którym chcesz zarejestrować serwer programu DPM.
3. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.

    ![Otwieranie menu magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. W oknie **Właściwości**  >  **poświadczenia kopii zapasowej** wybierz pozycję **Pobierz**. Portal generuje plik poświadczeń magazynu przy użyciu kombinacji nazwy magazynu i bieżącej daty i udostępnia je do pobrania.

    ![Pobierz poświadczenia](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Wybierz pozycję **Zapisz** , aby pobrać poświadczenia magazynu do folderu lub **Zapisz jako** i określ lokalizację. Wygenerowanie pliku będzie trwać do minuty.

## <a name="install-the-backup-agent"></a>Zainstaluj agenta kopii zapasowej

Każdy komputer, na którym jest tworzona kopia zapasowa Azure Backup musi mieć zainstalowany agent kopii zapasowych (znany również jako Agent usługi odzyskiwania Microsoft Azure (MARS). Zainstaluj agenta na serwerze programu DPM w następujący sposób:

1. Otwórz magazyn, do którego chcesz zarejestrować serwer programu DPM.
2. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.

    ![Otwórz ustawienia magazynu](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na stronie **Właściwości** pobierz agenta Azure Backup.

    ![Pobierz](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Po pobraniu Uruchom MARSAgentInstaller.exe. Aby zainstalować agenta na komputerze DPM.
5. Wybierz folder instalacyjny i folder pamięci podręcznej agenta. Ilość wolnego miejsca w lokalizacji pamięci podręcznej musi wynosić co najmniej 5% danych kopii zapasowej.
6. Jeśli używasz serwera proxy do łączenia się z Internetem, na ekranie **Konfiguracja serwera proxy** wprowadź szczegóły serwera proxy. Jeśli używasz uwierzytelnionego serwera proxy, wprowadź nazwę użytkownika i hasło na tym ekranie.
7. Agent Azure Backup instaluje .NET Framework 4,5 i Windows PowerShell (jeśli nie są zainstalowane), aby zakończyć instalację.
8. Po zainstalowaniu agenta **Zamknij** okno.

    ![Zamknij](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Rejestrowanie serwera DPM w magazynie

1. W konsoli administratora programu DPM > **Zarządzanie**, wybierz pozycję **online**. Wybierz pozycję **Zarejestruj**. Spowoduje to otwarcie Kreatora rejestrowania serwera.
2. W obszarze **Konfiguracja serwera proxy** Określ ustawienia serwera proxy zgodnie z wymaganiami.

    ![Konfiguracja serwera proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. W obszarze **Magazyn kopii zapasowych** przejdź do i wybierz pobrany plik poświadczeń magazynu.

    ![Poświadczenia magazynu](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. W **ustawieniach ograniczania** przepustowości można opcjonalnie włączyć ograniczanie przepustowość dla kopii zapasowych. Można ustawić limity szybkości dla opcji Określ godziny pracy i dni.

    ![Ustawienie ograniczenia przepustowości](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. W obszarze **Ustawienia folderu odzyskiwania** Określ lokalizację, która może być używana podczas odzyskiwania danych.

    - Azure Backup używa tej lokalizacji jako tymczasowego obszaru przechowywania dla odzyskiwanych danych.
    - Po zakończeniu odzyskiwania danych Azure Backup czyści dane w tym obszarze.
    - Lokalizacja musi mieć wystarczającą ilość miejsca, aby można było przechowywać elementy, które powinny być odzyskiwane równolegle.

    ![Ustawienia folderu odzyskiwania](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. W obszarze **ustawienie szyfrowania** Wygeneruj lub podaj hasło.

    - Hasło jest używane do szyfrowania kopii zapasowych w chmurze.
    - Określ co najmniej 16 znaków.
    - Zapisz plik w bezpiecznej lokalizacji, który jest wymagany do odzyskania.

    ![Szyfrowanie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Jesteś posiadaczem hasła szyfrowania, a firma Microsoft nie ma do niego wglądu.
    > Jeśli hasło zostanie utracone lub zapomniane, firma Microsoft nie może pomóc w odzyskiwaniu danych kopii zapasowej.

7. Wybierz pozycję **zarejestruj** , aby zarejestrować serwer programu DPM w magazynie.

Po pomyślnym zarejestrowaniu serwera w magazynie możesz rozpocząć tworzenie kopii zapasowej do Microsoft Azure. Należy skonfigurować grupę ochrony w konsoli programu DPM w celu tworzenia kopii zapasowych obciążeń na platformie Azure. [Dowiedz się, jak](/system-center/dpm/create-dpm-protection-groups) wdrażać grupy ochrony.

## <a name="troubleshoot-vault-credentials"></a>Rozwiązywanie problemów z poświadczeniami magazynu

### <a name="expiration-error"></a>Błąd wygaśnięcia

Plik poświadczeń magazynu jest prawidłowy tylko przez 48 godzin (po pobraniu z portalu). Jeśli wystąpi błąd na tym ekranie (na przykład "podany plik poświadczeń magazynu wygasł"), zaloguj się do Azure Portal i ponownie pobierz plik poświadczeń magazynu.

### <a name="access-error"></a>Błąd dostępu

Upewnij się, że plik poświadczeń magazynu jest dostępny w lokalizacji, do której można uzyskać dostęp za pomocą aplikacji Instalatora. Jeśli występują błędy związane z dostępem, skopiuj plik poświadczeń magazynu do lokalizacji tymczasowej na tym komputerze, a następnie spróbuj ponownie wykonać operację.

### <a name="invalid-credentials-error"></a>Błąd nieprawidłowych poświadczeń

Jeśli wystąpi błąd nieprawidłowego poświadczenia magazynu (na przykład "Nieprawidłowe poświadczenia magazynu podane"), plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania.

- Spróbuj ponownie wykonać operację po pobraniu nowego pliku poświadczeń magazynu z portalu.
- Ten błąd jest zazwyczaj widoczny w przypadku wybrania opcji **Pobierz poświadczenia magazynu** w Azure Portal, dwa razy w krótkim czasie. W takim przypadku tylko drugi plik poświadczeń magazynu jest prawidłowy.

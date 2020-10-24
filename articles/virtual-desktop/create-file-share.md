---
title: Tworzenie Azure Files udziału plików z kontrolerem domeny — Azure
description: Skonfiguruj kontener profilu FSLogix w udziale plików platformy Azure w istniejącej puli hostów Windows Virtual Desktop z domeną Active Directory.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aad5ebaf7eef5b404f7849b79694facf1efd01b4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519443"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Tworzenie kontenera profilu przy użyciu Azure Files i AD DS

W tym artykule dowiesz się, jak utworzyć udział plików platformy Azure uwierzytelniany przez kontroler domeny w istniejącej puli hostów systemu Windows Virtual Desktop. Ten udział plików służy do przechowywania profilów magazynu.

Ten proces używa Active Directory Domain Services (AD DS), czyli usługi katalogowej Premium. Jeśli szukasz informacji o sposobie tworzenia kontenera profilu FSLogix przy użyciu usługi Azure AD DS, zobacz [Tworzenie kontenera profilu FSLogix z Azure Files](create-profile-container-adds.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że kontroler domeny jest zsynchronizowany z platformą Azure i rozpoznawalny z sieci wirtualnej platformy Azure (VNET), z którymi są połączone hosty sesji.

## <a name="set-up-a-storage-account"></a>Konfigurowanie konta magazynu

Najpierw musisz skonfigurować konto magazynu Azure Files.

Aby skonfigurować konto magazynu:

1. Zaloguj się w witrynie Azure Portal.

2. Wyszukaj **konto magazynu** na pasku wyszukiwania.

3. Wybierz pozycję **+Dodaj**.

4. Wprowadź następujące informacje na stronie  **Tworzenie konta magazynu** :

    - Tworzenie nowej grupy zasobów
    - Wprowadź unikatową nazwę konta magazynu.
    - W przypadku **lokalizacji**zalecamy wybranie tej samej lokalizacji co Pula hostów pulpitów wirtualnych systemu Windows.
    - W obszarze **Wydajność** wybierz opcję **Standardowa**. (W zależności od wymagań IOPS. Aby uzyskać więcej informacji, zobacz [Opcje magazynu dla kontenerów profilów FSLogix na pulpicie wirtualnym systemu Windows](store-fslogix-profile.md).
    - W obszarze **Typ konta**wybierz pozycję **StorageV2** lub **FileStorage** (dostępne tylko wtedy, gdy warstwa wydajności to Premium).
    - W obszarze **replikacja**wybierz pozycję **Magazyn lokalnie nadmiarowy (LRS)**.

5. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**, a następnie wybierz pozycję **Utwórz**.

Jeśli potrzebujesz bardziej szczegółowych instrukcji konfiguracyjnych, zobacz [dostępność regionalna](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Następnie musisz utworzyć udział plików platformy Azure.

Aby utworzyć udział plików:

1. Wybierz pozycję **Przejdź do zasobu**.

2. Na stronie Przegląd wybierz pozycję **Udziały plików**.

3. Wybierz pozycję **+ udziały plików**, Utwórz nowy udział plików o nazwie **Profile**, a następnie wprowadź odpowiedni przydział lub pozostaw pole puste, aby nie mieć limitu przydziału.

4. Wybierz przycisk **Utwórz**.

## <a name="enable-active-directory-authentication"></a>Włącz uwierzytelnianie Active Directory

Następnie należy włączyć uwierzytelnianie Active Directory (AD). Aby włączyć te zasady, należy postępować zgodnie z instrukcjami w tej sekcji na komputerze, który jest już przyłączony do domeny. Aby włączyć uwierzytelnianie, postępuj zgodnie z tymi instrukcjami na maszynie wirtualnej z uruchomionym kontrolerem domeny:

1. Remote Desktop Protocol do maszyny wirtualnej przyłączonej do domeny.

2. Postępuj zgodnie z instrukcjami w temacie [Włączanie usługi azure AD DS Authentication dla udziałów plików platformy Azure](../storage/files/storage-files-identity-ad-ds-enable.md) , aby zainstalować moduł AzFilesHybrid i włączyć uwierzytelnianie.

3.  Otwórz Azure Portal, Otwórz konto magazynu, wybierz pozycję **Konfiguracja**, a następnie potwierdź, że **Active Directory (AD)** jest ustawiona na wartość **włączone**.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający stronę konfiguracji z włączonym Azure Active Directory (AD).](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Przypisywanie uprawnień usługi Azure RBAC do użytkowników pulpitu wirtualnego systemu Windows

Wszyscy użytkownicy, którzy muszą mieć profile FSLogix przechowywane na koncie magazynu, muszą mieć przypisaną rolę współautora udziału SMB danych pliku magazynu.

Użytkownicy logujący się do hostów sesji usługi Windows Virtual Desktop potrzebują uprawnień dostępu, aby uzyskiwać dostęp do udziału plików. Udzielenie dostępu do udziału plików platformy Azure obejmuje skonfigurowanie uprawnień zarówno na poziomie udziału, jak i na poziomie systemu plików NTFS, podobnie jak w przypadku tradycyjnego udziału systemu Windows.

Aby skonfigurować uprawnienia na poziomie udziału, przypisz każdemu użytkownikowi rolę z odpowiednimi uprawnieniami dostępu. Uprawnienia można przypisać do poszczególnych użytkowników lub grupy usługi Azure AD. Aby dowiedzieć się więcej, zobacz [przypisywanie uprawnień dostępu do tożsamości](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>Konta lub grupy, którym przypisujesz uprawnienia, powinny zostać utworzone w domenie i zsynchronizowane z usługą Azure AD. Konta utworzone w usłudze Azure AD nie działają.

Aby przypisać uprawnienia kontroli dostępu opartej na rolach (Azure RBAC):

1. Otwórz witrynę Azure Portal.

2. Otwórz konto magazynu utworzone w obszarze [Skonfiguruj konto magazynu](#set-up-a-storage-account).

3. Wybierz pozycję **udziały plików**, a następnie wybierz nazwę udziału plików, którego planujesz użyć.

4. Wybierz pozycję **Access Control (IAM)**.

5. Wybierz pozycję **Dodaj przypisanie roli**.

6. Na karcie **Dodaj przypisanie roli** wybierz pozycję **plik magazynu dane współautora udziału SMB** dla konta administratora.

     Aby przypisać użytkownikom uprawnienia do ich profilów FSLogix, wykonaj te same instrukcje. Jednak po przekroczeniu kroku 5 Wybierz opcję **plik magazynu dane współautor udział SMB** .

7. Wybierz pozycję **Zapisz**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Przypisywanie uprawnień użytkowników w udziale plików platformy Azure

Po przypisaniu uprawnień usługi Azure RBAC do użytkowników należy skonfigurować uprawnienia systemu plików NTFS.

Aby rozpocząć pracę, musisz znać dwie rzeczy z Azure Portal:

- Ścieżka UNC.
- Klucz konta magazynu.

### <a name="get-the-unc-path"></a>Pobieranie ścieżki UNC

Poniżej przedstawiono sposób pobierania ścieżki UNC:

1. Otwórz witrynę Azure Portal.

2. Otwórz konto magazynu utworzone w obszarze [Skonfiguruj konto magazynu](#set-up-a-storage-account).

3. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Właściwości**.

4. Skopiuj identyfikator URI **punktu końcowego usługi plików podstawowego** do wybranego edytora tekstu.

5. Po skopiowaniu identyfikatora URI wykonaj następujące czynności, aby zmienić je na UNC:

    - Usuń `https://` i Zamień na `\\`
    - Zastąp ukośnik odwrotny `/` ukośnikiem `\` .
    - Dodaj nazwę udziału plików utworzonego w obszarze [Utwórz udział plików platformy Azure](#create-an-azure-file-share) na końcu UNC.

        Na przykład: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu

Aby uzyskać klucz konta magazynu:

1. Otwórz witrynę Azure Portal.

2. Otwórz konto magazynu utworzone w obszarze [Skonfiguruj konto magazynu](#set-up-a-storage-account).

3. Na karcie **konto magazynu** wybierz pozycję **klucze dostępu**.

4. Skopiuj **Klucz1** lub **klucz2** do pliku na komputerze lokalnym.

### <a name="configure-ntfs-permissions"></a>Konfigurowanie uprawnień systemu plików NTFS

Aby skonfigurować uprawnienia systemu plików NTFS:

1. Otwórz wiersz polecenia na maszynie wirtualnej przyłączonej do domeny.

2. Uruchom następujące polecenie, aby zainstalować udział plików platformy Azure i przypisać mu literę dysku:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Uruchom następujące polecenie, aby przejrzeć uprawnienia dostępu do udziału plików platformy Azure:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Zamień na `<mounted-drive-letter>` literę dysku zamapowanego na.

    Zarówno *Użytkownicy NT Authority\Authenticated* , jak i *BUILTIN\Users* mają określone uprawnienia. Te uprawnienia domyślne umożliwiają użytkownikom odczytywanie kontenerów profilów innych użytkowników. Jednak uprawnienia opisane w artykule [Konfigurowanie uprawnień magazynu do użycia z kontenerami profilów i kontenerami pakietu Office](/fslogix/fslogix-storage-config-ht) nie umożliwiają użytkownikom odczytywania kontenerów profilów profilu.

4. Uruchom następujące polecenia, aby umożliwić użytkownikom pulpitu wirtualnego systemu Windows Tworzenie własnego kontenera profilu podczas blokowania dostępu do kontenerów profilów od innych użytkowników.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Zastąp <literę dysku zainstalowana> literą dysku użytego do zmapowania dysku.
     - Zastąp <User-e-mail> nazwą UPN grupy użytkowników lub Active Directory zawierającą użytkowników, którzy będą potrzebować dostępu do udziału.

     Przykład:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Konfigurowanie FSLogix na maszynach wirtualnych hosta sesji

W tej sekcji pokazano, jak skonfigurować maszynę wirtualną przy użyciu produktu FSLogix. Należy wykonać te instrukcje przy każdym konfigurowaniu hosta sesji. Przed rozpoczęciem konfigurowania postępuj zgodnie z instrukcjami podanymi w temacie [pobieranie i Instalowanie FSLogix](/fslogix/install-ht). Dostępnych jest kilka opcji, które zapewniają skonfigurowanie kluczy rejestru na wszystkich hostach sesji. Możesz określić te opcje w obrazie lub skonfigurować zasady grupy.

Aby skonfigurować produkt FSLogix na maszynie wirtualnej hosta sesji:

1. RDP z maszyną wirtualną hosta sesji puli hostów Windows Virtual Desktop.

2. [Pobierz i zainstaluj FSLogix](/fslogix/install-ht).

5. Wykonaj instrukcje podane w temacie [Konfigurowanie ustawień rejestru kontenera profilów](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Przejdź do **komputera**  >  **HKEY_LOCAL_MACHINE**  >  **oprogramowania**  >  **FSLogix**.

    - Utwórz klucz **profilów** .

    - Utwórz wartość **Enabled, DWORD** z wartością 1.

    - Utwórz **VHDLocations, Multi_SZ**.

    - Ustaw wartość **VHDLocations** na ścieżkę UNC wygenerowaną w polu [Pobierz ścieżkę UNC](#get-the-unc-path).

6. Uruchom ponownie maszynę wirtualną.

## <a name="testing"></a>Testowanie

Po zainstalowaniu i skonfigurowaniu FSLogix można przetestować wdrożenie, logując się przy użyciu konta użytkownika, do którego przypisano grupę aplikacji lub pulpit w puli hostów. Upewnij się, że konto użytkownika, za pomocą którego logujesz się, ma uprawnienia do udziału plików.

Jeśli użytkownik zalogował się wcześniej, będzie miał istniejący profil lokalny, który będzie używany podczas tej sesji. Aby uniknąć tworzenia profilu lokalnego, Utwórz nowe konto użytkownika do użycia w testach lub użyj metod konfiguracji opisanych w [samouczku: Konfigurowanie kontenera profilu do przekierowywania profilów użytkowników](/fslogix/configure-profile-container-tutorial/).

Aby sprawdzić swoje uprawnienia do Twojej sesji:

1. Rozpocznij sesję na pulpicie wirtualnym systemu Windows.

2. Otwórz witrynę Azure Portal.

3. Otwórz konto magazynu utworzone w obszarze [Skonfiguruj konto magazynu](#set-up-a-storage-account).

4. Wybierz pozycję **Utwórz udział** na stronie Utwórz udział plików platformy Azure.

5. Upewnij się, że folder zawierający profil użytkownika już istnieje w plikach.

W celu przeprowadzenia dodatkowego testowania postępuj zgodnie z instrukcjami w temacie [upewnij się, że Twój profil działa](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problem z FSLogix, zobacz [ten przewodnik rozwiązywania problemów](/fslogix/fslogix-trouble-shooting-ht).

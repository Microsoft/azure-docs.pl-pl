---
title: Tworzenie kontenera profilu FSLogix Azure Files Active Directory Domain Services — Azure
description: W tym artykule opisano sposób tworzenia kontenera profilu FSLogix z Azure Files i Azure Active Directory Domain Services.
author: Heidilohr
ms.topic: how-to
ms.date: 04/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 59e2fc1f528040515398e51d359840f6ef1bbefc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255804"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Tworzenie kontenera profilu przy użyciu Azure Files i platformy Azure AD DS

W tym artykule przedstawiono sposób tworzenia kontenera profilu FSLogix z Azure Files i Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zostało już skonfigurowane wystąpienie usługi Azure AD DS. Jeśli jeszcze tego nie masz, postępuj zgodnie z instrukcjami w temacie [Tworzenie podstawowej domeny zarządzanej](../active-directory-domain-services/tutorial-create-instance.md) , a następnie wróć tutaj.

## <a name="add-azure-ad-ds-admins"></a>Dodawanie administratorów usługi Azure AD DS

Aby dodać kolejnych administratorów, należy utworzyć nowego użytkownika i przyznać im uprawnienia.

Aby dodać administratora:

1. Wybierz **Azure Active Directory** z paska bocznego, a następnie wybierz pozycję **Wszyscy użytkownicy**, a następnie wybierz pozycję **nowy użytkownik**.

2.  Wprowadź szczegóły użytkownika w polach.

3. W okienku Azure Active Directory po lewej stronie ekranu wybierz pozycję **grupy**.

4. Wybierz grupę **Administratorzy domeny usługi AAD** .

5. W lewym okienku wybierz pozycję **elementy członkowskie**, a następnie wybierz pozycję **Dodaj członków** w okienku głównym. Zostanie wyświetlona lista wszystkich użytkowników dostępnych w usłudze Azure AD. Wybierz nazwę utworzonego profilu użytkownika.

## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Teraz czas na włączenie uwierzytelniania za pomocą usługi Azure AD DS przy użyciu bloku komunikatów serwera (SMB).

Aby włączyć uwierzytelnianie:

1. Jeśli jeszcze tego nie zrobiono, skonfiguruj i Wdróż konto usługi Azure Storage ogólnego przeznaczenia w wersji 2, postępując zgodnie z instrukcjami w temacie [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md).

2. Po zakończeniu konfigurowania konta wybierz pozycję **Przejdź do zasobu**.

3. Wybierz pozycję **Konfiguracja** z okienka po lewej stronie ekranu, a następnie Włącz **Azure Active Directory uwierzytelnianie dla Azure Files** w okienku głównym. Po zakończeniu wybierz pozycję **Zapisz**.

4. Wybierz pozycję **Przegląd** w okienku po lewej stronie ekranu, a następnie wybierz pozycję **pliki** w okienku głównym.

5. Wybierz pozycję **udział plików** , a następnie wprowadź **nazwę** i **przydział** do pól, które pojawiają się po prawej stronie ekranu.

## <a name="assign-access-permissions-to-an-identity"></a>Przypisywanie uprawnień dostępu do tożsamości

Inni użytkownicy będą musieli uzyskać dostęp do udziału plików. W tym celu należy przypisać każdemu użytkownikowi rolę z odpowiednimi uprawnieniami dostępu.

Aby przypisać uprawnienia dostępu użytkowników:

1. W Azure Portal Otwórz udział plików utworzony w obszarze [Konfigurowanie konta usługi Azure Storage](#set-up-an-azure-storage-account).

2. Wybierz pozycję **Access Control (IAM)**.

3. Wybierz pozycję **Dodaj przypisanie roli**.

4. Na karcie **Dodaj przypisanie roli** wybierz odpowiednią wbudowaną rolę z listy rola. Aby uzyskać odpowiednie uprawnienia, należy wybrać co najmniej pozycję **plik usługi Storage współautor udziału SMB** dla konta.

5. W obszarze **Przypisz dostęp do** wybierz pozycję **Azure Active Directory użytkownika, grupy lub nazwy głównej usługi**.

6. Wybierz nazwę lub adres e-mail dla Azure Active Directory tożsamości docelowej.

7. Wybierz pozycję **Zapisz**.

## <a name="get-the-storage-account-access-key"></a>Pobieranie klucza dostępu do konta magazynu

Następnie musisz uzyskać klucz dostępu do konta magazynu.

Aby uzyskać klucz dostępu do konta magazynu:

1. Na pasku bocznym Azure Portal wybierz pozycję **konta magazynu**.

2. Z listy kont magazynu wybierz konto, dla którego włączono usługę Azure AD DS i utworzono role niestandardowe w powyższych krokach.

3. W obszarze **Ustawienia** wybierz pozycję **klucze dostępu** i skopiuj klucz z **Klucz1**.

4. Przejdź do karty **Virtual Machines** i Znajdź wszystkie maszyny wirtualne, które staną się częścią puli hostów.

5. Wybierz nazwę maszyny wirtualnej (VM) w obszarze **Virtual Machines (adVM)** , a następnie wybierz pozycję **Połącz** .

    Spowoduje to pobranie pliku RDP, który umożliwi zalogowanie się do maszyny wirtualnej przy użyciu własnych poświadczeń.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu karty RDP okna łączenie z maszyną wirtualną.](media/rdp-tab.png)

6. Po zalogowaniu się do maszyny wirtualnej Uruchom wiersz polecenia jako administrator.

7. Uruchom następujące polecenie:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Zamień na `<desired-drive-letter>` wybraną literę dysku (na przykład `y:` ).
    - Zamień wszystkie wystąpienia z `<storage-account-name>` nazwą konta magazynu określonego wcześniej.
    - Zamień na `<share-name>` nazwę utworzonego wcześniej udziału.
    - Zamień na `<storage-account-key>` klucz konta magazynu z platformy Azure.

    Na przykład:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile
     ```

8. Uruchom następujące polecenia, aby umożliwić użytkownikom pulpitu wirtualnego systemu Windows Tworzenie własnego kontenera profilu podczas blokowania dostępu do kontenerów profilów od innych użytkowników.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Zamień na `<mounted-drive-letter>` literę dysku, który został użyty do mapowania dysku.
    - Zamień na `<user-email>` nazwę UPN użytkownika lub grupy Active Directory, która zawiera użytkowników, którzy będą potrzebować dostępu do udziału.

    Na przykład:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Tworzenie kontenera profilu

Teraz, gdy Twoje profile są gotowe, Utwórzmy kontener profilu FSLogix.

Aby skonfigurować kontener profilu FSLogix:

1. Zaloguj się do maszyny wirtualnej hosta sesji skonfigurowanej na początku tego artykułu, a następnie [Pobierz i Zainstaluj agenta FSLogix](/fslogix/install-ht/).

2. Rozpakuj pobrany plik agenta FSLogix i przejdź do wersji **x64**  >  , a następnie otwórz **FSLogixAppsSetup.exe**.

3. Po uruchomieniu Instalatora zaznacz opcję **Akceptuję warunki i postanowienia licencyjne.** Jeśli ma to zastosowanie, podaj nowy klucz.

4. Wybierz pozycję **Zainstaluj**.

5. Otwórz **dysk C**, a następnie przejdź do pozycji **Program Files**  >  **FSLogix**  >  **Apps** , aby upewnić się, że Agent FSLogix został prawidłowo zainstalowany.

     >[!NOTE]
     > Jeśli w puli hostów istnieje wiele maszyn wirtualnych, należy powtórzyć kroki od 1 do 5 dla każdej maszyny wirtualnej.

6. Uruchom **Edytor rejestru** (regedit) jako administrator.

7. Przejdź do **komputera**  >  **HKEY_LOCAL_MACHINE**  >  **Software**  >  **FSLogix**, kliknij prawym przyciskiem myszy pozycję **FSLogix**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **klucz**.

8. Utwórz nowy klucz o nazwie **Profile**.

9.  Kliknij prawym przyciskiem myszy pozycję **Profile**, wybierz pozycję **Nowy**, a następnie wybierz **wartość DWORD (32-bitową).** Nazwij wartość **Enabled** i ustaw wartość **danych** na **1**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający klucz profile. Plik REG_DWORD jest wyróżniony, a jego wartość danych jest ustawiona na 1.](media/dword-value.png)

10. Kliknij prawym przyciskiem myszy pozycję **Profile**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **wartość ciągu wielociągowego**. Nadaj wartość **VHDLocations** , a w polu wartość danych wprowadź identyfikator URI dla udziału Azure Files `\\fsprofile.file.core.windows.net\share` .

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający klucz profile pokazujący plik VHDLocations. Jego wartość danych pokazuje identyfikator URI udziału Azure Files.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Przypisywanie użytkowników do hosta sesji

Teraz musisz przypisać użytkowników do hosta sesji.

Aby przypisać użytkowników:

1. Uruchom program Windows PowerShell jako administrator, a następnie uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows przy użyciu programu PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Gdy zostanie wyświetlony monit o podanie poświadczeń, wprowadź tego samego użytkownika, którym udzielono roli współautor TenantCreator, RDS lub RDS w dzierżawie pulpitu wirtualnego systemu Windows.

2. Uruchom następujące polecenia cmdlet, aby przypisać użytkownika do grupy pulpitów zdalnych:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Podobnie jak w przypadku wcześniejszych poleceń cmdlet, pamiętaj, aby zastąpić `<your-wvd-tenant>` , `<wvd-pool>` i `<user-principal>` z odpowiednimi wartościami.

    Na przykład:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Upewnij się, że Twój profil działa

Teraz wystarczy upewnić się, że utworzony profil istnieje i działa zgodnie z oczekiwaniami.

Aby zweryfikować profil:

1. Otwórz przeglądarkę i przejdź do [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Zaloguj się przy użyciu konta użytkownika przypisanego do grupy Pulpit zdalny.

3. Po nawiązaniu sesji użytkownika Otwórz Azure Portal i zaloguj się przy użyciu konta administracyjnego.

4. Na pasku bocznym wybierz pozycję **konta magazynu**.

5. Wybierz konto magazynu skonfigurowane jako udział plików dla puli hostów sesji i włączone przy użyciu usługi Azure AD DS.

6. Wybierz ikonę **pliki** , a następnie rozwiń swój udział.

    Jeśli wszystko zostało skonfigurowane prawidłowo, powinien zostać wyświetlony **katalog** o nazwie, który jest sformatowany w następujący sposób: `<user SID>-<username>` .

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz alternatywnych metod tworzenia kontenerów profilów FSLogix, zapoznaj się z następującymi artykułami:

- [Utwórz kontener profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).
- [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu Azure NetApp Files](create-fslogix-profile-container.md)

Bardziej szczegółowe informacje na temat pojęć związanych z kontenerami FSlogix dla plików platformy Azure można znaleźć w [kontenerach profilów FSlogix i plikach platformy Azure](fslogix-containers-azure-files.md).

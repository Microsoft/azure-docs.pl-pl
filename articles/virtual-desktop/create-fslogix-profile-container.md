---
title: Kontenery profilu FSLogix NetApp Windows Virtual Desktop — Azure
description: Jak utworzyć kontener profilu FSLogix przy użyciu Azure NetApp Files na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016832"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Tworzenie kontenera profilu przy użyciu Azure NetApp Files i AD DS

Zalecamy używanie kontenerów profilów FSLogix jako rozwiązania profilu użytkownika dla [usługi pulpitu wirtualnego systemu Windows](overview.md). Kontenery profilu FSLogix przechowują pełny profil użytkownika w jednym kontenerze i są przeznaczone do roamingu profilów w nietrwałych środowiskach obliczeniowych, takich jak pulpity wirtualne systemu Windows. Po zalogowaniu kontener dynamicznie dołącza do środowiska obliczeniowego przy użyciu lokalnie obsługiwanego wirtualnego dysku twardego (VHD) i wirtualnego dysku twardego funkcji Hyper-V (VHDX). Te zaawansowane technologie sterowników filtru umożliwiają natychmiastowe udostępnienie profilu użytkownika i pojawi się w systemie tak samo jak profil użytkownika lokalnego. Aby dowiedzieć się więcej na temat kontenerów profilów FSLogix, zobacz [kontenery profilu FSLogix i usługi Azure Files](fslogix-containers-azure-files.md).

Kontenery profilu FSLogix można tworzyć przy użyciu [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), łatwej w użyciu natywnej platformy platformy Azure, która ułatwia klientom szybkie i niezawodne udostępnianie woluminów SMB klasy korporacyjnej dla środowisk pulpitu wirtualnego systemu Windows. Aby dowiedzieć się więcej na temat Azure NetApp Files, zobacz [co to jest Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

W tym przewodniku pokazano, jak skonfigurować konto Azure NetApp Files i utworzyć kontenery profilu FSLogix w systemie Windows Virtual Desktop.

W tym artykule przyjęto założenie, że [Pule hostów](create-host-pools-azure-marketplace.md) zostały skonfigurowane i pogrupowane w co najmniej jednej dzierżawie w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak skonfigurować dzierżawców, zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) i [nasz wpis na blogu społeczności Tech](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Instrukcje przedstawione w tym przewodniku są przeznaczone dla użytkowników pulpitu wirtualnego systemu Windows. Jeśli szukasz bardziej ogólnych wskazówek dotyczących sposobu konfigurowania Azure NetApp Files i tworzenia kontenerów profilów FSLogix poza pulpitem wirtualnym systemu Windows, zapoznaj się z tematem [konfigurowanie Azure NetApp Files i Tworzenie woluminu systemu plików NFS — szybki start](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Ten artykuł nie obejmuje najlepszych rozwiązań w zakresie zabezpieczania dostępu do udziału Azure NetApp Files.

>[!NOTE]
>Jeśli szukasz materiału do porównania o różnych opcjach magazynu kontenera profilu FSLogix na platformie Azure, zobacz [Opcje magazynu dla kontenerów profilów FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kontenera profilu FSLogix dla puli hostów należy:

- Konfigurowanie i Konfigurowanie pulpitu wirtualnego systemu Windows
- Inicjowanie obsługi administracyjnej puli hostów systemu Windows
- [Włączanie subskrypcji Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Skonfiguruj konto Azure NetApp Files

Aby rozpocząć, musisz skonfigurować konto Azure NetApp Files.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Upewnij się, że Twoje konto ma uprawnienia współautora lub administrator.

2. Wybierz **ikonę Azure Cloud Shell** po prawej stronie paska wyszukiwania, aby otworzyć Azure Cloud Shell.

3. Gdy Azure Cloud Shell jest otwarty, wybierz opcję **PowerShell**.

4. Jeśli korzystasz z usługi Azure Cloud Shell, Utwórz konto magazynu w tej samej subskrypcji, aby zachować Azure NetApp Files i pulpit wirtualny systemu Windows.

   > [!div class="mx-imgBorder"]
   > ![W oknie konto magazynu z przyciskiem Utwórz magazyn w dolnej części okna wyróżniono kolor czerwony.](media/create-storage-button.png)

5. Po załadowaniu Azure Cloud Shell Uruchom następujące dwa polecenia cmdlet.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Po lewej stronie okna wybierz pozycję **wszystkie usługi**. Wprowadź **Azure NetApp Files** w polu wyszukiwania, które pojawia się w górnej części menu.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający użytkownika, który wprowadza "Azure NetApp Files" do pola wyszukiwania wszystkie usługi. Wyniki wyszukiwania pokazują zasób Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Wybierz **Azure NetApp Files** w wynikach wyszukiwania, a następnie wybierz pozycję **Utwórz**.

8. Wybierz przycisk **Add** (Dodaj).
9. Gdy zostanie otwarta karta **nowe konto NetApp** , wprowadź następujące wartości:

    - W obszarze **Nazwa** wprowadź nazwę konta usługi NetApp.
    - W obszarze **subskrypcja** wybierz subskrypcję dla konta magazynu skonfigurowanego w kroku 4 z menu rozwijanego.
    - W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów z menu rozwijanego lub Utwórz nową, wybierając pozycję **Utwórz nowe**.
    - W polu **Lokalizacja** wybierz region konta usługi NetApp z menu rozwijanego. Ten region musi być tym samym regionem co maszyny wirtualne hosta sesji.

   >[!NOTE]
   >Azure NetApp Files obecnie nie obsługuje instalowania woluminu w różnych regionach.

10. Po zakończeniu wybierz pozycję **Utwórz** , aby utworzyć konto NetApp.

## <a name="create-a-capacity-pool"></a>Tworzenie puli pojemności

Następnie utwórz nową pulę pojemności:

1. Przejdź do menu Azure NetApp Files i wybierz nowe konto.
2. W menu Konto wybierz pozycję **Pule pojemności** w obszarze usługa magazynu.
3. Wybierz pozycję **Dodaj pulę**.
4. Gdy zostanie otwarta karta **Nowa pula pojemności** , wprowadź następujące wartości:

    - W polu **Nazwa** wprowadź nazwę nowej puli pojemności.
    - W obszarze **poziom usług** wybierz żądaną wartość z menu rozwijanego. Zalecamy korzystanie z wersji **Premium** dla większości środowisk.
       >[!NOTE]
       >Ustawienie Premium zapewnia minimalną przepływność dostępną dla poziomu usługi Premium, czyli 256 MB/s. Może być konieczne dostosowanie tej przepływności dla środowiska produkcyjnego. Końcowa przepływność jest oparta na relacji opisanej w obszarze [limity przepływności](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Dla **rozmiaru (TIB)** wprowadź rozmiar puli pojemności, który najlepiej odpowiada Twoim potrzebom. Minimalny rozmiar to 4 TiB.

5. Po zakończeniu wybierz opcję **OK**.

## <a name="join-an-active-directory-connection"></a>Dołącz do połączenia Active Directory

Po tym celu należy przyłączyć połączenie Active Directory.

1. Wybierz pozycję **połączenia Active Directory** w menu po lewej stronie strony, a następnie wybierz przycisk **Dołącz** , aby otworzyć stronę **Active Directory sprzężenia** .

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający menu przyłączanie Active Directory połączeń.](media/active-directory-connections-menu.png)

2. Wprowadź następujące wartości na stronie **Dołączanie Active Directory** , aby dołączyć do połączenia:

    - W przypadku **podstawowej usługi DNS** wprowadź adres IP serwera DNS w środowisku, który może rozpoznać nazwę domeny.
    - W obszarze **domena** wprowadź w pełni kwalifikowaną nazwę domeny (FQDN).
    - Dla **prefiksu serwer SMB (konto komputera)** wpisz ciąg, który ma zostać dołączony do nazwy konta komputera.
    - W polu **Nazwa użytkownika** wprowadź nazwę konta z uprawnieniami do wykonywania przyłączania do domeny.
    - W polu **hasło** wprowadź hasło konta.

## <a name="create-a-new-volume"></a>Utwórz nowy wolumin

Następnie musisz utworzyć nowy wolumin.

1. Wybierz pozycję **woluminy**, a następnie wybierz pozycję **Dodaj wolumin**.

2. Po otwarciu karty **Utwórz wolumin** wprowadź następujące wartości:

    - W polu **Nazwa woluminu** wprowadź nazwę nowego woluminu.
    - W obszarze **Pula pojemności** wybierz właśnie utworzoną pulę pojemności z menu rozwijanego.
    - Dla **limitu przydziału (GIB)** wprowadź rozmiar woluminu odpowiedni dla danego środowiska.
    - W obszarze **Sieć wirtualna** wybierz istniejącą sieć wirtualną z połączeniem z kontrolerem domeny z menu rozwijanego.
    - W obszarze **podsieć** wybierz pozycję **Utwórz nową**. Należy pamiętać, że ta podsieć zostanie delegowana do Azure NetApp Files.

3.  Wybierz pozycję **Dalej: \> \> Protokół** , aby otworzyć kartę Protokół i skonfigurować parametry dostępu do woluminu.

## <a name="configure-volume-access-parameters"></a>Konfigurowanie parametrów dostępu do woluminów

Po utworzeniu woluminu Skonfiguruj parametry dostępu do woluminu.

1.  Wybierz opcję **SMB** jako typ protokołu.
2.  W obszarze Konfiguracja w menu rozwijanym **Active Directory** wybierz ten sam katalog, z którym nawiązano połączenie przy [użyciu połączenia Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Należy pamiętać, że istnieje limit jednego Active Directory na subskrypcję.
3.  W polu tekstowym **Nazwa udziału** wprowadź nazwę udziału używanego przez pulę hostów sesji i jej użytkowników.

4.  Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony. Spowoduje to otwarcie strony Walidacja. Po pomyślnym zweryfikowaniu woluminu wybierz pozycję **Utwórz**.

5.  W tym momencie nowy wolumin rozpocznie wdrażanie. Po zakończeniu wdrażania możesz użyć udziału Azure NetApp Files.

6.  Aby wyświetlić ścieżkę instalacji, wybierz pozycję **Przejdź do zasobu** i Znajdź ją na karcie Przegląd.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający ekran przegląd z czerwoną strzałką wskazującą ścieżkę instalacji.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurowanie FSLogix na maszynach wirtualnych hosta sesji (VM)

Ta sekcja opiera się na [tworzeniu kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).

1. [Pobierz plik FSLogix Agent. zip,](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) gdy nadal masz zdalny na maszynie wirtualnej hosta sesji.

2. Rozpakuj pobrany plik.

3. W pliku przejdź do wersji **x64**  >   i uruchom **FSLogixAppsSetup.exe**. Zostanie otwarte menu instalacja.

4.  Jeśli masz klucz produktu, wprowadź go w polu tekstowym klucz produktu.

5. Zaznacz pole wyboru obok pozycji **Akceptuję warunki i postanowienia licencyjne**.

6. Wybierz pozycję **Zainstaluj**.

7. Przejdź do **C: \\ Program Files \\ FSLogix \\ Apps** , aby potwierdzić, że Agent jest zainstalowany.

8. Z menu Start Uruchom polecenie **regedit** jako administrator.

9. Przejdź do **komputera \\ HKEY_LOCAL_MACHINE \\ oprogramowania \\ FSLogix**.

10. Utwórz klucz o nazwie **Profile**.

11.  Utwórz wartość o nazwie **Enabled** z typem **REG_DWORD** ustawionym na wartość **1**.

12. Utwórz wartość o nazwie **VHDLocations** z typem **wielociągowym** i ustaw jej wartość danych na identyfikator URI udziału Azure NetApp Files.

13. Utwórz wartość o nazwie **DeleteLocalProfileWhenVHDShouldApply** z wartością DWORD 1, aby uniknąć problemów z istniejącymi profilami lokalnymi przed zalogowaniem się.

     >[!WARNING]
     >Podczas tworzenia wartości DeleteLocalProfileWhenVHDShouldApply należy zachować ostrożność. Gdy system profilów FSLogix określa, że użytkownik powinien mieć profil FSLogix, ale profil lokalny już istnieje, kontener profilu zostanie trwale usunięty. Użytkownik zostanie następnie zalogowany przy użyciu nowego profilu FSLogix.

## <a name="assign-users-to-session-host"></a>Przypisywanie użytkowników do hosta sesji

1. Otwórz program **POWERSHELL ISE** jako administrator i zaloguj się do pulpitu wirtualnego systemu Windows.

2. Uruchom następujące polecenia cmdlet:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Po wyświetleniu monitu o poświadczenia wprowadź poświadczenia dla użytkownika z rolą twórca dzierżawy lub właściciel usług pulpitu zdalnego/RDS w dzierżawie pulpitu wirtualnego systemu Windows.

4. Uruchom następujące polecenia cmdlet, aby przypisać użytkownika do grupy Pulpit zdalny:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Upewnij się, że użytkownicy mogą uzyskiwać dostęp do udziału plików usługi Azure NetApp

1. Otwórz przeglądarkę internetową i przejdź do <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Zaloguj się przy użyciu poświadczeń użytkownika przypisanych do grupy Pulpit zdalny.

3. Po ustanowieniu sesji użytkownika Zaloguj się do Azure Portal przy użyciu konta administracyjnego.

4. Otwórz **Azure NetApp Files**, wybierz konto Azure NetApp Files, a następnie wybierz pozycję **woluminy**. Po otwarciu menu woluminy wybierz odpowiedni wolumin.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu konta NetApp skonfigurowanego wcześniej w Azure Portal z wybranym przyciskiem woluminy.](media/netapp-account.png)

5. Przejdź do karty **Przegląd** i upewnij się, że kontener profilu FSLogix używa przestrzeni.

6. Połącz się bezpośrednio z dowolną częścią maszyny wirtualnej puli hostów przy użyciu Pulpit zdalny i Otwórz **Eksploratora plików.** Następnie przejdź do **ścieżki instalacji** (w poniższym przykładzie ścieżka instalacji to \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ ANF-vol).

   W tym folderze powinien istnieć dysk VHD (lub VHDX) profilu, taki jak ten, w poniższym przykładzie.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający zawartość folderu w ścieżce instalacji. Wewnątrz programu jest pojedynczy plik VHD o nazwie "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Następne kroki

Kontenery profilu FSLogix można użyć do skonfigurowania udziału profilu użytkownika. Aby dowiedzieć się, jak utworzyć udziały profilu użytkownika przy użyciu nowych kontenerów, zobacz [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).

Możesz również utworzyć Azure Files udział plików do przechowywania profilu FSLogix w programie. Aby dowiedzieć się więcej, zobacz [Tworzenie udziału plików Azure Files z kontrolerem domeny](create-file-share.md).
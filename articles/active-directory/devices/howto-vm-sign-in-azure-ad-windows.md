---
title: Logowanie do maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Azure Active Directory (wersja zapoznawcza)
description: Logowanie do maszyny wirtualnej platformy Azure z systemem Windows za pomocą usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ffc176fc890d90e4201069ec1728eed69d4011
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826661"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logowanie do maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu uwierzytelniania Azure Active Directory (wersja zapoznawcza)

Organizacje mogą teraz korzystać z uwierzytelniania Azure Active Directory (AD) na maszynach wirtualnych platformy Azure z systemem **Windows Server 2019 Datacenter Edition** lub **Windows 10 1809** lub nowszym. Uwierzytelnianie na maszynach wirtualnych za pomocą usługi Azure AD zapewnia możliwość centralnego kontrolowania i wymuszania zasad. Narzędzia, takie jak kontrola dostępu oparta na rolach (Azure RBAC) i dostęp warunkowy usługi Azure AD, umożliwiają kontrolowanie, kto może uzyskać dostęp do maszyny wirtualnej. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną z systemem Windows Server 2019 do korzystania z uwierzytelniania usługi Azure AD.

> [!NOTE]
> Logowanie za pomocą usługi Azure AD na potrzeby maszyn wirtualnych systemu Windows Azure jest publiczną funkcją w wersji zapoznawczej programu Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Istnieje wiele zalet używania uwierzytelniania usługi Azure AD do logowania się do maszyn wirtualnych z systemem Windows na platformie Azure, w tym:

- Korzystaj z tych samych federacyjnych lub zarządzanych poświadczeń usługi Azure AD, których zwykle używasz.
- Nie trzeba już zarządzać kontami administratorów lokalnych.
- Usługa Azure RBAC umożliwia udzielenie odpowiedniego dostępu do maszyn wirtualnych na podstawie potrzeb i usunięcie go, gdy nie jest już potrzebny.
- Przed zezwoleniem na dostęp do maszyny wirtualnej dostęp warunkowy usługi Azure AD może wymusić dodatkowe wymagania, takie jak: 
   - Uwierzytelnianie wieloskładnikowe
   - Sprawdzanie ryzyka związanego z logowaniem
- Automatyzuj i Skaluj przyłączanie usługi Azure AD do maszyn wirtualnych z systemem Windows Azure, które są częścią wdrożeń infrastruktury VDI.

> [!NOTE]
> Po włączeniu tej funkcji maszyny wirtualne z systemem Windows na platformie Azure będą przyłączone do usługi Azure AD. Nie można przyłączyć go do innej domeny, takiej jak lokalna usługa AD lub AD DS platformy Azure. Jeśli chcesz to zrobić, musisz odłączyć maszynę wirtualną od dzierżawy usługi Azure AD przez odinstalowanie rozszerzenia.

## <a name="requirements"></a>Wymagania

### <a name="supported-azure-regions-and-windows-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Windows

Następujące dystrybucje systemu Windows są obecnie obsługiwane w ramach wersji zapoznawczej tej funkcji:

- Windows Server 2019 Datacenter
- System Windows 10 1809 i nowsze

> [!IMPORTANT]
> Połączenie zdalne z maszynami wirtualnymi dołączonymi do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są zarejestrowane w usłudze Azure AD (począwszy od systemu Windows 10 20H1), dołączone do usługi Azure AD lub hybrydowe usługi Azure AD przyłączone do tego **samego** katalogu co maszyna wirtualna 

Poniższe regiony platformy Azure są obecnie obsługiwane w ramach wersji zapoznawczej tej funkcji:

- Wszystkie regiony globalne platformy Azure

> [!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucję systemu Windows i w obsługiwanym regionie platformy Azure. Ta funkcja nie jest obecnie obsługiwana w Azure Government lub suwerennych chmurach.

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Windows na platformie Azure, musisz upewnić się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych przez port TCP 443:

- https: \/ /enterpriseregistration.Windows.NET
- https:\//login.microsoftonline.com
- https: \/ /Device.Login.microsoftonline.com
- https: \/ /pas.Windows.NET

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Włączanie logowania za pomocą usługi Azure AD w programie dla maszyn wirtualnych z systemem Windows na platformie Azure

Aby korzystać z logowania za pomocą usługi Azure AD w programie dla maszyny wirtualnej z systemem Windows na platformie Azure, musisz najpierw włączyć opcję logowania usługi Azure AD dla maszyny wirtualnej z systemem Windows, a następnie skonfigurować przypisania ról platformy Azure dla użytkowników, którzy mają uprawnienia do logowania się do maszyny wirtualnej.
Istnieje wiele sposobów włączania logowania do usługi Azure AD dla maszyny wirtualnej z systemem Windows:

- Korzystanie z Azure Portal środowiska podczas tworzenia maszyny wirtualnej z systemem Windows
- Korzystanie z Azure Cloud Shell środowiska podczas tworzenia maszyny wirtualnej z systemem Windows **lub dla istniejącej maszyny wirtualnej z systemem Windows**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Korzystanie z Azure Portal tworzenia środowiska maszyny wirtualnej w celu włączenia logowania do usługi Azure AD

Możesz włączyć logowanie za pomocą usługi Azure AD dla systemu Windows Server 2019 Datacenter lub Windows 10 1809 i nowszych obrazów maszyn wirtualnych. 

Aby utworzyć maszynę wirtualną z systemem Windows Server 2019 Datacenter na platformie Azure przy użyciu usługi Azure AD Logon: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com)przy użyciu konta, które ma dostęp do tworzenia maszyn wirtualnych, a następnie wybierz pozycję **+ Utwórz zasób**.
1. Wpisz **Windows Server** na pasku wyszukiwania portalu Marketplace.
   1. Kliknij pozycję **Windows Server** i wybierz opcję **Windows Server 2019 Datacenter** z listy rozwijanej wybierz plan oprogramowania.
   1. Kliknij pozycję **Utwórz**.
1. Na karcie Zarządzanie, Włącz opcję **Zaloguj się przy użyciu poświadczeń usługi AAD (wersja zapoznawcza)** w sekcji Azure Active Directory od pozycji wyłączone do **usługi.**
1. Upewnij **się, że w sekcji**tożsamość jest ustawiona **tożsamość zarządzana przypisana przez system** . Ta akcja powinna nastąpić automatycznie po włączeniu logowania przy użyciu poświadczeń usługi Azure AD.
1. Przejdź do reszty środowiska tworzenia maszyny wirtualnej. W tej wersji zapoznawczej konieczne będzie utworzenie nazwy użytkownika i hasła administratora dla maszyny wirtualnej.

![Logowanie przy użyciu poświadczeń usługi Azure AD Tworzenie maszyny wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Aby można było zalogować się do maszyny wirtualnej przy użyciu poświadczeń usługi Azure AD, należy najpierw skonfigurować przypisania ról dla maszyny wirtualnej zgodnie z opisem w jednej z poniższych sekcji.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Korzystanie z Azure Cloud Shell środowiska w celu włączenia logowania do usługi Azure AD

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Po prostu wybierz przycisk Kopiuj, aby skopiować kod, wklej go do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby go uruchomić. Usługę Cloud Shell można otworzyć na kilka sposobów:

Wybierz pozycję Wypróbuj w prawym górnym rogu bloku kodu.
Otwórz usługę Cloud Shell w swojej przeglądarce.
Wybierz przycisk Cloud Shell w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Jeśli musisz zainstalować lub uaktualnić program, zobacz artykuł [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 
1. Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create) przy użyciu obsługiwanej dystrybucji w obsługiwanym regionie. 
1. Zainstaluj rozszerzenie maszyny wirtualnej do logowania do usługi Azure AD. 

W poniższym przykładzie wdrożono maszynę wirtualną o nazwie myVM, która używa Win2019Datacenter, do grupy zasobów o nazwie Moja zasobów w regionie southcentralus. W poniższych przykładach można podać własną grupę zasobów i nazwy maszyn wirtualnych zgodnie z potrzebami.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Wymagane jest włączenie tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej przed zainstalowaniem rozszerzenia maszyny wirtualnej logowania do usługi Azure AD.

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut.

Na koniec Zainstaluj rozszerzenie maszyny wirtualnej do logowania do usługi Azure AD, aby włączyć logowanie do usługi Azure AD dla maszyny wirtualnej z systemem Windows. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj opcji [AZ VM Extension](/cli/azure/vm/extension#az-vm-extension-set) Set, aby zainstalować rozszerzenie AADLoginForWindows na maszynie wirtualnej o nazwie myVM w grupie zasobów:

> [!NOTE]
> Możesz zainstalować rozszerzenie AADLoginForWindows na istniejącej maszynie wirtualnej z systemem Windows Server 2019 lub Windows 10 1809 lub nowszym, aby umożliwić jej uwierzytelnianie w usłudze Azure AD. Poniżej przedstawiono przykład polecenia AZ CLI.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState` `Succeeded` Jest wyświetlany, gdy rozszerzenie zostanie zainstalowane na maszynie wirtualnej.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny wirtualnej

Po utworzeniu maszyny wirtualnej należy skonfigurować zasady RBAC platformy Azure, aby określić, kto może logować się do maszyny wirtualnej. Dwie role platformy Azure są używane do autoryzacji logowania do maszyny wirtualnej:

- **Logowanie administratora maszyny wirtualnej**: Użytkownicy z przypisaną rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami administratora.
- **Logowanie użytkownika maszyny wirtualnej**: Użytkownicy z przypisaną rolą mogą logować się do maszyny wirtualnej platformy Azure przy użyciu zwykłych uprawnień użytkownika.

> [!NOTE]
> Aby zezwolić użytkownikowi na logowanie do maszyny wirtualnej za pośrednictwem protokołu RDP, należy przypisać rolę logowania administratora maszyny wirtualnej lub użytkownika maszyny wirtualnej. Użytkownik platformy Azure z rolami właściciela lub współautora przypisany do maszyny wirtualnej nie ma automatycznie uprawnień do logowania się do maszyny wirtualnej za pośrednictwem protokołu RDP. Ma to na celu zapewnienie przeprowadzenia inspekcji między zbiorem osób kontrolujących maszyny wirtualne a zestawem osób, które mogą uzyskiwać dostęp do maszyn wirtualnych.

Istnieje wiele sposobów konfigurowania przypisań ról dla maszyny wirtualnej:

- Korzystanie z portalu usługi Azure AD
- Korzystanie z Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Korzystanie z portalu usługi Azure AD

Aby skonfigurować przypisania ról dla maszyn wirtualnych z systemem Windows Server 2019 Datacenter z obsługą usługi Azure AD:

1. Przejdź do strony przeglądu określonej maszyny wirtualnej
1. Wybierz pozycję **Kontrola dostępu (IAM)** z opcji menu
1. Wybierz pozycję **Dodaj**, a następnie **Dodaj przypisanie roli** , aby otworzyć okienko Dodawanie przypisania roli.
1. Z listy rozwijanej **rola** wybierz rolę, taką jak **Identyfikator logowania administratora maszyny wirtualnej** lub **Logowanie użytkownika maszyny wirtualnej**.
1. W polu **Wybierz** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.
1. Wybierz pozycję **Zapisz**, aby przypisać rolę.

Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w wybranym zakresie.

![Przypisywanie ról do użytkowników, którzy będą uzyskiwać dostęp do maszyny wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Korzystanie z Azure Cloud Shell

Poniższy przykład używa [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby przypisać rolę logowania administratora maszyny wirtualnej do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure zostanie uzyskana za pomocą [AZ Account show](/cli/azure/account#az-account-show), a zakres jest USTAWIANY na maszynę wirtualną utworzoną w poprzednim kroku przy użyciu [AZ VM show](/cli/azure/vm#az-vm-show). Zakres może być również przypisany do grupy zasobów lub poziomu subskrypcji i obowiązują normalne uprawnienia dziedziczenia kontroli RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz [Logowanie do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu uwierzytelniania Azure Active Directory](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena usługi AAD i nazwa użytkownika logowania nie są zgodne, należy określić identyfikator obiektu konta użytkownika `--assignee-object-id` , a nie tylko nazwę użytkownika dla `--assignee` . Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą elementu [AZ AD User list](/cli/azure/ad/user#az-ad-user-list).

Aby uzyskać więcej informacji na temat korzystania z usługi Azure RBAC do zarządzania dostępem do zasobów subskrypcji platformy Azure, zobacz następujące artykuły:

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Korzystanie z dostępu warunkowego

Można wymusić zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe lub sprawdzanie ryzyka logowania użytkownika przed autoryzacją dostępu do maszyn wirtualnych z systemem Windows na platformie Azure, które są włączone przy użyciu usługi Azure AD. Aby zastosować zasady dostępu warunkowego, należy wybrać opcję "Logowanie do maszyny wirtualnej platformy Azure" w opcji przypisywania aplikacji lub akcji w chmurze, a następnie użyć ryzyka związanego z logowaniem jako warunku i/lub wymagać uwierzytelniania wieloskładnikowego jako udzielenia kontroli dostępu. 

> [!NOTE]
> Jeśli używasz "Wymagaj uwierzytelniania wieloskładnikowego" jako udzielenia kontroli dostępu w celu zażądania dostępu do aplikacji "Logowanie maszyn wirtualnych z systemem Windows Azure", musisz podać rolę uwierzytelniania wieloskładnikowego jako część klienta inicjującego sesję RDP na docelowej maszynie wirtualnej z systemem Windows na platformie Azure. Jedynym sposobem osiągnięcia tego celu na kliencie z systemem Windows 10 jest użycie numeru PIN usługi Windows Hello dla firm lub uwierzytelniania biometrycznego za pomocą klienta RDP. Dodano obsługę uwierzytelniania biometrycznego do klienta RDP w systemie Windows 10 w wersji 1809. Uwierzytelnianie za pomocą usługi Windows Hello dla firm jest dostępne tylko w przypadku wdrożeń korzystających z modelu zaufania certyfikatów i aktualnie niedostępnych dla modeli zaufania kluczy.

> [!WARNING]
> Włączona/wymuszona usługa Azure Multi-Factor Authentication dla poszczególnych użytkowników nie jest obsługiwana w przypadku logowania do maszyny wirtualnej.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logowanie przy użyciu poświadczeń usługi Azure AD z maszyną wirtualną z systemem Windows

> [!IMPORTANT]
> Połączenie zdalne z maszynami wirtualnymi przyłączonymi do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są zarejestrowane w usłudze Azure AD (minimalna wymagana kompilacja to 20H1) lub usługa Azure AD przyłączona do tego **samego** katalogu co maszyna wirtualna. Ponadto do protokołu RDP przy użyciu poświadczeń usługi Azure AD użytkownik musi należeć do jednej z dwóch ról platformy Azure, identyfikatora logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny wirtualnej. Jeśli korzystasz z zarejestrowanego komputera z systemem Windows 10 w usłudze Azure AD, musisz wprowadzić poświadczenia w formacie AzureAD\UPN (np. AzureAD\john@contoso.com ). W tej chwili nie można zalogować się za pomocą usługi Azure bastionu przy użyciu uwierzytelniania Azure Active Directory z rozszerzeniem AADLoginForWindows; obsługiwany jest tylko bezpośredni protokół RDP.

Aby zalogować się do maszyny wirtualnej z systemem Windows Server 2019 przy użyciu usługi Azure AD: 

1. Przejdź do strony Przegląd maszyny wirtualnej, która została włączona z logowaniem do usługi Azure AD.
1. Wybierz pozycję **Połącz** , aby otworzyć blok Połącz z maszyną wirtualną.
1. Wybierz pozycję **Pobierz plik RDP**.
1. Wybierz pozycję **Otwórz** , aby uruchomić klienta Podłączanie pulpitu zdalnego.
1. Wybierz pozycję **Połącz** , aby uruchomić okno dialogowe logowania systemu Windows.
1. Zaloguj się przy użyciu poświadczeń usługi Azure AD.

Użytkownik jest zalogowany do maszyny wirtualnej systemu Windows Server 2019 Azure z uprawnieniami roli przypisanymi, takimi jak użytkownik maszyny wirtualnej lub administrator maszyny wirtualnej. 

> [!NOTE]
> Możesz zapisać. Plik RDP lokalnie na komputerze, aby uruchomić przyszłe połączenia pulpitu zdalnego z maszyną wirtualną zamiast konieczności przechodzenia do strony przeglądu maszyn wirtualnych w Azure Portal i przy użyciu opcji Połącz.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

Aby maszyna wirtualna mogła ukończyć proces przyłączania do usługi Azure AD, rozszerzenie AADLoginForWindows musi zostać pomyślnie zainstalowane. Jeśli instalacja rozszerzenia maszyny wirtualnej nie powiedzie się poprawnie, wykonaj następujące czynności.

1. Połącz protokół RDP z maszyną wirtualną przy użyciu konta administratora lokalnego i przejrzyj CommandExecuti'n. log w obszarze  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Jeśli rozszerzenie zostanie ponownie uruchomione po awarii początkowej, dziennik z błędem wdrożenia zostanie zapisany jako CommandExecution_YYYYMMDDHHMMSSSSS. log. "
1. Otwórz wiersz polecenia programu PowerShell na maszynie wirtualnej i sprawdź, czy te zapytania dotyczą punktu końcowego Instance Metadata Service (IMDS) uruchomionego na hoście platformy Azure:

   | Polecenie do uruchomienia | Oczekiwane dane wyjściowe |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Popraw informacje o maszynie wirtualnej platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Prawidłowy identyfikator dzierżawy skojarzony z subskrypcją platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Prawidłowy token dostępu wystawiony przez Azure Active Directory dla zarządzanej tożsamości przypisanej do tej maszyny wirtualnej |

   > [!NOTE]
   > Token dostępu można zdekodować przy użyciu narzędzia, takiego jak [http://calebb.net/](http://calebb.net/) . Sprawdź, czy identyfikator "AppID" w tokenie dostępu jest zgodny z zarządzaną tożsamością przypisaną do maszyny wirtualnej.

1. Upewnij się, że wymagane punkty końcowe są dostępne z maszyny wirtualnej przy użyciu wiersza polecenia:
   
   - zwinięcie https: \/ /login.microsoftonline.com/-D —
   - zwinięcie https: \/ /login.microsoftonline.com/ `<TenantID>` /-D —

   > [!NOTE]
   > Zamień `<TenantID>` na identyfikator dzierżawy usługi Azure AD skojarzony z subskrypcją platformy Azure.

   - zwinięcie https: \/ /enterpriseregistration.Windows.NET/-D-
   - zwinięcie https: \/ /Device.Login.microsoftonline.com/-D-
   - zwinięcie https: \/ /pas.Windows.NET/-D-

1. Stan urządzenia można wyświetlić, uruchamiając `dsregcmd /status` . Celem jest wskazywanie stanu urządzenia `AzureAdJoined : YES` .

   > [!NOTE]
   > Działanie funkcji Azure AD Join jest przechwytywane w Podglądzie zdarzeń w dzienniku Registration\Admin urządzenia użytkownika.

Jeśli rozszerzenie AADLoginForWindows kończy się niepowodzeniem z określonym kodem błędu, można wykonać następujące czynności:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: nie można zainstalować rozszerzenia AADLoginForWindows z kodem błędu terminalu "1007" i kodem zakończenia:-2145648574.

Ten kod zakończenia tłumaczy na DSREG_E_MSI_TENANTID_UNAVAILABLE, ponieważ rozszerzenie nie może zbadać informacji o dzierżawie usługi Azure AD.

1. Upewnij się, że maszyna wirtualna platformy Azure może pobrać TenantID z Instance Metadata Service.

   - Połącz protokół RDP z maszyną wirtualną jako administrator lokalny i sprawdź, czy punkt końcowy zwraca prawidłowy identyfikator dzierżawy, uruchamiając to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień na maszynie wirtualnej:
      
      - zwinięcie-H metadanych: prawda http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Administrator maszyny wirtualnej próbuje zainstalować rozszerzenie AADLoginForWindows, ale tożsamość zarządzana przypisana przez system nie została najpierw włączona. Przejdź do bloku tożsamość maszyny wirtualnej. Na karcie przypisane do systemu sprawdź, czy stan jest włączony.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: nie można zainstalować rozszerzenia AADLoginForWindows z kodem zakończenia:-2145648607

Ten kod zakończenia tłumaczy na DSREG_AUTOJOIN_DISC_FAILED, ponieważ rozszerzenie nie jest w stanie połączyć się z `https://enterpriseregistration.windows.net` punktem końcowym.

1. Sprawdź, czy wymagane punkty końcowe są dostępne z maszyny wirtualnej przy użyciu wiersza polecenia:

   - zwinięcie https: \/ /login.microsoftonline.com/-D —
   - zwinięcie https: \/ /login.microsoftonline.com/ `<TenantID>` /-D —
   
   > [!NOTE]
   > Zamień `<TenantID>` na identyfikator dzierżawy usługi Azure AD skojarzony z subskrypcją platformy Azure. Jeśli musisz znaleźć identyfikator dzierżawy, możesz umieścić wskaźnik myszy nad nazwą konta, aby uzyskać identyfikator katalogu/dzierżawy, lub wybrać Azure Active Directory właściwości > > identyfikator katalogu w Azure Portal.

   - zwinięcie https: \/ /enterpriseregistration.Windows.NET/-D-
   - zwinięcie https: \/ /Device.Login.microsoftonline.com/-D-
   - zwinięcie https: \/ /pas.Windows.NET/-D-

1. Jeśli którekolwiek z poleceń zakończy się niepowodzeniem z "nie można rozpoznać hosta `<URL>` ", spróbuj uruchomić to polecenie, aby określić serwer DNS, który jest używany przez maszynę wirtualną.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Zamień na w `<URL>` pełni kwalifikowane nazwy domen używane przez punkty końcowe, takie jak "login.microsoftonline.com".

1. Następnie sprawdź, czy określono publiczny serwer DNS, aby można było pomyślnie wykonać polecenie:

   `nslookup <URL> 208.67.222.222`

1. W razie potrzeby zmień serwer DNS przypisany do sieciowej grupy zabezpieczeń, do której należy maszyna wirtualna platformy Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: nie można zainstalować rozszerzenia AADLoginForWindows z kodem zakończenia: 51

Kod zakończenia 51 tłumaczy na "to rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny wirtualnej".

W publicznej wersji zapoznawczej rozszerzenie AADLoginForWindows jest przeznaczone tylko do zainstalowania w systemie Windows Server 2019 lub Windows 10 (kompilacja 1809 lub nowsza). Upewnij się, że wersja systemu Windows jest obsługiwana. Jeśli kompilacja systemu Windows nie jest obsługiwana, Odinstaluj rozszerzenie maszyny wirtualnej.

### <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby połączenia RDP z poświadczeniami usługi Azure AD nie obejmują żadnych ról platformy Azure przypisanych, nieautoryzowanego klienta lub metody logowania funkcji 2FA. Skorzystaj z poniższych informacji, aby rozwiązać te problemy.

Stan urządzenia i logowania jednokrotnego można wyświetlić, uruchamiając `dsregcmd /status` . Celem jest stan urządzenia, który ma być wyświetlany jako `AzureAdJoined : YES` i `SSO State` do wyświetlenia `AzureAdPrt : YES` .

Ponadto logowanie za pomocą protokołu RDP przy użyciu kont usługi Azure AD jest przechwytywane w Podglądzie zdarzeń w dzienniku zdarzeń AAD\Operational.

#### <a name="azure-role-not-assigned"></a>Rola platformy Azure nie jest przypisana

Jeśli po zainicjowaniu połączenia pulpitu zdalnego z maszyną wirtualną zobaczysz następujący komunikat o błędzie: 

- Twoje konto jest skonfigurowane tak, aby uniemożliwić korzystanie z tego urządzenia. Aby uzyskać więcej informacji, skontaktuj się z administratorem systemu

![Twoje konto jest skonfigurowane tak, aby uniemożliwić korzystanie z tego urządzenia.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Sprawdź, czy [skonfigurowano zasady kontroli RBAC platformy Azure](../../virtual-machines/linux/login-using-aad.md) dla maszyny wirtualnej, która przyznaje użytkownikowi nazwę logowania administratora maszyny wirtualnej lub użytkownika maszyny wirtualnej:
 
#### <a name="unauthorized-client"></a>Nieautoryzowany klient

Jeśli po zainicjowaniu połączenia pulpitu zdalnego z maszyną wirtualną zobaczysz następujący komunikat o błędzie: 

- Twoje poświadczenia nie działają

![Twoje poświadczenia nie działają](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Należy sprawdzić, czy komputer z systemem Windows 10 używany do inicjowania połączenia pulpitu zdalnego jest przyłączony do usługi Azure AD, czy też hybrydowa usługa Azure AD przyłączona do tego samego katalogu usługi Azure AD, do którego jest dołączona maszyna wirtualna. Aby uzyskać więcej informacji na temat tożsamości urządzeń, zobacz artykuł [co to jest tożsamość urządzenia](./overview.md).

> [!NOTE]
> System Windows 10 Build 20H1 dodaliśmy obsługę zarejestrowanego komputera z usługą Azure AD w celu zainicjowania połączenia RDP z maszyną wirtualną. W przypadku korzystania z rejestracji usługi Azure AD (nie dołączonej do usługi Azure AD lub hybrydowej usługi Azure AD) jako klienta RDP do inicjowania połączeń z maszyną wirtualną należy wprowadzić poświadczenia w formacie AzureAD\UPn (np. AzureAD\john@contoso.com ).

Sprawdź również, czy rozszerzenie AADLoginForWindows nie zostało odinstalowane po zakończeniu dołączania do usługi Azure AD.
 
#### <a name="mfa-sign-in-method-required"></a>Wymagana metoda logowania MFA

Jeśli po zainicjowaniu połączenia pulpitu zdalnego z maszyną wirtualną zobaczysz następujący komunikat o błędzie: 

- Metoda logowania, której próbujesz użyć, jest niedozwolona. Wypróbuj inną metodę logowania lub skontaktuj się z administratorem systemu.

![Metoda logowania, której próbujesz użyć, jest niedozwolona.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Jeśli skonfigurowano zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego (MFA), zanim będzie można uzyskać dostęp do zasobu, należy się upewnić, że komputer z systemem Windows 10 inicjujący połączenie pulpitu zdalnego z maszyną wirtualną loguje się przy użyciu metody silnego uwierzytelniania, takiej jak Windows Hello. Jeśli nie używasz metody silnego uwierzytelniania dla połączenia pulpitu zdalnego, zobaczysz poprzedni błąd.

Jeśli usługa Windows Hello dla firm nie została wdrożona, a jeśli nie jest to możliwe, możesz wykluczyć wymaganie usługi MFA, konfigurując zasady dostępu warunkowego, które nie zawierają aplikacji "Logowanie maszyn wirtualnych systemu Azure" z listy aplikacji w chmurze, które wymagają uwierzytelniania wieloskładnikowego. Aby dowiedzieć się więcej o usłudze Windows Hello dla firm, zobacz [Windows Hello dla firm — Omówienie](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> W systemie Windows 10 w wersji 1809 jest obsługiwane uwierzytelnianie numeru PIN usługi Windows Hello dla firm przy użyciu protokołu RDP. Używanie uwierzytelniania usługi Windows Hello dla firm podczas korzystania z protokołu RDP jest dostępne tylko w przypadku wdrożeń korzystających z modelu zaufania certyfikatów i aktualnie niedostępnych dla modelu zaufania kluczy.
 
## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy przy użyciu jej na [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Active Directory, zobacz [co to jest Azure Active Directory](../fundamentals/active-directory-whatis.md)

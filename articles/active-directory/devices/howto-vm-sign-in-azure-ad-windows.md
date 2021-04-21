---
title: Logowanie do maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Azure Active Directory (wersja zapoznawcza)
description: Logowanie się do maszyny wirtualnej platformy Azure z systemem Windows w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776505"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logowanie do maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Azure Active Directory uwierzytelniania (wersja zapoznawcza)

Organizacje mogą teraz korzystać z uwierzytelniania Azure Active Directory (AD) dla swoich maszyn wirtualnych platformy Azure z systemem **Windows Server 2019 Datacenter w** wersji lub Windows 10 **1809** lub nowszym. Uwierzytelnianie na maszynach wirtualnych za pomocą usługi Azure AD umożliwia centralne kontrolowanie i wymuszanie zasad. Narzędzia, takie jak kontrola dostępu na podstawie ról (RBAC) platformy Azure i dostęp warunkowy usługi Azure AD, umożliwiają kontrolowanie, kto może uzyskać dostęp do maszyny wirtualnej. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną z systemem Windows Server 2019 do korzystania z uwierzytelniania usługi Azure AD.

> [!NOTE]
> Logowanie do usługi Azure AD dla maszyn wirtualnych z systemem Windows na platformie Azure jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Istnieje wiele korzyści związanych z uwierzytelnianiem usługi Azure AD w celu logowania się do maszyn wirtualnych z systemem Windows na platformie Azure, w tym:

- Użyj tych samych poświadczeń federowanych lub zarządzanych usługi Azure AD, których zwykle używasz.
- Nie trzeba już zarządzać kontami administratorów lokalnych.
- Funkcja RBAC platformy Azure umożliwia udzielenie odpowiedniego dostępu do maszyn wirtualnych w zależności od potrzeb i usunięcie go, gdy nie jest już potrzebny.
- Przed zezwoleniem na dostęp do maszyny wirtualnej dostęp warunkowy usługi Azure AD może wymusić dodatkowe wymagania, takie jak: 
   - Uwierzytelnianie wieloskładnikowe
   - Sprawdzanie ryzyka związanego z logowaniem
- Automatyzuj i skaluj dołączanie do usługi Azure AD maszyn wirtualnych platformy Azure z systemem Windows, które są częścią wdrożeń infrastruktury VDI.

> [!NOTE]
> Po włączeniu tej możliwości maszyny wirtualne z systemem Windows na platformie Azure zostaną przyłączone do usługi Azure AD. Nie można dołączyć go do innej domeny, na przykład lokalnej usługi AD lub Azure AD DS. W razie potrzeby należy odłączyć maszynę wirtualną od dzierżawy usługi Azure AD, odinstalując rozszerzenie.

## <a name="requirements"></a>Wymagania

### <a name="supported-azure-regions-and-windows-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Windows

Następujące dystrybucje systemu Windows są obecnie obsługiwane w wersji zapoznawczej tej funkcji:

- Windows Server 2019 Datacenter
- Windows 10 1809 i nowsze

> [!IMPORTANT]
> Zdalne połączenie z maszynami wirtualnmi przyłączone do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są zarejestrowane w usłudze  Azure AD (począwszy od Windows 10 20H1), przyłączone do usługi Azure AD lub hybrydowe przyłączone do tego samego katalogu co maszyna wirtualna. 

Następujące regiony platformy Azure są obecnie obsługiwane w wersji zapoznawczej tej funkcji:

- Wszystkie regiony globalne platformy Azure

> [!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucję systemu Windows i w obsługiwanym regionie świadczenia usługi Azure. Ta funkcja nie jest obecnie obsługiwana w chmurach Azure Government suwerennych.

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Windows na platformie Azure, należy upewnić się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych za pośrednictwem portu TCP 443:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Włączanie logowania do usługi Azure AD na maszynie wirtualnej z systemem Windows na platformie Azure

Aby użyć logowania usługi Azure AD na maszynie wirtualnej z systemem Windows na platformie Azure, należy najpierw włączyć opcję logowania usługi Azure AD dla maszyny wirtualnej z systemem Windows, a następnie skonfigurować przypisania ról platformy Azure dla użytkowników, którzy są autoryzowani do logowania się do maszyny wirtualnej.
Istnieje wiele sposobów włączania logowania do usługi Azure AD dla maszyny wirtualnej z systemem Windows:

- Korzystanie z Azure Portal podczas tworzenia maszyny wirtualnej z systemem Windows
- Korzystanie z Azure Cloud Shell podczas tworzenia maszyny wirtualnej z systemem Windows **lub istniejącej maszyny wirtualnej z systemem Windows**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Włączanie logowania Azure Portal Azure AD przy użyciu funkcji tworzenia maszyny wirtualnej

Możesz włączyć logowanie do usługi Azure AD dla systemu Windows Server 2019 Datacenter lub Windows 10 1809 lub nowszych obrazów maszyn wirtualnych. 

Aby utworzyć maszynę wirtualną z systemem Windows Server 2019 Datacenter na platformie Azure przy użyciu logowania usługi Azure AD: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com)przy użyciu konta, które ma dostęp do tworzenia maszyn wirtualnych, a następnie wybierz pozycję + Utwórz **zasób.**
1. W polu Wyszukaj na pasku wyszukiwania witryny Marketplace wpisz Windows **Server.**
   1. Kliknij **pozycję Windows Server i** wybierz pozycję Windows Server **2019 Datacenter** z listy rozwijanej Wybierz plan oprogramowania.
   1. Kliknij pozycję **Utwórz**.
1. Na karcie "Zarządzanie" włącz opcję Zaloguj się przy użyciu poświadczeń usługi **AAD (wersja zapoznawcza)** w sekcji Azure Active Directory od Wył. do **Wł.**.
1. Upewnij **się, że tożsamość zarządzana przypisana przez system** w sekcji Tożsamość jest ustawiona na **wartość Wł.** Ta akcja powinna nastąpić automatycznie po włączeniu logowania przy użyciu poświadczeń usługi Azure AD.
1. Przejdź przez pozostałą część środowiska tworzenia maszyny wirtualnej. W tej wersji zapoznawczej musisz utworzyć nazwę użytkownika i hasło administratora dla maszyny wirtualnej.

![Logowanie przy użyciu poświadczeń usługi Azure AD w celu utworzenia maszyny wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Aby zalogować się do maszyny wirtualnej przy użyciu poświadczeń usługi Azure AD, należy najpierw skonfigurować przypisania ról dla maszyny wirtualnej zgodnie z opisem w jednej z poniższych sekcji.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Włączanie logowania w usłudze Azure AD przy użyciu Azure Cloud Shell użytkownika

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Po prostu wybierz przycisk Kopiuj, aby skopiować kod, wklej go do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby go uruchomić. Usługę Cloud Shell można otworzyć na kilka sposobów:

- Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.
- Otwórz usługę Cloud Shell w swojej przeglądarce.
- Wybierz przycisk Cloud Shell w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Jeśli musisz zainstalować lub uaktualnić system , zobacz artykuł Instalowanie interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli)

1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). 
1. Utwórz maszynę wirtualną za pomocą [narzędzia az vm create](/cli/azure/vm#az_vm_create) przy użyciu obsługiwanej dystrybucji w obsługiwanym regionie. 
1. Zainstaluj rozszerzenie maszyny wirtualnej logowania usługi Azure AD. 

Poniższy przykład wdraża maszynę wirtualną o nazwie myVM, która używa win2019Datacenter, w grupie zasobów o nazwie myResourceGroup w regionie southcentralus. W poniższych przykładach możesz podać własną grupę zasobów i nazwy maszyn wirtualnych zgodnie z potrzebami.

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
> Przed zainstalowaniem rozszerzenia maszyny wirtualnej logowania usługi Azure AD należy włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej.

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut.

Na koniec zainstaluj rozszerzenie maszyny wirtualnej logowania usługi Azure AD, aby włączyć logowanie do usługi Azure AD dla maszyny wirtualnej z systemem Windows. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj [az vm extension](/cli/azure/vm/extension#az_vm_extension_set) set, aby zainstalować rozszerzenie AADLoginForWindows na maszynie wirtualnej o nazwie w grupie `myVM` `myResourceGroup` zasobów:

> [!NOTE]
> Rozszerzenie AADLoginForWindows można zainstalować na istniejącej maszynie wirtualnej z systemem Windows Server 2019 lub Windows 10 1809 lub nowszym, aby umożliwić jej uwierzytelnianie w usłudze Azure AD. Poniżej przedstawiono przykład interfejsu wiersza polecenia az.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Po zainstalowaniu rozszerzenia na maszynie wirtualnej zostanie wyświetlone `provisioningState` `Succeeded` rozszerzenie .

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny wirtualnej

Teraz, po utworzeniu maszyny wirtualnej, musisz skonfigurować zasady RBAC platformy Azure, aby określić, kto może zalogować się do maszyny wirtualnej. Do autoryzowania logowania do maszyny wirtualnej są używane dwie role platformy Azure:

- **Identyfikator logowania administratora maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami administratora.
- **Logowanie użytkownika maszyny wirtualnej:** użytkownicy z tą przypisaną rolą mogą logować się do maszyny wirtualnej platformy Azure przy użyciu zwykłych uprawnień użytkownika.

> [!NOTE]
> Aby umożliwić użytkownikowi logowanie się do maszyny wirtualnej za pośrednictwem protokołu RDP, musisz przypisać rolę Logowanie administratora maszyny wirtualnej lub Logowanie użytkownika maszyny wirtualnej. Użytkownik platformy Azure z rolami właściciela lub współautora przypisanymi do maszyny wirtualnej nie ma automatycznie uprawnień do logowania się do maszyny wirtualnej za pośrednictwem protokołu RDP. Ma to na celu zapewnienie inspekcji rozdzielenia między zestawem osób kontrolujących maszyny wirtualne a zestawem osób, które mogą uzyskać dostęp do maszyn wirtualnych.

Istnieje wiele sposobów konfigurowania przypisań ról dla maszyny wirtualnej:

- Korzystanie z portalu usługi Azure AD
- Korzystanie z Azure Cloud Shell użytkownika

> [!NOTE]
> Role Logowanie administratora maszyny wirtualnej i Logowanie użytkownika maszyny wirtualnej używają funkcji dataActions i dlatego nie można ich przypisać w zakresie grupy zarządzania. Obecnie te role można przypisać tylko w ramach subskrypcji, grupy zasobów lub zakresu zasobów.

### <a name="using-azure-ad-portal-experience"></a>Korzystanie z portalu usługi Azure AD

Aby skonfigurować przypisania ról dla maszyn wirtualnych z systemem Windows Server 2019 Datacenter z włączoną usługą Azure AD:

1. Przejdź do określonej strony przeglądu maszyny wirtualnej
1. Wybierz **pozycję Kontrola dostępu (IAM)** z opcji menu
1. Wybierz **pozycję** Dodaj , **Dodaj przypisanie roli,** aby otworzyć okienko Dodawanie przypisania roli.
1. Z listy **rozwijanej** Rola wybierz rolę, taką jak Identyfikator logowania **administratora** maszyny wirtualnej lub Identyfikator logowania **użytkownika maszyny wirtualnej.**
1. W polu **Wybierz** wybierz użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.
1. Wybierz **pozycję Zapisz,** aby przypisać rolę.

Po kilku chwilach podmiot zabezpieczeń zostanie przypisany do roli w wybranym zakresie.

![Przypisywanie ról do użytkowników, którzy będą mieć dostęp do maszyny wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Korzystanie z Azure Cloud Shell użytkownika

W poniższym przykładzie [użyto az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby przypisać rolę logowania administratora maszyny wirtualnej do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure jest uzyskiwana za pomocą narzędzia [az account show](/cli/azure/account#az_account_show), a zakres jest ustawiany na maszynę wirtualną utworzoną w poprzednim kroku za pomocą narzędzia az vm [show](/cli/azure/vm#az_vm_show). Zakres można również przypisać na poziomie grupy zasobów lub subskrypcji i mają zastosowanie normalne uprawnienia do dziedziczenia RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz Logowanie się do maszyny wirtualnej z systemem Linux na platformie Azure przy [użyciu Azure Active Directory uwierzytelniania.](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena usługi AAD i nazwa użytkownika logowania nie są zgodne, należy określić identyfikator obiektu konta użytkownika za pomocą , a nie tylko nazwy `--assignee-object-id` użytkownika dla `--assignee` . Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą az [ad user list](/cli/azure/ad/user#az_ad_user_list).

Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów subskrypcji platformy Azure przy użyciu kontroli dostępu na podstawie ról platformy Azure, zobacz następujące artykuły:

- [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Korzystanie z dostępu warunkowego

Przed autoryzacją dostępu do maszyn wirtualnych z systemem Windows na platformie Azure, które są włączone przy użyciu logowania w usłudze Azure AD, można wymusić zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe lub kontrola ryzyka logowania użytkowników. Aby zastosować zasady dostępu warunkowego, musisz wybrać aplikację "Logowanie do maszyny wirtualnej platformy Azure z systemem Windows" z opcji przypisywania aplikacji lub akcji w chmurze, a następnie użyć ryzyka logowania jako warunku i/lub wymagać uwierzytelniania wieloskładnikowego jako kontroli dostępu przy udzielaniu dostępu. 

> [!NOTE]
> Jeśli używasz uwierzytelniania wieloskładnikowego "Wymagaj uwierzytelniania wieloskładnikowego" jako kontroli dostępu do żądania dostępu do aplikacji "Logowanie do maszyny wirtualnej z systemem Windows na platformie Azure", musisz podać oświadczenie uwierzytelniania wieloskładnikowego jako część klienta, który inicjuje sesję RDP z docelową maszyną wirtualną z systemem Windows na platformie Azure. Jedynym sposobem osiągnięcia tego celu na kliencie Windows 10 jest użycie numeru PIN Windows Hello dla firm uwierzytelniania biometrycznego z klientem RDP. Obsługa uwierzytelniania biometrycznego została dodana do klienta RDP Windows 10 wersji 1809. Pulpit zdalny używający Windows Hello dla firm uwierzytelniania jest dostępny tylko dla wdrożeń, które korzystają z modelu zaufania certyfikatów i obecnie nie są dostępne dla modelu zaufania kluczy.

> [!WARNING]
> Uwierzytelnianie wieloskładnikowe usługi Azure AD włączone/wymuszane przez użytkownika nie jest obsługiwane w przypadku logowania do maszyny wirtualnej.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logowanie się przy użyciu poświadczeń usługi Azure AD na maszynie wirtualnej z systemem Windows

> [!IMPORTANT]
> Zdalne połączenie z maszynami wirtualnmi przyłączone do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są zarejestrowane w usłudze Azure  AD (minimalna wymagana kompilacja to 20H1) lub przyłączone do usługi Azure AD lub hybrydowe przyłączone do tego samego katalogu co maszyna wirtualna. Ponadto do protokołu RDP przy użyciu poświadczeń usługi Azure AD użytkownik musi należeć do jednej z dwóch ról platformy Azure, identyfikatora logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny wirtualnej. Jeśli używasz komputera zarejestrowanego w Windows 10 Azure AD, musisz wprowadzić poświadczenia w `AzureAD\UPN` formacie (na przykład `AzureAD\john@contoso.com` ). Obecnie nie można Azure Bastion do logowania się przy użyciu uwierzytelniania Azure Active Directory za pomocą rozszerzenia AADLoginForWindows; Obsługiwane jest tylko bezpośrednie połączenie RDP.

Aby zalogować się do maszyny wirtualnej z systemem Windows Server 2019 przy użyciu usługi Azure AD: 

1. Przejdź do strony przeglądu maszyny wirtualnej, która została włączona przy użyciu logowania usługi Azure AD.
1. Wybierz **pozycję Połącz,** aby otworzyć blok Łączenie z maszyną wirtualną.
1. Wybierz pozycję **Pobierz plik RDP**.
1. Wybierz **pozycję Otwórz,** aby uruchomić Podłączanie pulpitu zdalnego klienta.
1. Wybierz **pozycję Połącz,** aby uruchomić okno dialogowe logowania systemu Windows.
1. Logowanie przy użyciu poświadczeń usługi Azure AD.

Teraz zalogowano się do maszyny wirtualnej platformy Azure z systemem Windows Server 2019 z przypisanymi uprawnieniami roli, takimi jak Użytkownik maszyny wirtualnej lub Administrator maszyny wirtualnej. 

> [!NOTE]
> Możesz zapisać plik . Plik RDP lokalnie na komputerze w celu uruchomienia przyszłych połączeń pulpitu zdalnego z maszyną wirtualną bez konieczności przechodzenia do strony przeglądu maszyny wirtualnej w Azure Portal i używania opcji połączenia.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

Rozszerzenie AADLoginForWindows musi zostać pomyślnie zainstalowane, aby maszyna wirtualna zakończyła proces dołączania do usługi Azure AD. Jeśli instalacja rozszerzenia maszyny wirtualnej nie powiedzie się, wykonaj następujące kroki.

1. Nawiąz do maszyny wirtualnej za pomocą protokołu RDP przy użyciu konta administratora lokalnego i sprawdź `CommandExecution.log` plik w obszarze:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Jeśli rozszerzenie zostanie uruchomione ponownie po początkowym niepowodzeniu, dziennik z błędem wdrażania zostanie zapisany jako `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Otwórz wiersz polecenia programu PowerShell na maszynie wirtualnej i sprawdź następujące zapytania względem punktu końcowego usługi Instance Metadata Service (IMDS) uruchomionego na hoście platformy Azure:

   | Polecenie do uruchomienia | Oczekiwane dane wyjściowe |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Poprawne informacje o maszynie wirtualnej platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Prawidłowy identyfikator dzierżawy skojarzony z subskrypcją platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Prawidłowy token dostępu wystawiony przez Azure Active Directory tożsamości zarządzanej przypisanej do tej maszyny wirtualnej |

   > [!NOTE]
   > Token dostępu można zdekodowyć za pomocą narzędzia, takiego [jak calebb.net](http://calebb.net/). Sprawdź, `appid` czy token dostępu odpowiada tożsamości zarządzanej przypisanej do maszyny wirtualnej.

1. Upewnij się, że wymagane punkty końcowe są dostępne z maszyny wirtualnej przy użyciu wiersza polecenia:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Zastąp `<TenantID>` identyfikatorem dzierżawy usługi Azure AD skojarzonym z subskrypcją platformy Azure.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Stan urządzenia można wyświetlić, `dsregcmd /status` uruchamiając . Celem jest, aby stan urządzenia był pokazywany jako `AzureAdJoined : YES` .

   > [!NOTE]
   > Działanie dołączania do usługi Azure AD jest przechwytywane w podglądzie zdarzeń w `User Device Registration\Admin` dzienniku.

Jeśli rozszerzenie AADLoginForWindows zakończy się niepowodzeniem z kodem błędu, możesz wykonać następujące kroki:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1. Nie można zainstalować rozszerzenia AADLoginForWindows z kodem błędu terminalu "1007" i kodem zakończenia: -2145648574.

Ten kod zakończenia przekłada się na `DSREG_E_MSI_TENANTID_UNAVAILABLE` , ponieważ rozszerzenie nie może odpytować informacji o dzierżawie usługi Azure AD.

1. Sprawdź, czy maszyna wirtualna platformy Azure może pobrać identyfikator dzierżawy z Instance Metadata Service.

   - Nawiązyj z maszyną wirtualną za pomocą protokołu RDP jako administrator lokalny i sprawdź, czy punkt końcowy zwraca prawidłowy identyfikator dzierżawy, uruchamiając to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień na maszynie wirtualnej:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. Administrator maszyny wirtualnej próbuje zainstalować rozszerzenie AADLoginForWindows, ale tożsamość zarządzana przypisana przez system nie włączyła najpierw maszyny wirtualnej. Przejdź do bloku Tożsamość maszyny wirtualnej. Na karcie Przypisano system sprawdź, czy ustawienie Stan jest przełączone na pozycję Wł.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2. Nie można zainstalować rozszerzenia AADLoginForWindows z kodem zakończenia: -2145648607

Ten kod zakończenia przekłada się na , ponieważ rozszerzenie nie może uzyskać `DSREG_AUTOJOIN_DISC_FAILED` dostępu do punktu `https://enterpriseregistration.windows.net` końcowego.

1. Sprawdź, czy wymagane punkty końcowe są dostępne z maszyny wirtualnej przy użyciu wiersza polecenia:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Zastąp `<TenantID>` identyfikatorem dzierżawy usługi Azure AD skojarzonym z subskrypcją platformy Azure. Jeśli musisz znaleźć identyfikator dzierżawy, możesz zatrzymać wskaźnik myszy na nazwie konta, aby uzyskać identyfikator katalogu/dzierżawy, lub wybrać pozycję właściwości Azure Active Directory > > **Identyfikator** katalogu w Azure Portal.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Jeśli którekolwiek z poleceń zakończy się niepowodzeniem z błędem "Nie można rozpoznać hosta", spróbuj uruchomić to polecenie, aby określić serwer DNS używany `<URL>` przez maszynę wirtualną.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Zastąp `<URL>` w pełni kwalifikowane nazwy domen używane przez punkty końcowe, takie jak `login.microsoftonline.com` .

1. Następnie sprawdź, czy określenie publicznego serwera DNS umożliwia powodzenie polecenia:

   `nslookup <URL> 208.67.222.222`

1. W razie potrzeby zmień serwer DNS przypisany do sieciowej grupy zabezpieczeń, do której należy maszyna wirtualna platformy Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3. Nie można zainstalować rozszerzenia AADLoginForWindows z kodem zakończenia: 51

Kod zakończenia 51 przekłada się na "To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny wirtualnej".

W publicznej wersji zapoznawczej rozszerzenie AADLoginForWindows jest przeznaczone tylko do zainstalowania w systemie Windows Server 2019 lub Windows 10 (kompilacja 1809 lub nowszy). Upewnij się, że wersja systemu Windows jest obsługiwana. Jeśli kompilacja systemu Windows nie jest obsługiwana, odinstaluj rozszerzenie maszyny wirtualnej.

### <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby połączenia RDP z poświadczeniami usługi Azure AD obejmują brak przypisanych ról platformy Azure, nieautoryzowany klient lub metodę logowania 2FA. Skorzystaj z poniższych informacji, aby rozwiązać te problemy.

Stan urządzenia i logowania jednokrotnego można wyświetlić, `dsregcmd /status` uruchamiając . Celem jest, aby stan urządzenia był pokazywany jako `AzureAdJoined : YES` i `SSO State` `AzureAdPrt : YES` .

Ponadto logowanie RDP przy użyciu kont usługi Azure AD jest przechwytywane w Podglądzie zdarzeń w obszarze `AAD\Operational` dzienników zdarzeń.

#### <a name="azure-role-not-assigned"></a>Nie przypisano roli platformy Azure

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną zostanie wyświetlony następujący komunikat o błędzie: 

- Twoje konto jest skonfigurowane tak, aby uniemożliwić ci korzystanie z tego urządzenia. Aby uzyskać więcej informacji, skontaktuj się z administratorem systemu.

![Twoje konto jest skonfigurowane tak, aby uniemożliwić ci korzystanie z tego urządzenia.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Sprawdź, czy skonfigurowano zasady [RBAC](../../virtual-machines/linux/login-using-aad.md) platformy Azure dla maszyny wirtualnej, która przyznaje użytkownikowi rolę logowania administratora maszyny wirtualnej lub logowanie użytkownika maszyny wirtualnej:

> [!NOTE]
> Jeśli masz problemy z przypisaniami ról platformy Azure, zobacz [Troubleshoot Azure RBAC (Rozwiązywanie problemów z RBAC platformy Azure).](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)
 
#### <a name="unauthorized-client"></a>Nieautoryzowany klient

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną zostanie wyświetlony następujący komunikat o błędzie: 

- Twoje poświadczenia nie działają.

![Twoje poświadczenia nie działają](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Sprawdź, czy komputer Windows 10, którego używasz do inicjowania połączenia pulpitu zdalnego, to komputer przyłączony do usługi Azure AD lub przyłączony hybrydową usługę Azure AD do tego samego katalogu usługi Azure AD, do którego jest do niej dołączyć maszynę wirtualną. Aby uzyskać więcej informacji na temat tożsamości urządzenia, zobacz artykuł [Co to jest tożsamość urządzenia.](./overview.md)

> [!NOTE]
> Windows 10 Build 20H1 dodano obsługę komputera zarejestrowanego w usłudze Azure AD w celu zainicjowania połączenia RDP z maszyną wirtualną. W przypadku korzystania z komputera zarejestrowanego w usłudze Azure AD (nie przyłączony do usługi Azure AD ani hybrydowego komputera przyłączony do usługi Azure AD) jako klienta RDP do inicjowania połączeń z maszyną wirtualną należy wprowadzić poświadczenia w formacie `AzureAD\UPN` (na przykład `AzureAD\john@contoso.com` ).

Sprawdź, czy rozszerzenie AADLoginForWindows nie zostało odinstalowane po zakończeniu dołączania do usługi Azure AD.

Upewnij się również, że zasady zabezpieczeń "Zabezpieczenia sieci: Zezwalaj na żądania uwierzytelniania PKU2U do tego komputera do używania tożsamości online" są włączone zarówno na serwerze, jak i **na** kliencie.
 
#### <a name="mfa-sign-in-method-required"></a>Wymagana metoda logowania za pomocą uwierzytelniania wieloskładnikowego

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną zostanie wyświetlony następujący komunikat o błędzie: 

- Metoda logowania, z których próbujesz się zalogować, jest niedozwolone. Wypróbuj inną metodę logowania lub skontaktuj się z administratorem systemu.

![Metoda logowania, z których próbujesz się zalogować, jest niedozwolone.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Jeśli skonfigurowano zasady dostępu warunkowego wymagające uwierzytelniania wieloskładnikowego (MFA) przed uzyskaniem dostępu do zasobu, należy się upewnić, że komputer z systemem Windows 10 inicjujący połączenie pulpitu zdalnego z maszyną wirtualną jest inicjujący przy użyciu silnej metody uwierzytelniania, takiej jak Windows Hello. Jeśli nie używasz silnej metody uwierzytelniania dla połączenia pulpitu zdalnego, zostanie wyświetlony poprzedni błąd.

Jeśli nie wdrożono usługi Windows Hello dla firm i jeśli nie jest to na razie opcja, można wykluczyć wymaganie uwierzytelniania wieloskładnikowego, konfigurując zasady dostępu warunkowego, które wykluczają aplikację "Logowanie do maszyny wirtualnej platformy Azure z systemem Windows" z listy aplikacji w chmurze, które wymagają uwierzytelniania wieloskładnikowego. Aby dowiedzieć się więcej na temat Windows Hello dla firm, zobacz [Windows Hello dla firm Omówienie.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> Windows Hello dla firm numeru PIN przy użyciu protokołu RDP jest obsługiwane przez Windows 10 w kilku wersjach, jednak obsługę uwierzytelniania biometrycznego przy użyciu protokołu RDP dodano w wersji 1809 Windows 10 1809. Używanie Windows Hello dla firm podczas protokołu RDP jest dostępne tylko w przypadku wdrożeń, które korzystają z modelu zaufania certyfikatów i obecnie nie są dostępne dla modelu zaufania kluczy.
 
## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy, korzystając z tej funkcji na [forum opinii usługi Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Azure Active Directory, [zobacz Co to jest Azure Active Directory](../fundamentals/active-directory-whatis.md).

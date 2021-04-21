---
title: Logowanie się do maszyny wirtualnej z systemem Linux przy użyciu Azure Active Directory poświadczeń
description: Dowiedz się, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux, aby zalogować się przy użyciu Azure Active Directory uwierzytelniania.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 654d47102685c04d6440d7c155e4d6eb931abcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788119"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Wersja zapoznawcza: logowanie do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu Azure Active Directory uwierzytelniania

Aby zwiększyć bezpieczeństwo maszyn wirtualnych z systemem Linux na platformie Azure, można zintegrować je z uwierzytelnianiem Azure Active Directory (AD). Korzystając z uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux, możesz centralnie kontrolować i wymuszać zasady, które zezwalają na dostęp do maszyn wirtualnych lub odmawiają go. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux do korzystania z uwierzytelniania usługi Azure AD.


> [!IMPORTANT]
> Azure Active Directory uwierzytelnianie jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Użyj tej funkcji na testowej maszynie wirtualnej, która ma być odrzucana po zakończeniu testowania.
>


Korzystanie z uwierzytelniania usługi Azure AD w celu logowania się do maszyn wirtualnych z systemem Linux na platformie Azure ma wiele zalet, w tym:

- **Ulepszone zabezpieczenia:**
  - Możesz użyć poświadczeń firmowej usługi AD, aby zalogować się do maszyn wirtualnych z systemem Linux na platformie Azure. Nie ma potrzeby tworzenia kont administratorów lokalnych i zarządzania okresem istnienia poświadczeń.
  - Dzięki zmniejszeniu zależności od kont administratorów lokalnych nie musisz martwić się o utratę/kradzież poświadczeń, użytkowników konfigurując słabe poświadczenia itp.
  - Zasady złożoności hasła i okresu istnienia hasła skonfigurowane dla katalogu usługi Azure AD ułatwiają również zabezpieczanie maszyn wirtualnych z systemem Linux.
  - Aby dodatkowo zabezpieczyć logowanie do maszyn wirtualnych platformy Azure, można skonfigurować uwierzytelnianie wieloskładnikowe.
  - Możliwość logowania się do maszyn wirtualnych z systemem Linux przy użyciu usługi Azure Active Directory działa również w przypadku klientów korzystających z usług [federowania](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblemowa współpraca:** Za pomocą kontroli dostępu opartej na rolach (RBAC) platformy Azure można określić, kto może logować się do danej maszyny wirtualnej jako zwykły użytkownik lub z uprawnieniami administratora. Gdy użytkownicy dołączą do twojego zespołu lub odejdzie z niego, możesz zaktualizować zasady RBAC platformy Azure dla maszyny wirtualnej, aby udzielić dostępu zgodnie z potrzebami. To środowisko jest znacznie prostsze niż czyszczenie maszyn wirtualnych w celu usunięcia niepotrzebnych kluczy publicznych SSH. Gdy pracownicy opuszczają organizację, a ich konto użytkownika jest wyłączone lub usunięte z usługi Azure AD, nie mają już dostępu do Zasobów.

## <a name="supported-azure-regions-and-linux-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Linux

Następujące dystrybucje systemu Linux są obecnie obsługiwane w wersji zapoznawczej tej funkcji:

| Dystrybucja | Wersja |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Przestępny 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 i Ubuntu Server 18.04 |


Następujące regiony platformy Azure są obecnie obsługiwane w wersji zapoznawczej tej funkcji:

- Wszystkie globalne regiony platformy Azure

>[!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucję systemu Linux i w obsługiwanym regionie świadczenia usługi Azure. Ta funkcja nie jest obsługiwana w chmurach Azure Government suwerennych chmurach.
>
> Używanie tego rozszerzenia w klastrach usługi Azure Kubernetes Service (AKS) nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [Zasady pomocy technicznej dla aks.](../../aks/support-policies.md)


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Linux na platformie Azure, upewnij się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych za pośrednictwem portu TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> Obecnie nie można skonfigurować sieciowych grup zabezpieczeń platformy Azure dla maszyn wirtualnych włączonych przy użyciu uwierzytelniania usługi Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz grupę zasobów za pomocą narzędzia [az group create,](/cli/azure/group#az_group_create)a następnie utwórz maszynę wirtualną za pomocą narzędzia [az vm create](/cli/azure/vm#az_vm_create) przy użyciu obsługiwanej dystrybucji i w obsługiwanym regionie. Poniższy przykład wdraża maszynę wirtualną o nazwie *myVM,* która używa systemu *Ubuntu 16.04 LTS* w grupie zasobów o nazwie *myResourceGroup* w regionie *southcentralus.* W poniższych przykładach możesz podać własną grupę zasobów i nazwy maszyn wirtualnych zgodnie z potrzebami.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej logowania usługi Azure AD

> [!NOTE]
> Jeśli to rozszerzenie zostanie wdrożone na wcześniej utworzonej maszynie wirtualnej, upewnij się, że maszyna ma przydzielone co najmniej 1 GB pamięci. W innym przypadku instalacja rozszerzenia nie powiedzie się

Aby zalogować się do maszyny wirtualnej z systemem Linux przy użyciu poświadczeń usługi Azure AD, Azure Active Directory rozszerzenia maszyny wirtualnej logowania. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj [az vm extension set,](/cli/azure/vm/extension#az_vm_extension_set) aby zainstalować rozszerzenie *AADLoginForLinux* na maszynie wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Stan *provisioningState* *powodzenie jest* wyświetlany po pomyślnym zainstalowaniu rozszerzenia na maszynie wirtualnej. Aby zainstalować rozszerzenie, maszyna wirtualna wymaga uruchomionego agenta maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie agenta maszyny wirtualnej](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny wirtualnej

Zasady kontroli dostępu opartej na rolach (RBAC) platformy Azure określają, kto może logować się do maszyny wirtualnej. Do autoryzowania logowania do maszyny wirtualnej są używane dwie role platformy Azure:

- **Logowanie administratora maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami administratora systemu Windows lub użytkownika głównego systemu Linux.
- **Logowanie użytkownika maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure przy użyciu zwykłych uprawnień użytkownika.

> [!NOTE]
> Aby umożliwić użytkownikowi logowanie się do maszyny wirtualnej za  pośrednictwem sieci SSH, musisz przypisać rolę Logowanie administratora maszyny wirtualnej lub Logowanie użytkownika *maszyny wirtualnej.* Role Logowanie administratora maszyny wirtualnej i Logowanie użytkownika maszyny wirtualnej używają funkcji dataActions i dlatego nie można ich przypisać w zakresie grupy zarządzania. Obecnie te role można przypisać tylko w ramach subskrypcji, grupy zasobów lub zakresu zasobów. Użytkownik platformy Azure z  *rolami właściciela* lub współautora przypisanymi do maszyny wirtualnej nie ma automatycznie uprawnień do logowania się do maszyny wirtualnej za pośrednictwem SSH. 

W poniższym przykładzie [użyto az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby przypisać rolę logowania *administratora* maszyny wirtualnej do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure jest uzyskiwana  za pomocą narzędzia [az account show](/cli/azure/account#az_account_show), a zakres jest ustawiany na maszynę wirtualną utworzoną w poprzednim kroku za pomocą narzędzia az [vm show](/cli/azure/vm#az_vm_show). Zakres można również przypisać na poziomie grupy zasobów lub subskrypcji i mają zastosowanie normalne uprawnienia do dziedziczenia RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure RBAC (Azure RBAC)](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena usługi AAD i nazwa użytkownika logowania nie są zgodne, należy określić identyfikator obiektu konta użytkownika za pomocą identyfikatora *--assignee-object-id,* a nie tylko nazwy użytkownika *dla --assignee.* Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą az [ad user list](/cli/azure/ad/user#az_ad_user_list).

Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów subskrypcji platformy Azure przy użyciu kontroli dostępu na podstawie ról platformy Azure, zobacz using the Azure CLI , [Azure Portal](../../role-based-access-control/role-assignments-portal.md), or Azure PowerShell (Korzystanie z interfejsu wiersza polecenia platformy [Azure,](../../role-based-access-control/role-assignments-cli.md)usługi [Azure Portal lub Azure PowerShell).](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Korzystanie z dostępu warunkowego

Przed autoryzacją dostępu do maszyn wirtualnych z systemem Linux na platformie Azure, które są włączone przy użyciu logowania usługi Azure AD, można wymusić zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe lub kontrola ryzyka logowania użytkowników. Aby zastosować zasady dostępu warunkowego, musisz wybrać aplikację "logowanie do maszyny wirtualnej z systemem Linux w usłudze Microsoft Azure" z aplikacji w chmurze lub opcji przypisania akcji, a następnie użyć ryzyka logowania jako warunku i/lub wymagać uwierzytelniania wieloskładnikowego jako metody udzielania kontroli dostępu. 

> [!WARNING]
> Uwierzytelnianie wieloskładnikowe usługi Azure AD włączone/wymuszane przez użytkownika nie jest obsługiwane w przypadku logowania do maszyny wirtualnej.

## <a name="log-in-to-the-linux-virtual-machine"></a>Logowanie się do maszyny wirtualnej z systemem Linux

Najpierw wyświetl publiczny adres IP maszyny wirtualnej za pomocą az [vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Zaloguj się do maszyny wirtualnej platformy Azure z systemem Linux przy użyciu poświadczeń usługi Azure AD. Parametr `-l` umożliwia określenie własnego adresu konta usługi Azure AD. Zamień przykładowe konto na własne. Adresy kont należy wprowadzać małymi literami. Zastąp przykładowy adres IP publicznym adresem IP maszyny wirtualnej z poprzedniego polecenia.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Zostanie wyświetlony monit o zalogowanie się do usługi Azure AD przy użyciu kodu użycia tylko raz na stronie [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Skopiuj i wklej kod użycia tylko raz na stronie logowania urządzenia.

Po wyświetleniu monitu wprowadź swoje poświadczenia logowania usługi Azure AD na stronie logowania. 

Po pomyślnym uwierzytelnieniu w przeglądarce internetowej zostanie wyświetlony następujący komunikat: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zamknij okno przeglądarki, wróć do wiersza polecenia SSH i naciśnij klawisz **Enter.** 

Zalogowano się do maszyny wirtualnej platformy Azure z systemem Linux przy użyciu przypisanych uprawnień roli, takich jak użytkownik maszyny wirtualnej *lub* administrator *maszyny wirtualnej.* Jeśli twoje konto użytkownika ma przypisaną rolę *logowania administratora* maszyny wirtualnej, możesz użyć polecenia, które wymagają `sudo` uprawnień administratora.

## <a name="sudo-and-aad-login"></a>Logowanie sudo i aad

Przy pierwszym uruchomieniu sudo zostanie poproszona o uwierzytelnienie po raz drugi. Jeśli nie chcesz ponownie uwierzytelniać się, aby uruchomić plik sudo, możesz edytować plik sudoers i `/etc/sudoers.d/aad_admins` zastąpić ten wiersz:

```bash
%aad_admins ALL=(ALL) ALL
```

W tym wierszu:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby połączenia SSH przy użyciu poświadczeń usługi Azure AD obejmują brak przypisanych ról platformy Azure i powtarzające się monity o zalogowanie. Poniższe sekcje zawierają informacje na temat tych problemów.

### <a name="access-denied-azure-role-not-assigned"></a>Odmowa dostępu: nie przypisano roli platformy Azure

Jeśli w wierszu polecenia SSH zostanie wyświetlony następujący błąd, sprawdź, czy skonfigurowano zasady  RBAC platformy Azure dla maszyny wirtualnej, która przyznaje użytkownikowi rolę logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny *wirtualnej:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Jeśli masz problemy z przypisaniami ról platformy Azure, zobacz Troubleshoot Azure RBAC (Rozwiązywanie problemów [z RBAC platformy Azure).](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)

### <a name="continued-ssh-sign-in-prompts"></a>Dalsze monity logowania SSH

Jeśli krok uwierzytelniania zostanie pomyślnie ukończony w przeglądarce internetowej, może zostać natychmiast wyświetlony monit o zalogowanie się ponownie przy użyciu nowego kodu. Ten błąd jest zwykle spowodowany niezgodnością nazwy logowania określonej w wierszu polecenia SSH i kontem, za pomocą których zalogowano się do usługi Azure AD. Aby rozwiązać ten problem:

- Sprawdź, czy nazwa logowania określona w wierszu polecenia SSH jest poprawna. Literówka w nazwie logowania może spowodować niezgodność nazwy logowania określonej w wierszu polecenia SSH i konta, za pomocą których zalogowano się do usługi Azure AD. Na przykład wpisaliśmy tekst *azuresuer \@ contoso.onmicrosoft.com* zamiast *azureuser \@ contoso.onmicrosoft.com*.
- Jeśli masz wiele kont użytkowników, upewnij się, że nie podaniem innego konta użytkownika w oknie przeglądarki podczas logowania się do usługi Azure AD.
- Linux to system operacyjny, w przypadku których wielkość liter jest zróżnicowa. Istnieje różnica między Azureuser@contoso.onmicrosoft.com '' i ' azureuser@contoso.onmicrosoft.com ', która może spowodować niezgodność. Upewnij się, że w wierszu polecenia SSH określono nazwę UPN z prawidłową wielkością liter.

### <a name="other-limitations"></a>Inne ograniczenia

Użytkownicy dziedziczący prawa dostępu za pośrednictwem zagnieżdżonych grup lub przypisań ról nie są obecnie obsługiwani. Do użytkownika lub grupy należy bezpośrednio przypisać wymagane [przypisania ról.](#configure-role-assignments-for-the-vm) Na przykład użycie grup zarządzania lub zagnieżdżonych przypisań ról grup nie przyzna odpowiednich uprawnień, aby zezwolić użytkownikowi na logowanie.

## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy, korzystając z jej funkcji na [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Azure Active Directory, zobacz [Co to jest Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

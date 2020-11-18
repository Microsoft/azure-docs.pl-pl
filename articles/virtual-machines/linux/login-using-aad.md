---
title: Logowanie do maszyny wirtualnej z systemem Linux przy użyciu poświadczeń Azure Active Directory
description: Dowiedz się, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux, aby zalogować się przy użyciu uwierzytelniania Azure Active Directory.
author: SanDeo-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: sandeo
ms.openlocfilehash: 882df9d0dcb01d6321455b845fed087a5e14ccc6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843081"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Wersja zapoznawcza: Logowanie do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu uwierzytelniania Azure Active Directory

Aby zwiększyć bezpieczeństwo maszyn wirtualnych z systemem Linux na platformie Azure, można zintegrować z usługą Azure Active Directory (AD) Authentication. Korzystając z uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux, można centralnie kontrolować i wymuszać zasady zezwalające na dostęp do maszyn wirtualnych lub odmawiające tego dostępu. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux do korzystania z uwierzytelniania usługi Azure AD.


> [!IMPORTANT]
> Uwierzytelnianie Azure Active Directory jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Użyj tej funkcji na testowej maszynie wirtualnej, która powinna zostać odrzucona po przetestowaniu.
>


Istnieje wiele zalet używania uwierzytelniania usługi Azure AD do logowania się do maszyn wirtualnych z systemem Linux na platformie Azure, w tym:

- **Ulepszone zabezpieczenia:**
  - Aby zalogować się do maszyn wirtualnych z systemem Linux na platformie Azure, możesz użyć firmowych poświadczeń usługi AD. Nie ma potrzeby tworzenia kont administratorów lokalnych i zarządzania okresem istnienia poświadczeń.
  - Zmniejszając swój zależność od lokalnych kont administratorów, nie musisz martwić się o utratę poświadczeń/kradzież, użytkownicy konfigurujący słabe poświadczenia itp.
  - Zasady dotyczące złożoności haseł i haseł skonfigurowane dla katalogu usługi Azure AD również pomagają w zabezpieczaniu maszyn wirtualnych z systemem Linux.
  - Aby dodatkowo zabezpieczyć logowanie do usługi Azure Virtual Machines, można skonfigurować uwierzytelnianie wieloskładnikowe.
  - Możliwość zalogowania się do maszyn wirtualnych systemu Linux przy użyciu Azure Active Directory również działa dla klientów korzystających z [usług federacyjnych](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblemowa współpraca:** Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można określić, kto może logować się do danej maszyny wirtualnej jako zwykły użytkownik lub z uprawnieniami administratora. Gdy użytkownicy dołączają lub opuszczają Twój zespół, możesz zaktualizować zasady kontroli dostępu platformy Azure dla maszyny wirtualnej, aby przyznać im odpowiednie uprawnienia. To środowisko jest znacznie prostsze niż trzeba do szybkiej kontroli maszyn wirtualnych, aby usunąć zbędne klucze publiczne SSH. Gdy pracownicy opuszczają organizację, a ich konto użytkownika jest wyłączone lub usunięte z usługi Azure AD, nie mają już dostępu do zasobów.

## <a name="supported-azure-regions-and-linux-distributions"></a>Obsługiwane dystrybucje regionów platformy Azure i systemu Linux

Następujące dystrybucje systemu Linux są obecnie obsługiwane w ramach wersji zapoznawczej tej funkcji:

| Dystrybucja | Wersja |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE przestępnie 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 i Ubuntu Server 18,04 |


Poniższe regiony platformy Azure są obecnie obsługiwane w ramach wersji zapoznawczej tej funkcji:

- Wszystkie regiony globalne platformy Azure

>[!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucji systemu Linux i w obsługiwanym regionie platformy Azure. Ta funkcja nie jest obsługiwana w Azure Government lub suwerennych chmurach.
>
> Korzystanie z tego rozszerzenia w klastrach usługi Azure Kubernetes Service (AKS) nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej dla AKS](../../aks/support-policies.md).


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Linux na platformie Azure, musisz upewnić się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych przez port TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /Device.Login.microsoftonline.com
* https: \/ /pas.Windows.NET
* https:\//management.azure.com
* https: \/ /Packages.Microsoft.com

> [!NOTE]
> Obecnie nie można skonfigurować grup zabezpieczeń sieci platformy Azure dla maszyn wirtualnych z włączoną funkcją uwierzytelniania usługi Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create), a następnie utwórz maszynę wirtualną z funkcją [AZ VM Create](/cli/azure/vm#az-vm-create) przy użyciu obsługiwanego dystrybucji i w obsługiwanym regionie. W poniższym przykładzie wdrożono maszynę wirtualną o nazwie *myVM* , która używa *Ubuntu 16,04 LTS* do grupy zasobów o nazwie Moja *resourceName* w regionie *southcentralus* . W poniższych przykładach można podać własną grupę zasobów i nazwy maszyn wirtualnych zgodnie z potrzebami.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Zainstaluj rozszerzenie maszyny wirtualnej do logowania do usługi Azure AD

> [!NOTE]
> W przypadku wdrażania tego rozszerzenia na wcześniej utworzonej maszynie wirtualnej upewnij się, że maszyna ma co najmniej 1 GB przydzieloną pamięć, w przeciwnym razie rozszerzenie nie zostanie zainstalowane

Aby zalogować się do maszyny wirtualnej z systemem Linux przy użyciu poświadczeń usługi Azure AD, zainstaluj rozszerzenie maszyny wirtualnej logowania Azure Active Directory. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj opcji [AZ VM Extension Set](/cli/azure/vm/extension#az-vm-extension-set) , aby zainstalować rozszerzenie *AADLoginForLinux* na maszynie wirtualnej o nazwie *myVM* w *grupie zasobów* :

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* *powiodło* się, gdy rozszerzenie zostanie pomyślnie zainstalowane na maszynie wirtualnej. Aby można było zainstalować rozszerzenie, maszyna wirtualna musi być uruchomionym agentem maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie agenta maszyny wirtualnej](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny wirtualnej

Zasady kontroli dostępu opartej na rolach (RBAC) na platformie Azure określają, kto może logować się do maszyny wirtualnej. Dwie role platformy Azure są używane do autoryzacji logowania do maszyny wirtualnej:

- **Logowanie administratora maszyny wirtualnej**: Użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami administratora systemu Windows lub użytkownika root w systemie Linux.
- **Logowanie użytkownika maszyny wirtualnej**: Użytkownicy z przypisaną rolą mogą logować się do maszyny wirtualnej platformy Azure przy użyciu zwykłych uprawnień użytkownika.

> [!NOTE]
> Aby umożliwić użytkownikowi logowanie się do maszyny wirtualnej za pośrednictwem protokołu SSH, należy przypisać rolę logowania *administratora maszyny wirtualnej* lub *użytkownika maszyny wirtualnej* . Użytkownik platformy Azure z rolami *właściciela* lub *współautora* przypisany do maszyny wirtualnej nie ma automatycznie uprawnień do logowania się do maszyny wirtualnej za pośrednictwem protokołu SSH.

Poniższy przykład używa [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby przypisać rolę *logowania administratora maszyny wirtualnej* do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure zostanie uzyskana za pomocą [AZ Account show](/cli/azure/account#az-account-show), a *zakres* jest ustawiany na maszynę wirtualną utworzoną w poprzednim kroku przy użyciu [AZ VM show](/cli/azure/vm#az-vm-show). Zakres może być również przypisany do grupy zasobów lub poziomu subskrypcji i obowiązują normalne uprawnienia dziedziczenia kontroli RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena i nazwa użytkownika logowania w usłudze AAD nie są zgodne, należy określić identyfikator obiektu konta użytkownika z parametrem *--cesjonariusz-Object-ID*, a nie tylko nazwą użytkownika dla *--* przyłączania. Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą elementu [AZ AD User list](/cli/azure/ad/user#az-ad-user-list).

Aby uzyskać więcej informacji na temat korzystania z usługi Azure RBAC do zarządzania dostępem do zasobów subskrypcji platformy Azure, zobacz Korzystanie z [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)lub [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Możesz również skonfigurować usługę Azure AD, aby wymagać uwierzytelniania wieloskładnikowego dla określonego użytkownika do logowania się do maszyny wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure AD Multi-Factor Authentication w chmurze](../../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Logowanie do maszyny wirtualnej z systemem Linux

Najpierw Wyświetl publiczny adres IP maszyny wirtualnej za pomocą [AZ VM show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Zaloguj się do maszyny wirtualnej platformy Azure z systemem Linux przy użyciu poświadczeń usługi Azure AD. `-l`Parametr umożliwia określenie własnego adresu konta usługi Azure AD. Zastąp przykładowe konto własnym. Adresy kont należy wprowadzać w postaci tylko małych liter. Zastąp przykładowy adres IP publicznym adresem IP maszyny wirtualnej z poprzedniego polecenia.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Zostanie wyświetlony monit o zalogowanie się do usługi Azure AD za pomocą jednorazowego kodu w usłudze [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Skopiuj i wklej kod jednorazowego użycia na stronie logowania do urządzenia.

Po wyświetleniu monitu wprowadź poświadczenia logowania usługi Azure AD na stronie logowania. 

Po pomyślnym uwierzytelnieniu w przeglądarce internetowej zostanie wyświetlony następujący komunikat: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zamknij okno przeglądarki, Wróć do wiersza polecenia SSH, a następnie naciśnij klawisz **Enter** . 

Użytkownik jest zalogowany do maszyny wirtualnej platformy Azure z systemem Linux z uprawnieniami roli przypisanymi, takimi jak *użytkownik maszyny wirtualnej* lub *administrator maszyny wirtualnej*. Jeśli Twoje konto użytkownika ma przypisaną rolę *logowania administratora maszyny wirtualnej* , można użyć `sudo` programu do uruchamiania poleceń, które wymagają uprawnień głównych.

## <a name="sudo-and-aad-login"></a>Sudo i logowanie do usługi AAD

Przy pierwszym uruchomieniu sudo zostanie wyświetlony monit o uwierzytelnienie w drugim czasie. Jeśli nie chcesz ponownie uwierzytelniać się w celu uruchomienia programu sudo, możesz edytować plik sudo `/etc/sudoers.d/aad_admins` i zastąpić ten wiersz:

```bash
%aad_admins ALL=(ALL) ALL
```

Z tym wierszem:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby połączenia SSH z poświadczeniami usługi Azure AD nie obejmują żadnych przypisanych ról platformy Azure, a także powtórzonych wierszy logowania. Skorzystaj z poniższych sekcji, aby rozwiązać te problemy.

### <a name="access-denied-azure-role-not-assigned"></a>Odmowa dostępu: rola platformy Azure nie jest przypisana

Jeśli widzisz następujący błąd w monicie SSH, sprawdź, czy skonfigurowano zasady kontroli dostępu platformy Azure dla maszyny wirtualnej, która udziela użytkownikowi uprawnienia do *logowania administratora maszyny wirtualnej* lub *użytkownika maszyny wirtualnej* :

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Dalsze komunikaty logowania SSH

Jeśli krok uwierzytelniania został pomyślnie ukończony w przeglądarce sieci Web, może zostać wyświetlony monit o ponowne zalogowanie się przy użyciu nowego kodu. Ten błąd jest zwykle spowodowany niezgodnością między nazwą logowania określoną w monicie SSH i kontem, z którego zalogowano się w usłudze Azure AD. Aby rozwiązać ten problem:

- Sprawdź, czy nazwa logowania określona w monicie SSH jest poprawna. Literówka w nazwie logowania może spowodować niezgodność między nazwą logowania określoną w monicie SSH i kontem, które zostało zalogowane do usługi Azure AD. Na przykład wpisano *azuresuer \@ contoso.onmicrosoft.com* zamiast *azureuser \@ contoso.onmicrosoft.com*.
- Jeśli masz wiele kont użytkowników, upewnij się, że w oknie przeglądarki nie jest dostępne inne konto użytkownika podczas logowania się do usługi Azure AD.
- Linux to system operacyjny z uwzględnieniem wielkości liter. Istnieje różnica między elementami " Azureuser@contoso.onmicrosoft.com " i " azureuser@contoso.onmicrosoft.com ", co może spowodować niezgodność. Upewnij się, że nazwa UPN ma poprawną wielkość liter w wierszu polecenia SSH.

### <a name="other-limitations"></a>Inne ograniczenia

Użytkownicy, którzy dziedziczą prawa dostępu za poorednictwem zagnieżdżonych grup lub przypisań ról nie są obecnie obsługiwani. Użytkownik lub grupa musi mieć bezpośrednio przypisane [wymagane przypisania ról](#configure-role-assignments-for-the-vm). Na przykład użycie grup zarządzania lub przypisań ról grup zagnieżdżonych nie przyznaje odpowiednich uprawnień, aby umożliwić użytkownikowi logowanie się.

## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy przy użyciu jej na [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Active Directory, zobacz [co to jest Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

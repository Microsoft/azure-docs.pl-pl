---
title: Resetowanie dostępu do maszyny wirtualnej platformy Azure z systemem Linux
description: Jak zarządzać użytkownikami administracyjnymi i resetować dostęp na maszynach wirtualnych z systemem Linux przy użyciu rozszerzenia VMAccess i interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: e46f7f3d51d2841a2287c86f9e8dddd7460b1a5d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951489"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Zarządzanie użytkownikami administracyjnymi, SSH oraz sprawdzaniem i naprawianiem dysków na maszynach wirtualnych z systemem Linux przy użyciu rozszerzenia VMAccess z interfejsem wiersza polecenia platformy Azure
## <a name="overview"></a>Omówienie
Na dysku na maszynie wirtualnej z systemem Linux są wyświetlane błędy. Ty możesz zresetować hasło główne dla maszyny wirtualnej z systemem Linux lub przypadkowo usunąć klucz prywatny SSH. Jeśli ten problem wystąpił z powrotem w dniach centrum danych, należy tam tam znaleźć, a następnie otworzyć KVM, aby uzyskać dostęp do konsoli serwera. Należy wziąć pod uwagę rozszerzenie VMAccess platformy Azure, ponieważ przełącznik KVM pozwala uzyskać dostęp do konsoli programu w celu zresetowania dostępu do systemu Linux lub przeprowadzenia konserwacji na poziomie dysku.

W tym artykule pokazano, jak za pomocą rozszerzenia usługi Azure VMAccess sprawdzić lub naprawić dysk, zresetować dostęp użytkowników, zarządzać kontami użytkowników administracyjnych lub zaktualizować konfigurację protokołu SSH w systemie Linux, gdy są one uruchomione jako Azure Resource Manager maszyny wirtualne. Jeśli chcesz zarządzać klasycznymi maszynami wirtualnymi, możesz postępować zgodnie z instrukcjami znajdującymi się w [dokumentacji klasycznej maszyny wirtualnej](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic). 
 
> [!NOTE]
> Jeśli używasz rozszerzenia VMAccess do resetowania hasła maszyny wirtualnej po zainstalowaniu rozszerzenia logowania usługi AAD, musisz ponownie uruchomić rozszerzenie logowania usługi AAD, aby ponownie włączyć logowanie do usługi AAD dla maszyny.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="operating-system"></a>System operacyjny

Rozszerzenie dostępu do maszyny wirtualnej można uruchomić w odniesieniu do tych dystrybucji systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS i 12,04 LTS |
| Debian | Debian 7,9 +, 8.2 + |
| Red Hat | RHEL 6,7 +, 7.1 + |
| Oracle Linux | 6.4 +, 7.0 + |
| Szło | 11 i 12 |
| OpenSuse | openSUSE przestępny 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0 + |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sposoby korzystania z rozszerzenia VMAccess
Istnieją dwa sposoby używania rozszerzenia VMAccess na maszynach wirtualnych z systemem Linux:

* Użyj interfejsu wiersza polecenia platformy Azure i wymaganych parametrów.
* [Użyj nieprzetworzonych plików JSON, które są przetwarzane przez rozszerzenie VMAccess](#use-json-files-and-the-vmaccess-extension) , a następnie wykonaj działania.

W poniższych przykładach użyto polecenia [AZ VM User](/cli/azure/vm/user) Commands. Aby wykonać te kroki, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Update SSH key (Aktualizowanie klucza SSH)
Poniższy przykład aktualizuje klucz SSH dla użytkownika `azureuser` na maszynie wirtualnej o nazwie `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Uwaga:** `az vm user update` Polecenie dołącza nowy tekst klucza publicznego do `~/.ssh/authorized_keys` pliku dla użytkownika administratora na maszynie wirtualnej. Nie zastępuje to ani nie usunie żadnych istniejących kluczy SSH. Nie spowoduje to usunięcia poprzednich kluczy ustawionych w czasie wdrażania lub kolejnych aktualizacji za pośrednictwem rozszerzenia VMAccess.

## <a name="reset-password"></a>Resetowanie hasła
Poniższy przykład resetuje hasło dla użytkownika `azureuser` na maszynie wirtualnej o nazwie `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Ponowne uruchamianie protokołu SSH
Poniższy przykład powoduje ponowne uruchomienie demona SSH i zresetowanie konfiguracji SSH do wartości domyślnych na maszynie wirtualnej o nazwie `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Tworzenie użytkownika administracyjnego/sudo
Poniższy przykład tworzy użytkownika o nazwie `myNewUser` z uprawnieniami **sudo** . Konto używa klucza SSH do uwierzytelniania na maszynie wirtualnej o nazwie `myVM` . Ta metoda została zaprojektowana w celu ułatwienia odzyskania dostępu do maszyny wirtualnej w przypadku zgubienia lub zapomnienia bieżących poświadczeń. Najlepszym rozwiązaniem jest ograniczenie kont z uprawnieniami **sudo** .

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Usuwanie użytkownika
Poniższy przykład usuwa użytkownika o nazwie `myNewUser` do maszyny wirtualnej o nazwie `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Korzystanie z plików w formacie JSON i rozszerzenia VMAccess
W poniższych przykładach użyto nieprzetworzonych plików JSON. Użyj polecenie [AZ VM Extension Set](/cli/azure/vm/extension) , aby następnie wywołać pliki JSON. Te pliki JSON można również wywołać z szablonów platformy Azure. 

### <a name="reset-user-access"></a>Resetuj dostęp użytkownika
Jeśli masz utracony dostęp do katalogu głównego na maszynie wirtualnej z systemem Linux, możesz uruchomić skrypt VMAccess, aby zaktualizować klucz SSH lub hasło użytkownika.

Aby zaktualizować klucz publiczny SSH użytkownika, Utwórz plik o nazwie `update_ssh_key.json` i Dodaj ustawienia w następującym formacie. Zastąp własne wartości `username` `ssh_key` parametrów i:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Aby zresetować hasło użytkownika, należy utworzyć plik o nazwie `reset_user_password.json` i dodać ustawienia w następującym formacie. Zastąp własne wartości `username` `password` parametrów i:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Ponowne uruchamianie protokołu SSH
Aby ponownie uruchomić demona SSH i zresetować konfigurację SSH do wartości domyślnych, Utwórz plik o nazwie `reset_sshd.json` . Dodaj następującą zawartość:

```json
{
  "reset_ssh": true
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Zarządzanie użytkownikami administracyjnymi

Aby utworzyć użytkownika z uprawnieniami **sudo** , które używają klucza SSH do uwierzytelniania, Utwórz plik o nazwie `create_new_user.json` i Dodaj ustawienia w następującym formacie. Zastąp własne wartości `username` `ssh_key` parametrów i. Ta metoda została zaprojektowana w celu ułatwienia odzyskania dostępu do maszyny wirtualnej w przypadku zgubienia lub zapomnienia bieżących poświadczeń. Najlepszym rozwiązaniem jest ograniczenie kont z uprawnieniami **sudo** .

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Aby usunąć użytkownika, Utwórz plik o nazwie `delete_user.json` i Dodaj poniższą zawartość. Zastąp własną wartość `remove_user` parametru:

```json
{
  "remove_user":"myNewUser"
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Sprawdź lub Napraw dysk
Za pomocą VMAccess można także sprawdzić i naprawić dysk dodany do maszyny wirtualnej z systemem Linux.

Aby sprawdzić i naprawić dysk, Utwórz plik o nazwie `disk_check_repair.json` i Dodaj ustawienia w następującym formacie. Zastąp własną wartość nazwą `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Wykonaj skrypt VMAccess przy użyciu:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

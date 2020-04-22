---
title: Szybki start — tworzenie maszyny Wirtualnej z systemem Linux w witrynie Azure portal
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną systemu Linux za pomocą portalu Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6bf9a89a4806db53797191336578ef9148886181
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759241"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Linux w witrynie Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Portal Azure to interfejs użytkownika oparty na przeglądarce do tworzenia zasobów platformy Azure. Ten przewodnik Szybki start pokazuje, jak używać portalu Azure do wdrażania maszyny wirtualnej systemu Linux (VM) z systemem Ubuntu 18.04 LTS. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu SSH i zainstalujesz serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Otwórz powłokę Bash i użyj polecenia [ssh-keygen](https://www.ssh.com/ssh/keygen/), aby utworzyć parę kluczy SSH. Jeśli nie masz powłoki Bash na swoim komputerze lokalnym, możesz użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu u góry strony wybierz `>_` ikonę, aby otworzyć powłokę cloud shell.
1. Upewnij się, że CloudShell mówi **Bash** w lewym górnym rogu. Jeśli jest używany program PowerShell, użyj listy rozwijanej, aby wybrać **bash** i wybierz **pozycję Potwierdź,** aby zmienić na powłokę Bash.
1. Wpisz, `ssh-keygen -t rsa -b 2048` aby utworzyć klucz ssh. 
1. Zostanie wyświetlony monit o wprowadzenie pliku, w którym chcesz zapisać parę kluczy. Wystarczy **nacisnąć** klawisz Enter, aby zapisać w lokalizacji domyślnej wymienionej w nawiasach. 
1. Zostaniesz poproszony o wprowadzenie hasła. Możesz wpisać hasło do klucza SSH lub nacisnąć klawisz **Enter,** aby kontynuować bez hasła.
1. Polecenie `ssh-keygen` generuje klucze publiczne i prywatne `id_rsa` o `~/.ssh directory`domyślnej nazwie w pliku . Polecenie zwraca pełną ścieżkę do klucza publicznego. Użyj ścieżki do klucza publicznego, `cat` aby `cat ~/.ssh/id_rsa.pub`wyświetlić jego zawartość, wpisując .
1. Skopiuj dane wyjściowe tego polecenia i zapisz je gdzieś, aby użyć go w dalszej części tego artykułu. Jest to klucz publiczny i będzie potrzebny podczas konfigurowania konta administratora, aby zalogować się do maszyny Wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal,](https://portal.azure.com) jeśli jeszcze tego nie zrobiłeś.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wpisz **maszyny wirtualne** w wyszukiwaniu.
1. W obszarze **Usługi**wybierz pozycję **Maszyny wirtualne**.
1. Na stronie **Maszyny wirtualne** wybierz pozycję **Dodaj**. Zostanie otwarta strona **Utwórz maszynę wirtualną.**
1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz *myResourceGroup* dla nazwy.*. 

    ![Tworzenie nowej grupy zasobów dla maszyny wirtualnej](./media/quick-create-portal/project-details.png)

1. W obszarze **Szczegóły wystąpienia**wpisz *myVM* dla **nazwy maszyny wirtualnej**, wybierz *wschodnie stany USA* dla swojego **regionu**i wybierz *Ubuntu 18.04 LTS* dla **obrazu**. Inne wartości pozostaw domyślne.

    ![Sekcja Szczegóły wystąpienia](./media/quick-create-portal/instance-details.png)

1. W obszarze **Konto administratora**wybierz pozycję **Klucz publiczny SSH**, wpisz nazwę użytkownika, a następnie wklej klucz publiczny. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **Reguły portów** > **przychodzących Publiczne porty przychodzące**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz z listy rozwijanej pozycję **SSH (22)** i **HTTP (80).** 

    ![Otwieranie portów dla protokołów RDP i HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

    
## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie SSH z maszyną wirtualną.

1. Na stronie przeglądu wybierz przycisk **Połącz** dla swojej maszyny wirtualnej. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** pozostaw opcje domyślne, aby nawiązać połączenie za pomocą adresu IP na porcie 22. W obszarze **Logowanie za pomocą konta lokalnego maszyny wirtualnej** jest wyświetlane polecenie połączenia. Wybierz przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Używając tej samej powłoki bash, której użyto do utworzenia pary `>_` kluczy `https://shell.azure.com/bash`SSH (możesz ponownie otworzyć powłokę chmury, wybierając ponownie lub przechodząc do), wklej polecenie połączenia SSH do powłoki, aby utworzyć sesję SSH.

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy NGINX. Z poziomu sesji SSH zaktualizuj źródła pakietu, a następnie zainstaluj najnowszą wersję pakietu NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Gdy skończysz, wpisz polecenie `exit`, aby opuścić sesję SSH.


## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Użyj wybranej przeglądarki internetowej, aby wyświetlić domyślną strona powitalną serwera NGINX. Wpisz publiczny adres IP maszyny Wirtualnej jako adres internetowy. Publiczny adres IP można znaleźć na stronie przeglądu maszyny wirtualnej lub jako część wcześniej użytych parametrów połączenia SSH.

![Domyślna witryna serwera NGINX](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, utworzono sieciową grupę zabezpieczeń i regułę oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)

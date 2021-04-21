---
title: Samouczek — wdrażanie lamp i platformy WordPress na maszynie wirtualnej
description: Z tego samouczka dowiesz się, jak zainstalować stos LAMP i platformę WordPress na maszynie wirtualnej z systemem Linux na platformie Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816354"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Samouczek: instalowanie stosu LAMP na maszynie wirtualnej platformy Azure z systemem Linux

W tym artykule przedstawiono kroki wdrażania serwera internetowego Apache oraz oprogramowania MySQL i PHP (stosu LAMP) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Aby zobaczyć, jak działa serwer LAMP, możesz opcjonalnie zainstalować i skonfigurować witrynę WordPress. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu 
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania Apache, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie platformy WordPress 

Ta konfiguracja umożliwia szybkie przeprowadzenie testów lub weryfikacji koncepcji. Aby uzyskać więcej informacji na temat stosu LAMP, w tym zalecenia dotyczące środowiska produkcyjnego, zobacz [dokumentację systemu Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

W tym samouczku używany jest interfejs wiersza [polecenia Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w górnej części bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Następujący przykład umożliwia utworzenie maszyny wirtualnej o nazwie *myVM* i kluczy SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. To polecenie ustawia również nazwę *azureuser* jako nazwę użytkownika administratora. Użyjesz tej nazwy później, aby nawiązać połączenia z maszyną wirtualną. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostanie wyświetlona informacja podobna do następującej. Zwróć uwagę na element `publicIpAddress`. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w późniejszych krokach.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web 

Domyślnie dozwolone są tylko połączenia SSH z maszynami wirtualnymi z systemem Linux wdrożonymi na platformie Azure. Ponieważ ta maszyna wirtualna ma być serwerem sieci Web, port 80 należy otworzyć z Internetu. Użyj polecenia [az vm open-port,](/cli/azure/vm) aby otworzyć żądany port.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Aby uzyskać więcej informacji na temat otwierania portów dla maszyny wirtualnej, [zobacz Otwieranie portów](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Jeśli nie znasz jeszcze publicznego adresu IP maszyny wirtualnej, uruchom polecenie [az network public-ip list](/cli/azure/network/public-ip). Ten adres IP będzie potrzebny do wykonania kilku późniejszych kroków.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Pamiętaj o wstawieniu prawidłowego publicznego adresu IP swojej maszyny wirtualnej. W tym przykładzie adres IP to *40.68.254.142*. Element *azureuser* to nazwa użytkownika administratora określona podczas tworzenia maszyny wirtualnej.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Instalowanie oprogramowania Apache, MySQL i PHP

Uruchom następujące polecenie, aby zaktualizować źródła pakietów systemu Ubuntu oraz zainstalować oprogramowanie Apache, MySQL i PHP. Widoczny na końcu polecenia znak karetki (^) jest częścią nazwy pakietu `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Pojawi się monit o zainstalowanie pakietów i innych zależności. W tym procesie jest instalowana minimalna liczba wymaganych rozszerzeń PHP potrzebnych do używania języka PHP z oprogramowaniem MySQL.  

## <a name="verify-apache"></a>Weryfikowanie oprogramowania Apache

Sprawdź wersję oprogramowania Apache przy użyciu następującego polecenia:
```bash
apache2 -v
```

Po zainstalowaniu oprogramowania Apache i otwarciu portu 80 dla maszyny wirtualnej można uzyskać dostęp do serwera internetowego z Internetu. Aby wyświetlić stronę domyślną oprogramowania Apache2 Ubuntu, otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej. Wpisz publiczny adres IP użyty do nawiązania połączenia SSH z maszyną wirtualną:

![Strona domyślna oprogramowania Apache][3]


## <a name="verify-and-secure-mysql"></a>Weryfikowanie i zabezpieczanie oprogramowania MySQL

Sprawdź wersję oprogramowania MySQL przy użyciu następującego polecenia (zwróć uwagę na parametr `V` oznaczony wielką literą):

```bash
mysql -V
```

Aby ułatwić zabezpieczenie instalacji oprogramowania MySQL, włącznie z ustawieniem hasła głównego, uruchom skrypt `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Opcjonalnie możesz skonfigurować wtyczkę weryfikacji hasła (zalecane). Następnie ustaw hasło użytkownika głównego oprogramowania MySQL i skonfiguruj pozostałe ustawienia zabezpieczeń dla środowiska. Zalecamy udzielenie odpowiedzi Tak („Y”, czyli ang. „Yes”) na wszystkie pytania.

Jeśli chcesz wypróbować funkcje oprogramowania MySQL (na przykład utworzyć bazę danych MySQL, dodać użytkowników lub zmienić ustawienia konfiguracji), zaloguj się do systemu MySQL. Ten krok nie jest wymagany do ukończenia samouczka.

```bash
sudo mysql -u root -p
```

Gdy skończysz, zamknij wiersz polecenia mysql, wpisując `\q`.

## <a name="verify-php"></a>Weryfikowanie oprogramowania PHP

Sprawdź wersję oprogramowania PHP przy użyciu następującego polecenia:

```bash
php -v
```

Jeśli chcesz wykonać dodatkowe testy, utwórz prostą stronę z informacjami o PHP, przeznaczoną do wyświetlania w przeglądarce. Poniższe polecenie tworzy stronę z informacjami o języku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Możesz teraz sprawdzić zawartość utworzonej strony z informacjami o języku PHP. Otwórz przeglądarkę i przejdź pod adres `http://yourPublicIPAddress/info.php`. Zastąp publiczny adres IP maszyny wirtualnej. Zawartość okna powinna wyglądać mniej więcej tak.

![Strona z informacjami o języku PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer LAMP na platformie Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania Apache, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LAMP

Aby dowiedzieć się, jak zabezpieczyć serwery internetowe za pomocą certyfikatów TLS/SSL, należy przejść do następnego samouczka.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera internetowego przy użyciu TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png

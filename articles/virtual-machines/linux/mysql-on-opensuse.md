---
title: Instalowanie bazy danych MySQL na maszynie wirtualnej OpenSUSE na platformie Azure
description: Dowiedz się, jak zainstalować program MySQL na maszynie wirtualnej z systemem OpenSUSE Linux na platformie Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 608cfca3343b02101ac5e08acf0bec62900d1443
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108657"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalowanie bazy danych MySQL na maszynie wirtualnej z systemem OpenSUSE Linux na platformie Azure

[MySQL](https://www.mysql.com) to popularna, typu open-source baza danych SQL. W tym samouczku pokazano, jak utworzyć maszynę wirtualną z systemem OpenSUSE Linux, a następnie zainstalować MySQL.


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Tworzenie maszyny wirtualnej z systemem OpenSUSE Linux

Najpierw utwórz grupę zasobów. W tym przykładzie grupa zasobów ma nazwę *mySQSUSEResourceGroup* i jest tworzona w regionie *Wschodnie stany USA* .

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Utwórz maszynę wirtualną. W tym przykładzie maszyna wirtualna ma nazwę *myVM* , a rozmiar maszyny wirtualnej to *Standard_D2s_v3*, ale należy wybrać [rozmiar maszyny wirtualnej](../sizes.md) , która jest najbardziej odpowiednia dla obciążenia.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image SUSE:openSUSE-Leap:15-2:latest \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Należy również dodać regułę do sieciowej grupy zabezpieczeń, aby zezwalać na ruch przez port 3306 dla programu MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Do nawiązania połączenia z maszyną wirtualną będziesz używać protokołu SSH. W tym przykładzie publiczny adres IP maszyny wirtualnej to *10.111.112.113*. Podczas tworzenia maszyny wirtualnej można zobaczyć adres IP w danych wyjściowych.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizowanie maszyny wirtualnej
 
Po nawiązaniu połączenia z maszyną wirtualną Zainstaluj aktualizacje systemu i poprawki. 
   
```bash
sudo zypper update
```

Postępuj zgodnie z monitami, aby zaktualizować maszynę wirtualną.

## <a name="install-mysql"></a>Instalowanie bazy danych MySQL 


Zainstaluj program MySQL na maszynie wirtualnej za pośrednictwem protokołu SSH. Odpowiedź na pytanie, zgodnie z potrzebami.

```bash
sudo zypper install mysql
```
 
Skonfiguruj system MySQL do uruchamiania podczas uruchamiania systemu. 

```bash
sudo systemctl enable mysql
```
Sprawdź, czy baza danych MySQL jest włączona.

```bash
systemctl is-enabled mysql
```

Powinno to zwrócić: włączone.

Uruchom ponownie serwer.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Hasło MySQL

Po zakończeniu instalacji hasło główne programu MySQL jest domyślnie puste. Uruchom skrypt **\_ \_ instalacji bezpiecznego MySQL** , aby zabezpieczyć MySQL. Skrypt poprosi o zmianę hasła głównego programu MySQL, usunięcie kont użytkowników anonimowych, wyłączenie zdalnego logowania się, usunięcie testów baz danych i ponowne załadowanie tabeli uprawnień. 

Po ponownym uruchomieniu serwera należy ponownie przeprowadzić protokół SSH do maszyny wirtualnej.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Logowanie do bazy danych MySQL

Możesz teraz zalogować się i wprowadzić monit dotyczący programu MySQL.

```bash  
sudo mysql -u root -p
```
Spowoduje to przełączenie do monitu programu MySQL, w którym można wydać instrukcje SQL, aby móc korzystać z bazy danych.

Teraz Utwórz nowego użytkownika programu MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Średnik (;) na końcu wiersza są kluczowe znaczenie dla zakończenia polecenia.


## <a name="create-a-database"></a>Tworzenie bazy danych


Utwórz bazę danych i Udziel `mysqluser` uprawnień użytkownika.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Nazwy i hasła użytkowników bazy danych są używane tylko przez skrypty łączące się z bazą danych.  Nazwy kont użytkowników bazy danych nie muszą reprezentować rzeczywistych kont użytkowników w systemie.

Włącz logowanie się na innym komputerze. W tym przykładzie adres IP komputera, na którym jest dozwolone logowanie, to *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Aby zamknąć narzędzie do administrowania bazą danych MySQL, wpisz:

```    
quit
```


## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje o programie MySQL można znaleźć w [dokumentacji programu MySQL](https://dev.mysql.com/doc).

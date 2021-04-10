---
title: Samouczek — wdrażanie lampy na maszynie wirtualnej z systemem Linux na platformie Azure
description: Z tego samouczka dowiesz się, jak zainstalować stos LAMP na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.collection: linux
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 3813931f47c110abcfb595065c1415ca9ed84c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564717"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Samouczek: instalowanie serwera internetowego LAMP na maszynie wirtualnej z systemem Linux na platformie Azure

W tym artykule przedstawiono kroki wdrażania serwera internetowego Apache oraz oprogramowania MySQL i PHP (stosu LAMP) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Aby zobaczyć, jak działa serwer LAMP, możesz opcjonalnie zainstalować i skonfigurować witrynę WordPress. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu (oznaczonym literą „L” w stosie LAMP)
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania Apache, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LAMP

Ta konfiguracja umożliwia szybkie przeprowadzenie testów lub weryfikacji koncepcji. Aby uzyskać więcej informacji na temat stosu LAMP, w tym zalecenia dotyczące środowiska produkcyjnego, zobacz [dokumentację systemu Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalowanie oprogramowania Apache, MySQL i PHP

Uruchom następujące polecenie, aby zaktualizować źródła pakietów systemu Ubuntu oraz zainstalować oprogramowanie Apache, MySQL i PHP. Widoczny na końcu polecenia znak karetki (^) jest częścią nazwy pakietu `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Pojawi się monit o zainstalowanie pakietów i innych zależności. W tym procesie jest instalowana minimalna liczba wymaganych rozszerzeń PHP potrzebnych do używania języka PHP z oprogramowaniem MySQL.  

## <a name="verify-installation-and-configuration"></a>Weryfikowanie instalacji i konfiguracji


### <a name="verify-apache"></a>Weryfikowanie oprogramowania Apache

Sprawdź wersję oprogramowania Apache przy użyciu następującego polecenia:
```bash
apache2 -v
```

Po zainstalowaniu oprogramowania Apache i otwarciu portu 80 dla maszyny wirtualnej można uzyskać dostęp do serwera internetowego z Internetu. Aby wyświetlić stronę domyślną oprogramowania Apache2 Ubuntu, otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej. Wpisz publiczny adres IP użyty do nawiązania połączenia SSH z maszyną wirtualną:

![Strona domyślna oprogramowania Apache][3]


### <a name="verify-and-secure-mysql"></a>Weryfikowanie i zabezpieczanie oprogramowania MySQL

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

### <a name="verify-php"></a>Weryfikowanie oprogramowania PHP

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

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwery sieci Web przy użyciu certyfikatów TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera sieci Web za pomocą protokołu TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png

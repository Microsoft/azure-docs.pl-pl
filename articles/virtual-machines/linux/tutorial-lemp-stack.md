---
title: Samouczek — wdrażanie LEMP na maszynie wirtualnej z systemem Linux na platformie Azure
description: Z tego samouczka dowiesz się, jak zainstalować stos LEMP na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d603dbf2746608f499ba37b4f17b533b64bc941
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80154359"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Samouczek: instalowanie serwera internetowego LEMP na maszynie wirtualnej z systemem Linux na platformie Azure

W tym artykule przedstawiono kroki wdrażania serwera internetowego NGINX oraz oprogramowania MySQL i PHP (stosu LEMP) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Stos LEMP, który stanowi alternatywę dla popularnego [stosu LAMP](tutorial-lamp-stack.md), również można zainstalować na platformie Azure. Aby zobaczyć, jak działa serwer LEMP, możesz opcjonalnie zainstalować i skonfigurować witrynę WordPress. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu (oznaczonym literą „L” w stosie LEMP)
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania NGINX, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na serwerze LEMP

Ta konfiguracja umożliwia szybkie przeprowadzenie testów lub weryfikacji koncepcji.

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Instalowanie oprogramowania NGINX, MySQL i PHP

Uruchom następujące polecenie, aby zaktualizować źródła pakietów systemu Ubuntu oraz zainstalować oprogramowanie NGINX, MySQL i PHP. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

Pojawi się monit o zainstalowanie pakietów i innych zależności. W tym procesie jest instalowana minimalna liczba wymaganych rozszerzeń PHP potrzebnych do używania języka PHP z oprogramowaniem MySQL.  

## <a name="verify-installation-and-configuration"></a>Weryfikowanie instalacji i konfiguracji


### <a name="verify-nginx"></a>Weryfikowanie serwera NGINX

Sprawdź wersję oprogramowania NGINX przy użyciu następującego polecenia:
```bash
nginx -v
```

Po zainstalowaniu serwera NGINX i otwarciu portu 80 dla maszyny wirtualnej można uzyskać dostęp do serwera internetowego z Internetu. Aby wyświetlić stronę powitalną oprogramowania NGINX, otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej. Wpisz publiczny adres IP użyty do nawiązania połączenia SSH z maszyną wirtualną:

![Strona domyślna serwera NGINX][3]


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

Skonfiguruj serwer NGINX pod kątem używania menedżera procesów FastCGI języka PHP (PHP-FPM). Uruchom następujące polecenia, aby utworzyć kopię zapasową oryginalnego pliku konfiguracji bloku serwera NGINX, a następnie zmodyfikuj oryginalny plik w wybranym edytorze:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

W edytorze zastąp zawartość pliku `/etc/nginx/sites-available/default` następującym kodem. Komentarze zawierają opisy ustawień. Wartość *yourPublicIPAddress* zastąp publicznym adresem IP swojej maszyny wirtualnej, potwierdź wersję PHP w `fastcgi_pass`, a resztę ustawień pozostaw bez zmian. Następnie zapisz plik.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

Sprawdź konfigurację serwera NGINX pod kątem błędów składniowych:

```bash
sudo nginx -t
```

Jeśli składnia jest poprawna, uruchom ponownie serwer NGINX za pomocą następującego polecenia:

```bash
sudo service nginx restart
```

Jeśli chcesz wykonać dodatkowe testy, utwórz prostą stronę z informacjami o PHP, przeznaczoną do wyświetlania w przeglądarce. Poniższe polecenie tworzy stronę z informacjami o języku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Możesz teraz sprawdzić zawartość utworzonej strony z informacjami o języku PHP. Otwórz przeglądarkę i przejdź pod adres `http://yourPublicIPAddress/info.php`. Zastąp publiczny adres IP maszyny wirtualnej. Zawartość okna powinna wyglądać mniej więcej tak.

![Strona z informacjami o języku PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer LEMP na platformie Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu
> * Otwieranie portu 80 na potrzeby ruchu w sieci Web
> * Instalowanie oprogramowania NGINX, MySQL i PHP
> * Weryfikowanie instalacji i konfiguracji
> * Instalowanie oprogramowania WordPress na stosie LEMP

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwery sieci Web przy użyciu certyfikatów TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera sieci Web za pomocą protokołu TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png

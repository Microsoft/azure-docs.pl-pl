---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68857451"
---
## <a name="install-wordpress"></a>Instalowanie platformy WordPress

Jeśli chcesz wypróbować swój stos, zainstaluj aplikację przykładową. W ramach przykładu przedstawiono poniższe kroki, które pozwalają zainstalować platformę „open source” [WordPress](https://wordpress.org/) do tworzenia witryn internetowych i blogów. Inne obciążenia do wypróbowania to m.in. [Drupal](http://www.drupal.org) i [Moodle](https://moodle.org/). 

Ten instalator platformy WordPress służy wyłącznie do weryfikacji koncepcji. Aby zainstalować najnowszą platformę WordPress w produkcji z zalecanymi ustawieniami zabezpieczeń, zobacz [dokumentację platformy WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalowanie pakietu WordPress

Uruchom następujące polecenie:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Konfigurowanie usługi WordPress

Skonfiguruj platformę WordPress pod kątem używania rozwiązań MySQL i PHP.

W katalogu roboczym utwórz plik tekstowy `wordpress.sql`, aby skonfigurować bazę danych MySQL dla platformy WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Dodaj następujące polecenia, zastępując ciąg *yourPassword* swoim hasłem do bazy danych (inne wartości pozostaw bez zmian). Jeśli wcześniej skonfigurowano zasady zabezpieczeń MySQL w celu sprawdzania siły hasła, upewnij się, że hasło spełnia te wymagania. Zapisz plik.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

Uruchom następujące polecenie, aby utworzyć bazę danych:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Ponieważ plik `wordpress.sql` zawiera poświadczenia bazy danych, usuń go po użyciu:

```bash
sudo rm wordpress.sql
```

W celu skonfigurowania środowiska PHP uruchom następujące polecenie, aby otworzyć wybrany edytor tekstów i utworzyć plik `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Skopiuj następujące wiersze do pliku, zastępując ciąg *yourPassword* swoim hasłem do bazy danych platformy WordPress (inne wartości pozostaw bez zmian). Następnie zapisz plik.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Przenieś instalację platformy WordPress do katalogu głównego dokumentu serwera internetowego:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Teraz możesz ukończyć instalację platformy WordPress i zacząć na niej publikować. Otwórz przeglądarkę i przejdź pod adres `http://yourPublicIPAddress/wordpress`. Zastąp publiczny adres IP maszyny wirtualnej. Zawartość okna powinna wyglądać mniej więcej tak.

![Strona instalacji platformy WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)

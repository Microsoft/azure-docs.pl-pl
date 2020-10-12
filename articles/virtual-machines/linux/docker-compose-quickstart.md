---
title: Korzystanie z narzędzia Docker Compose
description: Jak zainstalować i używać platformy Docker i tworzyć maszyny wirtualne z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/14/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9f824a3b7e1e5e482bbc04c448ca4930bf88678
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500950"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Wprowadzenie do platformy Docker i redagowanie w celu zdefiniowania i uruchomienia aplikacji wielokontenera na platformie Azure
Dzięki [redagowaniu](https://github.com/docker/compose)można użyć prostego pliku tekstowego do zdefiniowania aplikacji składającej się z wielu kontenerów platformy Docker. Następnie można uruchomić aplikację w pojedynczym poleceniu, które wykonuje wszystkie czynności w celu wdrożenia określonego środowiska. Na przykład w tym artykule pokazano, jak szybko skonfigurować blog WordPress z bazą danych SQL zaplecza MariaDB na maszynie wirtualnej Ubuntu. Można również użyć opcji Zredaguj, aby skonfigurować bardziej złożone aplikacje.

Ten artykuł został ostatnio przetestowany w dniu 2/14/2019 przy użyciu [Azure Cloud Shell](https://shell.azure.com/bash) i [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Tworzenie hosta Docker przy użyciu interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

Najpierw utwórz grupę zasobów dla środowiska Docker za pomocą [AZ Group Create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Utwórz plik o nazwie *cloud-init.txt* i wklej następującą konfigurację. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. 

```yaml
#include https://get.docker.com
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM* i otwiera port 80 do ruchu w sieci Web.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Utworzenie maszyny wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji potrwa kilka minut. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Podczas tworzenia maszyny wirtualnej zanotuj wartość `publicIpAddress` wyświetlaną w wierszu polecenia platformy Azure. 

                 

## <a name="install-compose"></a>Instalowanie redagowania


Użyj protokołu SSH do nowej maszyny wirtualnej hosta platformy Docker. Podaj własny adres IP.

```bash
ssh azureuser@10.10.111.11
```

Zainstaluj tworzenie na maszynie wirtualnej.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Utwórz plik konfiguracji Docker-Compose. yml
Utwórz `docker-compose.yml` plik konfiguracji w celu zdefiniowania kontenerów platformy Docker do uruchomienia na maszynie wirtualnej. Plik określa obraz do uruchomienia dla każdego kontenera, niezbędne zmienne środowiskowe i zależności, porty i linki między kontenerami. Aby uzyskać szczegółowe informacje na temat składni pliku YML, zobacz [redagowanie pliku dokumentacja](https://docs.docker.com/compose/compose-file/).

Utwórz plik *Docker-Compose. yml* . Użyj swojego ulubionego edytora tekstu, aby dodać dane do pliku. Poniższy przykład tworzy plik z monitem o `sensible-editor` wybranie edytora, którego chcesz użyć.

```bash
sensible-editor docker-compose.yml
```

Wklej poniższy przykład do pliku Docker Compose. Ta konfiguracja używa obrazów z [rejestru DockerHub](https://registry.hub.docker.com/_/wordpress/) , aby zainstalować WordPress (blog typu open source i system zarządzania zawartością) oraz połączonej zaplecza usługi SQL Database MariaDB. Wprowadź własne *MYSQL_ROOT_PASSWORD*.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Uruchamianie kontenerów za pomocą redagowania
W tym samym katalogu, w którym znajduje się plik *Docker-Compose. yml* , uruchom następujące polecenie (w zależności od używanego środowiska, może być konieczne uruchomienie `docker-compose` polecenia using `sudo` ):

```bash
sudo docker-compose up -d
```

To polecenie powoduje uruchomienie kontenerów platformy Docker określonych w *Docker-Compose. yml*. Wykonanie tego kroku trwa minutę lub dwa. Zobaczysz dane wyjściowe podobne do następujących:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Aby sprawdzić, czy kontenery są aktualne, wpisz polecenie `sudo docker-compose ps` . Powinny zostać wyświetlone informacje podobne do następujących:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teraz możesz połączyć się z platformą WordPress bezpośrednio na maszynie wirtualnej na porcie 80. Otwórz przeglądarkę internetową i wprowadź nazwę adresu IP maszyny wirtualnej. Powinien zostać wyświetlony ekran startowy WordPress, w którym można ukończyć instalację i zacząć korzystać z aplikacji.

![Ekran startowy WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [informacjami dotyczącymi](https://docs.docker.com/compose/) tworzenia i wdrażania aplikacji z wieloma kontenerami w [wierszu polecenia redagowania](https://docs.docker.com/compose/reference/) .
* Aby wdrożyć maszynę wirtualną platformy Azure z platformą Docker i skonfigurować aplikację przy użyciu narzędzia do [tworzenia, użyj](https://azure.microsoft.com/documentation/templates/)szablonu Azure Resource Manager, własnego lub jednego z nich. Na przykład, [wdrażanie blogu WordPress z szablonem platformy Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) korzysta z platformy Docker i tworzenie, aby szybko wdrożyć platformę WordPress z zapleczem MySQL na maszynie wirtualnej Ubuntu.
* Spróbuj zintegrować Docker Compose z klastrem Docker Swarm. Zapoznaj [się z tematem using with Swarm](https://docs.docker.com/compose/swarm/) for scenariusze.

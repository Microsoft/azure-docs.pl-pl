---
title: Wdrażanie programu ElasticSearch na deweloperskiej maszynie wirtualnej na platformie Azure
description: Samouczek — Instalowanie programu Elastic Stack na deweloperskiej maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: c3d35940a445b73ecb9285355f36b141c3a151b8
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475060"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Instalowanie programu Elastic Stack na maszynie wirtualnej platformy Azure

W tym artykule przedstawiono sposób wdrażania programów [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) i [Kibana](https://www.elastic.co/products/kibana) na maszynie wirtualnej z systemem Ubuntu na platformie Azure. Aby zobaczyć program Elastic Stack w działaniu, można opcjonalnie połączyć się z programem Kibana i popracować z przykładowymi danymi rejestrów. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu w grupie zasobów platformy Azure
> * Instalowanie programów Elasticsearch, Logstash i Kibana na maszynie wirtualnej
> * Wysyłanie przykładowych danych do programu Elasticsearch za pomocą programu Logstash 
> * Otwieranie portów i praca z danymi w konsoli Kibana


 To wdrożenie nadaje się do podstawowego programowania z użyciem programu Elastic Stack. Aby uzyskać więcej informacji na temat programu Elastic Stack, w tym zalecenia co do środowiska produkcyjnego, zobacz [dokumentację programu Elastic](https://www.elastic.co/guide/index.html) i [Centrum architektury platformy Azure](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Następujący przykład umożliwia utworzenie maszyny wirtualnej o nazwie *myVM* i kluczy SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostanie wyświetlona informacja podobna do następującej. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.

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

## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Jeśli nie znasz jeszcze publicznego adresu IP maszyny wirtualnej, uruchom polecenie [AZ Network Public-IP list](/cli/azure/network/public-ip) :

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Pamiętaj o wstawieniu prawidłowego publicznego adresu IP swojej maszyny wirtualnej. W tym przykładzie adres IP to *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Instalowanie programu Elastic Stack

Zaimportuj klucz podpisywania programu Elasticsearch i zaktualizuj listę źródeł APT w celu uwzględnienia repozytorium pakietu Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Zainstaluj maszynę wirtualną Java na maszynie wirtualnej i skonfiguruj zmienną JAVA_HOME. Jest to niezbędne, aby składniki programu Elastic Stack mogły działać.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Uruchom następujące polecenia, aby zaktualizować źródła pakietu systemu Ubuntu i zainstalować programy Elasticsearch, Kibana i Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Szczegółowe instrukcje instalacji, w tym układy katalogów i wstępną konfigurację, można znaleźć w [dokumentacji programu Elastic](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Uruchamianie programu Elasticsearch 

Uruchom program Elasticsearch na maszynie wirtualnej za pomocą następującego polecenia:

```bash
sudo systemctl start elasticsearch.service
```

To polecenie nie daje żadnych danych wyjściowych, dlatego sprawdź, czy program Elasticsearch działa na maszynie wirtualnej, używając tego polecenia `curl`:

```bash
sudo curl -XGET 'localhost:9200/'
```

Jeśli program Elasticsearch działa, zobaczysz dane wyjściowe zbliżone do następujących:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Uruchamianie programu Logstash i dodawanie danych do programu Elasticsearch

Uruchom program Logstash za pomocą następującego polecenia:

```bash 
sudo systemctl start logstash.service
```

Przetestuj program Logstash w trybie interakcyjnym, aby upewnić się, że działa prawidłowo:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Jest to podstawowy [potok](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) logstash, który odwzorowuje dane ze standardowego wejścia na standardowym wyjściu. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Skonfiguruj program Logstash, aby kierował komunikaty jądra z tej maszyny wirtualnej do programu Elasticsearch. Utwórz nowy plik w pustym katalogu o nazwie `vm-syslog-logstash.conf` i wklej do niego następującą konfigurację programu Logstash:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Przetestuj tę konfigurację i wyślij dane dziennika systemu do programu Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

W terminalu zobaczysz wpisy dziennika systemu wyświetlane w miarę ich wysyłania do programu Elasticsearch. Użyj kombinacji klawiszy `CTRL+C`, aby wyjść z programu Logstash po wysłaniu danych.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Uruchamianie programu Kibana i wizualizowanie danych w programie Elasticsearch

Poddaj edycji plik `/etc/kibana/kibana.yml` i zmień adres IP, pod którym nasłuchuje program Kibana, tak aby był do niego dostęp z przeglądarki internetowej.

```bash
server.host:"0.0.0.0"
```

Uruchom program Kibana za pomocą następującego polecenia:

```bash
sudo systemctl start kibana.service
```

Otwórz port 5601 z wiersza polecenia platformy Azure, aby umożliwić uzyskiwanie zdalnego dostępu do konsoli Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Otwórz konsolę Kibana i wybierz pozycję **Utwórz** w celu wygenerowania domyślnego indeksu na podstawie danych dziennika systemu wysłanych wcześniej do programu Elasticsearch. 

![Zrzut ekranu pokazujący konsolę Kibana i wyróżniony przycisk Utwórz.](media/elasticsearch-install/kibana-index.png)

Wybierz pozycję **Discover** (Odnajdywanie) w konsoli Kibana, aby wyszukiwać, przeglądać i filtrować zdarzenia z dziennika systemu.

![Przeglądanie zdarzeń dziennika systemu w programie w Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku został wdrożony program Elastic Stack na deweloperskiej maszynie wirtualnej na platformie Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Ubuntu w grupie zasobów platformy Azure
> * Instalowanie programów Elasticsearch, Logstash i Kibana na maszynie wirtualnej
> * Wysyłanie przykładowych danych do programu Elasticsearch z programu Logstash 
> * Otwieranie portów i praca z danymi w konsoli Kibana

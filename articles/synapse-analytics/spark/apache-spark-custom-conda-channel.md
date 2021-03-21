---
title: Tworzenie niestandardowego kanału Conda na potrzeby zarządzania pakietami
description: Dowiedz się, jak utworzyć niestandardowy kanał Conda na potrzeby zarządzania pakietami
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107945"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Tworzenie niestandardowego kanału Conda na potrzeby zarządzania pakietami 
Podczas instalowania pakietów języka Python Menedżer pakietów Conda używa kanałów do wyszukania pakietów. Może być konieczne utworzenie niestandardowego kanału Conda z różnych powodów. Na przykład może się okazać, że:

- obszar roboczy to dane eksfiltracji chronione i są blokowane połączenia wychodzące.  
- masz pakiety, które nie mają być przekazywane do repozytoriów publicznych.
- chcesz skonfigurować alternatywne repozytorium dla użytkowników w obszarze roboczym.

W tym artykule przedstawimy przewodnik krok po kroku ułatwiający utworzenie niestandardowego kanału Conda na koncie Azure Data Lake Storage.

## <a name="set-up-your-local-machine"></a>Konfigurowanie komputera lokalnego

1. Zainstaluj program Conda na komputerze lokalnym. Możesz odwołać się do [środowiska uruchomieniowego platformy Azure Synapse Spark](./apache-spark-version-support.md) , aby zidentyfikować wersję Conda, która jest używana w tym samym środowisku uruchomieniowym.
   
2. Aby utworzyć niestandardowy kanał, zainstaluj Conda-Build.
```
conda install conda-build
```
3. Organizuj wszystkie pakiety w programie dla platformy, którą chcesz obpracować. W tym przykładzie zostanie zainstalowana archiwum Anaconda na komputerze lokalnym.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Zainstaluj konto magazynu na swojej maszynie
Następnie zainstalujemy konto Azure Data Lake Storage Gen2 na komputerze lokalnym. Ten proces można również wykonać przy użyciu konta WASB; jednak przejdziemy na przykład dla konta ADLSg2 
 
Aby uzyskać więcej informacji na temat sposobu instalowania konta magazynu na komputerze lokalnym, możesz odwiedzić [Tę stronę](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Możesz zainstalować blobfuse z repozytorium oprogramowania systemu Linux dla produktów firmy Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Utwórz mountpoint ( ```mkdir /path/to/mount``` ) i zainstaluj kontener obiektów blob z blobfuse. W tym przykładzie użyjemy wartości **privatechannel** dla zmiennej **webcontainerer** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Tworzenie kanału
W następnym zestawie kroków utworzymy niestandardowy kanał Conda. 

1. Na komputerze lokalnym Utwórz katalog, aby zorganizować wszystkie pakiety dla niestandardowego kanału.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organizuj wszystkie ```tar.bz2``` pakiety z poziomu https://repo.anaconda.com/pkgs/main/linux-64/ usługi w podkatalogu. Należy również uwzględnić wszystkie zależne pakiety tar. bz2. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Aby uzyskać więcej informacji, możesz również [odwiedzić Podręcznik użytkownika programu Conda](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) w celu utworzenia niestandardowych kanałów. 

## <a name="storage-account-permissions"></a>Uprawnienia konta magazynu
Teraz będziemy musieli sprawdzić poprawność uprawnień na koncie magazynu. Aby ustawić te uprawnienia, przejdź do ścieżki, w której zostanie utworzony niestandardowy kanał. Następnie utwórz token sygnatury dostępu współdzielonego dla programu, ```privatechannel``` który ma uprawnienia do odczytu, wyświetlania i wykonywania. 

Nazwa kanału będzie teraz adresem URL sygnatury dostępu współdzielonego obiektu BLOB wygenerowanego na podstawie tego procesu.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Utwórz przykładowy plik konfiguracji środowiska Conda
Na koniec upewnij się, że proces instalacji został utworzony przez utworzenie przykładowego ```environment.yml``` pliku Conda. Jeśli masz obszar roboczy obsługujący program DEP, musisz określić ``nodefaults`` kanał w pliku środowiska.

Oto przykładowy plik konfiguracji Conda:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Po utworzeniu przykładowego pliku Conda można utworzyć wirtualne środowisko Conda. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Po zweryfikowaniu niestandardowego kanału można użyć procesu [zarządzania pulą języka Python](./apache-spark-manage-python-packages.md) , aby zaktualizować biblioteki w puli Apache Spark.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
- Zarządzanie pakietami języka Python: [Zarządzanie pakietami Python](./apache-spark-manage-python-packages.md)


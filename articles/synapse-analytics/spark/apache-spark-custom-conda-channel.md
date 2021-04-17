---
title: Tworzenie niestandardowego kanału Conda do zarządzania pakietami
description: Dowiedz się, jak utworzyć niestandardowy kanał Conda do zarządzania pakietami
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588772"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Tworzenie niestandardowego kanału Conda do zarządzania pakietami 
Podczas instalowania pakietów języka Python menedżer pakietów Conda używa kanałów do wyszukiwania pakietów. Może być konieczne utworzenie niestandardowego kanału Conda z różnych powodów. Na przykład może się okazać, że:

- Obszar roboczy jest chroniony przed eksfiltracją danych i połączenia wychodzące są blokowane.  
- masz pakiety, których nie chcesz przekazywać do repozytoriów publicznych.
- chcesz skonfigurować alternatywne repozytorium dla użytkowników w obszarze roboczym.

W tym artykule udostępnimy przewodnik krok po kroku, który pomoże Ci utworzyć niestandardowy kanał Conda w ramach Azure Data Lake Storage konta.

## <a name="set-up-your-local-machine"></a>Konfigurowanie komputera lokalnego

1. Zainstaluj program Conda na komputerze lokalnym. Aby zidentyfikować wersję środowiska Conda używaną w tym samym [środowisku uruchomieniowym, Azure Synapse uruchomieniowym platformy Spark](./apache-spark-version-support.md) można odwołać się do środowiska uruchomieniowego platformy Spark.
   
2. Aby utworzyć kanał niestandardowy, zainstaluj program conda-build.
```
conda install conda-build
```
3. Zorganizuj wszystkie pakiety dla platformy, którą chcesz obsługiwać. W tym przykładzie zainstalujemy archiwum anaconda na komputerze lokalnym.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Zainstaluj konto magazynu na maszynie
Następnie zainstalujemy konto Azure Data Lake Storage Gen2 na komputerze lokalnym. Ten proces można również wykonać przy użyciu konta WASB. Jednak zostaniemy przejdą przez przykład dla konta ADLSg2 
 
Aby uzyskać więcej informacji na temat sposobu instalacji konta magazynu na komputerze lokalnym, odwiedź [tę stronę](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Możesz zainstalować system blobfuse z repozytorium oprogramowania systemu Linux dla produktów firmy Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Utwórz punkt instalacji ( ```mkdir /path/to/mount``` ) i zainstaluj kontener obiektów blob za pomocą biblioteki blobfuse. W tym przykładzie użyjemy wartości **privatechannel** dla **zmiennej mycontainer.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Tworzenie kanału
W następnym zestawie kroków utworzymy niestandardowy kanał Conda. 

1. Na komputerze lokalnym utwórz katalog, aby zorganizować wszystkie pakiety dla kanału niestandardowego.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Zorganizuj ```tar.bz2``` wszystkie pakiety w https://repo.anaconda.com/pkgs/main/linux-64/ podkatalogu . Pamiętaj również o dołączyć wszystkie zależne pakiety tar.bz2. 

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

Aby uzyskać więcej informacji, możesz również odwiedzić przewodnik użytkownika programu [Conda dotyczący](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) tworzenia kanałów niestandardowych. 

## <a name="storage-account-permissions"></a>Uprawnienia konta magazynu
Teraz musimy zweryfikować uprawnienia na koncie magazynu. Aby ustawić te uprawnienia, przejdź do ścieżki, w której zostanie utworzony kanał niestandardowy. Następnie utwórz token SAS dla tego ```privatechannel``` tokenu z uprawnieniami do odczytu, listy i wykonywania. 

Nazwa kanału będzie teraz adresem URL sygnatury dostępu współdzielonego obiektu blob wygenerowanym na podstawie tego procesu.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Tworzenie przykładowego pliku konfiguracji środowiska Conda
Na koniec zweryfikuj proces instalacji, tworząc przykładowy plik ```environment.yml``` Conda. Jeśli masz obszar roboczy z włączoną obsługą programu DEP, musisz określić ``nodefaults`` kanał w pliku środowiska.

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
Po utworzeniu przykładowego pliku Conda możesz utworzyć wirtualne środowisko Conda. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Teraz, po zweryfikowaniu kanału niestandardowego, możesz użyć procesu zarządzania pulą języka [Python,](./apache-spark-manage-python-packages.md) aby zaktualizować biblioteki w Apache Spark puli.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [obsługa Apache Spark wersji](apache-spark-version-support.md)
- Zarządzanie pakietami języka Python: [zarządzanie pakietami języka Python](./apache-spark-manage-python-packages.md)


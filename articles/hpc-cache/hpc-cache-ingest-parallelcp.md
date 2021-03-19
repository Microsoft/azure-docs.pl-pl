---
title: Azure HPC — skrypt do kopiowania danych w pamięci podręcznej
description: Jak używać skryptu kopiowania równoległego do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: ff7b15a36c5ef19a1fa6ffdca7697dd6ba97c29f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092358"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Pobieranie danych z pamięci podręcznej platformy Azure HPC — Metoda skryptu kopiowania równoległego

Ten artykuł zawiera instrukcje dotyczące tworzenia ``parallelcp`` skryptu i używania go do przenoszenia danych do kontenera magazynu obiektów BLOB do użycia z pamięcią podręczną platformy Azure HPC.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj temat [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Tworzenie skryptu parallelcp

Poniższy skrypt doda plik wykonywalny `parallelcp` . (Ten skrypt jest przeznaczony dla Ubuntu; w przypadku korzystania z innej dystrybucji należy zainstalować ``parallel`` osobno).

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Przykład kopiowania równoległego

W tym przykładzie użyto skryptu kopiowania równoległego do kompilowania ``glibc`` przy użyciu plików źródłowych w pamięci podręcznej platformy Azure HPC.

Pliki źródłowe są buforowane w punkcie instalacji pamięci podręcznej platformy Azure HPC, a pliki obiektów są przechowywane na lokalnym dysku twardym.

Ten przykład używa skryptu kopiowania równoległego z opcją ``-j`` i ``make`` do uzyskania przetwarzanie równoległe.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```

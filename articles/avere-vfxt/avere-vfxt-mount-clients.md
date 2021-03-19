---
title: Instalowanie programu avere vFXT — Azure
description: Dowiedz się, jak łączyć klientów z klastrem vFXT w usłudze avere vFXT for Azure oraz jak równoważyć obciążenie ruchu klientów między węzłami klastra.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272575"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Instalowanie klastra Avere vFXT

Wykonaj następujące kroki, aby połączyć komputery klienckie z klastrem vFXT.

1. Zdecyduj, jak równoważyć obciążenie ruchu klienckiego między węzłami klastra. Aby uzyskać szczegółowe informacje, przeczytaj temat [równoważenie obciążenia klienta](#balance-client-load)poniżej.
1. Określ adres IP i ścieżkę rozgałęzienia do zainstalowania.
1. Wydaj [polecenie instalacji](#mount-command-arguments)z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Równoważ obciążenie klienta

Aby zapewnić zrównoważenie żądań klientów między wszystkimi węzłami w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP skierowanych na klienta. Istnieje kilka prostych metod automatyzacji tego zadania.

> [!TIP]
> Inne metody równoważenia obciążenia mogą być odpowiednie dla dużych lub skomplikowanych systemów; [Otwórz bilet pomocy technicznej,](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) Aby uzyskać pomoc.
>
> Jeśli wolisz używać serwera DNS do automatycznego równoważenia obciążenia po stronie serwera, musisz skonfigurować własny serwer DNS i zarządzać nim na platformie Azure. W takim przypadku można skonfigurować serwer DNS działający w trybie okrężnym dla klastra vFXT zgodnie z tym dokumentem: [avere klastra Konfiguracja DNS](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Przykładowy skrypt z równoważeniem instalacji klienta

Ten przykład kodu używa adresów IP klienta jako elementu losowego do dystrybucji klientów do wszystkich adresów IP dostępnych w klastrze vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

Powyższa funkcja jest częścią przykładu partii dostępnego w witrynie [przykładów avere vFXT](https://github.com/Azure/Avere#tutorials) .

## <a name="create-the-mount-command"></a>Utwórz polecenie instalacji

> [!NOTE]
> Jeśli nie utworzono nowego kontenera obiektów BLOB podczas tworzenia klastra avere vFXT, Dodaj systemy magazynów zgodnie z opisem w sekcji [Konfigurowanie magazynu](avere-vfxt-add-storage.md) przed próbą instalacji klientów.

Z poziomu klienta ``mount`` polecenie mapuje serwer wirtualny (vserver) w klastrze vFXT na ścieżkę w lokalnym systemie plików. Format jest ``mount <vFXT path> <local path> {options}``

Polecenie instalacji ma trzy elementy:

* ścieżka vFXT-kombinacja adresu IP i ścieżki rozgałęzienia przestrzeni nazw w klastrze 9described poniżej)
* ścieżka lokalna — ścieżka na kliencie
* Opcje polecenia instalacji — wymienione w [argumentach polecenia instalacji](#mount-command-arguments)

### <a name="junction-and-ip"></a>Połączenie i adres IP

Ścieżka vserver jest kombinacją jego *adresu IP* oraz ścieżki do *rozgałęzienia przestrzeni nazw*. Rozgałęzienie przestrzeni nazw jest ścieżką wirtualną, która została zdefiniowana podczas dodawania systemu magazynu.

Jeśli klaster został utworzony przy użyciu magazynu obiektów blob, ścieżka przestrzeni nazw do tego kontenera to `/msazure`

Przykład: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Po dodaniu magazynu po utworzeniu klastra, ścieżka rozgałęzienia przestrzeni nazw jest wartością ustawioną w polu **ścieżka przestrzeni nazw** podczas tworzenia połączenia. Na przykład jeśli użyto ``/avere/files`` jako ścieżki przestrzeni nazw, klienci instalują *IP_address*:/avere/Files do ich lokalnego punktu instalacji.

![Okno dialogowe "Dodawanie nowego połączenia" z/avere/Files w polu Ścieżka przestrzeni nazw](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Adres IP jest jednym z adresów IP skierowanych do klienta zdefiniowanych dla vserver. Zakres adresów IP dostępnych dla klientów można znaleźć w dwóch miejscach w panelu sterowania avere:

* Tabela **VServers** (karta Pulpit nawigacyjny) —

  ![Karta Pulpit nawigacyjny panelu sterowania avere z kartą VServer wybraną w tabeli danych pod wykresem i sekcją adres IP w kółku](media/avere-vfxt-ip-addresses-dashboard.png)

* Strona ustawień **sieci dołączona do klienta** —

  ![Ustawienia > VServer > stronie konfiguracji sieci przeznaczonej dla klientów z okręgiem wokół sekcji Zakres adresów tabeli dla określonego vserver](media/avere-vfxt-ip-addresses-settings.png)

Oprócz ścieżek należy uwzględnić [argumenty polecenia instalacji](#mount-command-arguments) opisane poniżej podczas instalowania każdego klienta.

### <a name="mount-command-arguments"></a>Argumenty polecenia instalacji

Aby zapewnić bezproblemowe Instalowanie klienta, należy przekazać te ustawienia i argumenty w poleceniu instalacji:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Wymagane ustawienia | Opis |
--- | ---
``hard`` | Instalacje miękkie do klastra vFXT są skojarzone z awariami aplikacji i możliwymi utratą danych.
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30`` , aby uniknąć błędów instalacji przejściowej. (W instalacjach na pierwszym planie zalecana jest inna wartość).

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu klientów programu można używać ich do kopiowania danych do nowego kontenera magazynu obiektów BLOB w klastrze. Jeśli nie musisz wypełniać nowego magazynu, zapoznaj się z innymi linkami, aby dowiedzieć się więcej o dodatkowych zadaniach konfiguracyjnych:

* [Przenoszenie danych do podstawowego pliku klastra](avere-vfxt-data-ingest.md) — jak używać wielu klientów i wątków do wydajnego przekazywania danych do nowego podstawowego pliku
* [Dostosowywanie dostrajania klastra](avere-vfxt-tuning.md) — dostosowanie ustawień klastra do własnych obciążeń
* [Zarządzanie klastrem](avere-vfxt-manage-cluster.md) — jak uruchomić lub zatrzymać klaster i zarządzać węzłami

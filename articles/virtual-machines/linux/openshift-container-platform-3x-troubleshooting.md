---
title: Rozwiązywanie problemów z wdrożeniem platformy OpenShift Container platform 3,11 na platformie Azure
description: Rozwiązywanie problemów z wdrożeniem platformy OpenShift Container platform 3,11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672323"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Rozwiązywanie problemów z wdrożeniem platformy OpenShift Container platform 3,11 na platformie Azure

Jeśli klaster OpenShift nie zostanie pomyślnie wdrożony, Azure Portal dostarczy danych wyjściowych błędu. Dane wyjściowe mogą być trudne do odczytania, co utrudnia zidentyfikowanie problemu. Szybko Skanuj dane wyjściowe dla kodu zakończenia 3, 4 lub 5. Poniżej znajdują się informacje dotyczące tych trzech kodów zakończenia:

- Kod zakończenia 3: Nazwa użytkownika lub hasło subskrypcji Red Hat lub identyfikator organizacji/klucz aktywacji są nieprawidłowe
- Kod zakończenia 4: Identyfikator puli Red Hat jest niepoprawny lub nie ma uprawnień dostępnych
- Kod zakończenia 5: nie można zainicjować obsługi woluminu elastycznej puli platformy Docker

W przypadku wszystkich innych kodów zakończenia Połącz się z hostami za pośrednictwem protokołu SSH, aby wyświetlić pliki dziennika.

**OpenShift Container Platform 3.11**

SSH z hostem rozwiązania ansible element PlayBook. W przypadku szablonu lub oferty portalu Marketplace Użyj hosta bastionu. Z bastionu można SSH do wszystkich innych węzłów w klastrze (Master, infrastruktura, CNS, COMPUTE). Musisz być katalogiem głównym, aby wyświetlić pliki dziennika. Katalog główny jest domyślnie wyłączony dla dostępu SSH, dlatego nie należy używać elementu głównego do połączeń SSH z innymi węzłami.

**OKD**

SSH z hostem rozwiązania ansible element PlayBook. W przypadku szablonu OKD (wersja 3,9 i wcześniejsze) Użyj hosta Master-0. W przypadku szablonu OKD (wersja 3,10 i nowsze) Użyj hosta bastionu. Z hosta rozwiązania ansible element PlayBook można SSH do wszystkich innych węzłów w klastrze (Master, infrastruktura, CNS, COMPUTE). Musisz być głównym (sudo Su-), aby wyświetlić pliki dziennika. Katalog główny jest domyślnie wyłączony dla dostępu SSH, dlatego nie należy używać elementu głównego do połączeń SSH z innymi węzłami.

## <a name="log-files"></a>Pliki dziennika

Pliki dziennika (stderr i stdout) dla skryptów przygotowania hosta znajdują się `/var/lib/waagent/custom-script/download/0` na wszystkich hostach. Jeśli wystąpił błąd podczas przygotowywania hosta, Wyświetl te pliki dziennika, aby określić błąd.

Jeśli skrypty przygotowania zostały wykonane pomyślnie, należy sprawdzić pliki dziennika w `/var/lib/waagent/custom-script/download/1` katalogu hosta rozwiązania ansible element PlayBook. Jeśli wystąpił błąd podczas rzeczywistej instalacji OpenShift, zostanie wyświetlony komunikat o błędzie. Korzystając z tych informacji, skontaktuj się z pomocą techniczną, aby uzyskać dalszą pomoc.

Przykładowe dane wyjściowe

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Najczęstsze błędy podczas instalacji:

1. Klucz prywatny ma hasło
2. Wpis tajny magazynu kluczy z kluczem prywatnym nie został poprawnie utworzony
3. Poświadczenia jednostki usługi zostały wprowadzone nieprawidłowo
4. Jednostka usługi nie ma dostępu współautora do grupy zasobów

### <a name="private-key-has-a-passphrase"></a>Klucz prywatny ma hasło

Zostanie wyświetlony komunikat o błędzie z powodu odmowy uprawnień dla protokołu SSH. Aby wyszukać hasło klucza prywatnego, Użyj protokołu SSH do hosta rozwiązania ansible element PlayBook.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Wpis tajny magazynu kluczy z kluczem prywatnym nie został poprawnie utworzony

Klucz prywatny jest kopiowany do hosta rozwiązania ansible element PlayBook-~/.ssh/id_rsa. Potwierdź, że ten plik jest prawidłowy. Przetestuj, otwierając sesję SSH do jednego z węzłów klastra z hosta rozwiązania ansible element PlayBook.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Poświadczenia jednostki usługi zostały wprowadzone nieprawidłowo

Podczas przekazywania danych wejściowych do szablonu lub oferty w witrynie Marketplace podano nieprawidłowe informacje. Upewnij się, że używasz poprawnego identyfikatora appId (clientId) i hasła (clientSecret) dla jednostki usługi. Sprawdź, wydając następujące polecenie interfejsu wiersza polecenia platformy Azure.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Jednostka usługi nie ma dostępu współautora do grupy zasobów

Jeśli dostawca chmury platformy Azure jest włączony, użyta nazwa główna usługi musi mieć dostęp współautora do grupy zasobów. Sprawdź, wydając następujące polecenie interfejsu wiersza polecenia platformy Azure.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Dodatkowe narzędzia

W przypadku niektórych błędów można także użyć następujących poleceń, aby uzyskać więcej informacji:

1. stan systemctl \<service>
2. journalctl — XE

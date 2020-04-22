---
title: Rozwiązywanie problemów z wdrożeniem platformy kontenerowej OpenShift 3.11 na platformie Azure
description: Rozwiązywanie problemów z wdrożeniem platformy kontenerowej OpenShift 3.11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 90fd3680cfdc4ecd1dcb0ce33b63f8d76dd8bfae
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759466"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Rozwiązywanie problemów z wdrożeniem platformy kontenerowej OpenShift 3.11 na platformie Azure

Jeśli klaster OpenShift nie wdroży pomyślnie, portal Azure zapewni dane wyjściowe błędów. Dane wyjściowe mogą być trudne do odczytania, co utrudnia identyfikację problemu. Szybko zeskanuj to dane wyjściowe w poszukiwaniu kodu wyjściowego 3, 4 lub 5. Poniżej przedstawiono informacje na temat tych trzech kodów zakończenia:

- Kod zakończenia 3: Nazwa użytkownika subskrypcji Red Hat / Hasło lub Identyfikator organizacji / Klucz aktywacyjny jest nieprawidłowy
- Kod zakończenia 4: Twój identyfikator puli Red Hat jest nieprawidłowy lub nie ma dostępnych uprawnień
- Kod zakończenia 5: Nie można aprowizować woluminu cienkiej puli platformy Docker

W przypadku wszystkich innych kodów zakończenia należy połączyć się z hostem za pośrednictwem ssh, aby wyświetlić pliki dziennika.

**OpenShift Container Platform 3.11**

SSH do hosta podręcznika. W przypadku szablonu lub oferty Marketplace użyj hosta bastionu. Z bastionu można SSH do wszystkich innych węzłów w klastrze (master, infra, CNS, compute). Aby wyświetlić pliki dziennika, musisz być rootem. Katalog główny jest domyślnie wyłączony dla dostępu SSH, więc nie należy używać katalogu głównego do SSH w innych węzłach.

**OKD**

SSH do hosta podręcznika. W przypadku szablonu OKD (wersja 3.9 i wcześniejsza) użyj hosta master-0. W przypadku szablonu OKD (wersja 3.10 i nowsza) użyj hosta bastionu. Z hosta podręcznika ansible, można SSH do wszystkich innych węzłów w klastrze (master, infra, CNS, compute). Musisz być root (sudo su -), aby wyświetlić pliki dziennika. Katalog główny jest domyślnie wyłączony dla dostępu SSH, więc nie należy używać katalogu głównego do SSH w innych węzłach.

## <a name="log-files"></a>Pliki dziennika

Pliki dziennika (stderr i stdout) dla skryptów `/var/lib/waagent/custom-script/download/0` przygotowania hosta znajdują się na wszystkich hostach. Jeśli wystąpił błąd podczas przygotowywania hosta, wyświetl te pliki dziennika, aby określić błąd.

Jeśli skrypty przygotowania zostały pomyślnie uruchomiony, `/var/lib/waagent/custom-script/download/1` pliki dziennika w katalogu ansible hosta podręcznika będą musiały zostać zbadane. Jeśli wystąpił błąd podczas rzeczywistej instalacji OpenShift, plik stdout wyświetli błąd. Skorzystaj z tych informacji, aby skontaktować się z pomocą techniczną w celu uzyskania dalszej pomocy.

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

Najczęstsze błędy podczas instalacji to:

1. Klucz prywatny ma hasło
2. Klucz tajny magazynu kluczy z kluczem prywatnym nie został utworzony poprawnie
3. Poświadczenia głównej usługi zostały wprowadzone niepoprawnie
4. Podmiot zabezpieczeń usługi nie ma dostępu współautora do grupy zasobów

### <a name="private-key-has-a-passphrase"></a>Klucz prywatny ma hasło

Zobaczysz błąd, że uprawnienie zostało odrzucone dla ssh. ssh do ansible playbook hosta, aby sprawdzić hasło na klucz prywatny.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Klucz tajny magazynu kluczy z kluczem prywatnym nie został utworzony poprawnie

Klucz prywatny jest kopiowany do hosta podręcznika ansible - ~/.ssh/id_rsa. Upewnij się, że ten plik jest poprawny. Przetestuj, otwierając sesję SSH do jednego z węzłów klastra z hosta podręcznika ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Poświadczenia głównej usługi zostały wprowadzone niepoprawnie

Podczas dostarczania danych wejściowych do szablonu lub oferty marketplace, niepoprawne informacje zostały dostarczone. Upewnij się, że używasz poprawne appId (clientId) i hasło (clientSecret) dla jednostki usługi. Sprawdź, wydając następujące polecenie interfejsu wiersza polecenia azure.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Podmiot zabezpieczeń usługi nie ma dostępu współautora do grupy zasobów

Jeśli dostawca chmury platformy Azure jest włączony, a następnie jednostki usługi używane musi mieć dostęp współautora do grupy zasobów. Sprawdź, wydając następujące polecenie interfejsu wiersza polecenia azure.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Dodatkowe narzędzia

W przypadku niektórych błędów można również użyć następujących poleceń, aby uzyskać więcej informacji:

1. > usługi \<stanu systemctl
2. journalctl -xe

---
title: Omówienie obsługi funkcji Cloud-init dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Omówienie możliwości usługi Cloud-init w celu skonfigurowania maszyny wirtualnej w czasie aprowizacji na platformie Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/15/2020
ms.author: danis
ms.openlocfilehash: a87c2b571027e0304909e69b252c9e080c4da9c1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978632"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Obsługa usługi Cloud-init dla maszyn wirtualnych w systemie Azure
W tym artykule opisano obsługę funkcji [Cloud-init](https://cloudinit.readthedocs.io) w celu skonfigurowania maszyny wirtualnej lub zestawów skalowania maszyn wirtualnych w czasie aprowizacji na platformie Azure. Te konfiguracje usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure.  

Inicjowanie obsługi maszyn wirtualnych to proces, w którym platforma Azure przejdzie do lokalizacji maszyny wirtualnej Tworzenie wartości parametrów, takich jak nazwa hosta, nazwa użytkownika, hasło itp., oraz udostępnienie ich dla maszyny wirtualnej w trakcie jej uruchamiania. "Agent aprowizacji" użyje tych wartości, skonfiguruje maszynę wirtualną i zwróci zwrot z powrotem po zakończeniu. 

Platforma Azure obsługuje dwa agenci aprowizacji [Cloud-init](https://cloudinit.readthedocs.io)oraz [agenta systemu Azure Linux (wala)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Cloud-init — Omówienie
[Cloud-init](https://cloudinit.readthedocs.io) to szeroko stosowane podejście do dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego uruchomienia. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ usługa Cloud-init jest wywoływana podczas początkowego procesu rozruchu, nie ma dodatkowych kroków ani wymaganych agentów, aby zastosować konfigurację.  Aby uzyskać więcej informacji na temat poprawnego formatowania `#cloud-config` plików lub innych danych wejściowych, zobacz [witrynę dokumentacji Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` pliki są plikami tekstowymi zakodowanymi w formacie base64.

Usługa Cloud-init działa również między dystrybucjami. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Usługa Cloud-init automatycznie korzysta z natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie pracujemy z naszymi potwierdzonymi partnerami dystrybucji systemu Linux w celu udostępnienia obrazów z obsługą chmury w portalu Azure Marketplace. Te obrazy spowodują, że wdrożenia i konfiguracje usługi Cloud-init pracują bezproblemowo z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych. Początkowo firma Microsoft współpracuje z zatwierdzonymi partnerami dystrybucji systemu Linux i nadrzędnym, aby zapewnić funkcje Cloud-init z systemem operacyjnym na platformie Azure, a następnie pakiety są aktualizowane i udostępniane publicznie w repozytoriach pakietów dystrybucji. 

Istnieją dwa etapy umożliwiające udostępnienie usługi Cloud-init dla zatwierdzonego systemu operacyjnego Linux dystrybucji na platformie Azure, obsługę pakietów, a następnie obsługę obrazów:
* "Obsługa pakietu Cloud-init w przypadku dokumentów platformy Azure", które pakiety inicjowania usługi Cloud-init są obsługiwane lub w wersji zapoznawczej, dzięki czemu można używać tych pakietów z systemem operacyjnym w obrazie niestandardowym.
* "Image Cloud-init Read", jeśli obraz jest już skonfigurowany do korzystania z funkcji Cloud-init.


### <a name="canonical"></a>Canonical
| Wydawca/wersja| Oferta | SKU | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanoniczny 20,04 |UbuntuServer |18,04 – LTS |najnowsza |tak | tak |
|Kanoniczny 18,04 |UbuntuServer |18,04 – LTS |najnowsza |tak | tak |
|Kanoniczny 16,04|UbuntuServer |16.04-LTS |najnowsza |tak | tak |
|Kanoniczny 14,04|UbuntuServer |14.04.5-LTS |najnowsza |tak | tak |

### <a name="rhel"></a>RHEL
| Wydawca/wersja | Oferta | SKU | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |tak | tak — obsługa z wersji pakietu: *18.2-1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | tak (Uwaga: jest to obraz w wersji zapoznawczej i nie **może** być używany już. zostanie on usunięty 1 września 2020) | Nie dotyczy |
|RedHat 7,7 (Gen1)|RHEL |7,7 | 7.7.2020051912 | tak | tak — obsługa z wersji pakietu: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 77 – Gen2 | 7.7.2020051913 | tak | tak — obsługa z wersji pakietu: *18.5 -6. el7*|
|RedHat 7,7 (Gen1)|RHEL |7 — LVM | 7.7.2020051921 | tak | tak — obsługa z wersji pakietu: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 7lvm — Gen2 | 7.7.2020051922  | tak | tak — obsługa z wersji pakietu: *18.5 -6. el7*|
|RedHat 7,7 (Gen1) |RHEL — BYOS | RHEL — lvm77 | 7.7.20200416 | tak  | tak — obsługa z wersji pakietu: *18.5 -6. el7*|
|RedHat 8,1 (Gen1) |RHEL |8,1-Ci |8.1.2020042511 | tak (Uwaga: jest to obraz w wersji zapoznawczej, a wszystkie obrazy z systemem RHEL 8,1 obsługują funkcję Cloud-init, która zostanie usunięta 1 sierpnia 2020) | Nie, Eat w przypadku pełnej pomocy technicznej 2020 czerwca|
|RedHat 8,1 (Gen2) |RHEL |81-Ci-Gen2 |8.1.2020042524 | tak (Uwaga: jest to obraz w wersji zapoznawczej, a wszystkie obrazy z systemem RHEL 8,1 obsługują funkcję Cloud-init, która zostanie usunięta 1 sierpnia 2020) | Nie, Eat w przypadku pełnej pomocy technicznej 2020 czerwca |

* Wszystkie obrazy z RedHat: RHEL 7,8 i 8,2 (Gen1 i Gen2) są inicjowane przy użyciu funkcji Cloud-init.

### <a name="centos"></a>CentOS

| Wydawca/wersja | Oferta | SKU | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |tak (Uwaga: jest to obraz w wersji zapoznawczej i nie **może** być używany już. zostanie on usunięty 1 września 2020) | Nie dotyczy |
|OpenLogic 7,7 |CentOS | 7,7 |7.7.2020062400 |tak | tak — obsługa z wersji pakietu: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS | 7_7 — Gen2 |7.7.2020062401 |tak | tak — obsługa z wersji pakietu: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 |CentOS — HPC | 7,7 |7.6.2020062600 |tak | tak — obsługa z wersji pakietu: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS — HPC | 7_7 — Gen2 |7.6.2020062601 |tak | tak — obsługa z wersji pakietu: `18.5-6.el7.centos.5`|
|OpenLogic 8,1 |CentOS | 8_1 |8.1.2020062400 |tak | tak — obsługa z wersji pakietu: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS | 8_1 — Gen2 |8.1.2020062401 |tak | tak — obsługa z wersji pakietu: `18.5-7.el8_1.1`|
|OpenLogic 8,1 |CentOS — HPC | 8_1 |8.1.2020062400 |tak | tak — obsługa z wersji pakietu: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS-HPC: 8_1-Gen2 | 8_1 — Gen2 |8.1.2020062401 |tak | tak — obsługa z wersji pakietu: `18.5-7.el8_1.1`|

* Wszystkie obrazy z OpenLogic: CentOS 7,8 i 8,2 (Gen1 i Gen2) są inicjowane przy użyciu funkcji Cloud-init.

### <a name="oracle"></a>Oracle

| Wydawca/wersja | Oferta | SKU | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-Ci |7.7.01| Podgląd obrazu (Uwaga: jest to obraz w wersji zapoznawczej, a gdy wszystkie obrazy z systemem Oracle 7,7 obsługują funkcję Cloud-init, zostanie on 2020 usunięty. | nie, w wersji zapoznawczej pakiet jest: *18.5-3.0.1. el7*

### <a name="suse-sles"></a>SUSE SLES
Te obrazy SLES zostały zaktualizowane w celu inicjowania obsługi administracyjnej przy użyciu funkcji Cloud-init, a także wariantów obrazów Gen2.
* SUSE: SLES-15-SP1-{podstawowa/BYOS/HPC/HPC-BYOS/Chost-BYOS}: Gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1: Gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1-BYOS: Gen1:2020.06.10
* SUSE: Manager-proxy-4-BYOS: Gen1:2020.06.10
* SUSE: Manager-Server-4-BYOS: Gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 15:2020.06.10
* SUSE: SLES-12-SP5: Gen1:2020.06.10
* SUSE: SLES-12-SP5 {-BYOS/Basic/HPC-BYOS/HPC}: Gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP4:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP3:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP2:2020.06.10


### <a name="debian"></a>Debian
| Wydawca/wersja | Oferta | SKU | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| Debian (Gen1) |Debian-10 | 10 — cloudinit |Cloud-init — wersja zapoznawcza| tak (tylko wersja zapoznawcza) | Nie, w wersji zapoznawczej. |
| Debian (Gen2) |Debian-10 | 10-cloudinit-Gen2 |Cloud-init — wersja zapoznawcza| tak (tylko wersja zapoznawcza) | Nie, w wersji zapoznawczej. |




Obecnie Azure Stack obsługuje Inicjowanie obsługi obrazów z obsługą funkcji Cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Czym różni się usługa Cloud-init i Agent systemu Linux (WALA)?
WALA to Agent specyficzny dla platformy Azure służący do aprowizacji i konfigurowania maszyn wirtualnych oraz obsługi [rozszerzeń platformy Azure](../extensions/features-linux.md). 

Ulepszamy zadanie konfigurowania maszyn wirtualnych, aby korzystało z usługi Cloud-init zamiast agenta systemu Linux, aby umożliwić istniejącym klientom korzystającym z usługi Cloud-init używanie ich bieżących skryptów w chmurze lub nowych klientów w celu skorzystania z zaawansowanych funkcji konfiguracji usługi Cloud-init. Jeśli masz istniejące inwestycje w skrypty inicjowania usługi Cloud-init do konfigurowania systemów z systemem Linux, **nie ma dodatkowych ustawień wymaganych** do przetworzenia ich przez program Cloud-init. 

Usługa Cloud-init nie może przetwarzać rozszerzeń platformy Azure, więc WALA jest nadal wymagana w obrazie do przetwarzania rozszerzeń, ale musi mieć wyłączony kod aprowizacji, w przypadku zatwierdzonych obrazów systemu Linux dystrybucje, które są konwertowane na potrzeby inicjowania obsługi przez funkcję Cloud-init, będą mieć zainstalowane WALA i poprawnie skonfigurowane.

W przypadku tworzenia maszyny wirtualnej, jeśli nie dołączysz przełącznika interfejsu wiersza polecenia platformy Azure `--custom-data` w czasie aprowizacji, Usługa Cloud-init lub wala przyjmuje parametry minimalnej aprowizacji maszyny wirtualnej wymagane do zainicjowania obsługi maszyny wirtualnej i Ukończ wdrożenie przy użyciu ustawień domyślnych.  Jeśli odwołujesz się do konfiguracji Cloud-init przy użyciu `--custom-data` przełącznika, niezależnie od tego, jakie dane są zawarte w danych niestandardowych, będą dostępne w usłudze Cloud-init podczas uruchamiania maszyny wirtualnej.

konfiguracje inicjowania usługi Cloud-init stosowane do maszyn wirtualnych nie mają ograniczeń czasowych i nie spowodują, że wdrożenie zakończy się niepowodzeniem. Ta wartość nie jest prawdziwa w przypadku WALA, jeśli zmienisz domyślne ustawienia WALA na przetwarzanie danych niestandardowych, nie można przekroczyć łącznego czasu udostępniania maszyny wirtualnej o wartości 40mins, jeśli tak, Tworzenie maszyny wirtualnej zakończy się niepowodzeniem.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie maszyny wirtualnej z obsługą usługi Cloud-init
Wdrażanie maszyny wirtualnej z włączoną obsługą chmury jest proste, ponieważ odwołuje się do dystrybucji z włączoną obsługą chmury podczas wdrażania.  Obsłudze dystrybucji systemu Linux muszą włączyć i zintegrować usługę Cloud-init z podstawowymi opublikowanymi obrazami platformy Azure. Po potwierdzeniu obrazu, który chcesz wdrożyć, jest włączona funkcja Cloud-init, można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia obrazu. 

Pierwszym krokiem wdrożenia tego obrazu jest utworzenie grupy zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Następnym krokiem jest utworzenie pliku w bieżącej powłoce o nazwie *cloud-init.txt* i wklejenie poniższej konfiguracji. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij klawisz `ctrl-X` , aby zamknąć plik, wpisz polecenie, `y` Aby zapisać plik, a następnie naciśnij klawisz, `enter` Aby potwierdzić nazwę pliku przy zamykaniu.

Ostatnim krokiem jest utworzenie maszyny wirtualnej za pomocą polecenia [AZ VM Create](/cli/azure/vm) . 

Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74* i tworzy klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetli informacje specyficzne dla Twojego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Tworzenie maszyny wirtualnej trwa trochę czasu, pakiety do zainstalowania i aplikacja do uruchomienia. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Do maszyny wirtualnej można użyć protokołu SSH i wykonać kroki opisane w sekcji Rozwiązywanie problemów w celu wyświetlenia dzienników usługi Cloud-init. 

Możesz również wdrożyć maszynę wirtualną z włączoną obsługą chmury, przekazując [Parametry w szablonie usługi ARM](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters).

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z usługą Cloud-init
Po zainicjowaniu obsługi maszyny wirtualnej Usługa Cloud-init będzie działać za pomocą wszystkich modułów i skryptów zdefiniowanych w `--custom-data` programie w celu skonfigurowania maszyny wirtualnej.  Jeśli konieczne jest rozwiązanie błędów lub pominięć z konfiguracji, należy wyszukać nazwę modułu ( `disk_setup` lub `runcmd` na przykład) w dzienniku usługi Cloud-init znajdującym się w **/var/log/Cloud-init.log**.

> [!NOTE]
> Nie każdy błąd modułu powoduje krytyczny błąd konfiguracji w chmurze. Na przykład przy użyciu `runcmd` modułu, jeśli skrypt nie powiedzie się, Usługa Cloud-init nadal zgłasza pomyślne zainicjowanie obsługi, ponieważ moduł runcmd został wykonany.

Więcej szczegółów dotyczących rejestrowania w usłudze Cloud-init można znaleźć w [dokumentacji usługi Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów z usługą Cloud-init](cloud-init-troubleshooting.md).


Przykłady zmian konfiguracji w usłudze Cloud-init można znaleźć w następujących dokumentach:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)

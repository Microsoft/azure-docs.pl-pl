---
title: Korzystanie z narzędzi automatyzacji infrastruktury
description: Dowiedz się, jak korzystać z narzędzi do automatyzacji infrastruktury, takich jak rozwiązania ansible, Chef, Puppet, Terraform i pakujący, aby tworzyć maszyny wirtualne na platformie Azure i zarządzać nimi.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 7a702fbf405011a9df3e7e77936a9a37f0fdfad7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730642"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Korzystanie z narzędzi do automatyzacji infrastruktury z maszynami wirtualnymi na platformie Azure

Aby w spójny sposób tworzyć maszyny wirtualne platformy Azure i zarządzać nimi w odpowiedniej skali, zwykle jest wymagana pewna forma automatyzacji. Istnieje wiele narzędzi i rozwiązań umożliwiających automatyzację całego cyklu wdrażania infrastruktury platformy Azure i zarządzania nimi. W tym artykule przedstawiono niektóre narzędzia automatyzacji infrastruktury, których można użyć na platformie Azure. Te narzędzia często pasują do jednej z następujących metod:

- Automatyzowanie konfiguracji maszyn wirtualnych
    - Narzędzia obejmują [rozwiązania ansible](#ansible), [Chef](#chef), [Puppet](#puppet)i [szablon Azure Resource Manager](#azure-resource-manager-template).
    - Narzędzia specyficzne dla dostosowywania maszyn wirtualnych obejmują funkcję [Cloud-init](#cloud-init) dla maszyn wirtualnych z systemem Linux, [konfigurację żądanego stanu programu PowerShell (DSC)](#powershell-dsc)i [rozszerzenie niestandardowego skryptu platformy Azure](#azure-custom-script-extension) dla wszystkich maszyn wirtualnych platformy Azure.

- Automatyzowanie zarządzania infrastrukturą
    - Narzędzia obejmują [pakiet pakujący](#packer) do automatyzowania niestandardowych kompilacji obrazów maszyn wirtualnych i [Terraform](#terraform) w celu zautomatyzowania procesu tworzenia infrastruktury.
    - [Azure Automation](#azure-automation) mogą wykonywać działania na platformie Azure i w infrastrukturze lokalnej.

- Automatyzowanie wdrażania i dostarczania aplikacji
    - Przykłady obejmują [Azure DevOps Services](#azure-devops-services) i [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Rozwiązania ansible](https://www.ansible.com/) to aparat automatyzacji służący do zarządzania konfiguracją, tworzenia maszyn wirtualnych lub wdrażania aplikacji. Rozwiązania ansible używa modelu bez agenta, zazwyczaj z kluczami SSH, do uwierzytelniania i zarządzania komputerami docelowymi. Zadania konfiguracyjne są zdefiniowane w elementy PlayBook z szeregiem modułów rozwiązania ansible dostępnych do wykonywania określonych zadań. Aby uzyskać więcej informacji, zobacz [jak działa rozwiązania ansible](https://www.ansible.com/how-ansible-works).

Instrukcje:

- [Zainstaluj i skonfiguruj rozwiązania ansible w systemie Linux do użycia z platformą Azure](/azure/developer/ansible/install-on-linux-vm).
- [Utwórz maszynę wirtualną z systemem Linux](/azure/developer/ansible/vm-configure).
- [Zarządzanie maszyną wirtualną z systemem Linux](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) to platforma automatyzacji, która ułatwia definiowanie sposobu konfigurowania, wdrażania i zarządzania infrastrukturą. Dodatkowe składniki obejmują Chef siedlisko do automatyzacji cyklu życia aplikacji, a nie infrastruktury i Chef, które ułatwiają automatyzację zgodności z wymaganiami dotyczącymi zabezpieczeń i zasad. Klienci Chef są zainstalowani na maszynach docelowych z co najmniej jednym serwerem centralnym Chef, który przechowuje konfiguracje i zarządza nimi. Aby uzyskać więcej informacji, zobacz [Omówienie Chef](https://docs.chef.io/chef_overview.html).

Instrukcje:

- [Wdróż automatyzację Chef z poziomu portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Zainstaluj program Chef w systemie Windows i Utwórz maszyny wirtualne platformy Azure](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) to platforma automatyzacji gotowa dla przedsiębiorstw, która obsługuje proces dostarczania i wdrażania aplikacji. Agenci są zainstalowani na komputerach docelowych, aby umożliwić usłudze Puppet Master uruchamianie manifestów definiujących żądaną konfigurację infrastruktury i maszyn wirtualnych platformy Azure. Puppet można zintegrować z innymi rozwiązaniami, takimi jak Jenkins i GitHub, aby uzyskać Ulepszony przepływ pracy DevOps. Aby uzyskać więcej informacji, zobacz [jak działa Puppet](https://puppet.com/products/how-puppet-works).

Instrukcje:

- [Wdróż Puppet](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ usługa Cloud-init jest wywoływana podczas początkowego procesu rozruchu, nie ma dodatkowych kroków ani wymaganych agentów, aby zastosować konfigurację.  Aby uzyskać więcej informacji na temat prawidłowego formatowania `#cloud-config` plików, zobacz [witrynę dokumentacji Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` pliki są plikami tekstowymi zakodowanymi w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie pracujemy z naszymi potwierdzonymi partnerami dystrybucji systemu Linux w celu udostępnienia obrazów z obsługą chmury w portalu Azure Marketplace. Te obrazy sprawiają, że wdrożenia i konfiguracje init w chmurze działają bezproblemowo z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych.
Dowiedz się więcej o programie Cloud-init na platformie Azure:

- [Obsługa inicjowania w chmurze dla maszyn wirtualnych z systemem Linux na platformie Azure](./linux/using-cloud-init.md)
- [Wypróbuj samouczek dotyczący zautomatyzowanej konfiguracji maszyny wirtualnej przy użyciu funkcji Cloud-init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>Rozszerzenie DSC programu PowerShell
[Konfiguracja żądanego stanu programu PowerShell (DSC)](/powershell/scripting/dsc/overview/overview) to platforma zarządzania służąca do definiowania konfiguracji maszyn docelowych. Konfiguracja DSC może być również używana w systemie Linux za pomocą [serwera infrastruktury Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/).

Konfiguracje DSC definiują, co należy zainstalować na komputerze i jak skonfigurować hosta. Lokalny aparat Configuration Manager (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje w oparciu o wypychaną konfigurację. Serwer ściągania to usługa sieci Web działająca na hoście centralnym do przechowywania konfiguracji DSC i skojarzonych zasobów. Serwer ściągania komunikuje się z aparatem LCM na każdym hoście docelowym, aby zapewnić wymagane konfiguracje i raport zgodności.

Instrukcje:

- [Utwórz podstawową konfigurację DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Skonfiguruj serwer ściągający DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Użyj DSC dla systemu Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozszerzenie niestandardowego skryptu platformy Azure
Rozszerzenie niestandardowego skryptu platformy Azure dla systemu [Linux](./extensions/custom-script-linux.md) lub [Windows](./extensions/custom-script-windows.md) pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. Możesz użyć rozszerzenia podczas tworzenia maszyny wirtualnej lub dowolnego czasu po użyciu maszyny wirtualnej.

Skrypty można pobrać z usługi Azure Storage lub z dowolnej lokalizacji publicznej, takiej jak repozytorium GitHub. Za pomocą niestandardowego rozszerzenia skryptu można pisać skrypty w dowolnym języku, który działa na źródłowej maszynie wirtualnej. Te skrypty mogą służyć do instalowania aplikacji lub konfigurowania maszyny wirtualnej zgodnie z potrzebami. Aby zabezpieczyć poświadczenia, poufne informacje, takie jak hasła, mogą być przechowywane w chronionej konfiguracji. Te poświadczenia są odszyfrowywane tylko wewnątrz maszyny wirtualnej.

Instrukcje:

- [Utwórz maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i Użyj niestandardowego rozszerzenia skryptu](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Utwórz maszynę wirtualną z systemem Windows przy użyciu Azure PowerShell i Użyj niestandardowego rozszerzenia skryptu](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Usługi Packer
[Pakujący](https://www.packer.io) automatyzuje proces kompilacji podczas tworzenia niestandardowego obrazu maszyny wirtualnej na platformie Azure. Program Packer służy do definiowania systemu operacyjnego i uruchamiania skryptów po konfiguracji, które dostosowują maszynę wirtualną pod kątem konkretnych potrzeb. Po skonfigurowaniu maszyna wirtualna zostanie przechwycona jako obraz dysku zarządzanego. Program Pack automatyzuje proces tworzenia źródłowej zasobów maszyny wirtualnej, sieci i magazynu, uruchamiania skryptów konfiguracyjnych, a następnie tworzenia obrazu maszyny wirtualnej.

Instrukcje:

- [Użyj programu Packer, aby utworzyć obraz maszyny wirtualnej z systemem Linux na platformie Azure](./linux/build-image-with-packer.md).
- [Użyj programu Packer, aby utworzyć obraz maszyny wirtualnej z systemem Windows na platformie Azure](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) to narzędzie do automatyzacji, które umożliwia definiowanie i tworzenie całej infrastruktury platformy Azure przy użyciu jednego szablonu języka formatowania — HashiCorp Configuration Language (HCL). Za pomocą Terraform definiujesz szablony, które automatyzują proces tworzenia zasobów sieciowych, magazynowych i maszyn wirtualnych dla danego rozwiązania aplikacji. Możesz użyć istniejących szablonów Terraform dla innych platform platformy Azure, aby zapewnić spójność i uprościć wdrożenie infrastruktury bez konieczności konwertowania na szablon Azure Resource Manager.

Instrukcje:

- [Zainstaluj i skonfiguruj Terraform z platformą Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Utwórz infrastrukturę platformy Azure z usługą Terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) używa elementów Runbook do przetwarzania zestawu zadań dla docelowych maszyn wirtualnych. Azure Automation jest używany do zarządzania istniejącymi maszynami wirtualnymi, a nie do tworzenia infrastruktury. Azure Automation można uruchamiać na maszynach wirtualnych z systemem Linux i Windows, a także na maszynach wirtualnych lub fizycznych z hybrydowym procesem roboczym elementu Runbook. Elementy Runbook mogą być przechowywane w repozytorium kontroli źródła, takim jak GitHub. Te elementy Runbook można następnie uruchomić ręcznie lub zgodnie ze zdefiniowanym harmonogramem.

Azure Automation udostępnia również usługę konfiguracji żądanego stanu (DSC), która umożliwia tworzenie definicji, w jaki sposób należy skonfigurować dany zestaw maszyn wirtualnych. Usługa DSC gwarantuje, że wymagana konfiguracja zostanie zastosowana, a maszyna wirtualna pozostaje spójna. Azure Automation DSC działa na komputerach z systemem Windows i Linux.

Instrukcje:

- [Utwórz element Runbook programu PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Użyj hybrydowego procesu roboczego elementu Runbook do zarządzania zasobami lokalnymi](../automation/automation-hybrid-runbook-worker.md).
- [Użyj Azure Automation DSC](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Usługa Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) to pakiet narzędzi, które ułatwiają udostępnianie i śledzenie kodu, korzystanie z zautomatyzowanych kompilacji oraz tworzenie kompletnego potoku ciągłej integracji i tworzenia oprogramowania (Ci/CD). Azure DevOps Services integruje się z programem Visual Studio i innymi edytorami, aby uprościć użycie. Azure DevOps Services może również tworzyć i konfigurować maszyny wirtualne platformy Azure, a następnie wdrażać w nich kod.

Dowiedz się więcej na następujące tematy:

- [Azure DevOps Services](/azure/devops/user-guide/index).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) to serwer ciągłej integracji, który pomaga wdrażać i testować aplikacje oraz tworzyć zautomatyzowane potoki do dostarczania kodu. Istnieją setki wtyczek pozwalające zwiększyć podstawową platformę Jenkins i można także zintegrować z wieloma innymi produktami i rozwiązaniami za pomocą elementów webhook. Możesz ręcznie zainstalować Jenkins na maszynie wirtualnej platformy Azure, uruchomić Jenkins z poziomu kontenera Docker lub użyć wstępnie skompilowanego obrazu portalu Azure Marketplace.

Instrukcje:

- [Tworzenie infrastruktury deweloperskiej na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu usług Jenkins, GitHub i Docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia ona warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów w ramach subskrypcji platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i tagi, można zabezpieczyć oraz zorganizować zasoby po wdrożeniu.

Instrukcje:

- [Wdróż maszyny wirtualne przy użyciu szablonu Menedżer zasobów](./linux/spot-template.md).
- [Utwórz maszynę wirtualną z systemem Windows na podstawie szablonu Menedżer zasobów](./windows/ps-template.md).
- [Pobierz szablon dla maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template).
- [Utwórz szablon usługi Azure Image Builder](./linux/image-builder-json.md).

## <a name="next-steps"></a>Następne kroki
Istnieje wiele różnych opcji używania narzędzi do automatyzacji infrastruktury na platformie Azure. Możesz korzystać z rozwiązania, które najlepiej pasuje do Twoich potrzeb i środowiska. Aby rozpocząć pracę i wypróbować niektóre narzędzia wbudowane w platformę Azure, zobacz jak zautomatyzować Dostosowywanie maszyny wirtualnej z systemem [Linux](./linux/tutorial-automate-vm-deployment.md) lub [Windows](./windows/tutorial-automate-vm-deployment.md) .

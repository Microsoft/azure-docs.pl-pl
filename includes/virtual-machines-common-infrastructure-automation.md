---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 7c1ad2581d025a9259c5cb2a84e4237f938fc892
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637149"
---
Aby utworzyć maszyny wirtualne platformy Azure (VM) i zarządzać nimi w spójny sposób na dużą skalę, zazwyczaj pożądana jest pewna forma automatyzacji. Istnieje wiele narzędzi i rozwiązań, które umożliwiają automatyzację pełnego wdrażania infrastruktury platformy Azure i cyklu życia zarządzania. W tym artykule przedstawiono niektóre narzędzia automatyzacji infrastruktury, których można używać na platformie Azure. Narzędzia te często mieszczą się w jednym z następujących podejść:

- Automatyzacja konfiguracji maszyn wirtualnych
    - Narzędzia obejmują [szablon Ansible](#ansible), [Chef](#chef), [Puppet](#puppet)i Azure [Resource Manager](#azure-resource-manager-template).
    - Narzędzia specyficzne dla dostosowywania maszyn wirtualnych obejmują [init chmury](#cloud-init) dla maszyn wirtualnych z systemem Linux, [powershell żądanej konfiguracji stanu (DSC)](#powershell-dsc)i [rozszerzenia skryptów niestandardowych platformy Azure](#azure-custom-script-extension) dla wszystkich maszyn wirtualnych platformy Azure.

- Automatyzacja zarządzania infrastrukturą
    - Narzędzia obejmują [Packer](#packer) do automatyzacji niestandardowych kompilacji obrazów maszyn wirtualnych i [Terraform](#terraform) do automatyzacji procesu tworzenia infrastruktury.
    - [Usługa Azure Automation](#azure-automation) może wykonywać akcje w infrastrukturze platformy Azure i lokalnej.

- Automatyzacja wdrażania i dostarczania aplikacji
    - Przykłady obejmują [usługi Azure DevOps](#azure-devops-services) i [usługi Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) to aparat automatyzacji do zarządzania konfiguracją, tworzenia maszyn wirtualnych lub wdrażania aplikacji. Ansible używa modelu bez agenta, zazwyczaj z kluczami SSH, do uwierzytelniania i zarządzania maszynami docelowymi. Zadania konfiguracyjne są definiowane w podręcznikach, a liczba modułów Ansible jest dostępna do wykonywania określonych zadań. Aby uzyskać więcej informacji, zobacz [Jak działa Ansible](https://www.ansible.com/how-ansible-works).

Instrukcje:

- [Zainstaluj i skonfiguruj aplikację Ansible w systemie Linux do użytku z platformą Azure](../articles/ansible/ansible-install-configure.md).
- [Utwórz maszynę wirtualną systemu Linux](../articles/ansible/ansible-create-vm.md).
- [Zarządzanie maszyną wirtualną systemu Linux](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Program chef](https://www.chef.io/) to platforma automatyzacji, która pomaga zdefiniować sposób konfigurowania, wdrażania i zarządzania infrastrukturą. Dodatkowe składniki obejmowały środowisko Chef Habitat do automatyzacji cyklu życia aplikacji, a nie infrastrukturę, oraz program Chef InSpec, który pomaga zautomatyzować zgodność z wymaganiami dotyczącymi zabezpieczeń i zasad. Klienci szefa kuchni są instalowane na komputerach docelowych, z co najmniej jednym centralnym serwerem szefa kuchni, które przechowują konfiguracje i zarządzają nimi. Aby uzyskać więcej informacji, zobacz [Przegląd programu Chef](https://docs.chef.io/chef_overview.html).

Instrukcje:

- [Wdrażanie automatyzacji szefa kuchni z portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Zainstaluj program Chef w systemie Windows i utwórz maszyny wirtualne platformy Azure](../articles/chef/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) to platforma automatyzacji gotowa do pracy dla przedsiębiorstw, która obsługuje proces dostarczania i wdrażania aplikacji. Agenci są instalowane na komputerach docelowych, aby umożliwić marionetkowego wzorca do uruchamiania manifestów, które definiują żądaną konfigurację infrastruktury platformy Azure i maszyn wirtualnych. Puppet można zintegrować z innymi rozwiązaniami, takimi jak Jenkins i GitHub dla ulepszonego przepływu pracy devops. Aby uzyskać więcej informacji, zobacz [Jak działa lalka](https://puppet.com/products/how-puppet-works).

Instrukcje:

- [Wdrażanie marionetkowe z portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ cloud-init jest wywoływana podczas początkowego procesu rozruchu, nie ma żadnych dodatkowych kroków lub wymaganych agentów do zastosowania konfiguracji.  Aby uzyskać więcej informacji na `#cloud-config` temat prawidłowego formatowania plików, zobacz [witrynę dokumentacji init w chmurze](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`pliki są plikami tekstowymi zakodowanymi w base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z naszymi zatwierdzonymi partnerami dystrybucji systemu Linux, aby obrazy z obsługą chmury były dostępne w portalu Azure marketplace. Te obrazy sprawiają, że wdrożenia i konfiguracje init chmury działają bezproblemowo z maszyn wirtualnych i zestawów skalowania maszyny wirtualnej.
Dowiedz się więcej szczegółów na temat init chmury na platformie Azure:

- [Obsługa cloud-init dla maszyn wirtualnych systemu Linux na platformie Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Wypróbuj samouczek dotyczący zautomatyzowanej konfiguracji maszyny Wirtualnej przy użyciu funkcji cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>Rozszerzenie DSC programu PowerShell
[PowerShell żądana konfiguracja stanu (DSC)](/powershell/scripting/dsc/overview/overview) jest platformą zarządzania do definiowania konfiguracji maszyn docelowych. DSC może być również używany w systemie Linux za pośrednictwem [serwera Open Management Infrastructure (OMI).](https://collaboration.opengroup.org/omi/)

Konfiguracje DSC określają, co należy zainstalować na komputerze i jak skonfigurować hosta. Aparat lokalnego programu Menedżer konfiguracji (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje na podstawie wypychanych konfiguracji. Serwer ściągania to usługa sieci web, która działa na centralnym hoście do przechowywania konfiguracji DSC i skojarzonych zasobów. Serwer ściągania komunikuje się z aparatem LCM na każdym hoście docelowym, aby zapewnić wymagane konfiguracje i raport na temat zgodności.

Instrukcje:

- [Utwórz podstawową konfigurację DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Konfigurowanie serwera ściągania DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Użyj DSC dla Linuksa](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozszerzenie niestandardowego skryptu platformy Azure
Rozszerzenie skryptów niestandardowych platformy Azure dla [systemu Linux](../articles/virtual-machines/linux/extensions-customscript.md) lub [Windows](../articles/virtual-machines/windows/extensions-customscript.md) pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. Rozszerzenia można użyć podczas tworzenia maszyny Wirtualnej lub w dowolnym momencie po użyciu maszyny wirtualnej.

Skrypty można pobrać z magazynu platformy Azure lub dowolnej lokalizacji publicznej, takiej jak repozytorium GitHub. Za pomocą niestandardowego rozszerzenia skryptów można pisać skrypty w dowolnym języku, który działa na źródłowej maszynie wirtualnej. Skrypty te mogą służyć do instalowania aplikacji lub konfigurowania maszyny Wirtualnej zgodnie z potrzebami. Aby zabezpieczyć poświadczenia, poufne informacje, takie jak hasła, mogą być przechowywane w chronionej konfiguracji. Te poświadczenia są odszyfrowywane tylko wewnątrz maszyny Wirtualnej.

Instrukcje:

- [Utwórz maszynę wirtualną systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure i użyj niestandardowego rozszerzenia skryptu](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Utwórz maszynę wirtualną systemu Windows za pomocą programu Azure PowerShell i użyj niestandardowego rozszerzenia skryptu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Program Packer](https://www.packer.io) automatyzuje proces kompilacji podczas tworzenia niestandardowego obrazu maszyny Wirtualnej na platformie Azure. Program Packer służy do definiowania systemu operacyjnego i uruchamiania skryptów po konfiguracji, które dostosowują maszynę wirtualną do określonych potrzeb. Po skonfigurowaniu maszyna wirtualna jest następnie przechwytywany jako obraz dysku zarządzanego. Program Packer automatyzuje proces tworzenia źródłowej maszyny Wirtualnej, zasobów sieciowych i magazynowych, uruchamiania skryptów konfiguracji, a następnie tworzenia obrazu maszyny Wirtualnej.

Instrukcje:

- [Użyj programu Packer, aby utworzyć obraz maszyny Wirtualnej z systemem Linux na platformie Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Użyj programu Packer, aby utworzyć obraz maszyny Wirtualnej systemu Windows na platformie Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) to narzędzie automatyzacji, które pozwala zdefiniować i utworzyć całą infrastrukturę platformy Azure za pomocą jednego języka formatu szablonu - Języka Konfiguracji HashiCorp (HCL). Za pomocą programu Terraform można zdefiniować szablony, które automatyzują proces tworzenia zasobów sieci, magazynu i maszyn wirtualnych dla danego rozwiązania aplikacji. Istniejących szablonów Terraform dla innych platform z platformą Azure, aby zapewnić spójność i uprościć wdrażanie infrastruktury bez konieczności konwersji na szablon usługi Azure Resource Manager.

Instrukcje:

- [Zainstaluj i skonfiguruj program Terraform za pomocą platformy Azure](../articles/terraform/terraform-install-configure.md).
- [Tworzenie infrastruktury platformy Azure za pomocą programu Terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) używa śmiód eks-owych do przetwarzania zestawu zadań na maszynach wirtualnych, które są kierowane. Usługa Azure Automation służy do zarządzania istniejącymi maszynami wirtualnymi, a nie do tworzenia infrastruktury. Usługa Azure Automation może działać zarówno na maszynach wirtualnych z systemem Linux, jak i windows, a także na lokalnych komputerach wirtualnych lub fizycznych z hybrydowym środowiskiem roboczym uruchomieniu. żyjowych. Księgi runbook mogą być przechowywane w repozytorium kontroli źródła, takich jak GitHub. Te programy runbook można następnie uruchomić ręcznie lub zgodnie ze zdefiniowanym harmonogramem.

Usługa Azure Automation udostępnia również usługę konfiguracji żądanego stanu (DSC), która umożliwia tworzenie definicji dla sposobu konfigurowania danego zestawu maszyn wirtualnych. DSC następnie zapewnia, że wymagana konfiguracja jest stosowana i maszyna wirtualna pozostaje spójna. Usługa Azure Automation DSC działa zarówno na komputerach z systemem Windows, jak i Linux.

Instrukcje:

- [Tworzenie śmiętu programu PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hybrydowy proces roboczy żyła do zarządzania zasobami lokalnymi](../articles/automation/automation-hybrid-runbook-worker.md).
- [Użyj usługi Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Usługa Azure DevOps Services
[Usługi Azure DevOps](https://www.visualstudio.com/team-services/) to zestaw narzędzi, które pomagają udostępniać i śledzić kod, używać automatycznych kompilacji i tworzyć pełny potok ciągłej integracji i programowania (CI/CD). Usługi Azure DevOps integruje się z programem Visual Studio i innymi edytorami, aby uprościć użycie. Usługi Azure DevOps mogą również tworzyć i konfigurować maszyny wirtualne platformy Azure, a następnie wdrażać dla nich kod.

Dowiedz się więcej o usługach:

- [Usługi Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) to serwer ciągłej integracji, który pomaga wdrażać i testować aplikacje oraz tworzyć automatyczne potoki do dostarczania kodu. Istnieją setki wtyczek, aby rozszerzyć podstawową platformę Jenkins, a także można zintegrować z wieloma innymi produktami i rozwiązaniami za pomocą webhook. Możesz ręcznie zainstalować usługę Jenkins na maszynie wirtualnej platformy Azure, uruchomić usługę Jenkins z poziomu kontenera platformy Docker lub użyć wstępnie utworzonego obrazu portalu Azure Marketplace.

Instrukcje:

- [Tworzenie infrastruktury programistycznej na maszynie Wirtualnej systemu Linux na platformie Azure za pomocą usług Jenkins, GitHub i Docker](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md).


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
[Usługa Azure Resource Manager](../articles/azure-resource-manager/templates/overview.md) to usługa wdrażania i zarządzania dla platformy Azure. Zawiera warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów w ramach subskrypcji platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i tagi, można zabezpieczyć i zorganizować zasoby po wdrożeniu.

Instrukcje:

- [Wdrażanie maszyn wirtualnych punktowych przy użyciu szablonu Menedżera zasobów](../articles/virtual-machines/linux/spot-template.md).
- [Wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C# i szablonu Menedżera zasobów.](../articles/virtual-machines/windows/csharp-template.md)
- [Tworzenie maszyny wirtualnej systemu Windows na podstawie szablonu Menedżera zasobów](../articles/virtual-machines/windows/ps-template.md).
- [Pobierz szablon maszyny Wirtualnej](../articles/virtual-machines/windows/download-template.md).
- [Utwórz szablon konstruktora obrazów platformy Azure](../articles/virtual-machines/linux/image-builder-json.md).

## <a name="next-steps"></a>Następne kroki
Istnieje wiele różnych opcji do korzystania z narzędzi automatyzacji infrastruktury na platformie Azure. Masz swobodę korzystania z rozwiązania, które najlepiej pasuje do Twoich potrzeb i środowiska. Aby rozpocząć i wypróbować niektóre narzędzia wbudowane w platformę Azure, zobacz, jak zautomatyzować dostosowywanie maszyny Wirtualnej [systemu Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) lub [Windows.](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md)

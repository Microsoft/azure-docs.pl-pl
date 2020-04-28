---
title: Przykłady szablonów usługi Azure Resource Manager
description: Znajdź przykłady szablonów Azure Resource Manager do wdrożenia Azure Container Instances w różnych konfiguracjach
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981650"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Szablony Azure Resource Manager dla Azure Container Instances

Następujące przykładowe szablony wdrażają wystąpienia kontenerów w różnych konfiguracjach.

Aby zapoznać się z opcjami wdrażania, zobacz sekcję [wdrażanie](#deployment) . Jeśli chcesz utworzyć własne szablony, Azure Container Instances szablonu [Menedżer zasobów szczegóły odwołania szablonu][ref] i dostępne właściwości.

## <a name="sample-templates"></a>Przykładowe szablony

| | |
|-|-|
| **Aplikacje** ||
| [WordPress][app-wp] | Tworzy witrynę sieci Web WordPress i jej bazę danych MySQL w grupie kontenerów. Zawartość witryny WordPress i baza danych MySQL są utrwalane w udziale Azure Files. Program tworzy również bramę aplikacji, aby udostępnić publiczny dostęp do sieci WordPress. |
| [MS NAV z SQL Server i IIS][app-nav] | Wdraża pojedynczy kontener systemu Windows z w pełni wyróżnionym własnym środowiskiem programu Dynamics NAV/Dynamics 365 Business Central. |
| **Objętości** ||
| [emptyDir][vol-emptydir] | Wdraża dwa kontenery systemu Linux, które współużytkują wolumin emptyDir. |
| [gitRepo][vol-gitrepo] | Wdraża kontener systemu Linux, który klonuje repozytorium GitHub i instaluje go jako wolumin. |
| [wpisu][vol-secret] | Wdraża kontener systemu Linux z certyfikatem PFX zainstalowanym jako wolumin tajny. |
| **Networking** ||
| [Kontener narażony na ruch UDP][net-udp] | Wdraża kontener systemu Windows lub Linux, który uwidacznia port UDP. |
| [Kontener systemu Linux z publicznym adresem IP][net-publicip] | Wdraża pojedynczy kontener systemu Linux dostępny za pośrednictwem publicznego adresu IP. |
| [Wdrażanie grupy kontenerów za pomocą sieci wirtualnej (wersja zapoznawcza)][net-vnet] | Wdraża nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów. |
| **Zasoby platformy Azure** ||
| [Utwórz udział plików i konta usługi Azure Storage][az-files] | Używa interfejsu wiersza polecenia platformy Azure w wystąpieniu kontenera, aby utworzyć konto magazynu i udział Azure Files.

## <a name="deployment"></a>Wdrożenie

Istnieje kilka opcji wdrażania zasobów przy użyciu szablonów Menedżer zasobów:

[Interfejs wiersza polecenia platformy Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[Interfejs API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups

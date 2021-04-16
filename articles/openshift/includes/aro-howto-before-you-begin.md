---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520744"
---
### <a name="create-the-cluster"></a>Tworzenie klastra

Postępuj zgodnie z [samouczkiem, aby utworzyć Azure Red Hat OpenShift klastra.](../tutorial-create-cluster.md) Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Azure Red Hat OpenShift, należy użyć [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html), klienta wiersza polecenia OpenShift.

> [!NOTE]
> Zalecamy zainstalowanie [wiersza polecenia OpenShift](../tutorial-connect-cluster.md) na platformie [Azure Cloud Shell](https://shell.azure.com/) i używanie go dla wszystkich poniższych operacji wiersza polecenia. Uruchom powłokę z shell.azure.com lub klikając link:
>
> [![Uruchamianie osadzane](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com/bash)

Postępuj zgodnie z samouczkiem, aby zainstalować [](../tutorial-connect-cluster.md) interfejs wiersza polecenia, pobrać poświadczenia klastra i nawiązać połączenie z klastrem przy użyciu konsoli sieci Web i interfejsu wiersza polecenia usługi OpenShift.

Po zalogowaniu powinien zostać wyświetlony komunikat informujący, że używasz `default` projektu.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```

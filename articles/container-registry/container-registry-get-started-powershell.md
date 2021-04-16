---
title: Szybki start — tworzenie rejestru — PowerShell
description: Szybka nauka tworzenia rejestru prywatnego platformy Docker za pomocą programu Azure Container Registry PowerShell
ms.date: 01/22/2019
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: bd9b93e22081c43dfa3fd934f13da3713120aadb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537388"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Szybki start: tworzenie prywatnego rejestru kontenerów przy użyciu Azure PowerShell

Usługa Azure Container Registry to zarządzana, prywatna usługa rejestru kontenerów platformy Docker używana do kompilowania, przechowywania i obsługi obrazów kontenerów Docker. Z tego przewodnika Szybki start dowiesz się, jak utworzyć rejestr kontenerów platformy Azure przy użyciu programu PowerShell. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby sprawdzić zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Musisz mieć również zainstalowane lokalnie środowisko Docker. Platforma Docker zawiera pakiety dla systemów [macOS][docker-mac], [Windows][docker-windows] i [Linux][docker-linux].

Ze względu na to, że usługa Azure Cloud Shell nie zawiera wszystkich wymaganych składników platformy Docker (demon `dockerd`), nie można używać usługi Cloud Shell na potrzeby tego przewodnika Szybki start.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount][Connect-AzAccount] i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Po zakończeniu uwierzytelniania na platformie Azure utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup][New-AzResourceGroup]. Grupa zasobów to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tworzenie rejestru kontenerów

Następnie utwórz rejestr kontenerów w nowej grupie zasobów przy użyciu polecenia [New-AzContainerRegistry][New-AzContainerRegistry].

Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie jest tworzony rejestr o nazwie „myContainerRegistry007”. Zastąp wartość *myContainerRegistry007* w poniższym poleceniu, a następnie uruchom je w celu utworzenia rejestru:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług, zobacz Warstwy usług [rejestru kontenerów][container-registry-skus].

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do swojego rejestru. Aby ten przewodnik Szybki start był krótki, włącz administratora w rejestrze za pomocą polecenia [Get-AzContainerRegistryCredential.][Get-AzContainerRegistryCredential] W scenariuszach produkcyjnych należy użyć alternatywnej metody [uwierzytelniania](container-registry-authentication.md) na użytek dostępu do rejestru, takiej jak jednostki usługi. 

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Następnie uruchom polecenie [docker login][docker-login], aby się zalogować:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`.

> [!TIP]
> Interfejs wiersza polecenia platformy Azure udostępnia polecenie , czyli wygodny sposób logowania się do rejestru kontenerów przy użyciu indywidualnej tożsamości `az acr login` bez przekazywania poświadczeń platformy [](container-registry-authentication.md#individual-login-with-azure-ad)Docker.


[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z zasobami utworzonymi w tym przewodniku Szybki start użyj polecenia [Remove-AzResourceGroup][Remove-AzResourceGroup], aby usunąć grupę zasobów, rejestr kontenerów i przechowywane w nim obrazy kontenerów:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w programie Azure PowerShell, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [zadania usługi Azure Container Registry samouczków][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md

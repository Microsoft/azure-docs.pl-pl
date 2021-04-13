---
title: Konfigurowanie środowisk deweloperskich i wdrożeń Bicep
description: Jak skonfigurować Bicep środowiska projektowania i wdrażania
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2c905a3885fcfc5c9eb9d9db4004126882798611
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313559"
---
# <a name="install-bicep-preview"></a>Install Bicep (wersja zapoznawcza)

Dowiedz się, jak skonfigurować Bicep środowiska projektowania i wdrażania.

## <a name="development-environment"></a>Środowisko deweloperskie

Aby korzystać z najlepszego środowiska tworzenia Bicep, potrzebne są dwa składniki:

- **Rozszerzenie Bicep dla Visual Studio Code**. Do tworzenia plików Bicep potrzebny jest dobry edytor Bicep. Zalecamy [Visual Studio Code](https://code.visualstudio.com/) z [rozszerzeniem Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Te narzędzia zapewniają obsługę języka i Autouzupełnianie zasobów. Ułatwiają one tworzenie i weryfikowanie plików Bicep. Aby uzyskać więcej informacji na temat używania Visual Studio Code i rozszerzenia Bicep, zobacz [Szybki Start: Tworzenie plików Bicep z Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **Interfejs wiersza polecenia Bicep**. Użyj interfejsu wiersza polecenia Bicep, aby kompilować pliki Bicep do szablonów w usłudze ARM, i dekompilować szablony JSON programu ARM do plików Bicep. Instrukcje instalacji znajdują się w temacie [Install BICEP CLI](#install-manually).

## <a name="deployment-environment"></a>Środowisko wdrażania

Do wdrożenia lokalnych plików Bicep potrzebne są dwa składniki:

- **Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej lub Azure PowerShell w wersji 5.6.0 lub nowszej**. Instrukcje instalacji znajdują się w temacie:

  - [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
  - [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
  - [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)
  - [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Obecnie zarówno interfejs wiersza polecenia platformy Azure, jak i Azure PowerShell mogą wdrażać tylko lokalne pliki Bicep. Aby uzyskać więcej informacji o wdrażaniu plików Bicep przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Deploy-CLI](./deploy-cli.md#deploy-remote-template). Aby uzyskać więcej informacji o wdrażaniu plików Bicep przy użyciu Azure PowerShell, zobacz [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **Interfejs wiersza polecenia Bicep**. Interfejs wiersza polecenia Bicep jest wymagany do kompilowania plików Bicep do szablonów JSON przed wdrożeniem. Instrukcje instalacji znajdują się w temacie [Install BICEP CLI](#install-bicep-cli).

Po zainstalowaniu składników można wdrożyć plik Bicep z:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Instalowanie interfejsu wiersza polecenia Bicep

- Aby użyć interfejsu wiersza polecenia Bicep do kompilowania i dekompilowania plików Bicep, zobacz [Instalowanie ręczne](#install-manually).
- Aby wdrożyć pliki Bicep za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure](#use-with-azure-cli).
- Aby użyć Azure PowerShell do wdrożenia plików Bicep, zobacz [Używanie z Azure PowerShell](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Używanie z interfejsem wiersza polecenia platformy Azure

Przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej, interfejs wiersza polecenia Bicep jest instalowany automatycznie, gdy wykonywane jest polecenie, które jest od niego zależne. Na przykład:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

lub

```azurecli
az bicep ...
```

Interfejs wiersza polecenia można również zainstalować ręcznie przy użyciu wbudowanych poleceń:

```bash
az bicep install
```

Aby uaktualnić do najnowszej wersji:

```bash
az bicep upgrade
```

Aby zainstalować określoną wersję:

```bash
az bicep install --version v0.3.255
```

> [!IMPORTANT]
> Interfejs wiersza polecenia platformy Azure instaluje oddzielną wersję interfejsu wiersza polecenia Bicep, która nie jest w konflikcie z innymi zainstalowanymi instalacjami Bicep, a interfejs wiersza polecenia platformy Azure nie dodaje interfejsu wiersza polecenia Bicep do ścieżki. Aby użyć interfejsu wiersza polecenia Bicep do kompilowania/dekompilowania plików Bicep lub do wdrażania plików Bicep za pomocą Azure PowerShell, zobacz [Instalowanie ręczne](#install-manually) lub [Korzystanie z programu Azure PowerShell](#use-with-azure-powershell).

Aby wyświetlić listę wszystkich dostępnych wersji interfejsu wiersza polecenia Bicep:

```bash
az bicep list-versions
```

Aby wyświetlić zainstalowane wersje:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Używanie z Azure PowerShell

Azure PowerShell nie ma jeszcze możliwości zainstalowania interfejsu wiersza polecenia Bicep. Azure PowerShell (v 5.6.0 lub nowszy) oczekuje, że interfejs wiersza polecenia Bicep jest już zainstalowany i dostępny w ścieżce. Postępuj zgodnie z jedną z [metod instalacji ręcznej](#install-manually).

Aby wdrożyć pliki Bicep, wymagany jest interfejs wiersza polecenia Bicep w wersji 0.3.1 lub nowszej. Aby sprawdzić wersję interfejsu wiersza polecenia Bicep:

```cmd
bicep --version
```

> [!IMPORTANT]
> Interfejs wiersza polecenia platformy Azure instaluje własną, niezależną wersję interfejsu wiersza polecenia Bicep. Wdrożenie Azure PowerShell nie powiedzie się nawet wtedy, gdy są zainstalowane wymagane wersje interfejsu wiersza polecenia platformy Azure.

Po zainstalowaniu interfejsu wiersza polecenia Bicep interfejs wiersza polecenia Bicep jest wywoływany za każdym razem, gdy jest wymagany dla polecenia cmdlet wdrażania. Na przykład:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Instalowanie ręczne

Poniższe metody umożliwiają zainstalowanie interfejsu wiersza polecenia Bicep i dodanie go do ścieżki.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>za pośrednictwem oprogramowania Homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="macos-manual-install"></a>Ręczna instalacja macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Instalator Windows

Pobierz i uruchom [najnowszy Instalator Windows](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Instalator nie wymaga uprawnień administracyjnych. Po zakończeniu instalacji do ścieżki użytkownika zostanie dodany interfejs wiersza polecenia Bicep. Zamknij i Otwórz ponownie wszystkie otwarte okna powłoki poleceń, aby zmiany ścieżki zaczęły obowiązywać.

##### <a name="chocolatey"></a>Narzędzia Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Ręczne przy użyciu programu PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Zainstaluj nocne kompilacje

Jeśli chcesz wypróbować najnowsze wersje wstępne usługi Bicep przed ich udostępnieniem, zobacz [Instalowanie nocnych kompilacji](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Te kompilacje w wersji wstępnej są znacznie bardziej nieznane.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z [przewodnika Szybki Start Bicep](./quickstart-create-bicep-use-visual-studio-code.md).

---
title: Konfiguracja Bicep środowiska projektowania i wdrażania
description: Jak skonfigurować Bicep środowiska projektowania i wdrażania
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0434c08a4427d2549a9adf4e4ab1e7e73a465ad8
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620134"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Konfigurowanie środowiska deweloperskiego i wdrażania Bicep

Dowiedz się, jak skonfigurować Bicep środowiska deweloperskie i wdrożeniowe.

## <a name="development-environment"></a>Środowisko deweloperskie

Aby korzystać z najlepszego środowiska tworzenia Bicep, potrzebne są dwa składniki:

- **Rozszerzenie Bicep dla Visual Studio Code**. Do tworzenia plików Bicep potrzebny jest dobry edytor Bicep. Zalecamy [Visual Studio Code](https://code.visualstudio.com/) z [rozszerzeniem Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Te narzędzia zapewniają obsługę języka i Autouzupełnianie zasobów. Ułatwiają one tworzenie i weryfikowanie plików Bicep. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie plików Bicep z Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **Interfejs wiersza polecenia Bicep**. Użyj interfejsu wiersza polecenia Bicep, aby kompilować pliki Bicep do szablonów w usłudze ARM, i dekompilować szablony JSON programu ARM do plików Bicep. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia Bicep](#install-bicep-cli).

## <a name="deployment-environment"></a>Środowisko wdrażania

Pliki Bicep można wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. W przypadku interfejsu wiersza polecenia platformy Azure potrzebna jest wersja 2.20.0 lub nowsza; w przypadku Azure PowerShell wymagana jest wersja 5.6.0 lub nowsza. Instrukcje instalacji znajdują się w temacie:

- [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie macOS](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> Obecnie zarówno interfejs wiersza polecenia platformy Azure, jak i Azure PowerShell mogą wdrażać tylko lokalne pliki Bicep. Aby uzyskać więcej informacji o wdrażaniu plików Bicep przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Deploy-CLI](/deploy-cli.md#deploy-remote-template). Aby uzyskać więcej informacji o wdrażaniu plików Bicep przy użyciu Azure PowerShell, zobacz [Deploy-PowerShell](/deploy-powershell.md#deploy-remote-template).

Po zainstalowaniu obsługiwanej wersji Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można wdrożyć plik Bicep z:

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

Interfejs wiersza polecenia Bicep można zainstalować za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu Azure PowerShell lub ręcznie.

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Przy użyciu polecenia AZ CLI w wersji 2.20.0 lub nowszej, interfejs CLI Bicep jest automatycznie instalowany, gdy wykonywane jest polecenie, które jest od niego zależne. Na przykład: `az deployment ... -f *.bicep` lub `az bicep ...`.

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
az bicep install --version v0.2.212
```

> [!NOTE]
> AZ CLI instaluje oddzielną wersję interfejsu wiersza polecenia Bicep, która nie jest w konflikcie z innymi zainstalowanymi instalacjami Bicep, i AZ CLI nie dodaje Bicep do ścieżki.

Aby wyświetlić zainstalowane wersje:

```bash
az bicep version
```

Aby wyświetlić listę wszystkich dostępnych wersji interfejsu wiersza polecenia Bicep:

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Azure PowerShell nie ma jeszcze możliwości zainstalowania interfejsu wiersza polecenia Bicep. Azure PowerShell (v 5.6.0 lub nowszy) oczekuje, że interfejs wiersza polecenia Bicep jest już zainstalowany i dostępny w ścieżce. Postępuj zgodnie z jedną z [metod instalacji ręcznej](#install-manually). Po zainstalowaniu interfejsu wiersza polecenia Bicep interfejs wiersza polecenia Bicep jest wywoływany za każdym razem, gdy jest wymagany dla polecenia cmdlet wdrażania. Na przykład `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`.

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
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
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

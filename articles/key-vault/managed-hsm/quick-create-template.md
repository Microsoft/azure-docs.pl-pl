---
title: Przewodnik Szybki start platformy Azure — tworzenie zarządzanego modułu HSM przy użyciu Azure Resource Manager szablonu
description: Przewodnik Szybki start przedstawiający sposób tworzenia zarządzanego modułu HSM Azure Key Vault Azure przy użyciu Resource Manager szablonu
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 33c262c61d50b45663a627e40ea186f1f0dcde41
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532966"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Szybki start: tworzenie zarządzanego Key Vault HSM przy użyciu Azure Resource Manager szablonu

Zarządzany moduł HSM to w pełni zarządzana, wysoko dostępna, zgodna ze standardami usługa w chmurze z jedną dzierżawą, która umożliwia ochronę kluczy kryptograficznych dla aplikacji w chmurze przy użyciu modułów HSM zweryfikowanych w trybie **FIPS 140-2 poziom 3.**  

Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu Resource Manager w celu utworzenia zarządzanego modułu HSM.  [Szablon usługi Resource Manager](../../azure-resource-manager/templates/overview.md) to plik w formacie JavaScript Object Notation (JSON) definiujący infrastrukturę i konfigurację projektu. Szablon używa składni deklaratywnej, która pozwala określić, co zamierzasz wdrożyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Aby dowiedzieć się więcej na temat opracowywania szablonów usługi Resource Manager, zobacz [dokumentację usługi Resource Manager](../../azure-resource-manager/index.yml) i [dokumentację szablonu](/azure/templates/microsoft.keyvault/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć następujące elementy:

- Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
- Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy [Azure, zobacz Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, [zobacz Logowanie się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Tworzenie modułu HSM zarządzania

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

Zasób platformy Azure zdefiniowany w szablonie:

* **Microsoft.KeyVault/managedHSMs:** utwórz Azure Key Vault HSM.

Więcej Azure Key Vault przykładów szablonów można znaleźć [tutaj.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)

Szablon wymaga identyfikatora obiektu skojarzonego z Twoim kontem. Aby ją znaleźć, użyj polecenia [az ad user show](/cli/azure/ad/user#az_ad_user_show) interfejsu wiersza polecenia platformy Azure, przekazując swój adres e-mail do `--id` parametru . Dane wyjściowe można ograniczyć do identyfikatora obiektu tylko za pomocą `--query` parametru .

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Może być również potrzebny identyfikator dzierżawy. Aby ją znaleźć, użyj polecenia [az ad user show interfejsu wiersza polecenia platformy](/cli/azure/account#az_account_show) Azure. Dane wyjściowe można ograniczyć do identyfikatora dzierżawy tylko za pomocą `--query` parametru .

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.

    Jeśli nie zostanie określony, użyj wartości domyślnej, aby utworzyć magazyn kluczy i klucz tajny.

    - **Subskrypcja:** wybierz subskrypcję platformy Azure.
    - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK.**
    - **Lokalizacja:** wybierz lokalizację. Na przykład **Południowo-środkowe usa**.
    - **managedHSMName:** wprowadź nazwę zarządzanego modułu HSM.
    - **Identyfikator dzierżawy:** funkcja szablonu automatycznie pobiera identyfikator dzierżawy; nie zmieniaj wartości domyślnej.  Jeśli nie ma żadnej wartości, wprowadź identyfikator dzierżawy pobrany w [wymaganiach wstępnych.](#prerequisites)
    * **initialAdminObjectIds:** wprowadź identyfikator obiektu pobrany w te [tematu Prerequisites (Wymagania wstępne).](#prerequisites)

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu magazynu kluczy otrzymasz powiadomienie:

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz interfejsu Azure Portal można również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono zarządzany moduł HSM. Ten zarządzany moduł HSM nie będzie w pełni funkcjonalny, dopóki nie zostanie aktywowany. Zobacz [Aktywowanie zarządzanego modułu HSM,](quick-create-cli.md#activate-your-managed-hsm) aby dowiedzieć się, jak aktywować moduł HSM.

- Przeczytaj omówienie [zarządzanego modułu HSM](overview.md)
- Dowiedz się więcej [o zarządzaniu kluczami w zarządzanym modułze HSM](key-management.md)
- Zapoznaj się [z najlepszymi rozwiązaniami w zakresie zarządzanego modułu HSM](best-practices.md)

---
title: Tworzenie Azure Blockchain Service przy użyciu Azure Resource Manager szablonu
description: Dowiedz się, jak utworzyć Azure Blockchain Service przy użyciu Azure Resource Manager szablonu.
services: azure-resource-manager
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: c49e45ae84d58d62460d493887e2a4e78e40ba32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536290"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Szybki start: tworzenie Azure Blockchain Service przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start wdrożysz nowego członka łańcucha bloków i konsorcjum w grupie Azure Blockchain Service użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM). Członek Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnej konsorcjum. Podczas aprowizowania członka możesz utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba elementów członkowskich łańcucha bloków wymaganych przez uczestników zależy od scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą udostępniać członków innym uczestnikom. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Zasoby platformy Azure zdefiniowane w szablonie:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy link, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Określ ustawienia dla Azure Blockchain Service członkowskiego.

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla swojej usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą nazwę z subskrypcji.
    Region (Region) | Wybierz region, aby utworzyć grupę zasobów. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji. Dostępne lokalizacje wdrożenia to *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* i *japaneast.* Funkcje mogą nie być dostępne w niektórych regionach. Azure Blockchain Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
    Bc, nazwa członka | Wybierz unikatową nazwę dla Azure Blockchain Service członkowskiego. Nazwa członka łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi mieć od 2 do 20 znaków.
    Nazwa konsorcjum | Wprowadź unikatową nazwę. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).
    Hasło członka | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji członka łańcucha bloków.
    Hasło konta zarządzania konsorcjum | Hasło do konta konsorcjum jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Jest on używany do zarządzania konsorcjum.
    Warstwa SKU | Warstwa cenowa nowej usługi. Wybierz warstwę **Standardowa** **i** Podstawowa. Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy *Standardowa* dla wdrożeń klasy produkcyjnej. Użyj również warstwy *Standardowa,* jeśli używasz Blockchain Data Manager lub wysyłasz dużą ilość transakcji prywatnych. Zmiana warstwy cenowej między podstawową i standardową po utworzeniu członka nie jest obsługiwana.
    Nazwa SKU | Konfiguracja węzła i koszt nowej usługi. Użyj **B0 dla** wersji Podstawowa i **S0** dla wersji Standardowa.
    Lokalizacja | Wybierz lokalizację, w którym chcesz utworzyć członka. Domyślnie używana jest lokalizacja grupy zasobów `[resourceGroup().location]` . Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji. Dostępne lokalizacje wdrożenia to *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* i *japaneast.* Funkcje mogą nie być dostępne w niektórych regionach. Azure Blockchain Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.

1. Wybierz **pozycję Przeglądanie + tworzenie,** aby zweryfikować i wdrożyć szablon.

  Ten Azure Portal jest używany w tym miejscu do wdrożenia szablonu. Możesz również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Możesz użyć tego Azure Portal, aby wyświetlić szczegóły wdrożonego Azure Blockchain Service członkowskiego. W portalu przejdź do grupy zasobów zawierającej twój Azure Blockchain Service członkowski. Wybierz utworzony przez Ciebie członek łańcucha bloków.

![Szczegóły Azure Blockchain członek zespołu wdrożonego w Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć członka łańcucha bloków utworzonego w następnym przewodniku Szybki start lub samouczku. Gdy zasoby nie będą już potrzebne, możesz je usunąć, usuwając grupę zasobów utworzoną na potrzeby tego przewodnika Szybki start.

Aby usunąć grupę zasobów:

1. W okienku Azure Portal w  okienku nawigacji po lewej stronie przejdź do grupy zasobów i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono Azure Blockchain Service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby użyć Azure Blockchain Development Kit for Ethereum w celu dołączenia do Azure Blockchain Service członkowskiego.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia Visual Studio Code za pomocą Azure Blockchain Service](connect-vscode.md)

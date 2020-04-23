---
title: Tworzenie członka usługi Azure Blockchain przy użyciu szablonu usługi Azure Resource Manager
description: Dowiedz się, jak utworzyć członka usługi Azure Blockchain przy użyciu szablonu usługi Azure Resource Manager.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087597"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Tworzenie członka usługi Azure Blockchain przy użyciu szablonu usługi Azure Resource Manager

W tym przewodniku Szybki start można wdrożyć nowego członka łańcucha bloków i konsorcjum w usłudze Azure Blockchain przy użyciu szablonu Usługi Azure Resource Manager.

Członek usługi Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnego konsorcjum. Podczas inicjowania obsługi administracyjnej członka, można utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba członków łańcucha bloków potrzebnych uczestnikom zależy od scenariusza. Uczestnicy konsorcjum mogą mieć jednego lub więcej członków łańcucha bloków lub mogą dzielić się członkami z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/).

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Zasoby platformy Azure zdefiniowane w szablonie:

* [**Microsoft.Blockchain/blockchainCzłonek**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz następujące łącze, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Określ ustawienia dla członka usługi Azure Blockchain.

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla swojej usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą z subskrypcji.
    Lokalizacja | Wybierz lokalizację, aby utworzyć element członkowski. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji. Dostępne lokalizacje dla rozmieszczenia to *WestEurope, eastus, southeastasia, westeurope, northeurope, westus2*i *japaneast*.
    Nazwa elementu członkowskiego | Wybierz unikatową nazwę dla członka usługi Azure Blockchain Service. Nazwa członka łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi zawierać się o długości od 2 do 20 znaków.
    Nazwa konsorcjum | Wprowadź unikatową nazwę. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).
    Hasło członka | Hasło konta członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum, który jest tworzony dla twojego członka. Do zarządzania konsorcjum używasz konta członkowskiego i hasła do konta członkowskiego.
    Poziom Sku | Warstwa cenowa dla nowej usługi. Wybierz **warstwę Standardowa** i **Podstawowa.** Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji pojęć. Użyj warstwy *Standardowa* dla wdrożeń na poziomie produkcyjnym. Należy również użyć *warstwy Standardowa,* jeśli używasz Menedżera danych Blockchain lub wysyłasz dużą liczbę transakcji prywatnych. Zmiana warstwy cenowej między podstawową a standardową po utworzeniu elementu członkowskiego nie jest obsługiwana.
    Nazwa Sku | Konfiguracja węzła i koszt nowej usługi.

1. Wybierz **pozycję Zakup,** aby wdrożyć szablon.

  Witryna Azure portal jest używana w tym miejscu do wdrażania szablonu. Można również użyć interfejsu API programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i rest. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz użyć członka łańcucha bloków utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy nie są już potrzebne, można usunąć zasoby, usuwając grupę zasobów utworzoną dla szybkiego startu.

Aby usunąć grupę zasobów:

1. W witrynie Azure Portal przejdź do **grupy zasobów** w lewym okienku nawigacji i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono członka usługi Azure Blockchain service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby dołączyć do członka usługi Azure Blockchain Service za pomocą zestawu Azure Blockchain Development Kit for Ethereum.

> [!div class="nextstepaction"]
> [Łączenie się z usługą Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)
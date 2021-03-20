---
title: Wdróż klaster zarządzany Service Fabric (wersja zapoznawcza) przy użyciu Azure Resource Manager
description: Dowiedz się, jak utworzyć klaster zarządzany Service Fabric przy użyciu szablonu Azure Resource Manager
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91410507"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Szybki Start: Wdrażanie klastra zarządzanego w Service Fabric (wersja zapoznawcza) przy użyciu szablonu Azure Resource Manager

Service Fabric zarządzanych klastrów to ewolucja modelu zasobów klastra usługi Azure Service Fabric, który usprawnia środowisko zarządzania wdrożeniami i klastrami. Service Fabric klastrami zarządzanymi są w pełni hermetyzowane zasoby, które umożliwiają wdrożenie pojedynczego Service Fabric zasobu klastra zamiast wdrażania wszystkich zasobów źródłowych, które tworzą klaster Service Fabric. W tym artykule opisano sposób wdrażania Service Fabric zarządzanego klastra na potrzeby testowania na platformie Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).

Podstawowy klaster SKU z trzema węzłami wdrożony w tym samouczku jest przeznaczony tylko do celów informacyjnych (a nie obciążeń produkcyjnych). Aby uzyskać więcej informacji, zobacz  [Service Fabric zarządzanych jednostek SKU klastra](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym przewodnikiem Szybki Start:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [przykładów platformy Azure — Service Fabric szablonów klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Utwórz certyfikat klienta

Service Fabric klastry zarządzane używają certyfikatu klienta jako klucza do kontroli dostępu. Jeśli masz już certyfikat klienta, którego chcesz użyć na potrzeby kontroli dostępu do klastra, możesz pominąć ten krok.

Jeśli musisz utworzyć nowy certyfikat klienta, wykonaj kroki opisane w temacie [Ustawianie i pobieranie certyfikatu z Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Zanotuj odcisk palca certyfikatu, ponieważ będzie to wymagane do wdrożenia szablonu w następnym kroku.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

      [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości

    W tym przewodniku szybki start Podaj własne wartości dla następujących parametrów szablonu:

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**. Wprowadź unikatową nazwę grupy zasobów, *na przykład grupa zasobów, a* następnie wybierz przycisk **OK**.
    * **Lokalizacja**: Wybierz lokalizację, na przykład **eastus2**. Obsługiwane regiony dla Service Fabric klastrów zarządzanych w wersji zapoznawczej obejmują,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` i `eastus2` .
    * **Nazwa klastra**: Wprowadź unikatową nazwę klastra, na przykład *mysfcluster*.
    * **Nazwa użytkownika administratora**: Wprowadź nazwę administratora, który ma być używany na potrzeby protokołu RDP na podstawowych maszynach wirtualnych w klastrze.
    * **Hasło administratora**: wprowadź hasło administratora, które ma być używane na potrzeby protokołu RDP na podstawowych maszynach wirtualnych w klastrze.
    * **Odcisk palca certyfikatu klienta**: Podaj odcisk palca certyfikatu klienta, który ma być używany w celu uzyskania dostępu do klastra. Jeśli nie masz certyfikatu, postępuj zgodnie z [zestawem i pobieraniem certyfikatu](../key-vault/certificates/quick-create-portal.md) w celu utworzenia certyfikatu z podpisem własnym.
    * **Nazwa typu węzła**: Wprowadź unikatową nazwę typu węzła, na przykład *NT1*.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: zaznacz to pole wyboru, aby wyrazić zgodę. 

3. Wybierz pozycję **Kup**.

4. Wdrożenie zarządzanego klastra Service Fabric trwa kilka minut. Przed przejściem do następnych kroków poczekaj na pomyślne ukończenie wdrożenia.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrożenia Znajdź wartość Service Fabric Explorer w danych wyjściowych i Otwórz adres w przeglądarce sieci Web, aby wyświetlić swój klaster w Service Fabric Explorer. Po wyświetleniu monitu o certyfikat Użyj certyfikatu, dla którego odcisk palca klienta został podany w szablonie.

> [!NOTE]
> Dane wyjściowe wdrożenia można znaleźć w witrynie Azure Portal na karcie wdrożenia grupy zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów dla klastra zarządzanego Service Fabric nie jest już konieczna, usuń ją. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu *wyszukiwania* w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono zarządzany klaster Service Fabric. Aby dowiedzieć się więcej na temat skalowania klastra, zobacz:

> [!div class="nextstepaction"]
> [Skalowanie w poziomie Service Fabric klastra zarządzanego](tutorial-managed-cluster-scale.md)

---
title: Tworzenie kont integracji B2B i zarządzanie nimi
description: Tworzenie i łączenie kont integracji oraz zarządzanie nimi w celu integracji przedsiębiorstw z Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 9a7a0795a15de52c34b8591c4224c3ca5883445c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772077"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Tworzenie kont integracji na potrzeby integracji przedsiębiorstw B2B i zarządzanie nimi w usłudze Azure Logic Apps

Zanim będzie możliwe utworzenie [rozwiązań integracji dla przedsiębiorstw i B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) przy użyciu usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md), należy utworzyć konto integracji, czyli osobny zasób platformy Azure, który zapewnia bezpieczny, skalowalny i możliwy do zarządzania kontener na artefakty integracji, które są definiowane i używane z przepływami pracy aplikacji logiki.

Na przykład możesz tworzyć i przechowywać artefakty B2B, takie jak partnerzy handlowi, umowy, mapy, schematy, certyfikaty i konfiguracje partii, oraz zarządzać nimi. Ponadto przed rozpoczęciem pracy aplikacji logiki z tymi artefaktami i użyciem łączników Logic Apps B2B należy połączyć konto integracji [z](#link-account) aplikacją logiki. Zarówno konto integracji, jak i aplikacja logiki muszą istnieć w tej *samej* lokalizacji lub regionie.

> [!IMPORTANT]
> W zależności od wybranego typu konta integracji utworzenie konta integracji wiąże się z kosztami. Aby uzyskać więcej informacji, [zobacz Logic Apps cennika](logic-apps-pricing.md#integration-accounts) i rozliczeń oraz [Logic Apps cennika.](https://azure.microsoft.com/pricing/details/logic-apps/)

W tym temacie przedstawiono sposób wykonywania tych zadań:

* Utwórz konto integracji.

  > [!TIP]
  > Aby utworzyć konto integracji w środowisku [usługi integracji,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)zobacz [Tworzenie kont integracji w środowisku ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

* Połącz konto integracji z aplikacją logiki.

* Zmień warstwę cenową dla konta integracji.

* Odłącz konto integracji od aplikacji logiki.

* Przenieś konto integracji do innej grupy zasobów lub subskrypcji platformy Azure.

* Usuń konto integracji.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Tworzenie konta integracji

### <a name="portal"></a>[Portal](#tab/azure-portal)

W tym zadaniu możesz użyć interfejsu wiersza Azure Portal, korzystając z procedury opisanej w tej [sekcji,](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)Azure PowerShell lub interfejsu wiersza [polecenia platformy Azure.](/cli/azure/resource#az_resource_create)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź "konto integracji" jako filtr, a następnie wybierz pozycję **Konto integracji**.

   ![Tworzenie nowego konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. W **obszarze Konto integracji** wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Dodaj", aby utworzyć konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Podaj te informacje o koncie integracji:

   ![Podaj szczegóły konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*nazwa konta integracji*> | Nazwa konta integracji, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( , ) i `(` `)` kropki ( `.` ). W tym przykładzie użyto nazwy "Fabrikam-Integration". |
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | Tak | <*Nazwa grupy zasobów platformy Azure*> | Nazwa grupy zasobów [platformy Azure do organizowania](../azure-resource-manager/management/overview.md) powiązanych zasobów. W tym przykładzie utwórz nową grupę zasobów o nazwie "FabrikamIntegration-RG". |
   | **Warstwa cenowa** | Tak | <*poziom cen*> | Warstwa cenowa konta integracji, którą można później zmienić. W tym przykładzie wybierz pozycję **Bezpłatna.** Więcej informacji można znaleźć w następujących tematach: <p>- [Logic Apps cen](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps cen](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Lokalizacja** | Tak | <*Region świadczenia usługi Azure*> | Region, w którym mają być przechowywane metadane konta integracji. Wybierz tę samą lokalizację co aplikacja logiki lub utwórz aplikacje logiki w tej samej lokalizacji co konto integracji. W tym przykładzie użyj "Zachodnie stany USA". <p>**Uwaga:** Aby utworzyć konto integracji w środowisku usługi integracji [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wybierz to środowisko ISE jako lokalizację. Aby uzyskać więcej informacji, zobacz Create integration accounts in an ISE (Tworzenie kont [integracji w programie ISE).](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Log Analytics** | Nie | Wyłączone, Wł. | W tym **przykładzie** zachowaj ustawienie Wyłączone. |
   |||||

1. Po zakończeniu wybierz pozycję **Utwórz.**

   Po zakończeniu wdrażania platforma Azure otworzy Twoje konto integracji.

   ![Platforma Azure otwiera konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Zanim aplikacja logiki będzie w stanie korzystać z twojego konta integracji, wykonaj następne kroki, aby połączyć konto integracji i aplikację logiki.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Konto integracji można utworzyć przy użyciu poleceń interfejsu wiersza polecenia platformy Azure w tej sekcji.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Tworzenie konta integracji

Użyj tych poleceń, aby utworzyć konto integracji.

1. Aby dodać [rozszerzenie az logic integration-account,](/cli/azure/ext/logic/logic/integration-account) użyj polecenia az extension [add:](/cli/azure/extension#az_extension_add)

   ```azurecli
   az extension add –-name logic
   ```

1. Aby utworzyć grupę zasobów lub użyć istniejącej grupy zasobów, uruchom [polecenie az group create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Aby wyświetlić listę kont integracji dla grupy zasobów, użyj [polecenia az logic integration-account list:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list)

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Aby utworzyć konto integracji, uruchom [polecenie az logic integration-account create:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create)

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Nazwa konta integracji może zawierać tylko litery, cyfry, łączniki (-), podkreślenia (_), nawiasy ((, )) i kropki (.).

   > [!TIP]
   > Aby utworzyć konto integracji w środowisku usługi integracji [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wybierz to środowisko ISE jako lokalizację. Aby uzyskać więcej informacji, zobacz Create integration accounts in an ISE (Tworzenie kont [integracji w programie ISE).](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

   Aby wyświetlić określone konto integracji, użyj [polecenia az logic integration-account show:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Możesz zmienić swoją sku lub warstwę cenową za pomocą [polecenia az logic integration-account update:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update)

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Aby uzyskać więcej informacji o cenach, zobacz następujące zasoby:

   * [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps cen](https://azure.microsoft.com/pricing/details/logic-apps/)

Aby zaimportować konto integracji przy użyciu pliku JSON, użyj [polecenia az logic integration-account import:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import)

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Konto integracji można usunąć za pomocą [polecenia az logic integration-account delete:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete)

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Zanim aplikacja logiki będzie korzystać z konta integracji, połącz konto integracji i aplikację logiki. W następnej sekcji opisano łączenie.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Łączenie z aplikacją logiki

Aby zapewnić aplikacjom logiki dostęp do konta integracji zawierającego artefakty B2B, należy najpierw połączyć konto integracji z aplikacją logiki. Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tym samym regionie. Aby wykonać to zadanie, możesz użyć Azure Portal. Jeśli używasz usługi Visual Studio, a aplikacja logiki znajduje się w projekcie grupy zasobów platformy [Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)możesz połączyć aplikację logiki z kontem integracji przy użyciu [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. W Azure Portal znajdź i otwórz aplikację logiki.

1. W [Azure Portal](https://portal.azure.com)otwórz istniejącą aplikację logiki lub utwórz nową aplikację logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Ustawienia przepływu pracy.** W **obszarze Konto integracji** otwórz **listę Wybierz konto** integracji. Wybierz konto integracji, aby połączyć się z aplikacją logiki.

   ![Wybieranie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Aby zakończyć łączenie, wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający, gdzie wybrać pozycję Zapisz, aby wybrać konto integracji.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po pomyślnym nawiązyniu linku do konta integracji na platformie Azure zostanie wyświetlony komunikat z potwierdzeniem.

   ![Platforma Azure potwierdza pomyślne połączenie](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Teraz aplikacja logiki może używać artefaktów na koncie integracji oraz łączników B2B, takich jak walidacja XML i kodowanie plików flat lub dekodowanie.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

Aby zwiększyć [limity](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) dla konta integracji, możesz uaktualnić do [wyższej warstwy](#upgrade-pricing-tier)cenowej , jeśli jest dostępna. Na przykład możesz uaktualnić warstwę Bezpłatna do warstwy Podstawowa lub Standardowa. Możesz również przejść [na niższą warstwę](#downgrade-pricing-tier), jeśli jest dostępna. Aby uzyskać więcej informacji o cenach, zobacz następujące tematy:

* [Logic Apps cenowe](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Uaktualnianie warstwy cenowej

Aby wprowadzić tę zmianę, możesz użyć interfejsu wiersza Azure Portal platformy Azure.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure wyświetla wszystkie konta integracji w twoich subskrypcjach platformy Azure.

1. W **obszarze Konta** integracji wybierz konto integracji, które chcesz przenieść. W menu konta integracji wybierz pozycję **Przegląd.**

   ![W menu konta integracji wybierz pozycję "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **Uaktualnij warstwę cenową,** która zawiera listę wszystkich dostępnych wyższych warstw. Po wybraniu warstwy zmiana zostanie natychmiast w efekcie wywłaszczeniowa.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Jeśli jeszcze tego nie zrobiono, zainstaluj wymagania wstępne interfejsu wiersza [polecenia platformy Azure.](/cli/azure/get-started-with-azure-cli)

1. W Azure Portal otwórz środowisko [Azure Cloud Shell.](../cloud-shell/overview.md)

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź [ **polecenie az resource**](/cli/azure/resource#az_resource_update)i ustaw na `skuName` wyższą warstwę, której potrzebujesz.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Jeśli na przykład masz warstwę Podstawowa, możesz ustawić `skuName` wartość `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Obniżanie warstwy cenowej

Aby wprowadzić tę zmianę, użyj interfejsu wiersza [polecenia platformy Azure.](/cli/azure/get-started-with-azure-cli)

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj wymagania wstępne interfejsu](/cli/azure/get-started-with-azure-cli)wiersza polecenia platformy Azure.

1. W Azure Portal otwórz [Azure Cloud Shell](../cloud-shell/overview.md) środowisko.

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź [ **polecenie az resource** i](/cli/azure/resource#az_resource_update) ustaw niższą `skuName` warstwę, której potrzebujesz.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Jeśli na przykład masz warstwę Standardowa, możesz ustawić `skuName` wartość `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Odłączanie od aplikacji logiki

Jeśli chcesz połączyć aplikację logiki z innym kontem integracji lub nie używać już konta integracji z aplikacją logiki, usuń ten link przy użyciu Azure Resource Explorer.

1. Otwórz okno przeglądarki i przejdź do [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Zaloguj się przy użyciu tych samych poświadczeń konta platformy Azure.

   ![Eksplorator zasobów Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. W polu wyszukiwania wprowadź nazwę aplikacji logiki, aby znaleźć i wybrać aplikację logiki.

   ![Znajdowanie i wybieranie aplikacji logiki](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na pasku tytułu eksploratora wybierz pozycję **Odczyt/Zapis.**

   ![Włączanie trybu "Odczyt/zapis"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na karcie **Dane** wybierz pozycję **Edytuj**.

   ![Na karcie "Dane" wybierz pozycję "Edytuj"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. W edytorze znajdź obiekt i usuń tę `integrationAccount` właściwość, która ma ten format:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Na przykład:

   ![Znajdowanie obiektu "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na karcie **Dane** wybierz pozycję **Umieść,** aby zapisać zmiany.

   ![Aby zapisać zmiany, wybierz pozycję "Umieść"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. W Azure Portal znajdź i wybierz swoją aplikację logiki. W obszarze ustawień **przepływu pracy aplikacji** sprawdź, czy właściwość **Konto** integracji jest teraz pusta.

   ![Sprawdź, czy konto integracji nie jest połączone](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Przenoszenie konta integracji

Konto integracji można przenieść do innej grupy zasobów platformy Azure lub subskrypcji platformy Azure. Podczas przenoszenia zasobów platforma Azure tworzy nowe identyfikatory zasobów, dlatego należy użyć nowych identyfikatorów i zaktualizować wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami. Jeśli chcesz zmienić subskrypcję, musisz również określić istniejącą lub nową grupę zasobów.

W tym zadaniu możesz użyć interfejsu wiersza polecenia Azure Portal, korzystając z procedury opisanej w tej sekcji lub interfejsu wiersza [polecenia platformy Azure.](/cli/azure/resource#az_resource_move)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure wyświetla wszystkie konta integracji w twoich subskrypcjach platformy Azure.

1. W **obszarze Konta** integracji wybierz konto integracji, które chcesz przenieść. W menu konta integracji wybierz pozycję **Przegląd.**

   ![W menu konta integracji wybierz pozycję "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Obok opcji **Grupa zasobów** lub Nazwa **subskrypcji** wybierz pozycję zmień **.**

   ![Zmienianie grupy zasobów lub subskrypcji](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Wybierz wszystkie powiązane zasoby, które również chcesz przenieść.

1. W zależności od wybranej opcji wykonaj następujące kroki, aby zmienić grupę zasobów lub subskrypcję:

   * Grupa zasobów: z **listy Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów.**

   * Subskrypcja: z **listy Subskrypcja** wybierz subskrypcję docelową. Z **listy Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów.**

1. Aby potwierdzić, że wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizujesz ich przy użyciu nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz przycisk **OK.**

1. Po zakończeniu upewnij się, że wszystkie skrypty są aktualizowane przy użyciu nowych identyfikatorów zasobów dla przeniesionych zasobów.  

## <a name="delete-integration-account"></a>Usuwanie konta integracji

W tym zadaniu możesz użyć interfejsu Azure Portal, korzystając z procedury opisanej w tej sekcji, interfejsu wiersza polecenia platformy [Azure](/cli/azure/resource#az_resource_delete) [lub Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure wyświetla wszystkie konta integracji w twoich subskrypcjach platformy Azure.

1. W **obszarze Konta** integracji wybierz konto integracji, które chcesz usunąć. W menu konta integracji wybierz pozycję **Przegląd.**

   ![W menu konta integracji wybierz pozycję "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **Usuń**.

   ![W okienku "Przegląd" wybierz pozycję "Usuń"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Aby potwierdzić, że chcesz usunąć konto integracji, wybierz pozycję **Tak.**

   ![Aby potwierdzić usunięcie, wybierz pozycję "Tak"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych na koncie integracji](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów między partnerami na koncie integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)

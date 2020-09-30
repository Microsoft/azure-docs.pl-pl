---
title: Tworzenie kont integracji B2B lub zarządzanie nimi
description: Tworzenie i łączenie kont integracji dla integracji przedsiębiorstwa oraz zarządzanie nimi za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 2007e2dd2df959698e62ac753689556c2f17c005
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570265"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Tworzenie kont integracji na potrzeby integracji przedsiębiorstw B2B i zarządzanie nimi w usłudze Azure Logic Apps

Zanim będzie możliwe utworzenie [rozwiązań integracji dla przedsiębiorstw i B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) przy użyciu usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md), należy utworzyć konto integracji, czyli osobny zasób platformy Azure, który zapewnia bezpieczny, skalowalny i możliwy do zarządzania kontener na artefakty integracji, które są definiowane i używane z przepływami pracy aplikacji logiki.

Można na przykład tworzyć, przechowywać i zarządzać artefaktami B2B, takimi jak partnerzy handlowi, umowy, mapy, schematy, certyfikaty i konfiguracje usługi Batch. Ponadto przed rozpoczęciem pracy aplikacji logiki z tymi artefaktami i użyciem łączników Logic Apps B2B należy [połączyć konto integracji](#link-account) z aplikacją logiki. Zarówno konto integracji, jak i aplikacja logiki muszą znajdować się w *tej samej* lokalizacji lub regionie.

> [!TIP]
> Aby utworzyć konto integracji w [środowisku usługi integracji](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), zobacz [Tworzenie kont integracji w ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

W tym temacie przedstawiono sposób wykonywania następujących zadań:

* Utwórz konto integracji.
* Połącz swoje konto integracji z aplikacją logiki.
* Zmień warstwę cenową dla konta integracji.
* Odłączanie konta integracji od aplikacji logiki.
* Przenieś swoje konto integracji do innej grupy zasobów lub subskrypcji platformy Azure.
* Usuń konto integracji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Tworzenie konta integracji

W przypadku tego zadania można użyć Azure Portal, wykonując kroki opisane w tej sekcji, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)lub [interfejsie wiersza polecenia platformy Azure](/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź ciąg "konto integracji" jako filtr, a następnie wybierz pozycję **konto integracji**.

   ![Utwórz nowe konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. W obszarze **konto integracji**wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Dodaj", aby utworzyć konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Podaj te informacje o Twoim koncie integracji:

   ![Podaj szczegóły konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*Integracja — nazwa konta*> | Nazwa konta integracji, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie zastosowano "Fabrikam-Integration". |
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-Name*> | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) , która ma być używana do organizowania powiązanych zasobów. W tym przykładzie Utwórz nową grupę zasobów o nazwie "FabrikamIntegration-RG". |
   | **Warstwa cenowa** | Tak | <*Cennik — poziom*> | Warstwa cenowa konta integracji, którą można później zmienić. Na potrzeby tego przykładu wybierz pozycję **Free**. Więcej informacji można znaleźć w następujących tematach: <p>- [Logic Apps model cenowy](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Cennik Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Lokalizacja** | Tak | <*Platforma Azure — region*> | Region, w którym przechowywane są metadane konta integracji. Wybierz tę samą lokalizację, w której znajduje się aplikacja logiki, lub Utwórz Aplikacje logiki w tej samej lokalizacji co konto integracji. Na potrzeby tego przykładu Użyj "zachodnie stany USA". <p>**Uwaga**: Aby utworzyć konto integracji w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wybierz tę ISE jako lokalizację. Aby uzyskać więcej informacji, zobacz [Tworzenie kont integracji w ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Nie | Wyłączone, włączone | Pozostaw ustawienie **off** dla tego przykładu. |
   |||||

1. Po zakończeniu wybierz pozycję **Utwórz**.

   Po zakończeniu wdrożenia na platformie Azure zostanie otwarte konto integracji.

   ![Na platformie Azure zostanie otwarte konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Aby aplikacja logiki mogła korzystać z konta integracji, wykonaj następujące kroki, aby połączyć konto integracji i aplikację logiki ze sobą.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Łączenie z aplikacją logiki

Aby umożliwić aplikacjom logiki dostęp do konta integracji zawierającego artefakty B2B, musisz najpierw połączyć konto integracji z aplikacją logiki. Zarówno aplikacja logiki, jak i konto integracji muszą znajdować się w tym samym regionie. Aby wykonać to zadanie, można użyć Azure Portal. Jeśli używasz programu Visual Studio, a aplikacja logiki znajduje się w [projekcie grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), możesz [połączyć aplikację logiki z kontem integracji przy użyciu programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. W Azure Portal Znajdź i Otwórz aplikację logiki.

1. W [Azure Portal](https://portal.azure.com)Otwórz istniejącą aplikację logiki lub Utwórz nową aplikację logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**. W obszarze **konto integracji**Otwórz listę **Wybierz konto integracji** . Wybierz konto integracji, aby połączyć się z aplikacją logiki.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Aby zakończyć łączenie, wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący, gdzie wybrać opcję Zapisz, aby wybrać konto integracji.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po pomyślnym połączeniu konta integracji na platformie Azure zostanie wyświetlony komunikat z potwierdzeniem.

   ![Na platformie Azure potwierdzenie pomyślnego linku](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Teraz aplikacja logiki może używać artefaktów na koncie integracji oraz łączników B2B, takich jak sprawdzanie poprawności kodu XML i zwykłe Kodowanie plików lub dekodowanie.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

Aby zwiększyć [limity](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) dla konta integracji, można [przeprowadzić uaktualnienie do wyższej warstwy cenowej](#upgrade-pricing-tier), o ile jest ona dostępna. Na przykład możesz przeprowadzić uaktualnienie z warstwy Bezpłatna do warstwy Podstawowa lub standardowa. Możesz również zmienić [wersję na niższą](#downgrade-pricing-tier), jeśli jest dostępna. Aby uzyskać więcej informacji o cenach, zobacz następujące tematy:

* [Cennik Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Uaktualnij warstwę cenową

Aby wprowadzić tę zmianę, można użyć Azure Portal, wykonując kroki opisane w tej sekcji lub [interfejsie wiersza polecenia platformy Azure](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Na platformie Azure są wyświetlane wszystkie konta integracji w ramach subskrypcji platformy Azure.

1. W obszarze **konta integracji**wybierz konto integracji, które chcesz przenieść. W menu konto integracji wybierz pozycję **Przegląd**.

   ![W menu konto integracji wybierz pozycję "przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **warstwa cenowa uaktualnienia**, która zawiera listę dostępnych wyższych warstw. Po wybraniu warstwy zmiany zaczynają obowiązywać.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Jeśli jeszcze tego nie zrobiono, [Zainstaluj wymagania wstępne interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. W Azure Portal Otwórz środowisko [**Cloud Shell**](../cloud-shell/overview.md?view=azure-cli-latest) platformy Azure.

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź [polecenie **AZ Resource** ](/cli/azure/resource?view=azure-cli-latest#az-resource-update)i ustaw wyższą żądaną `skuName` warstwę.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Na przykład jeśli masz warstwę podstawową, możesz ustawić `skuName` `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Warstwa cenowa obniżenia poziomu

Aby wprowadzić tę zmianę, użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Jeśli jeszcze tego nie zrobiono, [Zainstaluj wymagania wstępne interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. W Azure Portal Otwórz środowisko [**Cloud Shell**](../cloud-shell/overview.md?view=azure-cli-latest) platformy Azure.

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź [polecenie **AZ Resource** ](/cli/azure/resource?view=azure-cli-latest#az-resource-update) i ustaw ją `skuName` na niższą warstwę.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Na przykład jeśli masz warstwę standardową, możesz ustawić `skuName` `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Odłączanie od aplikacji logiki

Jeśli chcesz połączyć aplikację logiki z innym kontem integracji lub nie korzystasz już z konta integracji z aplikacją logiki, Usuń łącze przy użyciu Azure Resource Explorer.

1. Otwórz okno przeglądarki i przejdź do [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Zaloguj się przy użyciu tych samych poświadczeń konta platformy Azure.

   ![Eksplorator zasobów Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. W polu wyszukiwania wprowadź nazwę aplikacji logiki, aby można było znaleźć i wybrać aplikację logiki.

   ![Znajdowanie i Wybieranie aplikacji logiki](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na pasku tytułu Eksploratora wybierz pozycję **Odczyt/zapis**.

   ![Włącz tryb "Odczyt/zapis"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na karcie **dane** wybierz pozycję **Edytuj**.

   ![Na karcie "dane" Wybierz pozycję "Edytuj".](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. W edytorze Znajdź `integrationAccount` obiekt i Usuń tę właściwość, która ma następujący format:

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

   ![Znajdź obiekt "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na karcie **dane** wybierz pozycję **Umieść** , aby zapisać zmiany.

   ![Aby zapisać zmiany, wybierz pozycję "Umieść"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. W Azure Portal Znajdź i wybierz aplikację logiki. W obszarze **Ustawienia przepływu pracy**aplikacji Sprawdź, czy właściwość **konto integracji** teraz jest pusta.

   ![Sprawdź, czy konto integracji nie jest połączone](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Przenoszenie konta integracji

Konto integracji można przenieść do innej grupy zasobów platformy Azure lub subskrypcji platformy Azure. Podczas przenoszenia zasobów platforma Azure tworzy nowe identyfikatory zasobów, dlatego należy zamiast tego użyć nowych identyfikatorów i zaktualizować wszelkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami. Jeśli chcesz zmienić subskrypcję, musisz także określić istniejącą lub nową grupę zasobów.

W przypadku tego zadania można użyć Azure Portal, wykonując kroki opisane w tej sekcji lub [interfejsie wiersza polecenia platformy Azure](/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Na platformie Azure są wyświetlane wszystkie konta integracji w ramach subskrypcji platformy Azure.

1. W obszarze **konta integracji**wybierz konto integracji, które chcesz przenieść. W menu konto integracji wybierz pozycję **Przegląd**.

   ![W menu konto integracji wybierz pozycję "przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Obok pozycji **Grupa zasobów** lub **Nazwa subskrypcji**wybierz pozycję **Zmień**.

   ![Zmień grupę zasobów lub subskrypcję](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Wybierz wszystkie powiązane zasoby, które chcesz również przenieść.

1. W zależności od dokonanego wyboru wykonaj następujące kroki, aby zmienić grupę zasobów lub subskrypcję:

   * Grupa zasobów: z listy **Grupa zasobów** wybierz docelową grupę zasobów. Lub, aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów**.

   * Subskrypcja: z listy **subskrypcja** wybierz subskrypcję docelową. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Lub, aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów**.

1. Aby potwierdzić zrozumienie, że wszelkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działały do momentu zaktualizowania ich przy użyciu nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz przycisk **OK**.

1. Po zakończeniu upewnij się, że wszystkie skrypty są aktualizowane przy użyciu nowych identyfikatorów zasobów dla przeniesionych zasobów.  

## <a name="delete-integration-account"></a>Usuwanie konta integracji

W przypadku tego zadania można użyć Azure Portal, wykonując kroki opisane w tej sekcji, [interfejsie wiersza polecenia platformy Azure](/cli/azure/resource?view=azure-cli-latest#az-resource-delete)lub [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź "konta integracji" jako filtr, a następnie wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Na platformie Azure są wyświetlane wszystkie konta integracji w ramach subskrypcji platformy Azure.

1. W obszarze **konta integracji**wybierz konto integracji, które chcesz usunąć. W menu konto integracji wybierz pozycję **Przegląd**.

   ![W menu konto integracji wybierz pozycję "przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **Usuń**.

   ![W okienku "przegląd" Wybierz pozycję "Usuń"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Aby potwierdzić, że chcesz usunąć konto integracji, wybierz pozycję **tak**.

   ![Aby potwierdzić usunięcie, wybierz pozycję "tak"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych na koncie integracji](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów między partnerami na koncie integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)

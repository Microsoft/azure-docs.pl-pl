---
title: Tworzenie obszarów roboczych w portalu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać Azure Machine Learning obszary robocze Azure Portal lub z zestawem SDK dla języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518929"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Tworzenie obszarów roboczych Azure Machine Learning i zarządzanie nimi 

W tym artykule opisano tworzenie, wyświetlanie i usuwanie [**Azure Machine Learning obszarów roboczych**](concept-workspace.md) dla [Azure Machine Learning](overview-what-is-azure-ml.md)przy użyciu Azure Portal lub [zestawu SDK dla języka Python](/python/api/overview/azure/ml/)

Wraz ze zmianami lub wymaganiami dotyczącymi usługi Automation można także tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)lub [za pośrednictwem rozszerzenia vs Code](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.
* W przypadku korzystania z zestawu SDK języka Python [Zainstaluj zestaw SDK](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Domyślnie tworzenie obszaru roboczego tworzy również Azure Container Registry (ACR).  Ponieważ ACR nie obsługuje obecnie znaków Unicode w nazwach grup zasobów, Użyj grupy zasobów, która nie zawiera tych znaków.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

# <a name="python"></a>[Python](#tab/python)

* **Specyfikacja domyślna.** Domyślnie zasoby zależne oraz Grupa zasobów zostaną utworzone automatycznie. Ten kod tworzy obszar roboczy o nazwie `myworkspace` i grupę zasobów o nazwie `myresourcegroup` w `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Ustaw `create_resource_group` wartość false, jeśli masz istniejącą grupę zasobów platformy Azure, której chcesz użyć dla obszaru roboczego.

* <a name="create-multi-tenant"></a>**Wielu dzierżawców.**  Jeśli masz wiele kont, Dodaj identyfikator dzierżawy Azure Active Directory, którego chcesz użyć.  Znajdź swój identyfikator dzierżawy z [Azure Portal](https://portal.azure.com) w obszarze **Azure Active Directory, tożsamości zewnętrzne**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* Niezależna **[chmura](reference-machine-learning-cloud-parity.md)**. Jeśli pracujesz w chmurze suwerennej, będziesz potrzebować dodatkowego kodu do uwierzytelnienia na platformie Azure.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Użyj istniejących zasobów platformy Azure**.  Możesz również utworzyć obszar roboczy, który używa istniejących zasobów platformy Azure w formacie identyfikatora zasobu platformy Azure. Znajdź określone identyfikatory zasobów platformy Azure w Azure Portal lub z zestawem SDK. W tym przykładzie przyjęto założenie, że grupa zasobów, konto magazynu, Magazyn kluczy, usługi App Insights i rejestr kontenerów już istnieje.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Aby uzyskać więcej informacji, zobacz [Dokumentacja zestawu SDK obszaru roboczego](/python/api/azureml-core/azureml.core.workspace.workspace).

Jeśli masz problemy z uzyskaniem dostępu do subskrypcji, zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md), a także [uwierzytelnianie w Azure Machine Learning](https://aka.ms/aml-notebook-auth) notesie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń dla subskrypcji platformy Azure. 

1. W lewym górnym rogu Azure Portal wybierz pozycję **+ Utwórz zasób**.

      ![Tworzenie nowego zasobu](./media/how-to-manage-workspace/create-workspace.gif)

1. Użyj paska wyszukiwania, aby znaleźć **Machine Learning**.

1. Wybierz **Machine Learning**.

1. W okienku **Machine Learning** wybierz pozycję **Utwórz** , aby rozpocząć.

1. Podaj następujące informacje, aby skonfigurować nowy obszar roboczy:

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **dokumentów-WS**. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby. W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj grupy zasobów istniejącej w Twojej subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używane są **dokumenty-AML**. Aby korzystać z istniejącej grupy zasobów, musisz mieć rolę *współautora* lub *właściciela* .  Aby uzyskać więcej informacji na temat dostępu, zobacz [Zarządzanie dostępem do obszaru roboczego Azure Machine Learning](how-to-assign-roles.md).
   Region (Region) | Wybierz region platformy Azure znajdujący się najbliżej Twoich użytkowników i zasoby danych, aby utworzyć obszar roboczy.
   | Konto magazynu | Domyślne konto magazynu dla obszaru roboczego. Domyślnie zostanie utworzony nowy. |
   | Key Vault | Azure Key Vault używany przez obszar roboczy. Domyślnie zostanie utworzony nowy. |
   | Application Insights | Wystąpienie usługi Application Insights dla obszaru roboczego. Domyślnie zostanie utworzony nowy. |
   | Container Registry | Azure Container Registry obszaru roboczego. Domyślnie nowy element _nie_ jest początkowo tworzony dla obszaru roboczego. Zamiast tego jest tworzony, gdy będzie potrzebny podczas tworzenia obrazu platformy Docker podczas uczenia lub wdrożenia. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Skonfiguruj obszar roboczy.":::

1. Po zakończeniu konfigurowania obszaru roboczego wybierz pozycję **Przegląd + Utwórz**. Opcjonalnie możesz użyć sekcji [Sieć](#networking) i [Zaawansowane](#advanced) , aby skonfigurować więcej ustawień obszaru roboczego.

1. Sprawdź ustawienia i wprowadź wszelkie dodatkowe zmiany lub poprawki. Gdy ustawienia są zadowalające, wybierz pozycję **Utwórz**.

   > [!Warning] 
   > Tworzenie obszaru roboczego w chmurze może potrwać kilka minut.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzeniu wdrożenia. 
 
 1. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.
 
---



### <a name="networking"></a>Networking  

> [!IMPORTANT]  
> Aby uzyskać więcej informacji na temat korzystania z prywatnego punktu końcowego i sieci wirtualnej z obszarem roboczym, zobacz [izolacja sieci i ochrona prywatności](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

Zestaw Azure Machine Learning Python SDK udostępnia klasę [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , która może być używana z elementem [Workspace. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) w celu utworzenia obszaru roboczego z prywatnym punktem końcowym. Ta klasa wymaga istniejącej sieci wirtualnej.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Domyślną konfiguracją sieci jest użycie __publicznego punktu końcowego__, który jest dostępny w publicznej sieci Internet. Aby ograniczyć dostęp do obszaru roboczego do utworzonego Virtual Network platformy Azure, zamiast tego możesz wybrać opcję __prywatny punkt końcowy__ (wersja zapoznawcza) jako __metodę połączenia__, a następnie użyć opcji __+ Dodaj__ , aby skonfigurować punkt końcowy.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Wybór prywatnego punktu końcowego":::  

1. W formularzu __Tworzenie prywatnego punktu końcowego__ Ustaw lokalizację, nazwę i sieć wirtualną do użycia. Jeśli chcesz użyć punktu końcowego ze strefą Prywatna strefa DNS, wybierz opcję __Zintegruj z prywatną strefą DNS__ i wybierz strefę przy użyciu pola __strefy prywatna strefa DNS__ . Wybierz __przycisk OK__ , aby utworzyć punkt końcowy.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Tworzenie prywatnego punktu końcowego":::   

1. Po zakończeniu konfigurowania sieci możesz wybrać pozycję __Przegląd + Utwórz__ lub przejdź do opcjonalnej __zaawansowanej__ konfiguracji.

---

> [!IMPORTANT]  
> Korzystanie z prywatnego punktu końcowego z obszarem roboczym Azure Machine Learning jest obecnie dostępne w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.     
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Wiele obszarów roboczych z prywatnym punktem końcowym

Podczas tworzenia prywatnego punktu końcowego zostanie utworzona nowa Prywatna strefa DNS strefa o nazwie __privatelink.API.azureml.MS__ . Zawiera link do sieci wirtualnej. Jeśli utworzysz wiele obszarów roboczych z prywatnymi punktami końcowymi w tej samej grupie zasobów, do strefy DNS może zostać dodany tylko sieć wirtualna dla pierwszego prywatnego punktu końcowego. Aby dodać wpisy dla sieci wirtualnych używanych przez dodatkowe obszary robocze/prywatne punkty końcowe, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz grupę zasobów, która zawiera obszar roboczy. Następnie wybierz zasób strefy Prywatna strefa DNS o nazwie __privatelink.API.azureml.MS__
2. W obszarze __Ustawienia__ wybierz pozycję __linki sieci wirtualnej__.
3. Wybierz pozycję __Dodaj__. Na stronie __Dodawanie łącza do sieci wirtualnej__ Podaj unikatową __nazwę łącza__, a następnie wybierz __sieć wirtualną__ , która ma zostać dodana. Wybierz __przycisk OK__ , aby dodać łącze sieciowe.

Aby uzyskać więcej informacji, zobacz [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Skanowanie pod kątem luk w zabezpieczeniach

Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Należy zezwolić Azure Security Center na skanowanie zasobów i postępować zgodnie z zaleceniami. Więcej informacji można znaleźć w temacie  [Azure Container Registry Scanning image Security Center](../security-center/defender-for-container-registries-introduction.md) i [Azure Kubernetes Services integration with a Security Center](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Zaawansowany

Domyślnie metadane obszaru roboczego są przechowywane w wystąpieniu Azure Cosmos DBnym przez firmę Microsoft. Te dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby ograniczyć dane gromadzone przez firmę Microsoft w obszarze roboczym, wybierz pozycję __duży obszar roboczy wpływ na działalność__ w portalu lub ustaw `hbi_workspace=true ` w języku Python. Aby uzyskać więcej informacji na temat tego ustawienia, zobacz [szyfrowanie w spoczynku](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> Wybór dużego wpływu na działalność biznesową można wykonać tylko podczas tworzenia obszaru roboczego. Tego ustawienia nie można zmienić po utworzeniu obszaru roboczego.   

#### <a name="use-your-own-key"></a>Użyj własnego klucza

Możesz podać własny klucz szyfrowania danych. Spowoduje to utworzenie wystąpienia Azure Cosmos DB, które przechowuje metadane w ramach subskrypcji platformy Azure.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Wykonaj następujące kroki, aby podać własny klucz:

> [!IMPORTANT]  
> Przed wykonaniem tych kroków należy najpierw wykonać następujące czynności:   
>
> 1. Autoryzuj __aplikację Machine Learning__ (w temacie Zarządzanie tożsamościami i dostępem) z uprawnieniami współautora w ramach subskrypcji.  
> 1. Wykonaj kroki opisane w temacie [Konfigurowanie kluczy zarządzanych przez klienta](../cosmos-db/how-to-setup-cmk.md) do:
>     * Rejestrowanie dostawcy Azure Cosmos DB
>     * Tworzenie i Konfigurowanie Azure Key Vault
>     * Generowanie klucza
>   
>     Nie musisz ręcznie tworzyć wystąpienia Azure Cosmos DB, po utworzeniu obszaru roboczego zostanie ono utworzone. To wystąpienie Azure Cosmos DB zostanie utworzone w oddzielnej grupie zasobów przy użyciu nazwy opartej na tym wzorcu: `<your-workspace-resource-name>_<GUID>` .   
>   
> Tego ustawienia nie można zmienić po utworzeniu obszaru roboczego. Jeśli usuniesz Azure Cosmos DB używany przez obszar roboczy, musisz również usunąć obszar roboczy, który go używa.

# <a name="python"></a>[Python](#tab/python)

Użyj `cmk_keyvault` i, `resource_cmk_uri` Aby określić klucz zarządzany przez klienta.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz pozycję __klucze zarządzane przez klienta__, a następnie wybierz __pozycję kliknij, aby wybrać klucz__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Klucze zarządzane przez klienta":::

1. W formularzu __Wybierz klucz z Azure Key Vault__ wybierz istniejący Azure Key Vault, klucz, który zawiera, i wersję klucza. Ten klucz służy do szyfrowania danych przechowywanych w Azure Cosmos DB. Na koniec użyj przycisku __Wybierz__ , aby użyć tego klucza.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Wybierz klucz":::

---

### <a name="download-a-configuration-file"></a>Pobierz plik konfiguracji

Jeśli utworzysz [wystąpienie obliczeniowe](tutorial-1st-experiment-sdk-setup.md#azure), Pomiń ten krok.  Wystąpienie obliczeniowe utworzyło już kopię tego pliku.

# <a name="python"></a>[Python](#tab/python)

Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego ( `ws` ), Zapisz plik konfiguracji:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz pozycję  **pobierz config.js** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz config.jsna](./media/how-to-manage-workspace/configure.png)

---

Umieść plik w strukturze katalogów za pomocą skryptów języka Python lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure* lub w katalogu nadrzędnym. Podczas tworzenia wystąpienia obliczeniowego ten plik jest dodawany do poprawnego katalogu na maszynie wirtualnej.

## <a name="connect-to-a-workspace"></a>Łączenie z obszarem roboczym

W kodzie w języku Python utworzysz obiekt obszaru roboczego, aby połączyć się z obszarem roboczym.  Ten kod odczyta zawartość pliku konfiguracji w celu znalezienia obszaru roboczego.  Zostanie wyświetlony monit o zalogowanie się, jeśli nie masz jeszcze uwierzytelnienia.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Wielu dzierżawców.**  Jeśli masz wiele kont, Dodaj identyfikator dzierżawy Azure Active Directory, którego chcesz użyć.  Znajdź swój identyfikator dzierżawy z [Azure Portal](https://portal.azure.com) w obszarze **Azure Active Directory, tożsamości zewnętrzne**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* Niezależna **[chmura](reference-machine-learning-cloud-parity.md)**. Jeśli pracujesz w chmurze suwerennej, będziesz potrzebować dodatkowego kodu do uwierzytelnienia na platformie Azure.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Jeśli masz problemy z uzyskaniem dostępu do subskrypcji, zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md), a także [uwierzytelnianie w Azure Machine Learning](https://aka.ms/aml-notebook-auth) notesie.

## <a name="find-a-workspace"></a><a name="view"></a>Znajdowanie obszaru roboczego

Zobacz listę wszystkich obszarów roboczych, których można użyć.

# <a name="python"></a>[Python](#tab/python)

Znajdź subskrypcje na [stronie Subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Skopiuj identyfikator i użyj go w poniższym kodzie, aby wyświetlić wszystkie obszary robocze dostępne dla tej subskrypcji.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnym polu wyszukiwania wpisz **Machine Learning**.  

1. Wybierz **Machine Learning**.

   ![Wyszukaj Azure Machine Learning obszar roboczy](./media/how-to-manage-workspace/find-workspaces.png)

1. Przejrzyj listę znalezionych obszarów roboczych. Można filtrować na podstawie subskrypcji, grup zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.

---


## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Jeśli obszar roboczy nie jest już potrzebny, usuń go.  

# <a name="python"></a>[Python](#tab/python)

Usuń obszar roboczy `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Domyślna akcja nie powoduje usunięcia zasobów skojarzonych z obszarem roboczym, takich jak rejestr kontenerów, konto magazynu, Magazyn kluczy i Application Insights.  Ustaw `delete_dependent_resources` wartość true, aby usunąć te zasoby.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Usuń**  w górnej części obszaru roboczego, który chcesz usunąć.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Usuń obszar roboczy":::

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Obsługiwane przeglądarki w programie Azure Machine Learning Studio**: zalecamy użycie najnowszej przeglądarki zgodnej z systemem operacyjnym. Obsługiwane są następujące przeglądarki:
  * Microsoft Edge (Nowa Microsoft Edge, Najnowsza wersja. Nie w starszej wersji programu Microsoft Edge)
  * Safari (najnowsza wersja, tylko Mac)
  * Chrome (najnowsza wersja)
  * Firefox (najnowsza wersja)

* **Azure Portal**: 
  * Jeśli przejdziesz bezpośrednio do obszaru roboczego z linku udostępniania z zestawu SDK lub Azure Portal, nie możesz wyświetlić standardowej strony **przeglądu** , która zawiera informacje o subskrypcji w rozszerzeniu. W tym scenariuszu nie można również przełączyć się do innego obszaru roboczego. Aby wyświetlić inny obszar roboczy, przejdź bezpośrednio do [Azure Machine Learning Studio](https://ml.azure.com) i wyszukaj nazwę obszaru roboczego.
  * Wszystkie elementy zawartości (zestawy danych, eksperymenty, obliczenia itd.) są dostępne tylko w programie [Azure Machine Learning Studio](https://ml.azure.com). *Nie* są one dostępne w Azure Portal.

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przeniesienie obszaru roboczego

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie Azure Container Registry

W przypadku niektórych operacji Azure Machine Learning obszar roboczy używa Azure Container Registry (ACR). Zostanie automatycznie utworzone wystąpienie ACR, gdy jest ono najpierw wymagane.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Przykłady

Przykłady tworzenia obszaru roboczego:
* Użyj Azure Portal, aby [utworzyć obszar roboczy i wystąpienie obliczeniowe](tutorial-1st-experiment-sdk-setup.md)
* Używanie zestawu SDK języka Python do [tworzenia obszaru roboczego w Twoim środowisku](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Następne kroki

Gdy masz obszar roboczy, Dowiedz się, jak [szkolić i wdrożyć model](tutorial-train-models-with-aml.md).
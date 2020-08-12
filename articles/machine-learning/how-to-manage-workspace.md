---
title: Tworzenie obszarów roboczych w portalu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze Azure Machine Learning w Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: fefc7b39a6539822686618d9f018084f65443ee1
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121754"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi w Azure Portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano tworzenie, wyświetlanie i usuwanie [**Azure Machine Learning obszarów roboczych**](concept-workspace.md) w Azure Portal dla [Azure Machine Learning](overview-what-is-azure-ml.md).  Portal jest najprostszym sposobem na rozpoczęcie pracy z obszarami roboczymi, ale w zależności od zmian lub wymagań dotyczących usługi Automation można także tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), [z kodem języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [za pośrednictwem rozszerzenia vs Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Do utworzenia obszaru roboczego potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń dla subskrypcji platformy Azure. 

1. W lewym górnym rogu Azure Portal wybierz pozycję **+ Utwórz zasób**.

      ![Tworzenie nowego zasobu](./media/how-to-manage-workspace/create-workspace.gif)

1. Użyj paska wyszukiwania, aby znaleźć **Machine Learning**.

1. Wybierz **Machine Learning**.

1. W okienku **Machine Learning** wybierz pozycję **Utwórz** , aby rozpocząć.

1. Podaj następujące informacje, aby skonfigurować nowy obszar roboczy:

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby. W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj grupy zasobów istniejącej w Twojej subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. 
   Lokalizacja | Wybierz lokalizację znajdującą się najbliżej użytkowników i zasoby danych, aby utworzyć obszar roboczy.
   Wersja obszaru roboczego | Wybierz pozycję **podstawowa** lub **Enterprise**.  Ta wersja obszaru roboczego określa funkcje, do których będziesz mieć dostęp i Cennik. Dowiedz się więcej o [ofertach Basic i Enterprise Edition](overview-what-is-azure-ml.md#sku). 

   :::image type="content" source="media/how-to-manage-workspace/select-edition.png" alt-text="Konfiguruj obszar roboczy":::

1. Po zakończeniu konfigurowania obszaru roboczego możesz wybrać pozycję **Przegląd + Utwórz**lub przejdź do konfiguracji opcjonalnej __sieci__ .

### <a name="optional-networking"></a>Obowiązkowe Sieci

> [!IMPORTANT]
> Aby uzyskać więcej informacji na temat korzystania z prywatnego punktu końcowego i sieci wirtualnej z obszarem roboczym, zobacz [izolacja sieci i ochrona prywatności](how-to-enable-virtual-network.md).

1. Domyślną konfiguracją sieci jest użycie __publicznego punktu końcowego__, który jest dostępny w publicznej sieci Internet. Aby ograniczyć dostęp do obszaru roboczego do utworzonego Virtual Network platformy Azure, możesz zamiast tego wybrać __prywatny punkt końcowy__ jako __metodę połączenia__, a następnie użyć opcji __+ Dodaj__ , aby skonfigurować punkt końcowy.

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Wybór prywatnego punktu końcowego":::

1. W formularzu __Tworzenie prywatnego punktu końcowego__ Ustaw lokalizację, nazwę i sieć wirtualną do użycia. Jeśli chcesz użyć punktu końcowego ze strefą Prywatna strefa DNS, wybierz opcję __Zintegruj z prywatną strefą DNS__ i wybierz strefę przy użyciu pola __strefy prywatna strefa DNS__ . Wybierz __przycisk OK__ , aby utworzyć punkt końcowy. 

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Tworzenie prywatnego punktu końcowego":::

1. Po zakończeniu konfigurowania sieci możesz wybrać pozycję __Przegląd + Utwórz__lub przejdź do opcjonalnej __zaawansowanej__ konfiguracji.

    > [!WARNING]
    > Podczas tworzenia prywatnego punktu końcowego zostanie utworzona nowa Prywatna strefa DNS strefa o nazwie __privatelink.API.azureml.MS__ . Zawiera link do sieci wirtualnej. Jeśli utworzysz wiele obszarów roboczych z prywatnymi punktami końcowymi w tej samej grupie zasobów, do strefy DNS może zostać dodany tylko sieć wirtualna dla pierwszego prywatnego punktu końcowego. Aby dodać wpisy dla sieci wirtualnych używanych przez dodatkowe obszary robocze/prywatne punkty końcowe, wykonaj następujące czynności:
    > 
    > 1. W [Azure Portal](https://portal.azure.com)wybierz grupę zasobów, która zawiera obszar roboczy. Następnie wybierz zasób strefy Prywatna strefa DNS o nazwie __privatelink.API.azureml.MS__.
    > 2. W obszarze __Ustawienia__wybierz pozycję __linki sieci wirtualnej__.
    > 3. Wybierz pozycję __Dodaj__. Na stronie __Dodawanie łącza do sieci wirtualnej__ Podaj unikatową __nazwę łącza__, a następnie wybierz __sieć wirtualną__ , która ma zostać dodana. Wybierz __przycisk OK__ , aby dodać łącze sieciowe.
    >
    > Aby uzyskać więcej informacji, zobacz [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](/azure/private-link/private-endpoint-dns).

### <a name="optional-advanced"></a>Obowiązkowe Doświadczonych

Domyślnie metryki i metadane obszaru roboczego są przechowywane w Azure Cosmos DB wystąpieniu, które utrzymuje firma Microsoft. Te dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

Aby ograniczyć ilość danych zbieranych przez firmę Microsoft w obszarze roboczym, wybierz pozycję __duży obszar roboczy wpływ na działalność biznesową__.

> [!IMPORTANT]
> Wybór dużego wpływu na działalność biznesową można wykonać tylko podczas tworzenia obszaru roboczego. Tego ustawienia nie można zmienić po utworzeniu obszaru roboczego.

Jeśli używasz wersji __Enterprise__ programu Azure Machine Learning, możesz zamiast tego podać własny klucz. Spowoduje to utworzenie wystąpienia Azure Cosmos DB, które przechowuje metryki i metadane w ramach subskrypcji platformy Azure. Wykonaj następujące kroki, aby użyć własnego klucza:

> [!IMPORTANT]
> Przed wykonaniem tych kroków należy najpierw wykonać następujące czynności:
>
> 1. Autoryzuj __aplikację Machine Learning__ (w temacie Zarządzanie tożsamościami i dostępem) z uprawnieniami współautora w ramach subskrypcji.
> 1. Wykonaj kroki opisane w temacie [Konfigurowanie kluczy zarządzanych przez klienta](/azure/cosmos-db/how-to-setup-cmk) do:
>     * Rejestrowanie dostawcy Azure Cosmos DB
>     * Tworzenie i Konfigurowanie Azure Key Vault
>     * Generowanie klucza
>
>     Nie musisz ręcznie tworzyć wystąpienia Azure Cosmos DB, po utworzeniu obszaru roboczego zostanie ono utworzone. To wystąpienie Azure Cosmos DB zostanie utworzone w oddzielnej grupie zasobów przy użyciu nazwy opartej na tym wzorcu: `<your-resource-group-name>_<GUID>` .
>
> Tego ustawienia nie można zmienić po utworzeniu obszaru roboczego. Jeśli usuniesz Azure Cosmos DB używany przez obszar roboczy, musisz również usunąć obszar roboczy, który go używa.

1. Wybierz pozycję __klucze zarządzane przez klienta__, a następnie wybierz __pozycję kliknij, aby wybrać klucz__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Klucze zarządzane przez klienta":::

1. W formularzu __Wybierz klucz z Azure Key Vault__ wybierz istniejący Azure Key Vault, klucz, który zawiera, i wersję klucza. Ten klucz służy do szyfrowania danych przechowywanych w Azure Cosmos DB. Na koniec użyj przycisku __Wybierz__ , aby użyć tego klucza.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Wybierz klucz":::


Po zakończeniu konfigurowania sieci wybierz pozycję __Przegląd + Utwórz__.

### <a name="review--create"></a>Przegląd + tworzenie

1. Sprawdź ustawienia i wprowadź wszelkie dodatkowe zmiany lub poprawki. Gdy ustawienia są zadowalające, wybierz pozycję **Utwórz**.

   > [!Warning] 
   > Tworzenie obszaru roboczego w chmurze może potrwać kilka minut.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzeniu wdrożenia. 
 
 1. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.

### <a name="download-a-configuration-file"></a>Pobierz plik konfiguracji

1. Jeśli utworzysz [wystąpienie obliczeniowe](tutorial-1st-experiment-sdk-setup.md#azure), Pomiń ten krok.

1. Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz pozycję **pobierz config.js** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz config.jsna](./media/how-to-manage-workspace/configure.png)
   
   Umieść plik w strukturze katalogów za pomocą skryptów języka Python lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym. Podczas tworzenia wystąpienia obliczeniowego ten plik jest dodawany do poprawnego katalogu na maszynie wirtualnej.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uaktualnianie do wersji Enterprise

Możesz uaktualnić obszar roboczy z wersji podstawowa do wersji Enterprise, aby korzystać z rozszerzonych funkcji, takich jak środowisko o małym kodzie i ulepszone funkcje zabezpieczeń.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz obszar roboczy, który chcesz uaktualnić.

1. Wybierz pozycję **Dowiedz się więcej** w prawym górnym rogu strony.

   [![Uaktualnianie obszaru roboczego ](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. W wyświetlonym oknie wybierz pozycję **Uaktualnij** .


> [!IMPORTANT]
> Nie można obniżyć poziomu obszaru roboczego Enterprise Edition do obszaru roboczego wersja podstawowa. 

## <a name="find-a-workspace"></a><a name="view"></a>Znajdowanie obszaru roboczego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnym polu wyszukiwania wpisz **Machine Learning**.  

1. Wybierz **Machine Learning**.

   ![Wyszukaj Azure Machine Learning obszar roboczy](./media/how-to-manage-workspace/find-workspaces.png)

1. Przejrzyj listę znalezionych obszarów roboczych. Można filtrować na podstawie subskrypcji, grup zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Usuń** w górnej części obszaru roboczego, który chcesz usunąć.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Usuwanie obszaru roboczego":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przeniesienie obszaru roboczego

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie Azure Container Registry

W przypadku niektórych operacji Azure Machine Learning obszar roboczy używa Azure Container Registry (ACR). Zostanie automatycznie utworzone wystąpienie ACR, gdy jest ono najpierw wymagane.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z samouczkiem o pełnej długości, aby dowiedzieć się, jak używać obszaru roboczego do kompilowania, uczenia i wdrażania modeli przy użyciu Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)

---
title: Szybki start — tworzenie wystąpienia usługi Azure API Management przy użyciu interfejsu wiersza polecenia
description: Utwórz nowe wystąpienie usługi Azure API Management service przy użyciu interfejsu wiersza polecenia platformy Azure.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775857"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Szybki start: tworzenie nowego wystąpienia usługi Azure API Management service przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. Usługa APIM umożliwia tworzenie nowoczesnych bram interfejsu API dla istniejących usług zaplecza hostowanych w dowolnym miejscu i zarządzanie nimi. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

W tym przewodniku Szybki start opisano kroki tworzenia nowego API Management przy użyciu poleceń [az apim](/cli/azure/apim) w interfejsie wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.11.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure API Management, podobnie jak wszystkie zasoby platformy Azure, musi zostać wdrożona w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji Środkowe usa za pomocą następującego [polecenia az group create:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

Teraz, gdy masz już grupę zasobów, możesz utworzyć API Management usługi. Utwórz je za pomocą [polecenia az apim create](/cli/azure/apim#az_apim_create) i podaj nazwę usługi oraz szczegóły wydawcy. Nazwa usługi musi być unikatowa w obrębie platformy Azure. 

W poniższym przykładzie *myapim* jest używana jako nazwa usługi. Zaktualizuj nazwę na unikatową wartość. Zaktualizuj również nazwę organizacji wydawcy interfejsu API i adres e-mail, aby otrzymywać powiadomienia. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Domyślnie polecenie tworzy wystąpienie w warstwie Deweloper, która jest ekonomiczną opcją oceny usługi Azure API Management. Ta warstwa nie jest do użytku produkcyjnego. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie). 

> [!TIP]
> Utworzenie i aktywowanie usługi API Management w tej warstwie może potrwać od 30 do 40 minut. Poprzednie polecenie używa opcji `--no-wait` , aby polecenie zwracało dane natychmiast po utworzeniu usługi.

Sprawdź stan wdrożenia, uruchamiając [polecenie az apim show:](/cli/azure/apim#az_apim_show)

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Początkowo dane wyjściowe są podobne do następujących, pokazując `Activating` stan:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Po aktywacji stan to , `Online` a wystąpienie usługi ma adres bramy i publiczny adres IP. Na razie te adresy nie uwidoczniają żadnej zawartości. Na przykład:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Gdy wystąpienie API Management usługi jest w trybie online, możesz z niego korzystać. Rozpocznij od samouczka, aby [zaimportować i opublikować pierwszy interfejs API.](import-and-publish.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wystąpienie usługi nie będą już potrzebne, możesz je usunąć za pomocą polecenia az API Management [group](/cli/azure/group#az_group_delete) delete.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)

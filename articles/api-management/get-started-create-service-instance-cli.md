---
title: Szybki Start — Tworzenie wystąpienia usługi Azure API Management przy użyciu interfejsu wiersza polecenia
description: Utwórz nowe wystąpienie usługi Azure API Management przy użyciu interfejsu wiersza polecenia platformy Azure.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 72d1faac02a21f23d46eb992af1d501bca89e71f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688080"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. APIM umożliwia tworzenie nowoczesnych bram interfejsu API i zarządzanie nimi dla istniejących usług zaplecza hostowanych w dowolnym miejscu. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

W tym przewodniku szybki start opisano procedurę tworzenia nowego wystąpienia API Management przy użyciu polecenia [AZ APIM](/cli/azure/apim) w interfejsie CLI platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.11.1 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wystąpienia API Management platformy Azure, takie jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

Najpierw utwórz grupę zasobów o nazwie Moja *resourceName* w lokalizacji środkowe stany USA przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

Teraz, gdy masz grupę zasobów, możesz utworzyć wystąpienie usługi API Management. Utwórz je za pomocą polecenia [AZ APIM Create](/cli/azure/apim#az-apim-create) i podaj nazwę usługi oraz szczegóły wydawcy. Nazwa usługi musi być unikatowa w ramach platformy Azure. 

W poniższym przykładzie nazwa usługi jest *myapim* . Zaktualizuj nazwę na unikatową wartość. Należy również zaktualizować nazwę organizacji wydawcy interfejsu API i adres e-mail, aby otrzymywać powiadomienia. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Domyślnie polecenie tworzy wystąpienie w warstwie Deweloper, która jest ekonomiczną opcją oszacowania API Management platformy Azure. Ta warstwa nie jest używana do użycia w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie). 

> [!TIP]
> Utworzenie i aktywowanie usługi API Management w tej warstwie może potrwać od 30 do 40 minut. Poprzednie polecenie używa tej `--no-wait` opcji, aby polecenie zwracało się natychmiast podczas tworzenia usługi.

Sprawdź stan wdrożenia, uruchamiając polecenie [AZ APIM show](/cli/azure/apim#az-apim-show) :

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Początkowo dane wyjściowe są podobne do następujących, pokazując `Activating` stan:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Po aktywacji stan ma wartość, `Online` a wystąpienie usługi ma adres bramy i publiczny adres IP. Na razie te adresy nie ujawniają żadnej zawartości. Na przykład:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Gdy wystąpienie usługi API Management jest w trybie online, możesz go używać. Zacznij od samouczka, aby [zaimportować i opublikować pierwszy interfejs API](import-and-publish.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) , aby usunąć grupę zasobów i wystąpienie usługi API Management.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)

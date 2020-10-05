---
title: Szybki Start — Tworzenie wystąpienia usługi Azure API Management przy użyciu interfejsu wiersza polecenia (wersja zapoznawcza)
description: Utwórz nowe wystąpienie usługi Azure API Management przy użyciu interfejsu wiersza polecenia platformy Azure.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 8e41e20752e4203bf9916813b86cd901f4f75c09
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90708227"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. APIM umożliwia tworzenie nowoczesnych bram interfejsu API i zarządzanie nimi dla istniejących usług zaplecza hostowanych w dowolnym miejscu. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

W tym przewodniku szybki start opisano procedurę tworzenia nowego wystąpienia API Management przy użyciu polecenia [AZ APIM](/cli/azure/apim) w interfejsie CLI platformy Azure. Polecenia w `az apim` grupie poleceń są obecnie dostępne w wersji zapoznawczej i mogą zostać zmienione lub usunięte w przyszłej wersji.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas pracy z tym przewodnikiem Szybki start możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, zalecana jest wersja 2.11.1 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

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

---
title: 'Szybki Start: Tworzenie grupy zarządzania za pomocą interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start utworzysz grupę zarządzania w celu zorganizowania zasobów w hierarchii zasobów przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: f07ae46c95f9ab9cc1ad973204ac5c50320fdf46
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237322"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Szybki Start: Tworzenie grupy zarządzania za pomocą interfejsu wiersza polecenia platformy Azure

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Ten przewodnik Szybki Start wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej, aby zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Tworzenie w interfejsie wiersza polecenia platformy Azure

Aby utworzyć nową grupę zarządzania, użyj polecenia [AZ Account Management-Group Create](/cli/azure/account/management-group#az-account-management-group-create) w interfejsie Azure. W tym przykładzie **Nazwa** grupy zarządzania to _contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**Nazwa** jest tworzonym unikatowym identyfikatorem. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania pokazywała inną nazwę w Azure Portal, Dodaj parametr **Display-Name** . Aby na przykład utworzyć grupę zarządzania z grupą Contoso i nazwę wyświetlaną "Grupa contoso", użyj następującego polecenia:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

W powyższych przykładach Nowa grupa zarządzania jest tworzona w ramach głównej grupy zarządzania. Aby określić inną grupę zarządzania jako nadrzędną, należy użyć parametru **nadrzędnego** i podać nazwę grupy nadrzędnej.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzoną powyżej grupę zarządzania, użyj polecenia [AZ Account Management-Group Delete](/cli/azure/account/management-group#az-account-management-group-delete) :

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)
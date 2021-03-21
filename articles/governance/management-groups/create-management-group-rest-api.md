---
title: 'Szybki Start: Tworzenie grupy zarządzania za pomocą interfejsu API REST'
description: W tym przewodniku szybki start za pomocą interfejsu API REST utworzysz grupę zarządzania w celu zorganizowania zasobów w hierarchii zasobów.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ff1487bf25945c733402ddb74d1e102bea80b4b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592504"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Szybki Start: Tworzenie grupy zarządzania za pomocą interfejsu API REST

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Jeśli jeszcze tego nie zrobiono, zainstaluj [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API REST opartych na Azure Resource Manager. Zamiast tego można użyć funkcji "Wypróbuj ją" w dokumentacji REST lub narzędzi, takich jak wywołanie programu PowerShell [-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) lub [Poster](https://www.postman.com).

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Tworzenie w interfejsie API REST

W przypadku interfejsu API REST Użyj punktu końcowego [grupy zarządzania-Create lub Update](/rest/api/resources/managementgroups/createorupdate) , aby utworzyć nową grupę zarządzania. W tym przykładzie **Identyfikator grupy zarządzania** to _contoso_.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Brak treści żądania

Identyfikator **GroupID** jest tworzonym unikatowym. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania pokazywała inną nazwę w Azure Portal, Dodaj właściwość **właściwości. DisplayName** w treści żądania. Aby na przykład utworzyć grupę zarządzania z identyfikatorem grupy  _contoso_ i nazwą wyświetlaną _Grupa contoso_, użyj następującego punktu końcowego i treści żądania:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Treść żądania

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

W powyższych przykładach Nowa grupa zarządzania jest tworzona w ramach głównej grupy zarządzania. Aby określić inną grupę zarządzania jako nadrzędną, należy użyć właściwości **Properties.Parent.ID** .

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Treść żądania

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzoną powyżej grupę zarządzania, użyj punktu końcowego [grupy zarządzania-Delete](/rest/api/resources/managementgroups/delete) :

- Identyfikator URI interfejsu API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Brak treści żądania

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)
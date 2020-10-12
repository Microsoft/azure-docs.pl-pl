---
title: Kontrola dostępu zdalnego do monitorowania — Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat sposobu konfigurowania kontroli dostępu opartej na rolach (RBAC) w akceleratorze rozwiązania do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969595"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Konfigurowanie kontroli dostępu opartej na rolach w akceleratorze rozwiązania do zdalnego monitorowania

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania kontroli dostępu opartej na rolach w akceleratorze rozwiązania do monitorowania zdalnego. Kontrola dostępu oparta na rolach umożliwia ograniczenie dostępu poszczególnych użytkowników do określonych funkcji w rozwiązaniu.

## <a name="default-settings"></a>Ustawienia domyślne

Podczas pierwszego wdrożenia rozwiązania do zdalnego monitorowania istnieją dwie role: **administrator** i **tylko do odczytu**.

Każdy użytkownik w roli **administratora** ma pełny dostęp do rozwiązania, w tym poniższe następujące uprawnienia. Użytkownik w roli **tylko do odczytu** będzie miał dostęp tylko do tego rozwiązania.

| Uprawnienie            | Administracja | Tylko do odczytu |
|----------------       |-------|-----------|
| Wyświetl rozwiązanie         | Tak   | Tak       |
| Aktualizuj alarmy         | Tak   | Nie        |
| Usuń alarmy         | Tak   | Nie        |
| Tworzenie urządzeń        | Tak   | Nie        |
| Aktualizowanie urządzeń        | Tak   | Nie        |
| Usuwanie urządzeń        | Tak   | Nie        |
| Tworzenie grup urządzeń  | Tak   | Nie        |
| Aktualizowanie grup urządzeń  | Tak   | Nie        |
| Usuwanie grup urządzeń  | Tak   | Nie        |
| Tworzenie reguł          | Tak   | Nie        |
| Reguły aktualizacji          | Tak   | Nie        |
| Usuwanie reguł          | Tak   | Nie        |
| Tworzenie zadań           | Tak   | Nie        |
| Aktualizowanie zarządzania SIM | Tak   | Nie        |

Domyślnie użytkownik, który wdrożył rozwiązanie, ma automatycznie przypisaną rolę **administratora** i jest właścicielem aplikacji Azure Active Directory. Jako właściciel aplikacji można przypisać role do innych użytkowników za pomocą Azure Portal. Jeśli chcesz, aby inny użytkownik przypisał role w rozwiązaniu, należy również ustawić jako właściciela aplikacji w Azure Portal.

> [!NOTE]
> Użytkownik, który wdrożył rozwiązanie, jest **jedyną osobą** , która może ją wyświetlić bezpośrednio po utworzeniu. Aby udzielić innym dostępu do wyświetlania aplikacji jako tylko do odczytu, administratora lub roli niestandardowej, zobacz poniższe wskazówki dotyczące dodawania lub usuwania użytkowników.

## <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników

Jako właściciel aplikacji Azure Active Directory możesz użyć Azure Portal do dodania lub usunięcia użytkownika do roli z rozwiązania do zdalnego monitorowania. W poniższych krokach użyto [aplikacji Azure Active Directory Enterprise](../active-directory/manage-apps/view-applications-portal.md) , która została utworzona podczas wdrażania rozwiązania do monitorowania zdalnego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Sprawdź, czy [użytkownik znajduje się w katalogu](../active-directory/fundamentals/add-users-azure-active-directory.md) , którego używasz. Należy wybrać katalog, który ma być używany podczas logowania do witryny [Microsoft Azure IoT Solution Accelerator](https://www.azureiotsolutions.com/Accelerators) . Nazwa katalogu jest widoczna w prawym górnym rogu [strony](https://www.azureiotsolutions.com/Accelerators).

1. Znajdź **aplikację dla przedsiębiorstw** dla swojego rozwiązania w Azure Portal. Przefiltruj listę, ustawiając **Typ aplikacji** na **wszystkie aplikacje**. Wyszukaj aplikację według nazwy aplikacji. Nazwa aplikacji jest nazwą Twojego rozwiązania do monitorowania zdalnego. Na poniższym zrzucie ekranu nazwa wyświetlana rozwiązania i aplikacji to **contoso-RM4**.

    ![Aplikacja dla przedsiębiorstw](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Sprawdź, czy jesteś właścicielem aplikacji, klikając aplikację, a następnie klikając pozycję **właściciele**. Na poniższym zrzucie ekranu **administrator contoso** jest właścicielem aplikacji **contoso-RM4** :

    ![Zrzut ekranu przedstawia wybraną opcję Zarządzaj właścicielami, która wyświetla właścicieli aplikacji Contoso r m 4.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Jeśli nie jesteś właścicielem, musisz poprosił istniejącego właściciela, aby dodał Cię do listy. Tylko właściciele mogą przypisywać role aplikacji, takie jak **administrator** , lub **tylko do odczytu** dla innych użytkowników.

1. Aby wyświetlić listę użytkowników przypisanych do ról w aplikacji, kliknij pozycję **Użytkownicy i grupy**.

1. Aby dodać użytkownika, kliknij pozycję **+ Dodaj użytkownika**, a następnie kliknij pozycję **Użytkownicy i grupy, brak wybranych** do wybrania użytkownika z katalogu.

1. Aby przypisać użytkownika do roli, kliknij pozycję **Wybierz rolę, brak wybranych** i wybierz dla użytkownika rolę **administratora** lub **tylko do odczytu** . Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz**.

    ![Wybierz rolę](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Użytkownik może teraz uzyskiwać dostęp do rozwiązania do zdalnego monitorowania z uprawnieniami zdefiniowanymi przez rolę.

1. Użytkowników z aplikacji można usunąć na stronie **Użytkownicy i grupy** w portalu.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Rozwiązanie do monitorowania zdalnego obejmuje **administratora** i **tylko do odczytu** ról podczas jego pierwszego wdrożenia. Można dodawać role niestandardowe z różnymi zestawami uprawnień. Aby zdefiniować rolę niestandardową, należy:

- Dodaj nową rolę do aplikacji w Azure Portal.
- Zdefiniuj zasady dla nowej roli w mikrousłudze uwierzytelniania i autoryzacji.
- Zaktualizuj interfejs użytkownika sieci Web rozwiązania.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Zdefiniuj rolę niestandardową w Azure Portal

Poniższe kroki zawierają opis sposobu dodawania roli do aplikacji w Azure Active Directory. W tym przykładzie utworzysz nową rolę, która umożliwia członkom tworzenie, aktualizowanie i usuwanie urządzeń w rozwiązaniu do zdalnego monitorowania.

1. Znajdź w Azure Portal **rejestrację aplikacji** dla Twojego rozwiązania. Nazwa aplikacji jest nazwą Twojego rozwiązania do monitorowania zdalnego. Na poniższym zrzucie ekranu nazwa wyświetlana rozwiązania i aplikacji to **contoso-RM4**.

    ![Rejestrowanie aplikacji](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Wybierz aplikację, a następnie kliknij pozycję **manifest**. Można wyświetlić dwie istniejące [role aplikacji](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) zdefiniowane dla aplikacji:

    ![Wyświetl manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Edytuj manifest, aby dodać rolę o nazwie **ManageDevices** , jak pokazano w poniższym fragmencie kodu. Potrzebujesz unikatowego ciągu, takiego jak identyfikator GUID dla nowego identyfikatora roli. Nowy identyfikator GUID można wygenerować za pomocą usługi, takiej jak [Generator identyfikatora GUID online](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Zapisz zmiany.

### <a name="define-a-policy-for-the-new-role"></a>Zdefiniuj zasady dla nowej roli

Po dodaniu roli do aplikacji w Azure Portal należy zdefiniować zasady w [roles.jsna](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) dla roli, która przypisuje uprawnienia wymagane do zarządzania urządzeniami.

1. Sklonuj repozytorium [mikrousług monitorowania zdalnego z usługi](https://github.com/Azure/remote-monitoring-services-dotnet) GitHub na komputerze lokalnym.

1. Edytuj plik **AUTH/Services/Data/Policy/roles.jsw** pliku, aby dodać zasady dla roli **ManageDevices** , jak pokazano w poniższym fragmencie kodu. Wartości **ID** i **role** muszą być zgodne z definicją roli w manifeście aplikacji z poprzedniej sekcji. Lista dozwolonych akcji umożliwia komuś w roli **ManageDevices** tworzenie, aktualizowanie i usuwanie urządzeń podłączonych do rozwiązania:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Po zakończeniu edycji **usługi/danych/zasad/roles.jsw** pliku należy ponownie skompilować i wdrożyć mikrousługę uwierzytelniania i autoryzacji do akceleratora rozwiązania.

### <a name="how-the-web-ui-enforces-permissions"></a>Jak interfejs użytkownika sieci Web wymusza uprawnienia

Interfejs użytkownika sieci Web używa [mikrousługi uwierzytelniania i autoryzacji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) , aby określić akcje, które użytkownik może wykonać i jakie kontrolki są widoczne w interfejsie użytkownika. Na przykład jeśli rozwiązanie ma nazwę **contoso-RM4**, interfejs użytkownika sieci Web pobiera listę dozwolonych akcji dla bieżącego użytkownika, wysyłając następujące żądanie:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

W przypadku użytkownika o nazwie **Menedżer urządzeń** w roli **ManageDevices** odpowiedź zawiera następujący kod JSON w treści:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Poniższy fragment kodu z [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) w [interfejsie użytkownika sieci Web](https://github.com/Azure/pcs-remote-monitoring-webui/) pokazuje, w jaki sposób uprawnienia są wymuszane deklaratywnie:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Aby uzyskać więcej informacji, zobacz [składniki chronione](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). W pliku [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) można zdefiniować dodatkowe uprawnienia.

### <a name="how-the-microservices-enforce-permissions"></a>Jak są wymuszane uprawnienia dla mikrousług

Mikrousługi sprawdzają także uprawnienia do ochrony przed nieautoryzowanymi żądaniami interfejsu API. Gdy mikrousługa otrzymuje żądanie interfejsu API, dekoduje i weryfikuje token JWT w celu uzyskania identyfikatora użytkownika i uprawnień skojarzonych z rolą użytkownika.

Poniższy fragment kodu z pliku [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) w [mikrousłudze IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)pokazuje, jak są wymuszane uprawnienia:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób implementacji kontroli dostępu opartej na rolach w akceleratorze rozwiązania do zdalnego monitorowania.

Zobacz [Konfigurowanie kontroli dostępu dla eksploratora Time Series Insights,](iot-accelerators-remote-monitoring-rbac-tsi.md) Aby uzyskać informacje na temat zarządzania dostępem do Eksploratora Time Series Insights w akceleratorze rozwiązania do zdalnego monitorowania.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do monitorowania zdalnego, zobacz [Dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
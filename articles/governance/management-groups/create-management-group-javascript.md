---
title: 'Szybki start: tworzenie grupy zarządzania przy użyciu języka JavaScript'
description: W tym przewodniku Szybki start użyjemy języka JavaScript do utworzenia grupy zarządzania w celu zorganizowania zasobów w hierarchię zasobów.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 553133f30bd16543444356e8cda3e70458247b5c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536005"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Szybki start: tworzenie grupy zarządzania przy użyciu języka JavaScript

Grupy zarządzania to kontenery, które ułatwiają zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z usługami [Azure Policy](../policy/overview.md) i kontrolą dostępu opartą [na rolach platformy Azure.](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](overview.md)

Ukończenie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkową konfigurację grup zarządzania.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Przed rozpoczęciem upewnij się, że zainstalowano co najmniej wersję 12Node.js[ programu ](https://nodejs.org/) .

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez przypisanego do niego uprawnienia do zapisu grupy zarządzania, jeśli ochrona hierarchii [nie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) jest włączona. Ta nowa grupa zarządzania staje się elementem [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) podrzędnym głównej grupy zarządzania lub domyślnej grupy zarządzania, a twórca ma przypisanie roli "Właściciel". Usługa grupy zarządzania umożliwia tę możliwość, dzięki czemu przypisania ról nie są wymagane na poziomie głównym. Żaden użytkownik nie ma dostępu do głównej grupy zarządzania po jej utworzeniu. Aby uniknąć przeszkód w znalezieniu administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Konfiguracja aplikacji

Aby umożliwić językowi JavaScript zarządzanie grupami zarządzania, należy skonfigurować środowisko. Ta konfiguracja działa wszędzie tam, gdzie można używać języka JavaScript, w tym [bash na Windows 10](/windows/wsl/install-win10).

1. Skonfiguruj nowy projekt Node.js, uruchamiając następujące polecenie.

   ```bash
   npm init -y
   ```

1. Dodaj odwołanie do modułu yargs.

   ```bash
   npm install yargs
   ```

1. Dodaj odwołanie do modułu Azure Resource Graph.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Dodaj odwołanie do biblioteki uwierzytelniania platformy Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Sprawdź, _package.jsjest_ w wersji 1.1.0 lub wyższej i jest w wersji `@azure/arm-managementgroups`  `@azure/ms-rest-nodeauth` **3.0.5** lub wyższej.

## <a name="create-the-management-group"></a>Tworzenie grupy zarządzania

1. Utwórz nowy plik o _nazwieindex.js_ i wprowadź następujący kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Pamiętaj, aby zastąpić każdy symbol zastępczy tokenu odpowiednio identyfikatorem grupy zarządzania i `<>` _przyjazną nazwą_ grupy  zarządzania.

   Podczas próby uwierzytelnienia skryptu w terminalu jest wyświetlany komunikat podobny do następującego:

   > Aby się zalogować, otwórz stronę w przeglądarce internetowej i wprowadź kod https://microsoft.com/devicelogin FGB56WJUGK w celu uwierzytelnienia.

   Po uwierzytelnieniu w przeglądarce skrypt będzie nadal działać.

Wynikiem utworzenia grupy zarządzania jest dane wyjściowe w konsoli programu .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki z aplikacji, uruchom następujące polecenie.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i sposobu zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami przy użyciu grup zarządzania](./manage.md)

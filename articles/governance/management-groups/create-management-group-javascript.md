---
title: 'Szybki Start: Tworzenie grupy zarządzania przy użyciu języka JavaScript'
description: W tym przewodniku szybki start utworzysz grupę zarządzania w celu zorganizowania zasobów w hierarchii zasobów przy użyciu języka JavaScript.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 25fd93e51c1b49519bb315aab28cdfc44a2a0c58
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593980"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Szybki Start: Tworzenie grupy zarządzania przy użyciu języka JavaScript

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Przed rozpoczęciem upewnij się, że jest zainstalowana co najmniej wersja 12 [Node.js](https://nodejs.org/) .

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Konfiguracja aplikacji

Aby włączyć JavaScript do tworzenia zapytań dotyczących grafu zasobów platformy Azure, należy skonfigurować środowisko. Ta konfiguracja działa wszędzie tam, gdzie można używać języka JavaScript, [w tym bash w systemie Windows 10](/windows/wsl/install-win10).

1. Skonfiguruj nowy projekt Node.js, uruchamiając następujące polecenie.

   ```bash
   npm init -y
   ```

1. Dodaj odwołanie do modułu yargs.

   ```bash
   npm install yargs
   ```

1. Dodaj odwołanie do modułu grafu zasobów platformy Azure.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Dodaj odwołanie do biblioteki uwierzytelniania platformy Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Sprawdź, czy w _package.jsjest w_ `@azure/arm-managementgroups` wersji **1.1.0** lub nowszej i czy `@azure/ms-rest-nodeauth` jest w wersji **3.0.5** lub nowszej.

## <a name="create-the-management-group"></a>Tworzenie grupy zarządzania

1. Utwórz nowy plik o nazwie _index.js_ i wprowadź następujący kod.

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

   Pamiętaj, aby zastąpić każdy `<>` symbol zastępczy tokenu _identyfikatorem grupy zarządzania_ i _przyjazną nazwą grupy zarządzania_.

   Po próbie uwierzytelnienia skryptu w terminalu zostanie wyświetlony komunikat podobny do następującego:

   > Aby się zalogować, użyj przeglądarki sieci Web, aby otworzyć stronę https://microsoft.com/devicelogin i wprowadzić kod FGB56WJUGK do uwierzytelnienia.

   Po uwierzytelnieniu w przeglądarce, skrypt będzie nadal działać.

Wynik tworzenia grupy zarządzania jest wyprowadzany do konsoli programu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki z aplikacji, uruchom następujące polecenie.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)
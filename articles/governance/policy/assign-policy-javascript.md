---
title: 'Szybki Start: nowe przypisanie zasad przy użyciu języka JavaScript'
description: W tym przewodniku szybki start utworzysz przypisanie Azure Policy, aby zidentyfikować niezgodne zasoby.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: d42156e68747b66778ea1cf2001d2b4bfc901bb0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090187"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu języka JavaScript

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które nie są _zgodne_.

Biblioteka JavaScript służy do zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów. W tym przewodniku wyjaśniono, jak używać biblioteki JavaScript do tworzenia przypisania zasad.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- **Node.js**: wymagana jest [Node.js](https://nodejs.org/) wersja 12 lub nowsza.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Dodawanie bibliotek zasad

Aby włączyć obsługę języka JavaScript Azure Policy, należy dodać biblioteki. Te biblioteki działają wszędzie tam, gdzie można używać języka JavaScript, [w tym bash w systemie Windows 10](/windows/wsl/install-win10).

1. Skonfiguruj nowy projekt Node.js, uruchamiając następujące polecenie.

   ```bash
   npm init -y
   ```

1. Dodaj odwołanie do biblioteki yargs.

   ```bash
   npm install yargs
   ```

1. Dodaj odwołanie do bibliotek Azure Policy.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Dodaj odwołanie do biblioteki uwierzytelniania platformy Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Sprawdź, czy w _package.jsjest w_ `@azure/arm-policy` wersji **3.1.0** lub nowszej, `@azure/arm-policyinsights` jest w wersji **3.2.0** lub nowszej i `@azure/ms-rest-nodeauth` ma wersję **3.0.5** lub nowszą.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz **maszyny wirtualne inspekcji, które nie używają definicji Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

1. Utwórz nowy plik o nazwie _policyAssignment.js_ i wprowadź następujący kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Poprzednie polecenia korzystają z następujących informacji:

- **subID** — Identyfikator subskrypcji dla kontekstu uwierzytelniania. Pamiętaj, aby zamienić ją na `{subscriptionId}` subskrypcję.
- **Nazwa** — unikatowa nazwa obiektu przypisania zasad. Powyższy przykład używa _inspekcji-VM-manageddisks_.
- **DisplayName** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz _maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych_.
- **policyDefID** — ścieżka definicji zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem _maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych_.
- **Opis** — dokładniejsze wyjaśnienie tego, czym są zasady lub dlaczego są przypisane do tego zakresu.
- **zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może ona przedziały od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jednym z następujących wzorców:
  - Grupa zarządzania: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subskrypcje: `/subscriptions/{subscriptionId}`
  - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Zasoby `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Po utworzeniu przypisania zasad można zidentyfikować niezgodne zasoby.

1. Utwórz nowy plik o nazwie _policyState.js_ i wprowadź następujący kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Zamień na `{subscriptionId}` subskrypcję, dla której chcesz zobaczyć wyniki zgodności dla przypisania zasad o nazwie "Audit-VM-manageddisks" utworzonej w poprzednich krokach. Aby zapoznać się z listą innych zakresów i sposobów podsumowywania danych, zobacz [PolicyStates *](/javascript/api/@azure/arm-policyinsights/) Methods.

Wyniki powinny wyglądać podobnie do następujących:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Wyniki są zgodne z informacjami wyświetlanymi na karcie **zgodność zasobów** w przypisaniu zasad w widoku Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

- Usuń _maszyny wirtualne inspekcji przypisania zasad bez przypisywania dysków zarządzanych_ za pośrednictwem portalu. Definicja zasad jest wbudowana, dlatego nie istnieje definicja do usunięcia.

- Jeśli chcesz usunąć zainstalowane biblioteki z aplikacji, uruchom następujące polecenie.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej o przypisywaniu definicji zasad w celu sprawdzenia, czy nowe zasoby są zgodne, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
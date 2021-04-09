---
title: Konfigurowanie usługi Azure Functions
description: Ten samouczek przedstawia sposób tworzenia aplikacji funkcji platformy Azure i konfigurowania jej do pracy z dostawcami niestandardowymi platformy Azure
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737323"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure

Dostawca niestandardowy to kontrakt między platformą Azure i punktem końcowym. Dostawcy niestandardowi mogą zmieniać przepływy pracy na platformie Azure. W tym samouczku pokazano, jak skonfigurować aplikację funkcji platformy Azure do pracy jako punkt końcowy dostawcy niestandardowego.

## <a name="create-the-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

> [!NOTE]
> W tym samouczku utworzysz prosty punkt końcowy usługi, który korzysta z aplikacji funkcji platformy Azure. Niestandardowi dostawcy mogą jednak korzystać z dowolnego dostępnego publicznie punktu końcowego. Alternatywy obejmują Azure Logic Apps, API Management platformy Azure i funkcję Web Apps Azure App Service.

Aby rozpocząć pracę z tym samouczkiem, należy najpierw wykonać czynności opisane w samouczku [Tworzenie pierwszej aplikacji funkcji platformy Azure w Azure Portal](../../azure-functions/functions-get-started.md). Ten samouczek tworzy funkcję elementu webhook platformy .NET Core, która może być modyfikowana w Azure Portal. Jest to również podstawa bieżącego samouczka.

## <a name="install-azure-table-storage-bindings"></a>Instalowanie powiązań usługi Azure Table Storage

Aby zainstalować powiązania usługi Azure Table Storage:

1. Przejdź do karty **integracja** dla HttpTrigger.
1. Wybierz pozycję **+ nowe dane wejściowe**.
1. Wybierz pozycję **Azure Table Storage**.
1. Zainstaluj rozszerzenie Microsoft. Azure. WebJobs. Extensions. Storage, jeśli nie jest jeszcze zainstalowane.
1. W polu **Nazwa parametru tabeli** wprowadź **tableStorage**.
1. W polu **Nazwa tabeli** wpisz **myCustomResources**.
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany parametr wejściowy.

![Przegląd dostawcy niestandardowego przedstawiający powiązania tabeli](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizowanie metod HTTP RESTful

Aby skonfigurować funkcję platformy Azure w celu uwzględnienia metod żądania RESTful dostawcy niestandardowego:

1. Przejdź do karty **integracja** dla HttpTrigger.
1. W obszarze **wybrane metody http** wybierz **pozycję Get**, **post**, **delete** i **Put**.

![Przegląd dostawcy niestandardowego przedstawiający metody HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Dodawanie Azure Resource Manager pakietów NuGet

> [!NOTE]
> Jeśli w katalogu projektu brakuje pliku projektu C#, można go dodać ręcznie. Lub zostanie on wyświetlony po zainstalowaniu rozszerzenia Microsoft. Azure. WebJobs. Extensions. Storage w aplikacji funkcji.

Następnie zaktualizuj plik projektu C# w celu uwzględnienia przydatnych bibliotek NuGet. Te biblioteki ułatwiają analizowanie przychodzących żądań od dostawców niestandardowych. Postępuj zgodnie z instrukcjami, aby [dodać rozszerzenia z portalu](../../azure-functions/functions-bindings-register.md) i zaktualizować plik projektu C# w celu uwzględnienia następujących odwołań do pakietów:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Następujący element XML jest przykładowym plikiem projektu C#:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz aplikację funkcji platformy Azure do pracy jako punkt końcowy niestandardowego dostawcy platformy Azure.

Aby dowiedzieć się, jak utworzyć punkt końcowy niestandardowego dostawcy RESTful, zobacz [Samouczek: Tworzenie punktu końcowego niestandardowego dostawcy RESTful](./tutorial-custom-providers-function-authoring.md).
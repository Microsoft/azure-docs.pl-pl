---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663033"
---
## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Narzędzie o nazwie Secret Manager przechowuje poufne dane do pracy programistycznej poza drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym. Wykonaj następujące kroki, aby włączyć używanie Menedżera wpisów tajnych w projekcie ASP.NET Core:

#### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

Przejdź do katalogu głównego projektu, a następnie uruchom następujące polecenie, aby włączyć Magazyn kluczy tajnych w projekcie:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`Do pliku *. csproj* zostaje dodany element zawierający identyfikator GUID:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Przejdź do katalogu głównego projektu, a następnie uruchom następujące polecenie, aby włączyć Magazyn kluczy tajnych w projekcie:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`Do pliku *. csproj* zostaje dodany element zawierający identyfikator GUID:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Otwórz plik *. csproj* .

1. Dodaj `UserSecretsId` element do pliku *. csproj* , jak pokazano poniżej. Możesz użyć tego samego identyfikatora GUID lub można zastąpić tę wartość własną.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Zapisz plik *. csproj* .

---

> [!TIP]
> Aby dowiedzieć się więcej na temat programu Secret Manager, zobacz [bezpieczne przechowywanie wpisów tajnych aplikacji w programie Development w ASP.NET Core](/aspnet/core/security/app-secrets).

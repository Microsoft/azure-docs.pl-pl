---
title: Korzystanie z niestandardowego źródła danych NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Użyj niestandardowego źródła danych NuGet, aby uzyskać dostęp do pakietów NuGet i korzystać z nich w obszarze dev platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
manager: gwallace
ms.openlocfilehash: 77c7b733b12d9b352f9a806cadc0f900b9283ef3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86229281"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Użyj niestandardowego źródła danych NuGet z Azure Dev Spaces

Źródło danych NuGet zapewnia wygodny sposób dołączania źródeł pakietów do projektu. Azure Dev Spaces musi uzyskać dostęp do tego kanału informacyjnego, aby można było poprawnie zainstalować zależności w kontenerze platformy Docker.

## <a name="set-up-a-nuget-feed"></a>Skonfiguruj źródło danych NuGet

Dodaj [odwołanie do pakietu](/nuget/consume-packages/package-references-in-project-files) dla zależności w pliku w `*.csproj` `PackageReference` węźle. Na przykład:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Utwórz plik [NuGet.Config](/nuget/reference/nuget-config-file) w folderze projektu i ustaw `packageSources` `packageSourceCredentials` sekcje i dla źródła danych NuGet. `packageSources`Sekcja zawiera adres URL źródła danych, który musi być dostępny z klastra AKS. `packageSourceCredentials`Są to poświadczenia do uzyskiwania dostępu do źródła danych. Na przykład:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Zaktualizuj wieloetapowe dockerfile, aby skopiować `NuGet.Config` plik do obrazu. Na przykład:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> W systemie Windows, `NuGet.Config` ,, `Nuget.Config` i `nuget.config` wszystkie działają jako prawidłowe nazwy plików. W systemie Linux `NuGet.Config` jest tylko prawidłową nazwą pliku dla tego pliku. Ponieważ Azure Dev Spaces używa platform Docker i Linux, ten plik musi mieć nazwę `NuGet.Config` . Nazwę można naprawić ręcznie lub przez uruchomienie `dotnet restore --configfile nuget.config` .


Jeśli używasz usługi git, nie musisz mieć poświadczeń dla źródła danych NuGet w kontroli wersji. Dodaj `NuGet.Config` do `.gitignore` projektu, tak aby `NuGet.Config` plik nie został dodany do kontroli wersji. Azure Dev Spaces będzie potrzebował tego pliku podczas procesu kompilowania obrazu kontenera, ale domyślnie uwzględnia reguły zdefiniowane w `.gitignore` i `.dockerignore` podczas synchronizacji. Aby zmienić ustawienie domyślne i Zezwalaj Azure Dev Spaces na synchronizowanie `NuGet.Config` pliku, zaktualizuj `azds.yaml` plik:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Jeśli nie korzystasz z usługi git, możesz pominąć ten krok.

Przy następnym uruchomieniu `azds up` lub trafieniu `F5` w programie Visual Studio Code lub Visual Studio, Azure dev Spaces zsynchronizuje `NuGet.Config` plik, użyje go do zainstalowania zależności pakietów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [NuGet i sposobie jego działania](/nuget/what-is-nuget).

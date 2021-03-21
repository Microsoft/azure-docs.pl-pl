---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564966"
---
## <a name="prepare-your-repository"></a>Przygotowywanie repozytorium

Aby uzyskać kompilacje automatyczne z programu Azure App Service Build Server, upewnij się, że katalog główny repozytorium ma poprawne pliki w projekcie.

| Środowisko uruchomieniowe | Pliki katalogu głównego |
|-|-|
| ASP.NET (tylko system Windows) | _*. sln_, _*. csproj_ lub _default. aspx_ |
| ASP.NET Core | _*. sln_ lub _*. csproj_ |
| PHP | _index. php_ |
| Ruby (tylko system Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ lub _package.jsna_ skrypcie uruchamiania |
| Python | _\* . pr_, _requirements.txt_ lub _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. ASP_, _index.htm_, _index.html_ lub _iisstart.htm_ |
| Zadania WebJob | _\<job_name>/Run.\<extension>_ w _obszarze \_ dane aplikacji/zadania/ciągłe_ dla ciągłych zadań WebJob _lub \_ dane/zadania aplikacji/wyzwolone_ dla wyzwalanych zadań WebJob. Aby uzyskać więcej informacji, zobacz [Dokumentacja kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkcje | Zobacz [ciągłe wdrażanie dla Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Aby dostosować wdrożenie, Uwzględnij plik *. Deployment* w katalogu głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożeń](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [niestandardowego skryptu wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Jeśli tworzysz w programie Visual Studio, pozwól [programowi Visual Studio utworzyć repozytorium](/azure/devops/repos/git/creatingrepo?tabs=visual-studio). Projekt jest natychmiast gotowy do wdrożenia przy użyciu narzędzia Git.
>


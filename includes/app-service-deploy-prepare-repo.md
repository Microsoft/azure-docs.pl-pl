---
title: plik dołączany
description: plik dołączany
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67836786"
---
## <a name="prepare-your-repository"></a>Przygotowywanie repozytorium

Aby uzyskać kompilacje automatyczne z programu Azure App Service kudu Build Server, upewnij się, że katalog główny repozytorium ma poprawne pliki w projekcie.

| Środowisko uruchomieniowe | Pliki katalogu głównego |
|-|-|
| ASP.NET (tylko system Windows) | _*. sln_, _*. csproj_lub _default. aspx_ |
| ASP.NET Core | _*. sln_ lub _*. csproj_ |
| PHP | _index. php_ |
| Ruby (tylko system Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_lub _package.jsna_ skrypcie uruchamiania |
| Python | _ \* . pr_, _requirements.txt_lub _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. ASP_, _index.htm_, _index.html_lub _iisstart.htm_ |
| Zadania WebJob | _\<job_name>/Run.\<extension>_ w _obszarze \_ dane aplikacji/zadania/ciągłe_ dla ciągłych zadań WebJob _lub \_ dane/zadania aplikacji/wyzwolone_ dla wyzwalanych zadań WebJob. Aby uzyskać więcej informacji, zobacz [Dokumentacja kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkcje | Zobacz [ciągłe wdrażanie dla Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Aby dostosować wdrożenie, Uwzględnij plik *. Deployment* w katalogu głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożeń](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [niestandardowego skryptu wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Jeśli tworzysz w programie Visual Studio, pozwól [programowi Visual Studio utworzyć repozytorium](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt jest natychmiast gotowy do wdrożenia przy użyciu narzędzia Git.
>


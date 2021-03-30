---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85570137"
---
## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pobrano pliki w pliku ZIP, najpierw Wyodrębnij pliki. Na przykład jeśli pobrano plik ZIP z usługi GitHub, nie można wdrożyć tego pliku jako-is. W witrynie GitHub są dodawane dodatkowe katalogi zagnieżdżone, które nie działają z App Service. 
>

W lokalnym oknie terminalu przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać plik wpisu do aplikacji sieci Web, taki jak _index.html_, _index. php_ i _app.js_. Może również zawierać pliki zarządzania pakietami, takie jak _project.json_, _composer.json_, _package.json_, _bower.json_ i _requirements.txt_.

Jeśli nie chcesz, aby App Service do uruchamiania automatyzacji wdrażania, uruchom wszystkie zadania kompilacji (na przykład,, `npm` , `bower` `gulp` `composer` i `pip` ) i upewnij się, że masz wszystkie pliki potrzebne do uruchomienia aplikacji. Ten krok jest wymagany, jeśli chcesz [uruchomić pakiet bezpośrednio](../articles/app-service/deploy-run-package.md).

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. W przypadku `dotnet` projektów ten folder jest folderem wyjściowym `dotnet publish` polecenia. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 


---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75681055"
---
W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp * \<>-from-Create-Step-zmienną deploymentlocalgiturl* adresem URL zdalnego narzędzia Git zapisanego w obszarze [Tworzenie aplikacji sieci Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy program git Credential Manager poprosi o podanie poświadczeń, upewnij się, że wprowadzono poświadczenia utworzone w obszarze [Konfigurowanie użytkownika wdrożenia](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), a nie poświadczenia, których używasz do logowania się do Azure Portal.

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

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
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102205989"
---
W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zamień na *\<deploymentLocalGitUrl-from-create-step>* adres URL zdalnego narzędzia Git, który został zapisany w obszarze [Tworzenie aplikacji sieci Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy program git Credential Manager poprosi o podanie poświadczeń, upewnij się, że wprowadzono poświadczenia utworzone w obszarze **Konfigurowanie użytkownika wdrożenia**, a nie poświadczenia, których używasz do logowania się do Azure Portal.

```bash
git push azure main
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

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
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998048"
---
W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zamień na *\<deploymentLocalGitUrl-from-create-step>* adres URL zdalnego narzędzia Git, który został zapisany w obszarze [Tworzenie aplikacji sieci Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy program git Credential Manager poprosi o podanie poświadczeń, upewnij się, że wprowadzono poświadczenia utworzone w obszarze **Konfigurowanie użytkownika wdrożenia**, a nie poświadczenia, których używasz do logowania się do Azure Portal.

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

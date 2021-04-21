---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764312"
---
Protokół FTP i lokalne narzędzie Git można wdrożyć w aplikacji internetowej platformy Azure przy użyciu *użytkownika wdrożenia.* Po skonfigurowaniu użytkownika wdrożenia można go używać we wszystkich wdrożeniach platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom [polecenie az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) w Azure Cloud Shell. Zastąp \<username> i nazwą użytkownika wdrożenia i \<password> hasłem. 

- Nazwa użytkownika musi być unikatowa w obrębie platformy Azure, a w przypadku lokalnych wypchnięć Git nie może zawierać \@ symbolu "". 
- Hasło musi mieć co najmniej osiem znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON pokazują hasło jako `null` . Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zanotuj nazwę użytkownika i hasło, które będą stosowane do wdrażania aplikacji internetowych.

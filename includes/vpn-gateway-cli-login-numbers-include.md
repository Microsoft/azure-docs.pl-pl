---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92756028"
---
1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [az login](/cli/azure/) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Aby uzyskać więcej informacji na temat logowania się, zobacz artykuł [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Jeśli masz więcej niż jedną subskrypcję platformy Azure, wyświetl subskrypcje dla konta.

   ```azurecli
   az account list --all
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```

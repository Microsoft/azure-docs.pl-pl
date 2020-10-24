---
author: baanders
description: plik dołączany służący do konfigurowania lokalnego uwierzytelniania DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — bez wprowadzania
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494666"
---
W programie `DefaultAzureCredential` próba wyszukiwania poświadczeń w środowisku lokalnym, takich jak logowanie do platformy Azure, w lokalnym [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub w programie Visual Studio/Visual Studio Code. Oznacza to, że należy **zalogować się lokalnie do platformy Azure** za pomocą jednego z tych mechanizmów, aby skonfigurować poświadczenia dla przykładu.

Jeśli używasz programu Visual Studio lub Visual Studio Code do uruchomienia przykładu kodu, upewnij się, że użytkownik jest zalogowany do tego edytora przy użyciu tych samych poświadczeń platformy Azure, których chcesz użyć w celu uzyskania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

W przeciwnym razie możesz [zainstalować lokalny **interfejs wiersza polecenia platformy Azure**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), uruchomić wiersz poleceń na maszynie, a następnie uruchomić `az login` polecenie, aby zalogować się do konta platformy Azure. Po uruchomieniu przykładu kodu, należy zalogować się automatycznie.
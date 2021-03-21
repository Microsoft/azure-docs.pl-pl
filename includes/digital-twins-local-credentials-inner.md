---
author: baanders
description: plik dołączany służący do konfigurowania lokalnego uwierzytelniania DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — bez wprowadzania
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212822"
---
W programie `DefaultAzureCredential` próba wyszukiwania poświadczeń w środowisku lokalnym, takich jak logowanie do platformy Azure w lokalnym [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub w programie Visual Studio lub Visual Studio Code. Z tego powodu należy *zalogować się lokalnie na platformie Azure* za pomocą jednego z tych mechanizmów, aby skonfigurować poświadczenia dla przykładu.

Jeśli używasz programu Visual Studio lub Visual Studio Code do uruchomienia przykładu kodu, upewnij się, że zalogowano się do tego edytora z tymi samymi poświadczeniami platformy Azure, które mają być używane w celu uzyskania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

W przeciwnym razie możesz [zainstalować lokalny interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), uruchomić wiersz poleceń na maszynie, a następnie uruchomić `az login` polecenie, aby zalogować się do konta platformy Azure. Po zalogowaniu się po uruchomieniu przykładowego kodu należy zalogować się automatycznie.
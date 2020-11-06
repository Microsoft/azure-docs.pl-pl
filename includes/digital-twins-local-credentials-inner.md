---
author: baanders
description: plik dołączany służący do konfigurowania lokalnego uwierzytelniania DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — bez wprowadzania
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329477"
---
W programie `DefaultAzureCredential` próba wyszukiwania poświadczeń w środowisku lokalnym, takich jak logowanie do platformy Azure w lokalnym [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub w programie Visual Studio lub Visual Studio Code. Z tego powodu należy *zalogować się lokalnie na platformie Azure* za pomocą jednego z tych mechanizmów, aby skonfigurować poświadczenia dla przykładu.

Jeśli używasz programu Visual Studio lub Visual Studio Code do uruchomienia przykładu kodu, upewnij się, że zalogowano się do tego edytora z tymi samymi poświadczeniami platformy Azure, które mają być używane w celu uzyskania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

W przeciwnym razie możesz [zainstalować lokalny interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), uruchomić wiersz poleceń na maszynie, a następnie uruchomić `az login` polecenie, aby zalogować się do konta platformy Azure. Po zalogowaniu się po uruchomieniu przykładowego kodu należy zalogować się automatycznie.
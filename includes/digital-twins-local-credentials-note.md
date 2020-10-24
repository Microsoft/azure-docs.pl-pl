---
author: baanders
description: plik dołączany do DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — Uwaga
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494482"
---
>[!NOTE]
> Ten przykład używa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (części `Azure.Identity` biblioteki) do uwierzytelniania użytkowników za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdy zostanie on uruchomiony na komputerze lokalnym. W przypadku tego typu uwierzytelniania Przykładowa próba wyszukiwania poświadczeń platformy Azure w środowisku lokalnym, takich jak logowanie z lokalnego [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub w programie Visual Studio/Visual Studio Code.
>
> Aby uzyskać więcej informacji na temat korzystania z `DefaultAzureCredential` i innych opcji uwierzytelniania, zobacz [*How to: Write App Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).
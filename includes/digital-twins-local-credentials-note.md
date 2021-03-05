---
author: baanders
description: plik dołączany do DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — Uwaga
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211790"
---
>[!NOTE]
> Ten przykład używa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (części `Azure.Identity` biblioteki) do uwierzytelniania użytkowników za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdy zostanie on uruchomiony na komputerze lokalnym. W przypadku tego typu uwierzytelniania Przykładowa próba wyszukiwania poświadczeń platformy Azure w środowisku lokalnym, takich jak logowanie z lokalnego [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub w programie Visual Studio/Visual Studio Code.
>
> Aby uzyskać więcej informacji na temat korzystania z `DefaultAzureCredential` i innych opcji uwierzytelniania, zobacz [*How to: Write App Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).
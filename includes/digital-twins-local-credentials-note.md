---
author: baanders
description: plik dołączany do DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — Uwaga
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473843"
---
>[!NOTE]
> Ten przykład używa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (części `Azure.Identity` biblioteki) do uwierzytelniania użytkowników za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdy zostanie on uruchomiony na komputerze lokalnym. W przypadku tego typu uwierzytelniania Przykładowa próba wyszukiwania poświadczeń platformy Azure w środowisku lokalnym, takich jak logowanie z lokalnego [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub w programie Visual Studio/Visual Studio Code.
>
> Aby uzyskać więcej informacji na temat korzystania z `DefaultAzureCredential` i innych opcji uwierzytelniania, zobacz [*How to: Write App Authentication Code*](../articles/digital-twins/how-to-authenticate-client.md).
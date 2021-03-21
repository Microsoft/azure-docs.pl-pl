---
author: baanders
description: plik dołączany służący do konfigurowania lokalnego uwierzytelniania DefaultAzureCredential w usłudze Azure Digital bliźniaczych reprezentacji Samples — z wprowadzeniem
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473863"
---
### <a name="set-up-local-azure-credentials"></a>Konfigurowanie lokalnych poświadczeń platformy Azure

Ten przykład używa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (części `Azure.Identity` biblioteki) do uwierzytelniania użytkowników za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdy zostanie on uruchomiony na komputerze lokalnym. Aby uzyskać więcej informacji na temat różnych sposobów uwierzytelniania aplikacji klienckiej za pomocą usługi Azure Digital bliźniaczych reprezentacji, zobacz [*How to: Write unauthentication Code*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]
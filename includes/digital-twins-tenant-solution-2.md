---
author: baanders
description: uwzględnij plik opisujący rozwiązanie kodu do ograniczenia między dzierżawami z Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589380"
---
W poniższym przykładzie pokazano, jak ustawić wartość identyfikatora dzierżawy dla `InteractiveBrowserTenantId` w `DefaultAzureCredential` opcjach:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Zrzut ekranu przedstawiający kod przedstawiający metodę DefaultAzureCredentialOptions. Wartość InteractiveBrowserTenantId jest ustawiona na przykładową wartość identyfikatora dzierżawy.":::

Dostępne są podobne opcje, aby ustawić dzierżawę do uwierzytelniania za pomocą Visual Studio i Visual Studio Code. Aby uzyskać więcej informacji na temat dostępnych opcji, zobacz [dokumentację defaultAzureCredentialOptions](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true).
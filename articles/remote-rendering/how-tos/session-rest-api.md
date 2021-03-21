---
title: Interfejs API REST zarządzania sesją
description: Opisuje sposób zarządzania sesjami
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 414dc161d7cba2e89d15c47b8da5abdfb7cb3338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712168"
---
# <a name="use-the-session-management-rest-api"></a>Korzystanie z interfejsu API REST zarządzania sesją

Aby korzystać z funkcji renderowania zdalnego platformy Azure, należy utworzyć *sesję*. Każda sesja odpowiada serwerowi przydzielonym na platformie Azure, z którym urządzenie klienckie może się połączyć. Po połączeniu urządzenia serwer renderuje żądane dane i zachowuje wynik jako strumień wideo. Podczas tworzenia sesji wybierasz [rodzaj serwera](../reference/vm-sizes.md) , na którym chcesz pracować, co określa Cennik. Gdy sesja nie jest już wymagana, powinna zostać zatrzymana. Jeśli nie zostanie zatrzymana ręcznie, zostanie ona ZAMKNIĘTA automatycznie po wygaśnięciu *czasu dzierżawy* sesji.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Informacje o interfejsie API REST można znaleźć [tutaj](/rest/api/mixedreality/2021-01-01preview/remoterendering) [i definicjach](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)struktury Swagger.
Udostępniamy skrypt programu PowerShell w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) w folderze *scripts* o nazwie *RenderingSession.ps1*, który demonstruje korzystanie z naszej usługi. Skrypt i jego konfigurację są opisane tutaj: [przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md).
Udostępniamy również zestawy SDK dla [platform .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java i Python.

> [!IMPORTANT]
> Opóźnienie jest ważnym czynnikiem podczas korzystania z renderowania zdalnego. Najlepsze środowisko tworzenia sesji w regionie, który znajduje się najbliżej Ciebie. [Test opóźnienia platformy Azure](https://www.azurespeed.com/Azure/Latency) może służyć do określenia, który region jest najbliższy.

> [!IMPORTANT]
> Aby urządzenie klienckie łączyło się z sesją renderowania, wymagany jest zestaw SDK środowiska uruchomieniowego ARR. Te zestawy SDK są dostępne w [architekturze .NET](/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) i [C++](/cpp/api/remote-rendering/). Oprócz nawiązywania połączenia z usługą te zestawy SDK mogą być również używane do uruchamiania i zatrzymywania sesji.

## <a name="next-steps"></a>Następne kroki

* [Używanie interfejsów API frontonu platformy Azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
---
title: Omówienie usługi Azure Media Services | Microsoft Docs
description: Microsoft Azure Media Services to oparta na chmurze rozszerzona platforma, która umożliwia deweloperom tworzenie skalowalnych aplikacji do dostarczania multimediów i zarządzania nimi. Ten artykuł zawiera omówienie Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008240"
---
# <a name="azure-media-services-overview"></a>Omówienie usługi Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 3](../latest/media-services-overview.md)
> * [Wersja 2](media-services-overview.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) to oparta na chmurze rozszerzalna platforma, która umożliwia deweloperom tworzenie skalowalnych aplikacji do dostarczania multimediów i zarządzania nimi. Usługi Media Services są oparte na interfejsach API REST, które umożliwiają bezpieczne przekazywanie, przechowywanie, kodowanie i tworzenie pakietów zawartości wideo lub audio na potrzeby transmisji strumieniowej na żądanie i na żywo do różnych klientów (np. odbiorników TV, komputerów i urządzeń przenośnych).

Korzystając wyłącznie z usługi Media Services, można tworzyć kompleksowe przepływy pracy. W przypadku niektórych części przepływu pracy można użyć składników innych firm. Na przykład kodowanie można wykonać przy użyciu kodera innego producenta. Natomiast przekazywanie, zabezpieczanie, tworzenie pakietów i dostarczanie można realizować za pomocą usługi Media Services. Możliwe jest przesyłanie strumieniowe zawartości na żywo lub dostarczanie zawartości na żądanie. 


## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania Azure Media Services i nie można używać Media Services lub żadnej usługi platformy Azure w sposób naruszający prawa innych osób lub mogą być szkodliwe dla innych osób.

Przed przekazaniem dowolnych filmów wideo/obrazów do Media Services należy dysponować wszystkimi odpowiednimi prawami do używania wideo/obrazu, w tym, gdzie jest to wymagane przez prawo, do użycia, przetwarzania i przechowywania danych w usłudze Media Services i na platformie Azure. Niektóre jurysdykcje mogą nakładać specjalne wymagania prawne dotyczące zbierania, przetwarzania online i przechowywania określonych kategorii danych, takich jak dane biometryczne. Przed rozpoczęciem korzystania z Media Services i platformy Azure do przetwarzania i przechowywania danych z zastrzeżeniem szczególnych wymagań prawnych należy zapewnić zgodność z wszelkimi wymaganiami prawnymi, które mogą mieć zastosowanie do Ciebie.

Aby dowiedzieć się więcej o zgodności, prywatności i zabezpieczeniach w Media Services odwiedź [Centrum zaufania](https://www.microsoft.com/trust-center/?rtc=1)firmy Microsoft. W przypadku zobowiązań związanych z ochroną prywatności firmy Microsoft, obsługi i przechowywania danych, w tym sposobu usuwania danych, zapoznaj się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [postanowieniami dotyczącymi usług online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Korzystając z Media Services, wyrażasz zgodę na powiązanie plików OST, DPA i zasad zachowania poufności informacji.
 
## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia korzystania z usługi Azure Media Services potrzebne są:

* Konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com).
* Konto usługi Azure Media Services. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](media-services-portal-create-account.md).
* (Opcjonalnie) Konfigurowanie środowiska deweloperskiego. Wybierz platformę .NET lub interfejs API REST dla środowiska deweloperskiego. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie środowiska](media-services-dotnet-how-to-use.md).

    Dowiedz się także, jak [programowo ustanawiać połączenie z interfejsem API usługi AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Punkt końcowy przesyłania strumieniowego (standardowy lub Premium) w stanie uruchomionym.  Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>Zestawy SDK i narzędzia

W celu tworzenia rozwiązań Media Services można użyć następujących elementów:

* [Interfejs API REST usługi Media Services](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Jeden z dostępnych zestawów SDK klienta:
    * Zestaw Azure Media Services SDK dla platformy .NET
    
        * [Pakiet NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Kod źródłowy usługi GitHub](https://github.com/Azure/azure-sdk-for-media-services)
    * [Zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java),
    * [Zestaw Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services dla środowiska Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Jest to wersja zestawu Node.js SDK firmy innej niż Microsoft. Jest ona opracowywana przez społeczność i aktualnie nie obsługuje 100% interfejsów API usług AMS.)
* Istniejące narzędzia:
    * [Witryna Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) to aplikacja Winforms/C# dla systemu Windows)

> [!NOTE]
> Aby uzyskać najnowszą wersję zestawu SDK języka Java i zacząć programować w języku Java, zobacz [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](./media-services-java-how-to-use.md). <br/>
> Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Przykłady kodu

Wiele przykładów kodu możesz znaleźć w galerii **Przykłady kodu platformy Azure**: [Przykłady kodu usługi Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Pojęcia

Pojęcia związane z usługą Azure Media Services zostały przedstawione w temacie [Pojęcia](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Obsługiwane scenariusze i dostępność usługi Media Services w centrach danych

Aby uzyskać więcej informacji o typowych scenariuszach platformy Azure, zobacz [scenariusze AMS](scenarios-and-availability.md).
Informacje o dostępności regionalnej można znaleźć w temacie [dostępność usługi Media Service](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)

Aby uzyskać więcej informacji, zobacz temat [Umowy dotyczące poziomu usług platformy Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Pomoc techniczna

[Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/options/) zapewnia opcje wsparcia technicznego dla platformy Azure, w tym dla usługi Media Services.

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

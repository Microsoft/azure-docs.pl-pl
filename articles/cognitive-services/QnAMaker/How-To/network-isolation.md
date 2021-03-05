---
title: Izolacja sieciowa
description: Użytkownicy mogą ograniczyć dostęp publiczny do zasobów QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203372"
---
# <a name="recommended-settings-for-network-isolation"></a>Zalecane ustawienia izolacji sieci

Należy postępować zgodnie z poniższymi krokami, aby ograniczyć dostęp publiczny do zasobów QnA Maker. Ochrona Cognitive Services zasobów przed dostępem publicznym przez [skonfigurowanie sieci wirtualnej](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Ograniczanie dostępu do zasobu Wyszukiwanie poznawcze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Konfigurowanie Wyszukiwanie poznawcze jako prywatnego punktu końcowego wewnątrz sieci wirtualnej. Gdy wystąpienie wyszukiwania jest tworzone podczas tworzenia zasobu QnA Maker, można wymusić Wyszukiwanie poznawcze, aby obsługiwał konfigurację prywatnego punktu końcowego utworzoną całkowicie w sieci wirtualnej klienta.

Wszystkie zasoby muszą zostać utworzone w tym samym regionie, aby można było użyć prywatnego punktu końcowego.

* Zasób QnA Maker
* nowy zasób Wyszukiwanie poznawcze
* nowy zasób Virtual Network

Wykonaj następujące kroki w [Azure Portal](https://portal.azure.com):

1. Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Utwórz nowy zasób Wyszukiwanie poznawcze z łącznością punktu końcowego (Data) ustawioną na wartość _Private_. Utwórz zasób w tym samym regionie, co zasób QnA Maker utworzony w kroku 1. Dowiedz się więcej na temat [tworzenia zasobu wyszukiwanie poznawcze](../../../search/search-create-service-portal.md), a następnie użyj tego linku, aby przejść bezpośrednio do [strony tworzenia zasobu](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Utwórz nowy [zasób Virtual Network](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Skonfiguruj sieć wirtualną w ramach zasobu usługi App Service utworzonego w kroku 1 tej procedury. Utwórz nowy wpis DNS w sieci wirtualnej dla nowego zasobu Wyszukiwanie poznawcze utworzonego w kroku 2. na Wyszukiwanie poznawcze adres IP.
5. [Skojarz usługę App Service z nowym zasobem wyszukiwanie poznawcze](../how-to/set-up-qnamaker-service-azure.md) utworzonym w kroku 2. Następnie można usunąć oryginalny zasób Wyszukiwanie poznawcze utworzony w kroku 1.
    
W [portalu QNA Maker](https://www.qnamaker.ai/)Utwórz pierwszą bazę wiedzy.

#  <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

[Utwórz prywatne punkty końcowe](../reference-private-endpoint.md) dla zasobu Azure Search.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Ogranicz dostęp do App Service (środowisko uruchomieniowe QnA)

Możesz dodać adresy IP do dozwolonych usługi App Service, aby ograniczyć dostęp lub skonfigurować App Service Environemnt do hostowania QnA Maker App Service.

#### <a name="add-ips-to-app-service-allowlist"></a>Dodaj adresy IP do App Service dozwolonych

1. Zezwalaj na ruch tylko z Cognitive Services adresów IP. Są one już zawarte w tagu usługi `CognitiveServicesManagement` . Jest to wymagane do tworzenia interfejsów API (Utwórz/zaktualizuj KB), aby odpowiednio wywoływać usługę App Service i zaktualizować usługę Azure Search. Zapoznaj się z [dodatkowymi informacjami na temat tagów usługi.](../../../virtual-network/service-tags-overview.md)
2. Upewnij się, że można również zezwolić na inne punkty wejścia, takie jak Azure Bot Service, QnA Maker Portal itd., aby uzyskać dostęp do interfejsu API przewidywania "GenerateAnswer".
3. Wykonaj następujące kroki, aby dodać zakresy adresów IP do dozwolonych:

   1. Pobierz [zakresy adresów IP dla wszystkich tagów usługi](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Wybierz adresy IP elementu "CognitiveServicesManagement".
   3. Przejdź do sekcji Sieć zasobu App Service i kliknij opcję "Konfiguruj ograniczenie dostępu", aby dodać adresy IP do dozwolonych.

    ![wyjątki portów przychodzących](../media/inbound-ports.png)

Mamy również zautomatyzowany skrypt do wykonania tych samych App Service. [Skrypt programu PowerShell można znaleźć w celu skonfigurowania usługi dozwolonych](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) w witrynie GitHub. Musisz wprowadzić identyfikator subskrypcji, grupę zasobów i rzeczywistą App Serviceową nazwę jako parametry skryptu. Uruchomienie skryptu spowoduje automatyczne dodanie adresów IP do App Service dozwolonych.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurowanie App Service Environment do hostowania QnA Maker App Service
    
App Service Environment (ASE) może służyć do hostowania QnA Maker App Service. Wykonaj poniższe kroki:

1. Utwórz App Service Environment i oznacz go jako "zewnętrzny". Aby uzyskać instrukcje, postępuj zgodnie z [samouczkiem](../../../app-service/environment/create-external-ase.md) .
2.  Utwórz usługę App Service w ramach App Service Environment.
    1. Sprawdź konfigurację usługi App Service i Dodaj element "PrimaryEndpointKey" jako ustawienie aplikacji. Wartość parametru "PrimaryEndpointKey" powinna być równa " \<app-name\> -PrimaryEndpointKey". Nazwa aplikacji jest definiowana w adresie URL usługi App Service. Na przykład jeśli adres URL usługi App Service to "mywebsite.myase.p.azurewebsite.net", nazwa aplikacji to "Witryna sieci Web". W takim przypadku wartość parametru "PrimaryEndpointKey" powinna być równa "PrimaryEndpointKey".
    2. Utwórz usługę Azure Search.
    3. Upewnij się, że ustawienia Azure Search i aplikacji zostały odpowiednio skonfigurowane. 
          Postępuj zgodnie z tym [samouczkiem](../reference-app-service.md?tabs=v1#app-service).
3.  Aktualizowanie sieciowej grupy zabezpieczeń skojarzonej z App Service Environment
    1. Zaktualizuj wstępnie utworzone reguły zabezpieczeń dla ruchu przychodzącego zgodnie z wymaganiami.
    2. Dodaj nową regułę zabezpieczeń dla ruchu przychodzącego ze źródłem jako "tag usługi" i tag usługi źródłowej jako "CognitiveServicesManagement".
4.  Utwórz wystąpienie QnA Maker poznawczej usługi (Microsoft. CognitiveServices/accounts) przy użyciu Azure Resource Manager, gdzie punkt końcowy QnA Maker powinien być ustawiony App Service na punkt końcowy utworzony powyżej (https://mywebsite.myase.p.azurewebsite.net).
    
---

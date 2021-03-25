---
title: Izolacja sieciowa
description: Użytkownicy mogą ograniczyć dostęp publiczny do zasobów QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c2fad19bd84418d41aca1b2e0770eaa3cde488b0
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043363"
---
# <a name="recommended-settings-for-network-isolation"></a>Zalecane ustawienia izolacji sieci

Należy postępować zgodnie z poniższymi instrukcjami, aby ograniczyć dostęp publiczny do zasobów QnA Maker. Ochrona Cognitive Services zasobów przed dostępem publicznym przez [skonfigurowanie sieci wirtualnej](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Ogranicz dostęp do App Service (środowisko uruchomieniowe QnA)

Możesz dodać adresy IP do listy dozwolonych w usłudze App Service, aby ograniczyć dostęp lub skonfigurować App Service Environment do hostowania QnA Maker App Service.

#### <a name="add-ips-to-app-service-allow-list"></a>Dodaj adresy IP do listy dozwolonych App Service

1. Zezwalaj na ruch tylko z Cognitive Services adresów IP. Są one już zawarte w tagu usługi `CognitiveServicesManagement` . Jest to wymagane do tworzenia interfejsów API (Utwórz/zaktualizuj KB), aby odpowiednio wywoływać usługę App Service i zaktualizować usługę Azure Search. Zapoznaj się z [dodatkowymi informacjami na temat tagów usługi.](../../../virtual-network/service-tags-overview.md)
2. Upewnij się, że można również zezwolić na inne punkty wejścia, takie jak Azure Bot Service, QnA Maker Portal itd., aby uzyskać dostęp do interfejsu API przewidywania "GenerateAnswer".
3. Wykonaj następujące kroki, aby dodać zakresy adresów IP do listy dozwolonych:

   1. Pobierz [zakresy adresów IP dla wszystkich tagów usługi](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Wybierz adresy IP elementu "CognitiveServicesManagement".
   3. Przejdź do sekcji Sieć zasobu App Service i kliknij opcję "Konfiguruj ograniczenie dostępu", aby dodać adresy IP do listy dozwolonych.

Mamy również zautomatyzowany skrypt do wykonania tych samych App Service. [Skrypt programu PowerShell umożliwia skonfigurowanie listy dozwolonych](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) w witrynie GitHub. Musisz wprowadzić identyfikator subskrypcji, grupę zasobów i rzeczywistą App Serviceową nazwę jako parametry skryptu. Uruchomienie skryptu spowoduje automatyczne dodanie adresów IP do listy dozwolonych App Service.

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
       
    ![wyjątki portów przychodzących](../media/inbound-ports.png)

4.  Utwórz wystąpienie QnA Maker poznawczej usługi (Microsoft. CognitiveServices/accounts) przy użyciu Azure Resource Manager, gdzie punkt końcowy QnA Maker powinien być ustawiony App Service na punkt końcowy utworzony powyżej (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Ograniczanie dostępu do zasobu Wyszukiwanie poznawcze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Wystąpienie Wyszukiwanie poznawcze może być izolowane za pośrednictwem prywatnego punktu końcowego po utworzeniu zasobów QnA Maker. Połączenia prywatnego punktu końcowego wymagają sieci wirtualnej, za pomocą której można uzyskać dostęp do wystąpienia Search Service. 

Jeśli QnA Maker App Service jest ograniczone przy użyciu App Service Environment, Użyj tej samej sieci wirtualnej, aby utworzyć połączenie prywatnego punktu końcowego z wystąpieniem Wyszukiwanie poznawcze. Utwórz nowy wpis DNS w sieci wirtualnej, aby zamapować punkt końcowy Wyszukiwanie poznawcze na Wyszukiwanie poznawcze adres IP prywatnego punktu końcowego. 

Jeśli App Service Environment nie jest używany dla App Service QnAMaker, najpierw należy utworzyć nowy zasób sieci wirtualnej, a następnie utworzyć połączenie prywatnego punktu końcowego z wystąpieniem Wyszukiwanie poznawcze. W takim przypadku App Service QnA Maker należy [zintegrować z siecią wirtualną](../../../app-service/web-sites-integrate-with-vnet.md) , aby połączyć się z wystąpieniem wyszukiwanie poznawcze. 

#  <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

[Utwórz prywatne punkty końcowe](../reference-private-endpoint.md) dla zasobu Azure Search.

---
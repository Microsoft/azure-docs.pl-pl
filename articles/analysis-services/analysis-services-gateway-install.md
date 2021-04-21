---
title: Instalowanie lokalnej bramy danych dla Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak zainstalować i skonfigurować lokalną bramę danych w celu nawiązywania połączenia z lokalnymi źródłami danych z Azure Analysis Services serwera.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43e5b64d06a6ec145876798b2e0da6499ab94bfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769233"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalowanie i konfigurowanie bramy danych lokalnych

Lokalna brama danych jest wymagana, gdy co najmniej jeden serwer Azure Analysis Services w tym samym regionie łączy się z lokalnymi źródłami danych.  Chociaż instalowana brama jest taka sama, jak używana przez inne usługi, takie jak Power BI, Power Apps i Logic Apps, podczas instalowania w programie Azure Analysis Services należy wykonać kilka dodatkowych kroków. Ten artykuł instalacji jest specyficzny dla **Azure Analysis Services**. 

Aby dowiedzieć się więcej na temat Azure Analysis Services z bramą, zobacz Connecting to on-premises data sources (Nawiązywanie połączenia z [lokalnymi źródłami danych).](analysis-services-gateway.md) Aby dowiedzieć się więcej o zaawansowanych scenariuszach instalacji i bramie, zobacz dokumentację lokalnych [bram danych](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Wymagania wstępne

**Minimalne wymagania:**

* Program .NET Framework 4.5
* 64-bitowa wersja systemu Windows 8 / Windows Server 2012 R2 (lub nowszy)

**Zalecane:**

* Procesor 8-rdzeniowy
* 8 GB pamięci
* 64-bitowa wersja systemu Windows 8 / Windows Server 2012 R2 (lub nowszy)

**Ważne kwestie:**

* Podczas instalacji podczas rejestrowania bramy na platformie Azure jest wybierany domyślny region subskrypcji. Możesz wybrać inną subskrypcję i region. Jeśli masz serwery w więcej niż jednym regionie, musisz zainstalować bramę dla każdego regionu. 
* Bramy nie można zainstalować na kontrolerze domeny.
* Na jednym komputerze można zainstalować tylko jedną bramę.
* Zainstaluj bramę na komputerze, który pozostaje w trybie uśpienia i nie jest w stanie uśpienia.
* Nie instaluj bramy na komputerze z połączeniem bezprzewodowym tylko z siecią. Wydajność może zostać zmniejszona.
* Podczas instalowania bramy konto użytkownika, za pomocą których zalogowano się na komputerze, musi mieć uprawnienia Zaloguj się jako usługa. Po zakończeniu instalacji usługa lokalnej bramy danych używa konta NT SERVICE\PBIEgwService do logowania się jako usługa. Podczas instalacji lub w usługach można określić inne konto po zakończeniu instalacji. Upewnij zasady grupy ustawienia zezwalają zarówno na konto, za pomocą których zalogowano się podczas instalacji, jak i na konto usługi, które wybierzesz, mają uprawnienia Zaloguj się jako usługa.
* Zaloguj się do platformy Azure przy użyciu konta w usłudze Azure AD dla tej [samej](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) dzierżawy, w ramach subskrypcji, w ramach których rejestrujesz bramę. Konta usługi Azure B2B (gościa) nie są obsługiwane podczas instalowania i rejestrowania bramy.
* Jeśli źródła danych znajdują się w usłudze Azure Virtual Network (VNet), należy skonfigurować właściwość [serwera AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a>Pobierz

 [Pobieranie bramy](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalowanie

1. Uruchom instalatora.

2. Wybierz **pozycję Lokalna brama danych.**

   ![Wybierz pozycję](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Wybierz lokalizację, zaakceptuj warunki, a następnie kliknij pozycję **Zainstaluj.**

   ![Lokalizacja instalacji i postanowienia licencyjne](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Zaloguj się do platformy Azure. Konto musi znajdować się w Azure Active Directory. To konto jest używane przez administratora bramy. Konta usługi Azure B2B (gościa) nie są obsługiwane podczas instalowania i rejestrowania bramy.

   ![Logowanie do platformy Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Jeśli zalogujesz się przy użyciu konta domeny, zostanie ono zamapowane na konto organizacyjne w usłudze Azure AD. Twoje konto organizacyjne jest używane jako administrator bramy.

## <a name="register"></a>Zarejestruj

Aby utworzyć zasób bramy na platformie Azure, należy zarejestrować wystąpienie lokalne zainstalowane z usługą bramy w chmurze. 

1.  Wybierz **pozycję Zarejestruj nową bramę na tym komputerze.**

    ![Zrzut ekranu przedstawiający opcję Zarejestruj nową bramę na tym komputerze.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Wpisz nazwę i klucz odzyskiwania bramy. Domyślnie brama używa domyślnego regionu subskrypcji. Jeśli musisz wybrać inny region, wybierz pozycję **Zmień region**.

    > [!IMPORTANT]
    > Zapisz klucz odzyskiwania w bezpiecznym miejscu. Klucz odzyskiwania jest wymagany w celu przejęcia, zmigrowania lub przywrócenia bramy. 

   ![Zarejestruj](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Tworzenie zasobu bramy platformy Azure

Po zainstalowaniu i zarejestrowaniu bramy musisz utworzyć zasób bramy na platformie Azure. Zaloguj się do platformy Azure przy użyciu tego samego konta, które było używane podczas rejestrowania bramy.

1. W Azure Portal kliknij pozycję **Utwórz zasób, wyszukaj** pozycję **Lokalna brama** danych, a następnie kliknij pozycję **Utwórz.**

   ![Tworzenie zasobu bramy](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. W **jęz. Tworzenie** bramy połączeń wprowadź następujące ustawienia:

   * **Nazwa:** wprowadź nazwę zasobu bramy. 

   * **Subskrypcja:** wybierz subskrypcję platformy Azure, która ma być skojarzona z zasobem bramy. 
   
     Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania.

   * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.

   * **Lokalizacja:** wybierz region, w którym zarejestrowano bramę.

   * **Nazwa instalacji:** jeśli instalacja bramy nie została jeszcze wybrana, wybierz bramę zainstalowaną na komputerze i zarejestrowaną. 

     Gdy wszystko będzie gotowe, kliknij pozycję **Utwórz.**

## <a name="connect-gateway-resource-to-server"></a>Łączenie zasobu bramy z serwerem

> [!NOTE]
> Nawiązywanie połączenia z zasobem bramy w innej subskrypcji niż serwer nie jest obsługiwane w portalu, ale jest obsługiwane przy użyciu programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W przeglądzie Azure Analysis Services kliknij pozycję **Lokalna brama danych.**

   ![Łączenie serwera z bramą](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Na **stronie Wybierz lokalną bramę danych** do nawiązania połączenia wybierz zasób bramy, a następnie kliknij pozycję Połącz **wybraną bramę.**

   ![Łączenie serwera z zasobem bramy](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Jeśli brama nie jest wyświetlana na liście, serwer prawdopodobnie nie znajduje się w tym samym regionie co region określony podczas rejestrowania bramy.

    Po pomyślnym nawiązaniu połączenia między serwerem i zasobem bramy stan będzie miał stan **Połączono.**


    ![Powodzenie połączenia serwera z zasobem bramy](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj [get-AzResource,](/powershell/module/az.resources/get-azresource) aby uzyskać resourceID bramy. Następnie połącz zasób bramy z istniejącym lub nowym serwerem, określając wartość **-GatewayResourceID** w pliku [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) lub [New-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver)

Aby uzyskać identyfikator zasobu bramy:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Aby skonfigurować istniejący serwer:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

To wszystko. Jeśli musisz otworzyć porty lub rozwiązać problemy, zapoznaj się z [tematem Lokalna brama danych.](analysis-services-gateway.md)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie usługami Analysis Services](analysis-services-manage.md)   
* [Pobieranie danych z usługi Azure Analysis Services](analysis-services-connect.md)   
* [Używanie bramy dla źródeł danych w usłudze Azure Virtual Network](analysis-services-vnet-gateway.md)

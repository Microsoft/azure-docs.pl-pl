---
title: Zainstaluj lokalną bramę danych dla Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak zainstalować i skonfigurować lokalną bramę danych w celu nawiązania połączenia z lokalnymi źródłami danych z serwera Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062153"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalowanie i konfigurowanie bramy danych lokalnych

Lokalna Brama danych jest wymagana, gdy co najmniej jeden serwer Azure Analysis Services w tym samym regionie nawiązuje połączenie z lokalnymi źródłami danych.  Gdy instalowana Brama jest taka sama jak używana przez inne usługi, takie jak Power BI, aplikacje zaawansowane i Logic Apps, podczas instalowania programu Azure Analysis Services należy wykonać kilka dodatkowych kroków. Ten artykuł instalacyjny jest specyficzny dla **Azure Analysis Services**. 

Aby dowiedzieć się więcej o tym, jak Azure Analysis Services współpracuje z bramą, zobacz [nawiązywanie połączenia z lokalnymi źródłami danych](analysis-services-gateway.md). Aby dowiedzieć się więcej o scenariuszach instalacji zaawansowanej i ogólnie dostępnej bramie, zobacz [dokumentację lokalnych bram danych](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Wymagania wstępne

**Minimalne wymagania:**

* Program .NET Framework 4.5
* 64 — bitowa wersja systemu Windows 8/Windows Server 2012 R2 (lub nowszego)

**Rekomendowane**

* Procesor 8-rdzeniowy
* 8 GB pamięci
* 64 — bitowa wersja systemu Windows 8/Windows Server 2012 R2 (lub nowszego)

**Ważne zagadnienia:**

* Podczas konfigurowania bramy na platformie Azure podczas instalacji jest wybierany region domyślny dla subskrypcji. Możesz wybrać inną subskrypcję i region. W przypadku serwerów w więcej niż jednym regionie należy zainstalować bramę dla każdego regionu. 
* Nie można zainstalować bramy na kontrolerze domeny.
* Na pojedynczym komputerze można zainstalować tylko jedną bramę.
* Zainstaluj bramę na komputerze, który pozostanie włączony i nie przechodzi do trybu uśpienia.
* Nie należy instalować bramy na komputerze z połączeniem bezprzewodowym tylko z siecią. Wydajność może być mniejsza.
* Podczas instalacji bramy konto użytkownika, które jest zalogowany na komputerze, musi mieć uprawnienia Logowanie jako usługa. Po zakończeniu instalacji usługa lokalnej bramy danych używa konta NT SERVICE\PBIEgwService do logowania się jako usługa. Można określić inne konto podczas instalacji lub usług po zakończeniu instalacji. Upewnij się, że ustawienia zasady grupy umożliwiają zarówno konto, za pomocą którego zalogowano się podczas instalacji, jak i wybrane konto usługi ma uprawnienia Logowanie jako usługa.
* Zaloguj się do platformy Azure przy użyciu konta w usłudze Azure AD dla tej samej [dzierżawy](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) , która jest zarejestrowana w ramach subskrypcji. Konta B2B (gość) platformy Azure nie są obsługiwane podczas instalowania i rejestrowania bramy.
* Jeśli źródła danych znajdują się w usłudze Azure Virtual Network (VNet), należy skonfigurować właściwość serwera [AlwaysUseGateway](analysis-services-vnet-gateway.md) .

## <a name="download"></a><a name="download"></a>Pliki do pobrania

 [Pobierz bramę](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Instalowanie

1. Uruchom Instalatora.

2. Wybierz pozycję **lokalna Brama danych**.

   ![Wybierz](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Wybierz lokalizację, zaakceptuj warunki, a następnie kliknij przycisk **Instaluj**.

   ![Lokalizacja instalacji i postanowienia licencyjne](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Zaloguj się do platformy Azure. Konto musi znajdować się w Azure Active Directory Twojej dzierżawy. To konto jest używane przez administratora bramy. Konta B2B (gość) platformy Azure nie są obsługiwane podczas instalowania i rejestrowania bramy.

   ![Logowanie do platformy Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Jeśli zalogujesz się przy użyciu konta domeny, zostanie ono zamapowane na konto organizacyjne w usłudze Azure AD. Konto organizacyjne jest używane jako Administrator bramy.

## <a name="register"></a><a name="register"></a>Zarejestruj

Aby utworzyć zasób bramy na platformie Azure, musisz zarejestrować lokalne wystąpienie zainstalowane w usłudze bramy w chmurze. 

1.  Wybierz pozycję **zarejestruj nową bramę na tym komputerze**.

    ![Zarejestruj](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Wpisz nazwę i klucz odzyskiwania dla bramy. Domyślnie brama używa domyślnego regionu subskrypcji. Jeśli musisz wybrać inny region, wybierz pozycję **Zmień region**.

    > [!IMPORTANT]
    > Zapisz klucz odzyskiwania w bezpiecznym miejscu. Klucz odzyskiwania jest wymagany w celu przejęcia, przeprowadzenia migracji lub przywrócenia bramy. 

   ![Zarejestruj](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Tworzenie zasobu bramy platformy Azure

Po zainstalowaniu i zarejestrowaniu bramy musisz utworzyć zasób bramy na platformie Azure. Zaloguj się do platformy Azure przy użyciu tego samego konta, które zostało użyte podczas rejestrowania bramy.

1. W Azure Portal kliknij pozycję **Utwórz zasób**, a następnie wyszukaj pozycję **lokalna Brama danych**, a następnie kliknij pozycję **Utwórz**.

   ![Tworzenie zasobu bramy](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. W obszarze **Utwórz bramę połączenia**wprowadź następujące ustawienia:

   * **Nazwa**: Wprowadź nazwę zasobu bramy. 

   * **Subskrypcja**: wybierz subskrypcję platformy Azure, która ma zostać skojarzona z zasobem bramy. 
   
     Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania się.

   * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.

   * **Lokalizacja**: Wybierz region, w którym zarejestrowano bramę.

   * **Nazwa instalacji**: Jeśli instalacja bramy nie została jeszcze wybrana, wybierz bramę zainstalowaną na komputerze i zarejestrowano. 

     Gdy skończysz, kliknij przycisk **Utwórz**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Łączenie serwerów z zasobem bramy

1. Na serwerze Azure Analysis Services przegląd kliknij pozycję **lokalna Brama danych**.

   ![Połącz serwer z bramą](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. W obszarze **Wybierz lokalną bramę danych do nawiązania połączenia**wybierz zasób bramy, a następnie kliknij pozycję **Połącz wybraną bramę**.

   ![Połącz serwer z zasobem bramy](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Jeśli Brama nie znajduje się na liście, serwer prawdopodobnie nie znajduje się w tym samym regionie, co region określony podczas rejestrowania bramy.

    Po pomyślnym nawiązaniu połączenia między serwerem a zasobem bramy zostanie wyświetlony stan **połączone**.


    ![Pomyślne połączenie serwera z bramą](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Gotowe. Jeśli konieczne jest otwarcie portów lub Rozwiązywanie problemów, należy sprawdzić, czy [lokalna Brama danych](analysis-services-gateway.md)jest Wyewidencjonuj.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie usługami Analysis Services](analysis-services-manage.md)   
* [Pobierz dane z Azure Analysis Services](analysis-services-connect.md)   
* [Używanie bramy dla źródeł danych w usłudze Azure Virtual Network](analysis-services-vnet-gateway.md)

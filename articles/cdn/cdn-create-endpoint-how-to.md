---
title: Tworzenie punktu końcowego Azure CDN | Microsoft Docs
description: W tym artykule pokazano, jak utworzyć nowy punkt końcowy usługi Azure Content Delivery Network (CDN), w tym ustawienia zaawansowane.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7bbd54ed68863a4704319a25af96b6463ad2377b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539699"
---
# <a name="create-an-azure-cdn-endpoint"></a>Tworzenie punktu końcowego Azure CDN
W tym artykule opisano wszystkie ustawienia dotyczące tworzenia punktu końcowego [usługi Azure Content Delivery Network (CDN)](cdn-overview.md) w istniejącym profilu CDN. Po utworzeniu profilu i punktu końcowego możesz rozpocząć dostarczanie zawartości do klientów. Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia profilu i punktu końcowego, zobacz [Szybki Start: tworzenie Azure CDN profilu i punktu końcowego](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było utworzyć punkt końcowy usługi CDN, należy utworzyć co najmniej jeden profil CDN, który może zawierać jeden lub więcej punktów końcowych usługi CDN. Do organizowania punktów końcowych usługi CDN według domeny internetowej, aplikacji internetowej lub innych kryteriów można używać wielu profilów. Ponieważ Cennik usługi CDN jest stosowany na poziomie profilu usługi CDN, należy utworzyć wiele profilów usługi CDN, jeśli chcesz używać różnych warstw cenowych Azure CDN. Aby utworzyć profil usługi CDN, zobacz [Tworzenie nowego profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

## <a name="create-a-new-cdn-endpoint"></a>Tworzenie nowego punktu końcowego usługi CDN

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego profilu CDN. Być może został on przypięty do pulpitu nawigacyjnego w poprzednim kroku. Jeśli nie, możesz go znaleźć, wybierając kolejno pozycje **Wszystkie usługi** i **Profile CDN**. W okienku **Profile CDN** wybierz profil, do którego planujesz dodać punkt końcowy. 
   
    Zostanie wyświetlone okienko profilu CDN.

2. Wybierz **punkt końcowy**.
   
    ![Punkt końcowy wybierania usługi CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Zostanie wyświetlona strona **Dodawanie punktu końcowego**.
   
    ![Dodaj stronę punktu końcowego](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. W polu **Nazwa** wprowadź unikatową nazwę nowego punktu końcowego CDN. Ta nazwa służy do uzyskiwania dostępu do buforowanych zasobów w domenie _\<endpointname>_ . azureedge.NET.

4. W **polu Typ źródła** wybierz jeden z następujących typów źródła: 
   - **Magazyn** dla usługi Azure Storage
   - **Usługa w chmurze** dla platformy Azure Cloud Services
   - **Aplikacja sieci Web** dla usługi Azure Web Apps
   - **Niestandardowe źródło** dla wszystkich innych publicznie dostępnych źródeł serwera sieci Web (hostowanych na platformie Azure lub w innym miejscu)

5. W polu **Nazwa hosta źródła** wybierz lub wprowadź domenę serwera pochodzenia. Na liście rozwijanej są wyświetlane wszystkie dostępne serwery źródłowe typu określonego w kroku 4. Jeśli wybrano opcję **Źródło niestandardowe** jako typ źródła, wprowadź domenę serwera niestandardowego źródła danych.
    
6. W polu **ścieżka do źródła** wprowadź ścieżkę do zasobów, które mają być buforowane. Aby umożliwić buforowanie dowolnego zasobu w domenie określonej w kroku 5, pozostaw to ustawienie puste.
    
7. W polu **Nagłówek hosta źródła** wprowadź nagłówek hosta, który usługa Azure CDN ma wysłać z każdym żądaniem, lub pozostaw wartość domyślną.
   
   > [!NOTE]
   > Niektóre typy źródeł, takie jak usługi Azure Storage i Web Apps, wymagają, aby nagłówek hosta był zgodny z domeną źródła. Pozostaw wartość domyślną, chyba że masz źródło, które wymaga nagłówka hosta innego niż jego domena.
   > 
    
8. Dla **protokołu** i **portu pochodzenia** Określ protokoły i porty, które mają być używane do uzyskiwania dostępu do zasobów na serwerze źródłowym. Należy wybrać co najmniej jeden protokół (HTTP lub HTTPS). Użyj domeny dostarczonej przez usługę CDN ( _\<endpointname>_ azureedge.NET) w celu uzyskania dostępu do zawartości https. 
   
   > [!NOTE]
   > Wartość **portu źródła** określa tylko port wykorzystywany przez punkt końcowy do pobierania informacji z serwera pochodzenia. Sam punkt końcowy jest dostępny dla klientów końcowych tylko na domyślnych portach HTTP i HTTPS (80 i 443), niezależnie od wartości **Port źródła**.  
   > 
   > Punkty końcowe w profilach usługi **Azure CDN from Akamai** nie zezwalają na pełny zakres portów TCP dla portów źródeł. Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Azure CDN from Akamai — dozwolone porty źródłowe).  
   > 
   > Obsługa protokołu HTTPS dla Azure CDN domen niestandardowych nie jest obsługiwana w przypadku **Azure CDN z produktów Akamai** . Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md).
    
9. Dla opcji **zoptymalizowane pod kątem** wybierz typ optymalizacji, który najlepiej pasuje do scenariusza i typu zawartości, która ma być dostarczany przez punkt końcowy. Aby uzyskać więcej informacji, zobacz [optymalizacja Azure CDN dla typu dostarczania zawartości](cdn-optimization-overview.md).

    Obsługiwane są następujące ustawienia typu optymalizacji, zgodnie z typem profilu:
    - **Standard Azure CDN z profilów firmy Microsoft** :
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard z Verizon** i **Azure CDN Premium z profilów Verizon** :
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Przyspieszanie witryn dynamicznych**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Standard Azure CDN z profilów Akamai** :
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Ogólne przesyłanie strumieniowe multimediów**](cdn-optimization-overview.md#general-media-streaming)
       - [**Przesyłanie strumieniowe multimediów wideo na żądanie**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Pobieranie dużych plików**](cdn-optimization-overview.md#large-file-download)
       - [**Przyspieszanie witryn dynamicznych**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Wybierz pozycję **Dodaj**, aby utworzyć nowy punkt końcowy.
   
    Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych dla profilu.
    
    ![Punkt końcowy usługi CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Punkt końcowy nie jest natychmiast dostępny do użycia, ponieważ propagacja rejestracji zajmuje trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** , Propagacja jest zwykle zakończona w ciągu 30 minut. 
   
    Jeśli próbujesz użyć nazwy domeny usługi CDN przed przekazaniem konfiguracji punktu końcowego do serwerów punktu obecności (POP), może zostać wyświetlony stan odpowiedzi HTTP 404. Jeśli masz kilka godzin od momentu utworzenia punktu końcowego, a nadal otrzymujesz 404 o stanie odpowiedzi, zobacz [Rozwiązywanie problemów Azure CDN punkty końcowe, które zwracają kod stanu 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć punkt końcowy, gdy nie jest już wymagany, zaznacz go, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o domenach niestandardowych, przejdź do samouczka dotyczącego dodawania domeny niestandardowej do punktu końcowego usługi CDN.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](cdn-map-content-to-custom-domain.md)



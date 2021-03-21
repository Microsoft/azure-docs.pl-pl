---
title: App Service Environment sieci
description: Szczegóły sieci App Service Environment
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735037"
---
# <a name="app-service-environment-networking"></a>App Service Environment sieci

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v3 (wersja zapoznawcza)
> 

App Service Environment (ASE) jest wdrożeniem pojedynczej dzierżawy Azure App Service, które obsługuje aplikacje sieci Web, aplikacje interfejsu API i aplikacje funkcji. Podczas instalacji środowiska ASE wybiera się Virtual Network platformy Azure, w ramach której ma zostać wdrożone. Wszystkie aplikacje ruchu przychodzącego i wychodzącego będą znajdować się w określonej sieci wirtualnej.  

ASEv3 używa dwóch podsieci.  Jedna podsieć jest używana dla prywatnego punktu końcowego, który obsługuje ruch przychodzący. Może to być istniejąca lub Nowa podsieć.  Podsieci przychodzącej można używać do innych celów niż prywatny punkt końcowy. Podsieci wychodzącej można używać tylko dla ruchu wychodzącego ze środowiska ASE. Nic innego nie można umieścić w podsieci wychodzącej środowiska ASE.

## <a name="addresses"></a>Adresy 
Podczas tworzenia środowisko ASE ma następujące adresy:

| Typ adresu | description (opis) |
|--------------|-------------|
| Adres przychodzący | Adres przychodzący to prywatny adres punktu końcowego używany przez środowisko ASE. |
| Podsieć wychodząca | Podsieć wychodząca jest również podsiecią środowiska ASE. W trakcie podglądu ta podsieć jest używana tylko dla ruchu wychodzącego. |
| Adres wychodzący systemu Windows | Aplikacje systemu Windows w tym środowisku ASE będą używać tego adresu domyślnie podczas wykonywania wywołań wychodzących do Internetu. |
| Adres wychodzący systemu Linux | W przypadku aplikacji systemu Linux w tym środowisku ASE ten adres jest domyślnie używany podczas wykonywania wywołań wychodzących do Internetu. |

ASEv3 zawiera szczegółowe informacje dotyczące adresów używanych przez środowisko ASE w części **adresy IP** portalu środowiska ASE.

![Interfejs użytkownika środowiska ASE](./media/networking/networking-ip-addresses.png)

W przypadku usunięcia prywatnego punktu końcowego używanego przez środowisko ASE nie będzie można nawiązać połączenia z aplikacjami w środowisku ASE.  

Środowisko ASE używa adresów w podsieci wychodzącej do obsługi infrastruktury używanej przez środowisko ASE. W miarę skalowania planów App Service w środowisku ASE będziesz używać więcej adresów. Aplikacje w środowisku ASE nie mają dedykowanych adresów w podsieci wychodzącej. Adresy używane przez aplikację w podsieci wychodzącej przez aplikację zmieniają się z upływem czasu.

## <a name="ports"></a>Porty

Środowisko ASE odbiera ruch aplikacji na portach 80 i 443.  Środowisko ASE nie ma wymagań dotyczących portów przychodzących lub wychodzących. 

## <a name="extra-configurations"></a>Dodatkowe konfiguracje

W przeciwieństwie do ASEv2, z ASEv3 można ustawić sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i tabele tras (UDR), ponieważ są one widoczne bez ograniczeń. Jeśli chcesz wymusić tunelowanie całego ruchu wychodzącego ze środowiska ASE do urządzenia urządzenie WUS. Urządzenia WAF można umieścić przed całym ruchem przychodzącym do środowiska ASE. 

## <a name="dns"></a>DNS

Aplikacje w środowisku ASE będą używać systemu DNS, z którym jest skonfigurowana Sieć wirtualna. Postępuj zgodnie z instrukcjami w temacie [using a App Service Environment](./using.md#dns-configuration) , aby skonfigurować serwer DNS tak, aby WSKAZYWAŁ środowisko ASE. Jeśli chcesz, aby niektóre aplikacje używały innego serwera DNS niż konfiguracja sieci wirtualnej, możesz ustawić je ręcznie dla poszczególnych aplikacji przy użyciu ustawień aplikacji WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER. Ustawienie aplikacji WEBSITE_DNS_ALT_SERVER konfiguruje pomocniczy serwer DNS. Pomocniczy serwer DNS jest używany tylko wtedy, gdy nie ma odpowiedzi z podstawowego serwera DNS. 

## <a name="preview-limitation"></a>Ograniczenie wersji zapoznawczej

Istnieje kilka funkcji sieciowych, które nie są dostępne w programie ASEv3.  Elementy, które nie są dostępne w ASEv3 obejmują:

• FTP • zdalne debugowanie • wdrożenie zewnętrznego modułu równoważenia obciążenia • możliwość uzyskiwania dostępu do prywatnego rejestru kontenerów dla wdrożeń kontenerów • możliwość wykonywania wywołań globalnie równorzędnych sieci wirtualnych • możliwość tworzenia kopii zapasowej/przywracania za pomocą punktu końcowego usługi lub bezpiecznego prywatnego punktu końcowego konto magazynu • możliwość używania w punkcie końcowym usługi lub prywatnych kontach magazynu kluczy ustawień aplikacji, które umożliwiają użycie BYOS do punktu końcowego usługi lub prywatnego bezpiecznego konta magazynu, z użyciem Network Watcher lub przepływu sieciowej grupy zabezpieczeń dla ruchu wychodzącego
    
    
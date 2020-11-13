---
title: Wskazówki dotyczące automatyzacji wirtualnych sieci WAN platformy Azure | Microsoft Docs
description: Skonfiguruj środowisko automatyzacji do łączenia i konfigurowania lokalnego urządzenia sieci VPN lub SD-WAN CPE lub oddziału dla wirtualnej sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566473"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Wytyczne dotyczące automatyzacji dla partnerów usługi Virtual WAN

Ten artykuł pomaga zrozumieć, jak skonfigurować środowisko automatyzacji do łączenia i konfigurowania urządzenia rozgałęzienia (lokalnego urządzenia sieci VPN lub SDWAN CPE) dla wirtualnej sieci WAN platformy Azure. Jeśli jesteś dostawcą udostępniającym urządzenia oddziałów, które mogą obsługiwać połączenia sieci VPN za pośrednictwem protokołu IPsec/IKEv2 lub protokołu IPsec/IKEv1, ten artykuł jest dla Ciebie.

Urządzenie rozgałęzienia (lokalne urządzenie sieci VPN lub SDWAN CPE) zwykle używa pulpitu nawigacyjnego kontrolera/urządzenia do aprowizacji. Administratorzy rozwiązań typu SD-WAN mogą często korzystać z konsoli zarządzania w celu wstępnego udostępnienia urządzenia przed podłączeniem go do sieci. To urządzenie z obsługą sieci VPN pobiera logikę płaszczyzny kontroli z kontrolera. Urządzenie sieci VPN lub kontroler SD-WAN może używać interfejsów API platformy Azure do automatyzowania łączności z wirtualną siecią WAN platformy Azure. Ten typ połączenia wymaga, aby urządzenie lokalne miało przypisaną zewnętrznie publiczny adres IP.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Przed rozpoczęciem automatyzowania

* Sprawdź, czy urządzenie obsługuje protokół IPsec IKEv1/IKEv2. Zobacz [zasady domyślne](#default).
* Wyświetl [interfejsy API REST](#additional) , których używasz do automatyzowania łączności z wirtualną siecią WAN platformy Azure.
* Przetestuj środowisko portalu w wirtualnej sieci WAN platformy Azure.
* Następnie zdecyduj, która część kroków łączności, które chcesz zautomatyzować. Zalecamy automatyzację:

  * Kontrola dostępu
  * Przekazywanie informacji o urządzeniu rozgałęzień do wirtualnej sieci WAN platformy Azure
  * Pobieranie konfiguracji platformy Azure i Konfigurowanie łączności z urządzenia oddziału w usłudze Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Dodatkowe informacje

* [Interfejs API REST](/rest/api/virtualwan/virtualhubs) do automatyzowania tworzenia koncentratora wirtualnego
* [Interfejs API REST](/rest/api/virtualwan/vpngateways) do automatyzowania bramy sieci VPN platformy Azure dla wirtualnej sieci WAN
* [Interfejs API REST](/rest/api/virtualwan/vpnconnections) do łączenia VPNSite z Centrum sieci VPN platformy Azure
* [Domyślne zasady protokołu IPsec](#default)

## <a name="customer-experience"></a><a name ="ae"></a>obsługa klienta

Zapoznaj się z oczekiwanym doświadczeniem klienta w połączeniu z wirtualną siecią WAN platformy Azure.

  1. Zazwyczaj wirtualny użytkownik sieci WAN rozpocznie proces przez utworzenie zasobu wirtualnej sieci WAN.
  2. Użytkownik skonfiguruje dostęp do grupy zasobów opartej na jednostce usługi dla systemu lokalnego (kontroler rozgałęzienia lub oprogramowanie do aprowizacji urządzeń sieci VPN) w celu zapisania informacji o gałęzi w wirtualnej sieci WAN platformy Azure.
  3. Użytkownik może w tym czasie zalogować się do interfejsu użytkownika i skonfigurować poświadczenia nazwy głównej usługi. Po zakończeniu tego procesu kontroler powinien mieć możliwość przekazywania informacji o gałęzi z automatyzacją, którą zapewnisz. Ręczne odpowiednik tego elementu na stronie platformy Azure to "Utwórz witrynę".
  4. Gdy informacje o witrynie (urządzeniu rozgałęzienia) są dostępne na platformie Azure, użytkownik będzie łączył lokację z centrum. Koncentratorem wirtualnym jest sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. Może istnieć tylko jeden koncentrator dla regionu platformy Azure, a punkt końcowy sieci VPN (bramy vpngateway) jest tworzony w trakcie tego procesu. Brama sieci VPN to skalowalna brama, która odpowiednio zmienia rozmiar na podstawie przepustowości i potrzeb związanych z połączeniami. Możesz zautomatyzować tworzenie centrów wirtualnych i bramy vpngateway z poziomu pulpitu nawigacyjnego kontrolera urządzeń gałęzi.
  5. Po skojarzeniu koncentratora wirtualnego z lokacją zostanie wygenerowany plik konfiguracji, który użytkownik może pobrać ręcznie. Jest to miejsce, w którym znajduje się Automatyzacja i sprawia, że środowisko użytkownika działa bezproblemowo. Zamiast użytkownika, który ma ręcznie pobierać i konfigurować urządzenie oddziału, można ustawić automatyzację i zapewnić minimalne środowisko klikania w interfejsie użytkownika, eliminując w ten sposób typowe problemy z łącznością, takie jak niezgodność klucza wspólnego, niezgodność parametrów protokołu IPSec, czytelność plików konfiguracji itp.
  6. Po zakończeniu tego kroku w rozwiązaniu użytkownik będzie miał bezproblemowe połączenie między lokacjami i koncentratorem wirtualnym. Możesz również skonfigurować dodatkowe połączenia w innych centrach. Każde połączenie to tunel aktywny-aktywny. Klient może zdecydować się na użycie innego usługodawcy internetowego dla każdego z linków dla tunelu.
  7. Należy rozważyć zapewnienie możliwości rozwiązywania problemów i monitorowania w interfejsie zarządzania CPE. Typowe scenariusze obejmują "klient nie może uzyskać dostępu do zasobów platformy Azure z powodu problemu CPE", "Pokaż parametry protokołu IPsec na stronie CPE" itd.

## <a name="automation-details"></a><a name ="understand"></a>Szczegóły automatyzacji

###  <a name="access-control"></a><a name="access"></a>Kontrola dostępu

Klienci muszą mieć możliwość skonfigurowania odpowiedniej kontroli dostępu dla wirtualnej sieci WAN w interfejsie użytkownika urządzenia. Jest to zalecane przy użyciu nazwy głównej usługi platformy Azure. Dostęp oparty na jednostce usługi zapewnia kontrolerowi urządzenia odpowiednie uwierzytelnianie w celu przekazania informacji o gałęzi. Aby uzyskać więcej informacji, zobacz [Tworzenie nazwy głównej usługi](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Chociaż ta funkcja jest poza ofertą wirtualnej sieci WAN platformy Azure, zostanie wystawiona poniżej typowych kroków w celu skonfigurowania dostępu na platformie Azure, po czym odpowiednie szczegóły są wprowadzane na pulpicie nawigacyjnym zarządzania urządzeniami.

* Utwórz aplikację Azure Active Directory dla lokalnego kontrolera urządzenia.
* Pobieranie identyfikatora aplikacji i klucza uwierzytelniania
* Pobieranie identyfikatora dzierżawy
* Przypisywanie aplikacji do roli "Współautor"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Przekazywanie informacji o urządzeniu gałęzi

Należy zaprojektować środowisko użytkownika w celu przekazywania informacji o rozgałęzieniu (lokacji lokalnej) do platformy Azure. Aby utworzyć informacje o lokacji w wirtualnej sieci WAN, można użyć [interfejsów API REST](/rest/api/virtualwan/vpnsites) dla VPNSite. Wszystkie urządzenia SDWAN/VPN można udostępnić lub wybrać odpowiednie dostosowania urządzeń.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Pobieranie i łączność konfiguracji urządzeń

Ten krok obejmuje pobranie konfiguracji platformy Azure i skonfigurowanie łączności z urządzenia oddziału w usłudze Azure Virtual WAN. W tym kroku klient, który nie korzysta z dostawcy, ręcznie pobierze konfigurację platformy Azure i zastosuje ją do lokalnego urządzenia SDWAN/VPN. Jako dostawca należy zautomatyzować ten krok. Aby uzyskać dodatkowe informacje, zobacz [interfejsy API REST](/rest/api/virtualwan/vpnsitesconfiguration/download) pobierania. Kontroler urządzenia może wywoływać interfejs API REST "GetVpnConfiguration", aby pobrać konfigurację platformy Azure.

**Uwagi dotyczące konfiguracji**

  * Jeśli usługa Azure sieci wirtualnych jest dołączana do koncentratora wirtualnego, zostaną one wyświetlone jako ConnectedSubnets.
  * Połączenie sieci VPN korzysta z konfiguracji opartej na trasach i obsługuje protokoły IKEv1 i IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Plik konfiguracji urządzenia

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** ta sekcja zawiera informacje dotyczące następujących kwestii:

    * **Przestrzeń adresowa** wirtualnej koncentratora (s).<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, która jest połączona z centrum.<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie z bramą vpngateway obejmuje dwa tunele w konfiguracji aktywne-aktywne, w pliku wymienione będą oba adresy IP. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Bramy vpngateway szczegóły konfiguracji połączenia** , takie jak BGP, klucz wstępny itp. PSK to klucz wstępny, który jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
**Przykładowy plik konfiguracji urządzenia**

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="connectivity-details"></a><a name="default"></a>Szczegóły łączności

Lokalne urządzenie SDWAN/VPN lub Konfiguracja SD-WAN muszą być zgodne lub zawierać następujące algorytmy i parametry, które można określić w zasadach usługi Azure IPsec/IKE.

* Algorytm szyfrowania IKE
* Algorytm integralności IKE
* Grupa DH
* Algorytm szyfrowania IPsec
* Algorytm integralności IPsec
* Grupa PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Zasady domyślne dla łączności protokołu IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Zasady niestandardowe dla łączności protokołu IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [temat informacje o wirtualnej sieci WAN platformy Azure](virtual-wan-about.md) i [usłudze Azure Virtual WAN — często zadawane pytania](virtual-wan-faq.md).

Aby uzyskać dodatkowe informacje, Wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com> . W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”).
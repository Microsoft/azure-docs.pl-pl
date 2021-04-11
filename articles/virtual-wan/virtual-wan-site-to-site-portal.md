---
title: 'Samouczek: tworzenie połączeń lokacja-lokacja za pomocą usługi Azure Virtual WAN'
description: Z tego samouczka dowiesz się, jak utworzyć połączenie lokacja-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 365952beca96b91d312eab209c5332fca2d4842b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061880"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Samouczek: tworzenie połączenia lokacja-lokacja przy użyciu usługi Azure Virtual WAN

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv1 i IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie koncentratora
> * Tworzenie lokacji
> * Łączenie lokacji z centrum
> * Łączenie witryny sieci VPN z centrum
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobierz plik konfiguracji
> * Konfigurowanie bramy sieci VPN

> [!NOTE]
> Jeśli masz wiele lokacji, skorzystaj z usług [partnera usługi Virtual WAN](https://aka.ms/virtualwan) w celu utworzenia tej konfiguracji. Możesz też utworzyć tę konfigurację samodzielnie, jeśli masz wiedzę z zakresu sieci i doświadczenie w konfigurowaniu urządzeń sieci VPN.
>

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Tworzenie wirtualnej sieci WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Tworzenie koncentratora

Centrum to sieć wirtualna, która może zawierać bramy do funkcji lokacja-lokacja, ExpressRoute lub punkt-lokacja. Po utworzeniu koncentratora będzie za niego naliczana opłata, nawet jeśli nie dołączysz żadnych lokacji. Utworzenie bramy sieci VPN typu lokacja-lokacja w koncentratorze wirtualnym trwa 30 minut.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Tworzenie witryny

W tej sekcji utworzysz lokację. Lokacje odpowiadają lokalizacji fizycznej. Utwórz tyle witryn, ile potrzebujesz. Jeśli na przykład masz oddziały w Nowym Jorku, Londynie i Los Angeles, należy utworzyć trzy oddzielne lokacje. W tych lokacjach znajdują się punkty końcowe lokalnych urządzeń sieci VPN. W wirtualnej sieci WAN można utworzyć maksymalnie 1000 witryn na koncentratorze wirtualnym. Jeśli masz wiele centrów, możesz utworzyć 1000 dla każdego z tych centrów. Jeśli masz urządzenie z wirtualnym partnerem WAN CPE, skontaktuj się z nimi, aby dowiedzieć się więcej o automatyzacji na platformie Azure. Zazwyczaj Automatyzacja oznacza proste środowisko klikania do eksportowania informacji o gałęziach o dużej skali na platformę Azure oraz konfigurowania łączności od CPE do bramy sieci VPN Azure Virtual WAN. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące automatyzacji od platformy Azure do partnerów CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Łączenie witryny sieci VPN z centrum

W tym kroku połączysz swoją lokację sieci VPN z centrum.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Łączenie sieci wirtualnej z centrum

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Pobieranie konfiguracji sieci VPN

Skorzystaj z konfiguracji urządzenia sieci VPN, aby skonfigurować swoje lokalne urządzenie sieci VPN.

1. Na stronie usługi Virtual WAN kliknij pozycję **Przegląd**.
2. W górnej części strony **centrum >VPNSite** kliknij pozycję **Pobierz konfigurację sieci VPN**. Platforma Azure tworzy konto magazynu w grupie zasobów "Microsoft-Network-[Location]", gdzie lokalizacja jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji na urządzeniach sieci VPN możesz usunąć to konto magazynu.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Zastosuj konfigurację do lokalnego urządzenia sieci VPN.

### <a name="about-the-vpn-device-configuration-file"></a>Informacje o pliku konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** Ta sekcja zawiera informacje o następujących ustawieniach:

    * **Przestrzeń adresowa** wirtualnej koncentratora (s).<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, która jest połączona z centrum.<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie bramy vpngateway składa się z dwóch tuneli w konfiguracji Active-Active, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Bramy vpngateway szczegóły konfiguracji połączenia** , takie jak BGP, klucz wstępny itp. PSK to klucz wstępny, który jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
### <a name="example-device-configuration-file"></a>Przykładowy plik konfiguracji urządzenia

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

### <a name="configuring-your-vpn-device"></a>Konfigurowanie urządzenia sieci VPN

>[!NOTE]
> Jeśli używasz rozwiązania partnera usługi Virtual WAN, konfiguracja urządzenia sieci VPN odbywa się automatycznie. Kontroler urządzenia uzyskuje plik konfiguracji z platformy Azure i stosuje go do urządzenia w celu skonfigurowania połączenia z platformą Azure. Oznacza to, że nie musisz wiedzieć, w jaki sposób skonfigurować urządzenie sieci VPN ręcznie.
>

Jeśli chcesz wiedzieć, jak skonfigurować urządzenie, możesz skorzystać z instrukcji na [stronie ze skryptami konfigurowania urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzeń sieci VPN nie zostały napisane dla usługi Virtual WAN, ale możesz skorzystać z wartości z pliku konfiguracji usługi Virtual WAN, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania przeznaczone dla usługi VPN Gateway nie działają w przypadku usługi Virtual WAN, ponieważ ich konfiguracja różni się.
* Nowa wirtualna sieć WAN może obsługiwać zarówno protokół IKEv1, jak i IKEv2.
* Wirtualna sieć WAN może używać zarówno urządzeń sieci VPN opartych na zasadach, jak i opartych na trasach.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>Konfigurowanie bramy sieci VPN

Ustawienia bramy sieci VPN można wyświetlać i konfigurować w dowolnym momencie, wybierając pozycję **Wyświetl/Skonfiguruj**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;sieć VPN (lokacja-lokacja)&quot; ze strzałką wskazującą akcję &quot;Wyświetl/Konfiguruj&quot;." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Na stronie **edytowanie VPN Gateway** można wyświetlić następujące ustawienia:

* VPN Gateway publiczny adres IP (przypisany przez platformę Azure)
* VPN Gateway prywatny adres IP (przypisany przez platformę Azure)
* VPN Gateway domyślny adres IP protokołu BGP (przypisany przez platformę Azure)
* Opcja konfiguracji dla niestandardowego adresu IP protokołu BGP: to pole jest zarezerwowane dla funkcji APIPA (automatyczne prywatne adresy IP). Platforma Azure obsługuje adres IP BGP w zakresach 169.254.21. * i 169.254.22. *. Platforma Azure akceptuje połączenia BGP w tych zakresach, ale nawiąże połączenie z domyślnym adresem IP protokołu BGP.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Wyświetl konfigurację" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Czyszczenie zasobów

Jeśli zasoby, które zostały utworzone, nie są już potrzebne, usuń je. Niektóre z zasobów wirtualnych sieci WAN muszą zostać usunięte w określonej kolejności z powodu zależności. Usuwanie może potrwać około 30 minut.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz:

> [!div class="nextstepaction"]
> * [Często zadawane pytania dotyczące usługi Virtual WAN](virtual-wan-faq.md)

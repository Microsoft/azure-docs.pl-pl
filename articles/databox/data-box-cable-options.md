---
title: Opcje okablowania urządzenia Azure Data Box | Microsoft Docs
description: Dowiedz się więcej na temat różnych sposobów, aby podłączyć Azure Data Box do transferu danych przy użyciu portu zarządzania lub portu danych.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/24/2018
ms.author: alkohli
ms.openlocfilehash: bab81809f42c9afa3d2c701398fa5ff8ffc2800d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744172"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Opcje okablowania urządzenia Azure Data Box

W tym artykule opisano różne sposoby okablowania urządzenia Azure Data Box na potrzeby transferu danych.

## <a name="transfer-via-mgmt-port"></a>Transfer za pośrednictwem portu MGMT

Ta opcja jest absolutnie minimalną konfiguracją dla usługi Data Box. Możesz skonfigurować tylko port MGMT na potrzeby zarówno zarządzania, jak i danych.

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Kabel Ethernet RJ-45 dla portu MGMT.
- Źródło danych z [obsługiwanym systemem operacyjnym](data-box-system-requirements.md#supported-operating-systems-for-clients).

Wykonaj poniższe kroki, aby okablować urządzenie.

1. Za pomocą kabla RJ-45 podłącz port MGMT do serwera, na którym znajdują się dane.

    ![Konfiguracja jednego portu](media/data-box-cable-options/cabling-mgmt-only.png)

2. Na serwerze ustaw:

    - **Adres IP** na 192.168.100.5
    - **Podsieć** na 255.255.255.0

3. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem 192.168.100.10. Zaloguj się i odblokuj urządzenie Data Box przy użyciu hasła odblokowania z witryny Azure Portal.


## <a name="transfer-via-data-port-with-static-ips"></a>Transfer za pośrednictwem portu DATA przy użyciu statycznych adresów IP

Na urządzeniu Data Box możesz skonfigurować dwa porty: port MGMT na potrzeby ruchu związanego z zarządzaniem i jeden z portów danych (DATA) na potrzeby danych. Portami danych mogą być porty DATA 1, DATA 2 lub DATA 3.

W przypadku konfigurowania tylko jednego portu zdecydowanie zaleca się, aby był to port 10 GbE, taki jak DATA 1 lub DATA 2. Użycie portu 1 GbE może znacznie wydłużyć czas potrzebny na transfer danych.

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Kabel Ethernet RJ-45 dla portu MGMT.
- Kabel miedziany SFP+ Twinax 10 GbE dla każdego portu danych 10 GbE, który chcesz podłączyć.
- Co najmniej jedno źródło danych z [obsługiwanym systemem operacyjnym](data-box-system-requirements.md#supported-operating-systems-for-clients).

### <a name="option-1---initial-setup-via-server"></a>Opcja 1 — Konfiguracja początkowa za pomocą serwera

Wykonaj poniższe kroki, aby okablować urządzenie.

1. Za pomocą kabla Ethernet RJ-45 podłącz serwer bezpośrednio do portu MGMT na potrzeby konfiguracji.
2. Za pomocą kabla RJ-45 (w przypadku portu DATA 3) lub kabla SFP+ (w przypadku portu DATA 1 lub DATA 2) podłącz urządzenie do serwera działającego jako źródło danych. Zalecamy użycie portów 10 GbE DATA 1 lub DATA 2 w celu uzyskania dobrej wydajności.
3. Na serwerze ustaw:

   - **Adres IP** na 192.168.100.5
   - **Podsieć** na 255.255.255.0

     ![Konfiguracja dwóch portów](media/data-box-cable-options/cabling-2-port-setup.png)

3. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem 192.168.100.10. Zaloguj się i odblokuj urządzenie Data Box przy użyciu hasła odblokowania z witryny Azure Portal.
4. Przypisz statyczne adresy IP do skonfigurowanych portów danych.

### <a name="option-2---initial-setup-via-separate-computer"></a>Opcja 2 — Konfiguracja początkowa za pomocą oddzielnego komputera

Wykonaj poniższe kroki, aby okablować urządzenie.

1. Aby skonfigurować konfigurację, użyj kabla Ethernet RJ45 z osobnego komputera bezpośrednio do portu zarządzania.
2. Za pomocą kabla RJ-45 (w przypadku portu DATA 3) lub kabla SFP+ (w przypadku portu DATA 1 lub DATA 2) podłącz urządzenie do serwera. Zalecamy użycie portów 10 GbE DATA 1 lub DATA 2 w celu uzyskania dobrej wydajności. Porty danych są połączone z serwerem zawierającym dane za pośrednictwem przełącznika 10 GbE.
3. Skonfiguruj kartę Ethernet na komputerze przenośnym używanym do nawiązywania połączenia z urządzeniem za pomocą następujących ustawień:

   - **Adres IP** 192.168.100.5
   - **Podsieć** 255.255.255.0.

     ![Konfiguracja dwóch portów z przełącznikiem](media/data-box-cable-options/cabling-with-static-ip.png)

3. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem 192.168.100.10. Zaloguj się i odblokuj urządzenie Data Box przy użyciu hasła odblokowania z witryny Azure Portal.
4. Zidentyfikuj adresy IP przypisane przez serwer DHCP.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Transfer za pośrednictwem portu DATA z użyciem przełącznika 

Tej konfiguracji należy użyć w przypadku wielu źródeł danych w sieciach 1 GbE i 10 GbE.

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Kabel Ethernet RJ-45 dla portu MGMT.
- Kabel miedziany SFP+ Twinax 10 GbE dla każdego portu danych 10 GbE, który chcesz podłączyć.
- Co najmniej jedno źródło danych z [obsługiwanym systemem operacyjnym](data-box-system-requirements.md#supported-operating-systems-for-clients). Te źródła danych mogą znajdować się w różnych sieciach, takich jak sieci 1 GbE lub 10 GbE.

Wykonaj poniższe kroki, aby okablować urządzenie.

1. Za pomocą kabla Ethernet RJ-45 podłącz serwer bezpośrednio do portu MGMT na potrzeby konfiguracji.
2. Za pomocą kabla RJ-45 (w przypadku portu DATA 3) lub kabla SFP+ (w przypadku portu DATA 1 lub DATA 2) podłącz urządzenie do serwera. Zalecamy użycie portów 10 GbE DATA 1 lub DATA 2 w celu uzyskania dobrej wydajności.
3. Skonfiguruj kartę Ethernet na komputerze przenośnym używanym do nawiązywania połączenia z urządzeniem za pomocą następujących ustawień:

   - **Adres IP** 192.168.100.5
   - **Podsieć** 255.255.255.0.

     ![Konfiguracja dwóch portów z przełącznikiem 2](media/data-box-cable-options/cabling-with-switch-static-ip.png)

3. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem 192.168.100.10. Zaloguj się i odblokuj urządzenie Data Box przy użyciu hasła odblokowania z witryny Azure Portal.
4. Przypisz statyczne adresy IP do skonfigurowanych portów danych.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>Transfer za pośrednictwem portu DATA w środowisku DHCP

Użyj tej konfiguracji, jeśli urządzenie będzie pracować w środowisku DHCP.

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Kabel RJ-45, jeśli chcesz podłączyć port DATA 1.
- Kabel miedziany SFP+ Twinax 10 GbE dla każdego portu danych 10 GbE, który chcesz podłączyć.
- Co najmniej jedno źródło danych z [obsługiwanym systemem operacyjnym](data-box-system-requirements.md#supported-operating-systems-for-clients). Te źródła danych mogą znajdować się w różnych sieciach, takich jak sieci 1 GbE lub 10 GbE.

Wykonaj poniższe kroki, aby okablować urządzenie.

1. Użyj kabla RJ-45 lub SFP+ za pośrednictwem przełącznika (jeżeli serwer DHCP jest dostępny) do serwera.

    ![Konfiguracja dwóch portów z przełącznikiem 3](media/data-box-cable-options/cabling-dhcp-data-only.png)
2. Użyj serwera DHCP lub serwera DNS, aby zidentyfikować adres IP.
3. Z serwera w tej samej sieci uzyskaj dostęp do lokalnego interfejsu użytkownika urządzenia przy użyciu adresu IP przypisanego przez serwer DHCP. Zaloguj się i odblokuj urządzenie Data Box przy użyciu hasła odblokowania z witryny Azure Portal.

## <a name="next-steps"></a>Następne kroki

- Po okablowaniu urządzenia przejdź do samouczka [Copy data to your Azure Data Box (Kopiowanie danych na urządzenie Azure Data Box)](data-box-deploy-copy-data.md).

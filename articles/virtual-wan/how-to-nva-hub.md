---
title: 'Wirtualna sieć WAN platformy Azure: Tworzenie sieciowego urządzenia wirtualnego (urządzenie WUS) w centrum'
description: W tym samouczku dowiesz się, jak wdrożyć sieciowe urządzenie wirtualne w wirtualnym koncentratorze sieci WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 014339b02167a1bb4cba11cc10c9740b8fa53f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91773250"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Jak utworzyć sieciowe urządzenie wirtualne w centrum sieci wirtualnej platformy Azure (wersja zapoznawcza)

W tym samouczku pokazano, jak używać wirtualnej sieci WAN do łączenia się z zasobami na platformie Azure za pomocą **sieciowego urządzenia wirtualnego** (urządzenie WUS) na platformie Azure. Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Kroki opisane w tym artykule ułatwiają tworzenie wirtualnego urządzenia sieciowego **Barracuda CLOUDGEN WAN** w koncentratorze sieci WAN. Aby wykonać to ćwiczenie, musisz mieć urządzenie lokalne w chmurze Barracuda (CPE) i licencję na urządzenie sieci WAN CloudGen Barracuda, które zostało wdrożone w centrum przed rozpoczęciem.

Aby zapoznać się z dokumentacją dotyczącą wdrażania **Cisco SD-WAN** w wirtualnej sieci WAN platformy Azure, Wyślij wiadomość E-mail do firmy Cisco pod następującym adresem e-mail: vwan_public_preview@external.cisco.com


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Uzyskaj licencję dla bramy sieci WAN Barracuda CloudGen. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [dokumentację sieci WAN Barracuda CloudGen](https://www.barracuda.com/products/cloudgenwan)

* Masz sieć wirtualną, z którą chcesz nawiązać połączenie. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zobacz [Przewodnik Szybki Start](../virtual-network/quick-create-portal.md).

* Twoja sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (VPN lub ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga, aby w zamian były połączone sieci wirtualne z bramą wirtualnego centrum sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Centrum jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla centrum nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi się łączysz. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Tworzenie wirtualnej sieci WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Tworzenie koncentratora

Centrum to sieć wirtualna, która może zawierać bramy dla funkcji lokacja-lokacja, ExpressRoute, punkt-lokacja lub sieciowe urządzenie wirtualne. Po utworzeniu koncentratora będzie za niego naliczana opłata, nawet jeśli nie dołączysz żadnych lokacji. W przypadku wybrania opcji utworzenia bramy sieci VPN typu lokacja-lokacja do utworzenia bramy sieci VPN typu lokacja-lokacja w koncentratorze wirtualnym trwa 30 minut. W przeciwieństwie do typu lokacja-lokacja, ExpressRoute lub punkt-lokacja należy najpierw utworzyć centrum, aby można było wdrożyć sieciowe urządzenie wirtualne w sieci wirtualnej centrum.

1. Zlokalizuj utworzoną wirtualną sieć WAN. Na stronie **wirtualna sieć WAN** w obszarze **łączność** wybierz pozycję **centra**.
1. Na stronie **centra** wybierz pozycję + nowe centrum, aby otworzyć stronę **Tworzenie wirtualnego centrum** .

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Podstawy":::
1. Na karcie Tworzenie **podstawy** strony **wirtualnego centrum** wykonaj następujące pola:

   **Szczegóły projektu**

   * Region (wcześniej nazywany lokalizacją)
   * Nazwa
   * Prywatna przestrzeń adresowa centrum. Minimalną przestrzenią adresową jest/24, aby utworzyć centrum, co oznacza, że każdy zakres od/25 do/32 spowoduje wystąpienie błędu podczas tworzenia. Wirtualna sieć WAN platformy Azure, będąca usługą zarządzaną przez firmę Microsoft, tworzy odpowiednie podsieci w koncentratorze wirtualnym dla różnych bram/usług. (Na przykład: wirtualne urządzenia sieciowe, bramy sieci VPN, bramy ExpressRoute, sieci VPN użytkownika/bramy punkt-lokacja, Zapora, routing itp.). Nie ma potrzeby, aby użytkownik jawnie zaplanował przestrzeń adresową podsieci dla usług w koncentratorze wirtualnym, ponieważ firma Microsoft robi to jako część usługi.
1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność.
1. Wybierz pozycję **Utwórz** , aby utworzyć centrum.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Tworzenie sieciowego urządzenia wirtualnego w centrum

W tym kroku utworzysz sieciowe urządzenie wirtualne w centrum. Procedura dla każdego urządzenie WUSu będzie różna dla każdego produktu partnerskiego urządzenie WUS. W tym przykładzie tworzymy bramę WAN Barracuda CloudGen.

1. Znajdź koncentrator wirtualnej sieci WAN utworzony w poprzednim kroku, a następnie otwórz go.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Podstawy":::
1. Znajdź kafelek sieciowe urządzenia wirtualne i wybierz łącze **Utwórz** .
1. W bloku **wirtualnego urządzenia sieciowego** wybierz pozycję **Barracuda CloudGen WAN**, a następnie wybierz przycisk **Utwórz** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Podstawy":::
1. Spowoduje to przejście do oferty portalu Azure Marketplace dotyczącej bramy Barracuda CloudGen WAN. Przeczytaj warunki, a następnie wybierz przycisk **Utwórz** , gdy wszystko będzie gotowe.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Podstawy":::
1. Na stronie **podstawowe** należy podać następujące informacje:

   * **Subskrypcja** — wybierz subskrypcję używaną do wdrożenia wirtualnej sieci WAN i centrum.
   * **Grupa zasobów** — wybierz tę samą grupę zasobów, która została użyta do wdrożenia wirtualnej sieci WAN i centrum.
   * **Region** — wybierz ten sam region, w którym znajduje się zasób koncentratora wirtualnego.
   * **Nazwa aplikacji** — Barracuda NextGen firewall WAN jest zarządzaną aplikacją. Wybierz nazwę, która ułatwia identyfikowanie tego zasobu, ponieważ będzie ona wywoływana, gdy zostanie wyświetlona w subskrypcji.
   * **Zarządzana Grupa zasobów** — jest to nazwa zarządzanej grupy zasobów, w której Barracuda będą wdrażać zarządzane przez nich zasoby. Nazwa powinna być wstępnie wypełniona.
1. Wybierz przycisk **Dalej: CLOUDGEN WAN Gateway** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="Podstawy":::
1. Podaj następujące informacje:

   * **Wirtualne Centrum sieci WAN** — Wirtualne Centrum sieci WAN, do którego chcesz WDROŻYĆ ten urządzenie WUS.
   * **Jednostki infrastruktury urządzenie WUS** — wskazuje liczbę jednostek infrastruktury urządzenie WUS, dla których chcesz WDROŻYĆ ten urządzenie WUS. Wybierz ilość zagregowanej pojemności przepustowości, która ma być udostępniana wszystkim lokacjom oddziałów, które będą łączyć się z tym centrum za pośrednictwem tej urządzenie WUS.
   * **Token** -Barracuda wymaga podania tokenu uwierzytelniania w tym miejscu w celu zidentyfikowania siebie jako zarejestrowanego użytkownika tego produktu. Musisz uzyskać ten dostęp z Barracuda.
1. Wybierz przycisk **Przeglądaj i Utwórz** , aby wykonać operację.
1. Na tej stronie zostanie wyświetlony monit o zaakceptowanie warunków umowy dostępu Co-Admin. Jest to wersja standardowa z zarządzanymi aplikacjami, w których Wydawca będzie miał dostęp do niektórych zasobów w tym wdrożeniu. Zaznacz pole **Zgadzam się na powyższe warunki i postanowienia** , a następnie wybierz pozycję **Utwórz**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Łączenie sieci wirtualnej z centrum

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
* Aby dowiedzieć się więcej na temat urządzeń WUS w wirtualnym koncentratorze sieci WAN, zobacz [Informacje o urządzeniu wirtualnym w sieci wirtualnej (wersja zapoznawcza)](about-nva-hub.md).

---
title: Zarządzanie bezpiecznym dostępem do zasobów w sieci wirtualnych szprych dla klientów P2S
titleSuffix: Azure Virtual WAN
description: Ten artykuł pomaga w korzystaniu z reguł wirtualnych sieci WAN i zapory platformy Azure w celu zarządzania bezpiecznym dostępem do sieci wirtualnych na potrzeby klientów sieci VPN (punkt-lokacja).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048266"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Zarządzanie bezpiecznym dostępem do zasobów w sieci wirtualnych szprych dla klientów sieci VPN użytkowników

W tym artykule opisano sposób używania reguł i filtrów wirtualnych sieci WAN oraz zapory platformy Azure do zarządzania bezpiecznym dostępem do połączeń z zasobami na platformie Azure za pośrednictwem protokołu IKEv2 do lokacji lub otwierania połączeń sieci VPN. Ta konfiguracja jest przydatna, jeśli masz użytkowników zdalnych, dla których chcesz ograniczyć dostęp do zasobów platformy Azure lub zabezpieczyć zasoby na platformie Azure.

Kroki opisane w tym artykule pomagają utworzyć architekturę na poniższym diagramie, aby umożliwić klientom sieci VPN użytkowników dostęp do określonego zasobu (VM1) w sieci wirtualnej szprych połączonej z koncentratorem wirtualnym, ale nie z innymi zasobami (VM2). Użyj tego przykładu architektury jako podstawowej wskazówki.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagram: bezpieczny koncentrator wirtualny" :::

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Dostępne są wartości dla konfiguracji uwierzytelniania, która ma być używana. Na przykład serwer RADIUS, uwierzytelnianie Azure Active Directory lub [generowanie i eksportowanie certyfikatów](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Tworzenie wirtualnej sieci WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definiowanie parametrów konfiguracji P2S

Konfiguracja punkt-lokacja (P2S) definiuje parametry połączenia klientów zdalnych. Ta sekcja pomaga definiować parametry konfiguracji P2S, a następnie utworzyć konfigurację, która będzie używana dla profilu klienta sieci VPN. Poniższe instrukcje zależą od metody uwierzytelniania, która ma być używana.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Po wybraniu metody uwierzytelniania są dostępne trzy opcje. Każda metoda ma określone wymagania. Wybierz jedną z następujących metod, a następnie wykonaj kroki opisane poniżej.

* **Uwierzytelnianie Azure Active Directory:** Uzyskaj następujące czynności:

   * **Identyfikator aplikacji** dla aplikacji przedsiębiorstwa sieci VPN platformy Azure zarejestrowanej w dzierżawie usługi Azure AD.
   * **Wystawca**. Przykład: `https://sts.windows.net/your-Directory-ID`.
   * **Dzierżawa usługi Azure AD**. Przykład: `https://login.microsoftonline.com/your-Directory-ID`.

* **Uwierzytelnianie oparte na usłudze RADIUS:** Uzyskaj adres IP serwera RADIUS, klucz tajny serwera usługi RADIUS i informacje o certyfikacie.

* **Certyfikaty platformy Azure:** W przypadku tej konfiguracji wymagane są certyfikaty. Konieczne jest wygenerowanie lub uzyskanie certyfikatów. Dla każdego klienta wymagany jest certyfikat klienta. Ponadto należy przekazać informacje o certyfikacie głównym (klucz publiczny). Więcej informacji o wymaganych certyfikatach znajduje się w temacie [generowanie i eksportowanie certyfikatów](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Tworzenie centrum i bramy

W tej sekcji utworzysz koncentrator wirtualny z bramą punkt-lokacja. Podczas konfigurowania programu można użyć następujących przykładowych wartości:

* **Prywatna przestrzeń adresów IP centrum:** 10.0.0.0/24
* **Pula adresów klienta:** 10.5.0.0/16
* **Niestandardowe serwery DNS:** Można wyświetlić maksymalnie 5 serwerów DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generuj pliki konfiguracji klienta sieci VPN

Ta sekcja umożliwia generowanie i pobieranie plików profilu konfiguracji. Te pliki służą do konfigurowania natywnego klienta sieci VPN na komputerze klienckim. Informacje o zawartości plików profilu klienta znajdują się w temacie [Konfiguracja typu punkt-lokacja — certyfikaty](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Konfigurowanie klientów sieci VPN

Użyj pobranego profilu, aby skonfigurować klientów zdalnego dostępu. Procedura dla każdego systemu operacyjnego jest inna, postępuj zgodnie z instrukcjami dotyczącymi systemu.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Łączenie sieci wirtualnej szprychy

W tej sekcji dołączono sieć wirtualną szprychy do wirtualnego koncentratora sieci WAN.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz dwie maszyny wirtualne w sieci wirtualnej, VM1 i VM2. W diagramie sieciowym korzystamy z 10.18.0.4 i 10.18.0.5. Podczas konfigurowania maszyn wirtualnych upewnij się, że została wybrana Sieć wirtualna, która została utworzona na karcie Sieć. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Szybki Start: Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Zabezpieczanie koncentratora wirtualnego

Standardowe centrum wirtualne nie ma wbudowanych zasad zabezpieczeń, aby chronić zasoby w sieciach wirtualnych szprych. Bezpieczny koncentrator wirtualny używa zapory platformy Azure lub innego dostawcy do zarządzania ruchem przychodzącym i wychodzącym w celu ochrony zasobów na platformie Azure.

Przekonwertuj centrum na zabezpieczone centrum, korzystając z następującego artykułu: [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Tworzenie reguł dotyczących zarządzania ruchem i ich filtrowania

Utwórz reguły określające zachowanie zapory platformy Azure. Dzięki zabezpieczeniu centrum firma Microsoft gwarantuje, że wszystkie pakiety wprowadzane do centrum wirtualnego podlegają przetwarzaniu zapory przed uzyskaniem dostępu do zasobów platformy Azure.

Po wykonaniu tych kroków zostanie utworzona architektura umożliwiająca użytkownikom sieci VPN dostęp do maszyny wirtualnej z prywatnym adresem IP 10.18.0.4, ale **nie** dostęp do maszyny wirtualnej przy użyciu prywatnego adresu IP 10.18.0.5

1. W Azure Portal przejdź do **Menedżera zapory**.
1. W obszarze Zabezpieczenia wybierz pozycję **zasady zapory platformy Azure**.
1. Wybierz pozycję **Utwórz zasady zapory platformy Azure**.
1. W obszarze **Szczegóły zasad** wpisz nazwę i wybierz region, w którym jest wdrożone centrum wirtualne.
1. Wybierz pozycję **Dalej: ustawienia DNS (wersja zapoznawcza)**.
1. Wybierz pozycję **Dalej: reguły**.
1. Na karcie **reguły** wybierz pozycję **Dodaj kolekcję reguł**.
1. Podaj nazwę kolekcji. Ustaw typ jako **Sieć**. Dodaj wartość priorytetu **100**.
1. Wypełnij pola Nazwa reguły, typ źródła, źródło, protokół, porty docelowe i typ docelowy, jak pokazano w poniższym przykładzie. Następnie wybierz pozycję **Dodaj**. Ta reguła umożliwia dowolnemu adresowi IP z puli klienta sieci VPN dostęp do maszyny wirtualnej z prywatnym adresem IP 10.18.04, ale nie z żadnym innym zasobem podłączonym do koncentratora wirtualnego. Utwórz odpowiednie reguły, które pasują do żądanej architektury i reguł uprawnień.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Reguły zapory" :::

1. Wybierz pozycję **Dalej: analiza zagrożeń**.
1. Wybierz pozycję **Dalej: centra**.
1. Na karcie **centra** wybierz opcję **Skojarz centra wirtualne**.
1. Wybierz utworzony wcześniej centrum wirtualne, a następnie wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

Ukończenie tego procesu może potrwać co najmniej 5 minut.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Kierowanie ruchu przez zaporę platformy Azure

W tej sekcji należy upewnić się, że ruch jest kierowany przez zaporę platformy Azure.

1. W portalu w obszarze **Menedżer zapory** wybierz pozycję **zabezpieczone centra wirtualne**.
1. Wybierz utworzony centrum wirtualne.
1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja zabezpieczeń**.
1. W obszarze **ruch prywatny** wybierz pozycję **Wyślij za pośrednictwem zapory platformy Azure**.
1. Sprawdź, czy połączenie sieci wirtualnej i ruch prywatny połączenia gałęzi są zabezpieczone przez zaporę platformy Azure.
1. Wybierz pozycję **Zapisz**.

## <a name="validate"></a><a name="validate"></a>Walidacja

Sprawdź konfigurację bezpiecznego centrum.

1. Nawiąż połączenie z **bezpiecznym koncentratorem wirtualnym** za pośrednictwem sieci VPN z urządzenia klienckiego.
1. Wyślij polecenie ping do adresu IP **10.18.0.4** z klienta. Powinna zostać wyświetlona odpowiedź.
1. Wyślij polecenie ping do adresu IP **10.18.0.5** z klienta. Nie powinno być możliwe wyświetlenie odpowiedzi.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że **tabela obowiązujące trasy** w zabezpieczonym koncentratorze wirtualnym ma następny przeskok dla ruchu prywatnego przez zaporę. Aby uzyskać dostęp do tabeli obowiązujące trasy, przejdź do zasobu **koncentratora wirtualnego** . W obszarze **łączność** wybierz pozycję **Routing**, a następnie wybierz pozycję **efektywne trasy**. Z tego miejsca wybierz **domyślną** tabelę tras.
* Sprawdź, czy utworzono reguły w sekcji [Tworzenie reguł](#create-rules) . Jeśli te kroki nie zostaną pominięte, utworzone reguły nie zostaną skojarzone z centrum, a tabela tras i przepływ pakietów nie będą używać zapory platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz temat [często zadawane pytania dotyczące wirtualnej sieci WAN](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat zapory platformy Azure, zobacz [często zadawane pytania dotyczące zapory platformy Azure](../firewall/firewall-faq.yml).

---
title: Azure bastionu | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure bastionu, która zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bez udostępniania portów protokołu RDP/SSH zewnętrznie.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 09/01/2020
ms.author: cherylmc
ms.openlocfilehash: 680eb8414696109e8cc15d82d8bd5f2c5740f488
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89291878"
---
# <a name="what-is-azure-bastion"></a>Co to jest usługa Azure Bastion?

Usługa Azure bastionu to nowa w pełni oparta na platformie usługa PaaS, która jest dostarczana w ramach sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu TLS. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP.

Bastionu zapewnia bezpieczną łączność RDP i SSH ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której jest inicjowana obsługa administracyjna. Korzystanie z usługi Azure bastionu chroni maszyny wirtualne przed udostępnieniem portów protokołu RDP/SSH na świecie zewnętrznym, zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH. Za pomocą usługi Azure bastionu można nawiązać połączenie z maszyną wirtualną bezpośrednio z Azure Portal. Nie jest potrzebny dodatkowy klient, agent ani oprogramowanie.

## <a name="architecture"></a>Architektura

Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej.

Protokoły RDP i SSH stanowią podstawowe metody, za pomocą których można nawiązywać połączenia z obciążeniami działającymi na platformie Azure. Udostępnianie portów protokołu RDP/SSH przez Internet nie jest wymagane i jest widoczne jako znacząca powierzchnia zagrożenia. Jest to często spowodowane lukami w zabezpieczeniach protokołów. Aby można było obsłużyć tę powierzchnię zagrożenia, możesz wdrożyć hosty bastionu (znane również jako serwery przeskoków) po stronie publicznej sieci obwodowej. Serwery hosta bastionu są zaprojektowane i skonfigurowane pod kątem ataków. Serwery bastionu zapewniają również łączność RDP i SSH z obciążeniami znajdującymi się w tle, a także w sieci.

![Architektura usługi Azure bastionu](./media/bastion-overview/architecture.png)

Na tym rysunku przedstawiono architekturę wdrożenia usługi Azure bastionu. Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik nawiązuje połączenie z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik wybierze maszynę wirtualną, z którą ma zostać nawiązane połączenie.
* Po jednym kliknięciu sesja RDP/SSH zostanie otwarta w przeglądarce.
* Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP.

## <a name="key-features"></a>Najważniejsze funkcje

Dostępne są następujące funkcje:

* **Protokoły RDP i SSH bezpośrednio w Azure Portal:** Możesz bezpośrednio uzyskać dostęp do sesji RDP i SSH bezpośrednio w Azure Portal przy użyciu jednego kliknięcia.
* **Sesja zdalna przez protokół TLS i Przechodzenie zapory dla protokołu RDP/SSH:** Usługa Azure bastionu używa klienta sieci Web opartego na języku HTML5, który jest automatycznie przesyłany strumieniowo do urządzenia lokalnego, dzięki czemu można uzyskać sesję protokołu RDP/SSH za pośrednictwem protokołu TLS na porcie 443, co umożliwia bezpieczne przechodzenie przez firmowe zapory.
* **Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP:** Usługa Azure bastionu otwiera połączenie RDP/SSH z maszyną wirtualną platformy Azure przy użyciu prywatnego adresu IP na maszynie wirtualnej. Nie potrzebujesz publicznego adresu IP na maszynie wirtualnej.
* **Brak problemów z zarządzaniem sieciowych grup zabezpieczeń:** Azure bastionu to w pełni zarządzana usługa platformy PaaS z platformy Azure, która jest używana wewnętrznie w celu zapewnienia bezpiecznej łączności RDP/SSH. Nie musisz stosować żadnych sieciowych grup zabezpieczeń w podsieci usługi Azure bastionu. Ponieważ usługa Azure bastionu łączy się z maszynami wirtualnymi za pośrednictwem prywatnego adresu IP, można skonfigurować sieciowych grup zabezpieczeń, aby zezwalała na używanie protokołu RDP/SSH tylko na platformie Azure bastionu. Pozwala to usunąć problemy związane z zarządzaniem sieciowych grup zabezpieczeń za każdym razem, gdy będzie konieczne bezpieczne łączenie się z maszynami wirtualnymi.
* **Ochrona przed skanowaniem portów:** Ponieważ nie musisz uwidaczniać maszyn wirtualnych w publicznej sieci Internet, Twoje maszyny wirtualne są chronione przed skanowaniem portów przez nieautoryzowanych i złośliwych użytkowników znajdujących się poza siecią wirtualną.
* **Ochrona przed atakami na zero dni. Ograniczanie funkcjonalności tylko w jednym miejscu:** Azure bastionu to w pełni zarządzana platforma usługi PaaS. Ponieważ znajduje się on na obrzeżu sieci wirtualnej, nie trzeba martwić się o zabezpieczenia wszystkich maszyn wirtualnych w sieci wirtualnej. Platforma Azure chroni przed wielodniowymi programami wykorzystującymi luki w zabezpieczeniach, utrzymując ochronę systemu Azure bastionu i zawsze na bieżąco.

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Zasubskrybuj źródło danych RSS i zapoznaj się z najnowszymi aktualizacjami funkcji platformy Azure bastionu na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion) .

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Utwórz zasób hosta usługi Azure bastionu](bastion-create-host-portal.md).
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

---
title: Azure bastionu | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure bastionu, która zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bez udostępniania portów protokołu RDP/SSH zewnętrznie.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: f4aa1ef29ffb27efb29d969e48af4cf5288e08ae
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183916"
---
# <a name="what-is-azure-bastion"></a>Co to jest usługa Azure Bastion?

Azure bastionu to wdrażana usługa, która umożliwia łączenie się z maszyną wirtualną przy użyciu przeglądarki i Azure Portal. Usługa Azure bastionu to w pełni zarządzana platformą usługa PaaS, która jest dostarczana w ramach sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio z Azure Portal za pośrednictwem protokołu TLS. Po nawiązaniu połączenia za pośrednictwem usługi Azure bastionu maszyny wirtualne nie potrzebują publicznego adresu IP, agenta ani specjalnego oprogramowania klienckiego.

Bastionu zapewnia bezpieczną łączność RDP i SSH ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której jest inicjowana obsługa administracyjna. Korzystanie z usługi Azure bastionu chroni maszyny wirtualne przed udostępnieniem portów protokołu RDP/SSH na świecie zewnętrznym, zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH.

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

* [Samouczek: Tworzenie hosta usługi Azure bastionu i nawiązywanie połączenia z maszyną wirtualną z systemem Windows](tutorial-create-host-portal.md).
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

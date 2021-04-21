---
title: Utwórz ofertę maszyny wirtualnej na Azure Marketplace.
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej na platformie handlowej firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/08/2021
ms.openlocfilehash: f0c1d9d528ed4fbf61786042fb6fb34f05fec5d5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812597"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Jak utworzyć ofertę maszyny wirtualnej na Azure Marketplace

W tym artykule opisano sposób tworzenia oferty maszyny wirtualnej platformy Azure [dla](https://azuremarketplace.microsoft.com/)Azure Marketplace . Dotyczy zarówno maszyn wirtualnych z systemem Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) i maksymalnie 16 dysków danych.

Przed rozpoczęciem utwórz konto [platformy handlowej w witrynie Partner Center](create-account.md). Upewnij się, że Twoje konto jest zarejestrowane w programie platformy handlowej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli jeszcze tego nie zrobiono, zapoznaj się z ofertą [Planowanie maszyny wirtualnej.](marketplace-virtual-machines.md) Objaśnia ona wymagania techniczne dotyczące maszyny wirtualnej oraz zawiera listę informacji i zasobów, które będą potrzebne podczas tworzenia oferty.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).
2. W okienku po lewej stronie wybierz pozycję **Omówienie komercyjnej**  >  **platformy handlowej.**
3. Na stronie **Przegląd** wybierz pozycję **+ Nowa oferta** Maszyna  >  **wirtualna platformy Azure.**

    ![Zrzut ekranu przedstawiający opcje menu okienka po lewej stronie i przycisk "Nowa oferta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po opublikowaniu oferty wszelkie zmiany, które w nim wyczyszczysz, Partner Center w oknie Azure Marketplace dopiero po jej ponownej opublikowaniu. Pamiętaj, aby zawsze ponownie opublikować ofertę po wrzuceniu w nie zmian.

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty Azure Marketplace oraz w usługach Azure PowerShell i interfejsie wiersza polecenia platformy Azure, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Identyfikator może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczony do 50 znaków. Jeśli na przykład **wpiszemy wartość test-offer-1,** adres internetowy oferty będzie mieć wartość `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz**.

Wprowadź alias **oferty**. Alias oferty to nazwa, która jest używana dla oferty w Partner Center.

- Ta nazwa nie jest używana w Azure Marketplace. Różni się ona od nazwy oferty i innych wartości wyświetlanych klientom.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę i kontynuować. Partner Center zostanie otwarta **strona Konfiguracja** oferty.

## <a name="enable-a-test-drive-optional"></a>Włączanie dysku testowego (opcjonalnie)

Dysk testowy to doskonały sposób prezentowania oferty potencjalnym klientom przez udzielenie im dostępu do wstępnie skonfigurowanego środowiska przez stałą liczbę godzin. Oferowanie dysku testowego powoduje zwiększenie współczynnika konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [Co to jest dysk testowy?](./what-is-test-drive.md).

> [!TIP]
> Wersja testowa różni się od bezpłatnej wersji próbnej. Możesz zaoferować wersję testową, bezpłatną wersję próbną lub obie te wersje. Oba te rozwiązania zapewniają klientom Twoje rozwiązanie na stały okres. Jednak test obejmuje również praktyczne, samodzielne przewodnik po kluczowych funkcjach i korzyściach produktu, które są demonstrowane w rzeczywistym scenariuszu implementacji.

Aby włączyć dysk testowy, zaznacz **pole wyboru Włącz dysk** testowy. Później skonfigurujesz dysk testowy. W przypadku wersji testowej wymagane jest skonfigurowanie systemu CRM (zobacz następną sekcję).

## <a name="configure-customer-leads-management"></a>Konfigurowanie zarządzania potencjalnymi klientami

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem do następnej karty w menu nav po lewej **stronie, Właściwości.**

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować właściwości oferty maszyny wirtualnej](azure-vm-create-properties.md)
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
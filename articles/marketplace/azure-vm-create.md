---
title: Utwórz ofertę maszyny wirtualnej w witrynie Azure Marketplace.
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej w portalu komercyjnym firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: bfd67688ce30b62002e26cea9f7be4df1cb6e622
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126451"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Jak utworzyć ofertę maszyny wirtualnej w witrynie Azure Marketplace

W tym artykule opisano sposób tworzenia oferty maszyny wirtualnej platformy Azure dla [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/). Dotyczy zarówno maszyn wirtualnych opartych na systemie Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) oraz maksymalnie 16 dysków danych.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](partner-center-portal/create-account.md). Upewnij się, że Twoje konto zostało zarejestrowane w komercyjnym programie Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli jeszcze tego nie zrobiono, zapoznaj [się z tematem planowanie oferty maszyny wirtualnej](marketplace-virtual-machines.md). Wyjaśni wymagania techniczne dla maszyny wirtualnej oraz listę informacji i zasobów potrzebnych podczas tworzenia oferty. 

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W okienku po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview** .
3. Na stronie **Przegląd** wybierz pozycję **Nowa oferta**  >  **Azure Virtual Machine** .

    ![Zrzut ekranu przedstawiający opcje menu po lewej stronie i przycisk "Nowa oferta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po opublikowaniu oferty wszelkie zmiany wprowadzone w centrum partnerskim są wyświetlane w witrynie Azure Marketplace dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie opublikować ofertę po wprowadzeniu w niej zmian.

Wprowadź **Identyfikator oferty** . To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web oferty usługi Azure Marketplace oraz w Azure PowerShell i interfejsu wiersza polecenia platformy Azure, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Identyfikator może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczony do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1** , adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz** .

Wprowadź **alias oferty** . Alias oferty to nazwa, która jest używana na potrzeby oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Azure Marketplace. Różni się od nazwy oferty i innych wartości, które są widoczne dla klientów.

## <a name="enable-a-test-drive-optional"></a>Włącz stację testową (opcjonalnie)

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, zapewniając im dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Oferowanie dysku testowego skutkuje zwiększoną szybkością konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](./what-is-test-drive.md)

> [!TIP]
> Stacja testowa jest różna od bezpłatnej wersji próbnej. Możesz zaoferować dysk testowy, bezpłatny okres próbny lub oba te elementy. Oba te osoby zapewniają klientom swoje rozwiązanie przez stały okres. Jednak dysk testowy zawiera również praktyczny przewodnik po najważniejszych funkcjach produktu i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

**Aby włączyć stację testową**
1.  W obszarze **testowanie dysku** zaznacz pole wyboru **Włącz dysk testowy** .
1.  Wybierz typ dysku testowego z wyświetlonej listy.

## <a name="configure-lead-management"></a>Konfigurowanie zarządzania potencjalnymi klientami

Po opublikowaniu oferty na komercyjnym rynku z Centrum partnerskiego połącz ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu. Połączenie z programem CRM jest wymagane, jeśli chcesz włączyć dysk testowy (zobacz poprzednią sekcję). W przeciwnym razie połączenie z programem CRM jest opcjonalne.

1. w obszarze **potencjalni klienci** wybierz link **Połącz** .
1. W oknie dialogowym **szczegóły połączenia** wybierz lokalizację docelową potencjalnego klienta z listy.
1. Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Aby sprawdzić podaną konfigurację, wybierz link **Weryfikuj** .
1. Aby zamknąć okno dialogowe, wybierz **przycisk OK** .

## <a name="resell-through-csps"></a>Odsprzedaż za poorednictwem dostawców CSP

Rozszerz zasięg oferty, udostępniając ją partnerom w programie [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Wszystkie plany przyłączenia do własnej licencji (BYOL) są automatycznie dodanych do programu. Możesz również zdecydować się na wybór planów BYOL.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj właściwości oferty maszyny wirtualnej](azure-vm-create-properties.md)
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
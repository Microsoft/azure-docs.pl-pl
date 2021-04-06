---
title: Utwórz ofertę maszyny wirtualnej w witrynie Azure Marketplace.
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej w portalu komercyjnym firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ffc09daa15e742ca2b5b8a2fa9323e33fe317c60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200386"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Jak utworzyć ofertę maszyny wirtualnej w witrynie Azure Marketplace

W tym artykule opisano sposób tworzenia oferty maszyny wirtualnej platformy Azure dla [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/). Dotyczy zarówno maszyn wirtualnych opartych na systemie Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) oraz maksymalnie 16 dysków danych.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](partner-center-portal/create-account.md). Upewnij się, że Twoje konto zostało zarejestrowane w komercyjnym programie Marketplace.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli jeszcze tego nie zrobiono, zapoznaj [się z tematem planowanie oferty maszyny wirtualnej](marketplace-virtual-machines.md). Wyjaśni wymagania techniczne dla maszyny wirtualnej oraz listę informacji i zasobów potrzebnych podczas tworzenia oferty.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W okienku po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  .
3. Na stronie **Przegląd** wybierz pozycję **+ Nowa oferta**  >  **Azure Virtual Machine**.

    ![Zrzut ekranu przedstawiający opcje menu po lewej stronie i przycisk "Nowa oferta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po opublikowaniu oferty wszelkie zmiany wprowadzone w centrum partnerskim są wyświetlane w witrynie Azure Marketplace dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie opublikować ofertę po wprowadzeniu w niej zmian.

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web oferty usługi Azure Marketplace oraz w Azure PowerShell i interfejsu wiersza polecenia platformy Azure, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Identyfikator może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczony do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Alias oferty to nazwa, która jest używana na potrzeby oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Azure Marketplace. Różni się od nazwy oferty i innych wartości, które są widoczne dla klientów.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować. W centrum partnerskim zostanie otwarta strona **Konfiguracja oferty** .

## <a name="enable-a-test-drive-optional"></a>Włącz stację testową (opcjonalnie)

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, zapewniając im dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Oferowanie dysku testowego skutkuje zwiększoną szybkością konwersji i generuje wysoce kwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](./what-is-test-drive.md)

> [!TIP]
> Stacja testowa jest różna od bezpłatnej wersji próbnej. Możesz zaoferować dysk testowy, bezpłatny okres próbny lub oba te elementy. Oba te osoby zapewniają klientom swoje rozwiązanie przez stały okres. Jednak dysk testowy zawiera również praktyczny przewodnik po najważniejszych funkcjach produktu i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

Aby włączyć dysk testowy, zaznacz pole wyboru **Włącz dysk testowy** . Dysk testowy zostanie skonfigurowany później. W przypadku dysku testowego wymagane jest skonfigurowanie programu CRM (zobacz następną sekcję).

## <a name="configure-customer-leads-management"></a>Konfigurowanie zarządzania klientami

Po opublikowaniu oferty na komercyjnym rynku z Centrum partnerskiego połącz ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu. Połączenie z programem CRM jest wymagane, jeśli chcesz włączyć dysk testowy (zobacz poprzednią sekcję). W przeciwnym razie połączenie z programem CRM jest opcjonalne.

1. W obszarze **potencjalni klienci** wybierz łącze **Połącz** .
1. W oknie dialogowym **szczegóły połączenia** wybierz miejsce docelowe potencjalnego klienta.
1. Wypełnij pola, które są wyświetlane. Aby uzyskać szczegółowe instrukcje, zobacz następujące artykuły:

   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Skonfiguruj ofertę do wysyłania potencjalnych klientów do usługi dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dawniej Dynamics CRM Online)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Aby sprawdzić podaną konfigurację, wybierz link **Weryfikuj** .
1. Wybierz pozycję **Połącz**.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu po lewej stronie, **Właściwości**.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować właściwości oferty maszyny wirtualnej](azure-vm-create-properties.md)
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
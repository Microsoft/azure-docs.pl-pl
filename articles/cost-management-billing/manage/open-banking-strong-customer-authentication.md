---
title: Otwarta bankowość (PSD2) i silne uwierzytelnianie klienta (SCA) dla klientów platformy Azure
description: W tym artykule wyjaśniono, dlaczego uwierzytelnianie wieloskładnikowe jest wymagane w przypadku niektórych zakupów na platformie Azure i jak ukończyć uwierzytelnianie.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b4c2db76405e7bad7653c1801eb2bcc5db82fe9b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684800"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Otwarta bankowość (PSD2) i silne uwierzytelnianie klienta (SCA) dla klientów platformy Azure

Od 14 września 2019 r. banki w 31 krajach/regionach [Europejskiego Obszaru Gospodarczego](https://en.wikipedia.org/wiki/European_Economic_Area) są zobowiązane do zweryfikowania tożsamości osoby dokonującej zakupu w trybie online przed przetworzeniem płatności. Ta weryfikacja wymaga uwierzytelniania wieloskładnikowego, aby pomóc w zagwarantowaniu, że zakupy online są bezpieczne i chronione. W niektórych krajach/regionach data wejścia w życie tych wymagań zostanie przesunięta. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące dyrektywy PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Jakie znaczenie ma dyrektywa PSD2 dla klientów platformy Azure

Jeśli płacisz za platformę Azure za pomocą karty kredytowej wydanej przez bank w [Europejskim Obszarze Gospodarczym](https://en.wikipedia.org/wiki/European_Economic_Area), może być konieczne przeprowadzenie uwierzytelniania wieloskładnikowego dla formy płatności Twojego konta. Prośba o przeprowadzenie uwierzytelniania wieloskładnikowego może być też wyświetlana podczas tworzenia lub podwyższania poziomu konta na platformie Azure — nawet jeśli w danym momencie nie są dokonywane żadne zakupy. Wymóg dokonania uwierzytelniania wieloskładnikowego może też się pojawić w innych sytuacjach, na przykład podczas zmiany formy płatności na koncie platformy Azure, usuwania limitu wydatków lub dokonywania natychmiastowych płatności z poziomu witryny Azure Portal, takich jak rozliczanie zaległych sald lub kupowanie środków na korzystanie z platformy Azure.

Jeśli bank odrzuci miesięczne opłaty za korzystanie z platformy Azure, otrzymasz wiadomość e-mail z informacją o upłynięciu terminu płatności i instrukcjami dotyczącymi rozwiązania tego problemu. W takim przypadku możesz przeprowadzić proces uwierzytelniania wieloskładnikowego i rozliczyć zaległe opłaty w witrynie Azure Portal.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Przeprowadzanie uwierzytelniania wieloskładnikowego w witrynie Azure Portal

W poniższych sekcjach opisano sposób przeprowadzania uwierzytelniania wieloskładnikowego w witrynie Azure Portal. Skorzystaj z tych informacji, które mają zastosowanie w Twoim przypadku.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Zmiana aktywnej formy płatności dla konta platformy Azure

Wykonaj następujące czynności, aby zmienić aktywną formę płatności dla konta platformy Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta i przejdź do obszaru **Zarządzanie kosztami i rozliczenia**.
2. Na stronie **Przegląd** wybierz odpowiednią subskrypcję z siatki **Moje subskrypcje**.
3. W obszarze Rozliczenia wybierz pozycję **Formy płatności**. Możesz dodać nową kartę kredytową lub ustawić istniejącą kartę jako aktywną formę płatności dla subskrypcji. Jeśli Twój bank wymaga uwierzytelniania wieloskładnikowego, zostanie wyświetlony monit o ukończenie uwierzytelniania.

Aby uzyskać więcej informacji, zobacz [Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Rozliczanie zaległych opłat za usługi platformy Azure

Jeśli Twój bank odrzuci opłaty, stan konta platformy Azure w witrynie Azure Portal zmieni się na **Upłynął termin**. Wykonaj następujące czynności, aby sprawdzić stan konta:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.
2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
3. Na stronie **Zarządzanie kosztami i rozliczenia** **Przegląd** przejrzyj kolumnę stanu w siatce **Moje subskrypcje**.
4. Jeśli Twoja subskrypcja ma etykietę **Upłynął termin**, kliknij pozycję **Ureguluj saldo**. Zostanie wyświetlony monit o ukończenie uwierzytelniania wieloskładnikowego.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Rozliczanie zaległych opłat za zakupy w witrynie Marketplace i rezerwacje

Zakupy w witrynie Marketplace i opłaty za rezerwacje są rozliczane niezależnie od opłat za usługi platformy Azure. Jeśli Twój bank odrzuci opłaty za zakupy w witrynie Marketplace lub za rezerwacje, faktura stanie się przeterminowana, a w witrynie Azure Portal będzie widoczna opcja **Zapłać teraz**. Aby opłacić przeterminowane faktury za zakupy w witrynie Marketplace i za rezerwacje, wykonaj następujące czynności:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.
2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
3. W obszarze Rozliczenia wybierz pozycję **Faktury**.
5. Za pomocą filtru w postaci listy rozwijanej subskrypcji wybierz subskrypcję skojarzoną z zakupem w witrynie Marketplace lub rezerwacji.
6. W siatce faktur przejrzyj kolumnę typu. Jeśli typ to **Azure Marketplace i rezerwacje**, a termin płatności faktury zbliża się lub upłynął, będzie widoczny link **Zapłać teraz**. Jeśli link **Zapłać teraz** nie jest widoczny, faktura została już opłacona. W procesie płatności zostanie wyświetlony monit o ukończenie uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
- Jeśli chcesz uregulować opłaty za korzystanie z platformy Azure, zobacz [Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure](resolve-past-due-balance.md).

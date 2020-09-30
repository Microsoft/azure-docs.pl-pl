---
title: Wiadomość e-mail z platformy Azure dotycząca zaległego salda
description: Opis sposobu dokonywania płatności w przypadku, gdy Twoja subskrypcja platformy Azure ma zaległe saldo
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 3c42db58017dc3a692c227f6eb454cf7b597e77b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297588"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure

Ten artykuł dotyczy klientów, którzy zarejestrowali się w usłudze Azure Online przy użyciu karty kredytowej i mają konto rozliczeniowe programu Microsoft Online Services. Dowiedz się, jak [sprawdzić typ konta rozliczeniowego](#check-the-type-of-your-account). Jeśli masz konto rozliczeniowe dla umowy klienta firmy Microsoft, zapoznaj się z tematem [Opłacanie rachunku za korzystanie z platformy Microsoft Azure](../understand/pay-bill.md).

Jeśli nie otrzymamy Twojej płatności lub jeśli nie będziemy mogli jej przetworzyć, otrzymasz wiadomość e-mail i zobaczysz alert w witrynie Azure Portal z informacją o tym, że Twoja subskrypcja ma zaległe opłaty. Jeśli domyślną formą płatności jest karta kredytowa, [administrator konta](../understand/subscription-transfer.md#whoisaa) może uregulować zaległe opłaty w witrynie Azure Portal. W przypadku płatności na podstawie faktury (czekiem lub przelewem) wyślij płatność do lokalizacji wskazanej na dole faktury.

> [!IMPORTANT]
> * Jeśli masz wiele subskrypcji korzystających z tej samej karty kredytowej i za wszystkie z nich masz zaległe płatności, musisz jednorazowo uregulować całe należne saldo.
> * Karta kredytowa, za pomocą której uregulujesz zaległe opłaty, stanie się nową domyślną formą płatności dla wszystkich subskrypcji, które korzystały z formy płatności zakończonej niepowodzeniem.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Rozwiązywanie problemów dotyczących zaległego salda w witrynie Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. Wybierz subskrypcję, dla której minął termin płatności, na stronie **Przegląd**.
1. Na stronie **Przegląd subskrypcji** kliknij czerwony baner zaległości, aby rozliczyć saldo.
    > [!NOTE]
    > Jeśli nie jesteś administratorem konta, nie będziesz w stanie uregulować salda.
1. Na nowej stronie **Ureguluj saldo** kliknij pozycję **Wybierz formę płatności**.
    ![Zrzut ekranu pokazujący link umożliwiający wybór formy płatności](./media/resolve-past-due-balance/settle-balance-screen.png)

1. W nowym bloku po prawej stronie wybierz kartę kredytową z listy rozwijanej lub dodaj nową, klikając niebieski link **Dodaj nową formę płatności**. Ta karta kredytowa stanie się aktywną formą płatności dla wszystkich subskrypcji, które aktualnie korzystają z formy płatności zakończonej niepowodzeniem.
     > [!NOTE]
     > * Łączne należne saldo odzwierciedla zaległe opłaty za wszystkie usługi firmy Microsoft korzystające z formy płatności zakończonej niepowodzeniem.
     > * Jeśli wybrana forma płatności ma także zaległe opłaty za usługi firmy Microsoft, zostanie to odzwierciedlone w łącznym należnym saldzie. Te zaległe opłaty muszą zostać również uregulowane.
1. Kliknij pozycję **Zapłać**.

## <a name="troubleshoot-declined-credit-card"></a>Rozwiązywanie problemów z odrzuconą kartą kredytową

W razie odrzucenia przez instytucję finansową płatności za pomocą karty kredytowej skontaktuj się z tą instytucją, aby rozwiązać ten problem. W swoim banku upewnij się, że spełnione są te warunki:
- Na karcie są włączone transakcje międzynarodowe.
- Karta ma wystarczający limit kredytowy lub środki do rozliczenia salda.
- Na karcie są włączone płatności cykliczne.

## <a name="not-getting-billing-email-notifications"></a>Nie otrzymujesz powiadomień e-mail dotyczących rozliczeń?

Jeśli jesteś administratorem konta, [sprawdź, jaki adres e-mail jest używany na potrzeby powiadomień](change-azure-account-profile.md). Zalecamy korzystanie z adresu e-mail, który jest regularnie sprawdzany. Jeśli adres e-mail jest prawidłowy, sprawdź swój folder spamu.

## <a name="if-i-forget-to-pay-what-happens"></a>Co się stanie, jeśli zapomnę uregulować płatność?

Usługa zostanie anulowana, a Twoje zasoby nie będą już dostępne. Twoje dane zostaną usunięte z platformy Azure po 90 dniach od zakończenia usługi. Aby dowiedzieć się więcej, zobacz [Centrum zaufania firmy Microsoft — jak zarządzamy Twoimi danymi](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Jeśli wiesz, że Twoja płatność została przetworzona, ale Twoja subskrypcja jest nadal wyłączona, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Sprawdzanie typu konta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

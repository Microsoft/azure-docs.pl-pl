---
title: Połącz swój identyfikator partnera, aby śledzić wpływ na delegowane zasoby
description: Dowiedz się, jak skojarzyć swój identyfikator partnera, aby otrzymywać środki na korzystanie z partnerów (PEC) w zasobach klientów zarządzanych za pomocą usługi Azure Lighthouse.
ms.date: 01/28/2021
ms.topic: how-to
ms.openlocfilehash: 46338b925b1ad44019c9cc95e4b7f8c0963c07c4
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985975"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Połącz swój identyfikator partnera, aby śledzić wpływ na delegowane zasoby 

Jeśli jesteś członkiem [Microsoft Partner Network](https://partner.microsoft.com/), możesz połączyć swój identyfikator partnera z poświadczeniami używanymi do zarządzania delegowanymi zasobami klientów, co pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy kończą sukcesy klientów platformy Azure. Ten link umożliwia także partnerom [CSP (dostawcy rozwiązań w chmurze)](/partner-center/csp-overview) otrzymywanie środków na korzystanie z [usług zarządzanych (Pec](/partner-center/partner-earned-credit) ) dla klientów, którzy [podpisali umowę klienta firmy Microsoft (MCA)](/partner-center/confirm-customer-agreement) i są [objęte planem platformy Azure](/partner-center/azure-plan-get-started).

Jeśli dołączysz [klientów z ofertami usług zarządzanych w portalu Azure Marketplace](publish-managed-services-offers.md), konsolidacja odbywa się automatycznie przy użyciu identyfikatora MPN skojarzonego z kontem Centrum partnerskiego używanym do publikowania ofert. Nie jest wymagana żadna dodatkowa akcja, aby śledzić wpływ tych klientów.

W przypadku dołączania [klientów przy użyciu szablonów usługi Azure Resource Management](onboard-customer.md)należy wykonać akcję tworzenia tego linku. W tym celu należy [połączyć swój identyfikator MPN](../../cost-management-billing/manage/link-partner-id.md) z co najmniej jednym kontem użytkownika w dzierżawie zarządzającej, który ma dostęp do każdej z dołączanych subskrypcji.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Skojarz swój identyfikator partnera podczas dołączania nowych klientów

Podczas dołączania klientów za pomocą szablonów Azure Resource Manager (szablony ARM) Użyj poniższego procesu, aby połączyć identyfikator partnera (i włączyć środki dla partnerów, jeśli ma to zastosowanie). Aby wykonać te czynności, musisz znać [Identyfikator partnera MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Pamiętaj, aby użyć wartości **Skojarzony identyfikator MPN** wyświetlanej w profilu partnera.

Dla uproszczenia zalecamy utworzenie konta nazwy głównej usługi w dzierżawie, połączenie go ze **skojarzonym identyfikatorem MPN**, a następnie udzielenie mu dostępu do każdego klienta, który zostanie dołączony do [roli wbudowanej platformy Azure, która kwalifikuje się do komputera PEC](/partner-center/azure-roles-perms-pec).

1. [Utwórz konto nazwy głównej usługi](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) w dzierżawie zarządzającej. W tym przykładzie użyjemy *konta automatyzacji dostawcy* nazw dla tej nazwy głównej usługi.
1. Korzystając z tego konta usługi, [Połącz się ze skojarzonym identyfikatorem MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) w dzierżawie zarządzającej. Wystarczy to zrobić tylko raz.
1. Po dołączeniu [klienta przy użyciu szablonów usługi ARM](onboard-customer.md)Pamiętaj o uwzględnieniu autoryzacji, która obejmuje konto automatyzacji dostawcy jako użytkownika z [wbudowaną rolą platformy Azure, która kwalifikuje się do komputera PEC](/partner-center/azure-roles-perms-pec).

Wykonując następujące kroki, każda dzierżawa klienta, którą zarządzasz, zostanie skojarzona z IDENTYFIKATORem partnera. Konto automatyzacji dostawcy nie musi uwierzytelniać ani wykonywać żadnych akcji w dzierżawie klienta.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram przedstawiający proces łączenia identyfikatora partnera z usługą Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Dodaj swój identyfikator partnera do wcześniej dodanych klientów

Jeśli klient został już dołączony, możesz nie chcieć wykonać kolejnego wdrożenia w celu dodania nazwy głównej usługi konta automatyzacji dostawcy. Zamiast tego można połączyć **SKOJARZONY identyfikator MPN** z kontem użytkownika, które ma już dostęp do pracy w dzierżawie tego klienta. Upewnij się, że konto ma przydzielone [wbudowaną rolę platformy Azure, która kwalifikuje się do komputera PEC](/partner-center/azure-roles-perms-pec).

Gdy konto zostanie [połączone ze skojarzonym identyfikatorem MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) w dzierżawie zarządzającej, będzie możliwe śledzenie wpływu na tego klienta.

## <a name="confirm-partner-earned-credit"></a>Potwierdź środki uzyskane przez partnera

Możesz [wyświetlić szczegóły dotyczące Pec w Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) i potwierdzić, które koszty otrzymały korzyść dla PEC. Należy pamiętać, że PEC ma zastosowanie tylko do klientów dostawcy usług kryptograficznych, którzy podpisały ten MCA, i podlegają planowi platformy Azure.

Jeśli wykonano powyższe kroki i nie widzisz oczekiwanego skojarzenia, Otwórz żądanie obsługi w Azure Portal.

Możesz również użyć [zestawu SDK Centrum partnerskiego](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) i przefiltrować, `rateOfPartnerEarnedCredit` Aby ZAUTOMATYZOWAĆ weryfikację PEC dla subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Microsoft Partner Network](/partner-center/mpn-overview).
- Dowiedz się [, jak jest obliczany i naliczany czas Pec](/partner-center/partner-earned-credit-explanation).
- Dołączanie [klientów](onboard-customer.md) do usługi Azure Lighthouse.
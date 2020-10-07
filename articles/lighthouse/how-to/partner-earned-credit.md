---
title: Połącz swój identyfikator partnera, aby umożliwić partnerowi uzyskiwanie środków na zasoby delegowane
description: Dowiedz się, jak skojarzyć swój identyfikator partnera, aby otrzymywać środki na korzystanie z partnerów (PEC) w zasobach klientów zarządzanych za pomocą usługi Azure Lighthouse.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 5caa205ce74152c7ec047952f66c1bf9188ddf02
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776174"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Połącz swój identyfikator partnera, aby umożliwić partnerowi uzyskiwanie środków na zasoby delegowane

Jeśli jesteś członkiem [Microsoft Partner Network](https://partner.microsoft.com/), możesz połączyć swój identyfikator partnera z poświadczeniami używanymi do zarządzania delegowanymi zasobami klientów. Dzięki temu można śledzić wpływ na zaangażowanie klientów i otrzymywać [środki na korzystanie z partnerów w przypadku usług zarządzanych (PEC)](/partner-center/partner-earned-credit).

Jeśli dołączysz [klientów z ofertami usług zarządzanych w portalu Azure Marketplace](publish-managed-services-offers.md), ten link odbywa się automatycznie przy użyciu identyfikatora MPN skojarzonego z kontem Centrum partnerskiego używanym do publikowania ofert. W celu uzyskania PEC dla tych klientów nie są konieczne żadne dalsze działania.

W przypadku dołączania [klientów przy użyciu szablonów usługi Azure Resource Management](onboard-customer.md)należy wykonać akcję tworzenia tego linku. W tym celu należy [połączyć swój identyfikator MPN](../../cost-management-billing/manage/link-partner-id.md) z co najmniej jednym kontem użytkownika w dzierżawie zarządzającej, który ma dostęp do każdej z dołączanych subskrypcji.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Skojarz swój identyfikator partnera podczas dołączania nowych klientów

Podczas dołączania klientów za pomocą szablonów Azure Resource Manager (szablony ARM) Użyj poniższego procesu, aby połączyć identyfikator partnera i włączyć środki partnera. Aby wykonać te czynności, musisz znać [Identyfikator partnera MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Upewnij się, że używasz **SKOJARZONEGO identyfikatora MPN** pokazanego w profilu partnera.

Dla uproszczenia zalecamy utworzenie konta nazwy głównej usługi w dzierżawie, połączenie go ze **skojarzonym identyfikatorem MPN**, a następnie udzielenie mu dostępu do każdego klienta, który zostanie dołączony do [roli wbudowanej platformy Azure, która kwalifikuje się do komputera PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Utwórz konto nazwy głównej usługi](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) w dzierżawie zarządzającej. W tym przykładzie zmienimy nazwę tego konta automatyzacji głównej nazwy usługi.
1. Korzystając z tego konta usługi, [Połącz się ze skojarzonym identyfikatorem MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) w dzierżawie zarządzającej. Wystarczy to zrobić tylko raz.
1. Po dołączeniu [klienta przy użyciu szablonów usługi ARM](onboard-customer.md)Pamiętaj o uwzględnieniu autoryzacji obejmującej konto automatyzacji Pec jako użytkownik z [wbudowaną rolą platformy Azure, która kwalifikuje się do komputera PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Wykonując następujące kroki, każda dzierżawa klienta, którą zarządzasz, zostanie skojarzona z IDENTYFIKATORem partnera, co pozwoli na otrzymywanie PEC dla tych klientów. Konto automatyzacji PEC nie musi uwierzytelniać ani wykonywać żadnych akcji w dzierżawie klienta.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Dodaj swój identyfikator partnera do wcześniej dodanych klientów

Jeśli klient został już dołączony, możesz nie chcieć wykonać kolejnego wdrożenia w celu dodania nazwy głównej usługi konta usługi Automation. Zamiast tego można połączyć **SKOJARZONY identyfikator MPN** z kontem użytkownika, które ma już dostęp do pracy w dzierżawie tego klienta. Upewnij się, że konto ma przydzielone [wbudowaną rolę platformy Azure, która kwalifikuje się do komputera PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Gdy konto zostanie [połączone ze skojarzonym identyfikatorem MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) w dzierżawie zarządzającej, będzie można otrzymać wartość PEC dla tego klienta.

## <a name="confirm-partner-earned-credit"></a>Potwierdź środki uzyskane przez partnera

Możesz [wyświetlić szczegóły dotyczące Pec w Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) i potwierdzić, które koszty otrzymały korzyść dla PEC.

Jeśli wykonano powyższe kroki i nie widzisz skojarzenia, Otwórz żądanie obsługi w Azure Portal.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Microsoft Partner Network](/partner-center/mpn-overview).
- Dowiedz się [, jak jest obliczany i naliczany czas Pec](/partner-center/partner-earned-credit-explanation).
- Dołączanie [klientów](onboard-customer.md) do usługi Azure Lighthouse.

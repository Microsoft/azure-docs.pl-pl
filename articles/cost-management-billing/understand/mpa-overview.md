---
title: Wprowadzenie do konta rozliczeniowego w ramach Umowy partnerskiej firmy Microsoft — Azure CSP
description: Omówienie konta rozliczeniowego w ramach Umowy partnerskiej firmy Microsoft (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4dff56db9f1450ff7eb2a2b9ca6f4ca648e2ac38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150092"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Wprowadzenie do konta rozliczeniowego w ramach Umowy partnerskiej firmy Microsoft

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład utworzyć konto platformy Azure w celu pracy z projektami osobistymi. Dostęp do platformy Azure można również uzyskiwać w ramach zawartej przez organizację umowy Enterprise Agreement, Umowy klienta firmy Microsoft lub Umowy partnerskiej firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe.

Ten artykuł dotyczy kont rozliczeniowych dla umów partnerskich firmy Microsoft Te konta są tworzone dla dostawców rozwiązań w chmurze w celu zarządzania rozliczeniami dla klientów w nowym środowisku handlowym. To nowe środowisko jest dostępne tylko dla partnerów mających co najmniej jednego klienta, który zaakceptował umowę klienta firmy Microsoft i ma plan platformy Azure. [Sprawdź, czy masz dostęp do umowy partnerskiej firmy Microsoft](#check-access-to-a-microsoft-partner-agreement). [Plan platformy Azure](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) zapewnia klientom dostęp do usług platformy Azure według stawek płatności zgodnie z rzeczywistym użyciem w ramach umowy klienta firmy Microsoft.

## <a name="your-billing-account"></a>Konto rozliczeniowe

Konto rozliczeniowe dla umowy partnerskiej firmy Microsoft zawiera profile rozliczeniowe dla poszczególnych walut, w których prowadzisz działalność. Profil rozliczeniowy umożliwia zarządzanie w danej walucie. Po ustanowieniu relacji z klientami, w zależności od ich walut, subskrypcje platformy Azure i inne zakupy są rozliczane zgodnie z odpowiednimi profilami rozliczeń.

Na poniższym diagramie przedstawiono relację między kontem rozliczeniowym, profilami rozliczeniowymi, klientami i odsprzedawcami.

![Diagram przedstawiający hierarchię rozliczeń w Umowie partnerskiej firmy Microsoft](./media/mpa-overview/mpa-hierarchy.svg)

Użytkownicy mający rolę **administratora globalnego** i **agentów administratora** w organizacji mogą zarządzać kontami rozliczeniowymi, profilami rozliczeniowymi i klientami. Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Profile rozliczeniowe

Użyj profilu rozliczeniowego, aby zarządzać fakturami w danej walucie. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera opłaty w walucie profilu rozliczeniowego za wszystkie subskrypcje platformy Azure i inne zakupy w poprzednim miesiącu.

W witrynie Azure Portal możesz wyświetlić fakturę i pobrać powiązane dokumenty, takie jak plik użycia i arkusz cen. Aby uzyskać więcej informacji, zobacz [Pobieranie faktur za umowę partnerską firmy Microsoft](download-azure-invoice.md).

> [!IMPORTANT]
>
> Faktury za profile rozliczeniowe zawierają opłaty dla klientów korzystających z planów platformy Azure oraz rozwiązań SaaS, usługi Azure Marketplace i zakupów rezerwowanych dla klientów, którzy nie zaakceptowali umowy klienta firmy Microsoft i nie mają planów platformy Azure.

## <a name="customers"></a>Klienci

W witrynie Azure Portal możesz wyświetlać klientów, którzy zaakceptowali umowę klienta firmy Microsoft i mają plan Azure, oraz zarządzać nimi. Możesz wyświetlać opłaty i transakcje oraz utworzyć subskrypcje platformy Azure dla tych klientów i zarządzać nimi.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Włączanie zasad w celu zapewnienia widoczności kosztów

Zastosuj zasady, aby kontrolować, czy użytkownicy w organizacji klienta mogą wyświetlać i analizować koszt według stawek płatności zgodnie z rzeczywistym użyciem dla ich użycia platformy Azure. Te zasady są domyślnie wyłączone, a użytkownicy nie mogą wyświetlić kosztu. Po włączeniu zasad użytkownicy mający odpowiedni dostęp [dla ról kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md) w subskrypcji mogą wyświetlać i analizować koszt dla subskrypcji.

Aby włączyć zasady:

1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawia wyszukiwanie narzędzia Cost Management + Billing w witrynie Azure Portal.](./media/mpa-overview/search-cmb.png)

1. Wybierz pozycję **Klienci** po lewej stronie, a następnie wybierz klienta z listy.

   ![Zrzut ekranu pokazujący wybieranie klienta](./media/mpa-overview/mpa-customers.png)

1. Wybierz pozycję **Zasady** po lewej stronie.

   ![Zrzut ekranu przedstawiający zasady](./media/mpa-overview/mpa-change-policy.png)

1. Wybierz pozycję **Tak**.

## <a name="resellers"></a>Odsprzedawcy

Dostawcy pośredni w [modelu dwuwarstwowym](/partner-center) dostawców usług w chmurze mogą wybrać odsprzedawcę podczas tworzenia subskrypcji dla klientów w witrynie Azure Portal. Po utworzeniu mogą wyświetlić listę subskrypcji z możliwością filtrowania według odsprzedawców i analizować koszt dla klienta zależnie od odsprzedawcy w ramach analizy kosztów platformy Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Sprawdzanie dostępu do umowy partnerskiej firmy Microsoft
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Tworzenie dodatkowej subskrypcji platformy Azure dla Umowy partnerskiej firmy Microsoft](../manage/create-subscription.md)
- Integrowanie danych dotyczących rozliczeń z własnym systemem raportowania przy użyciu [interfejsów API rozliczeń platformy Azure](/rest/api/billing/)
- [Przewodnik Szybki start dla partnerów dotyczący usługi Azure Cost Management](../costs/get-started-partners.md)
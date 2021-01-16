---
title: Rozwiązywanie problemów Apache Kafka w przypadku rozwiązań partnerskich z chmurą Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów i często zadawanych pytań dotyczących usługi Cloud w chmurze na platformie Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253528"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Apache Kafka rozwiązywania problemów z rozwiązaniami dla rozwiązań w chmurze

Ten dokument zawiera informacje na temat rozwiązywania problemów z rozwiązaniami, które używają Apache Kafka dla chmury.

Jeśli nie znajdziesz odpowiedzi lub nie możesz rozwiązać problemu, [Utwórz żądanie za pomocą Azure Portal](manage.md#get-support) lub skontaktuj się z [pomocą techniczną](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Nie można znaleźć oferty w portalu Marketplace

Aby znaleźć ofertę w portalu Azure Marketplace, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.
1. Wyszukaj _Apache Kafka w chmurze z_ systemem.
1. Wybierz kafelek aplikacji.

Jeśli oferta nie zostanie wyświetlona, skontaktuj się z [pomocą techniczną](https://support.confluent.io). Identyfikator dzierżawy Azure Active Directory musi znajdować się na liście dozwolonych dzierżawców. Aby dowiedzieć się, jak znaleźć swój identyfikator dzierżawy, zobacz [jak znaleźć identyfikator dzierżawy Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="conflict-error"></a>Błąd konfliktu

Jeśli wcześniej zarejestrowano Cię w chmurze z systemem, musisz użyć nowego adresu e-mail, aby utworzyć inny zasób organizacji z chmurą. W przypadku korzystania z wcześniej zarejestrowanego adresu e-mail zostanie wyświetlony komunikat **o błędzie.** Zarejestruj się ponownie, ale tym razem z nowym adresem e-mail.

## <a name="deploymentfailed-error"></a>Błąd DeploymentFailed

Jeśli wystąpi błąd **DeploymentFailed** , sprawdź stan subskrypcji platformy Azure. Upewnij się, że nie ma żadnych problemów z rozliczeniami.

## <a name="resource-isnt-displayed"></a>Zasób nie jest wyświetlany

Jeśli w portalu nie są wyświetlane bloki **przeglądu** lub **usuwania** z chmury, spróbuj odświeżyć stronę. Ten błąd może być sporadycznym problemem z portalem. Jeśli to nie zadziała, skontaktuj się z [pomocą techniczną](https://support.confluent.io).

Jeśli na liście **wszystkie zasoby** platformy Azure nie znaleziono zasobu w chmurze, skontaktuj się z [pomocą techniczną](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>Tworzenie zasobów trwa długo

Jeśli ukończenie procesu wdrażania trwa dłużej niż trzy godziny, skontaktuj się z pomocą techniczną.

Jeśli wdrożenie nie powiedzie się, a zasób w chmurze ma stan `Failed` , Usuń zasób. Po usunięciu spróbuj ponownie utworzyć zasób.

## <a name="offer-plan-doesnt-load"></a>Nie załadowano planu oferty

Ten błąd może być sporadycznym problemem z Azure Portal. Spróbuj ponownie wdrożyć ofertę.

## <a name="unable-to-delete"></a>Nie można usunąć pliku

Jeśli nie możesz usunąć zasobów, upewnij się, że masz uprawnienia do usunięcia zasobu. Musisz mieć możliwość podjęcia działań od firmy Microsoft... Informacje o wyświetlaniu uprawnień znajdują się w temacie Wyświetlanie [przypisań ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-list-portal.md).

Jeśli masz odpowiednie uprawnienia, ale nadal nie można usunąć zasobu, skontaktuj się z [pomocą techniczną](https://support.confluent.io). Ten stan może być związany z zasadami przechowywania zasad. Pomoc techniczna dotycząca poparcia może usunąć organizację i adres e-mail.

## <a name="unable-to-use-single-sign-on"></a>Nie można użyć logowania jednokrotnego

Jeśli logowanie jednokrotne nie działa w przypadku portalu z chmurą SaaS Cloud, upewnij się, że używasz poprawnej Azure Active Directory e-mail. Musisz również mieć zgodę, aby zezwolić na dostęp do portalu usługi SaaS (Cloud Software as a Service). Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące logowania](manage.md#single-sign-on)jednokrotnego.

Jeśli problem będzie się powtarzać, skontaktuj się z [pomocą techniczną](https://support.confluent.io).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzaniu wystąpieniem](manage.md) Apache Kafka w chmurze z usługą.

---
title: Użyj Azure Monitor skoroszytów z Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak za pomocą skoroszytów Azure Monitor przejrzeć inspekcje zabezpieczeń i poznać problemy w Azure Active Directory Domain Servicesej domenie zarządzanej.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 02c4679016ea80a4ac8bb53b502292945cbe9d7b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967073"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Przeglądanie zdarzeń inspekcji zabezpieczeń w Azure Active Directory Domain Services przy użyciu skoroszytów Azure Monitor

Aby ułatwić zrozumienie stanu domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS), możesz włączyć zdarzenia inspekcji zabezpieczeń. Te zdarzenia inspekcji zabezpieczeń można następnie przejrzeć przy użyciu Azure Monitor skoroszytów, które łączą tekst, zapytania analityczne i parametry w rozbudowanych raportach interaktywnych. Usługa Azure AD DS zawiera szablony skoroszytów do przeglądu zabezpieczeń i działania związane z kontem, które pozwalają Dig zdarzenia inspekcji i zarządzać środowiskiem.

W tym artykule pokazano, jak używać skoroszytów Azure Monitor do przeglądania zdarzeń inspekcji zabezpieczeń w usłudze Azure AD DS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Włączono zdarzenia inspekcji zabezpieczeń dla domeny zarządzanej, które przesyła strumieniowo dane do obszaru roboczego Log Analytics.
    * W razie potrzeby [Włącz inspekcje zabezpieczeń dla AD DS platformy Azure][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Przegląd Azure Monitor skoroszytów

Po włączeniu zdarzeń inspekcji zabezpieczeń w usłudze Azure AD DS może być trudno analizować i identyfikować problemy w domenie zarządzanej. Azure Monitor umożliwia agregowanie tych zdarzeń inspekcji zabezpieczeń i wykonywanie zapytań dotyczących danych. Za pomocą Azure Monitor skoroszytów można wizualizować te dane, aby szybciej i łatwiej identyfikować problemy.

Szablony skoroszytów są raportami nadzorowanymi, które są przeznaczone do elastycznego ponownego wykorzystania przez wielu użytkowników i zespoły. Po otwarciu szablonu skoroszytu są ładowane dane ze środowiska Azure Monitor. Można używać szablonów bez wpływu na innych użytkowników w organizacji i mogą zapisywać własne skoroszyty na podstawie szablonu.

Usługa Azure AD DS obejmuje dwa następujące szablony skoroszytu:

* Raport przegląd zabezpieczeń
* Raport aktywności konta

Aby uzyskać więcej informacji na temat edytowania skoroszytów i zarządzania nimi, zobacz [Azure monitor skoroszyty — Omówienie](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Korzystanie z skoroszytu raportu Omówienie zabezpieczeń

W celu lepszego zrozumienia użycia i zidentyfikowania potencjalnych zagrożeń bezpieczeństwa raport Przegląd zabezpieczeń podsumowuje dane logowania i identyfikuje konta, które można chcieć sprawdzić. Można wyświetlać zdarzenia w określonym zakresie dat, a także przechodzić do określonych zdarzeń logowania, takich jak błędne hasła lub w przypadku, gdy konto zostało wyłączone.

Aby uzyskać dostęp do szablonu skoroszytu dla raportu Przegląd zabezpieczeń, wykonaj następujące czynności:

1. Wyszukaj i wybierz **Azure Active Directory Domain Services** w Azure Portal.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*
1. Z menu po lewej stronie wybierz pozycję **monitorowanie > skoroszyty**

    ![Zrzut ekranu, który hightlights, gdzie można wybrać raport Przegląd zabezpieczeń i raport aktywności konta.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Wybierz **raport Przegląd zabezpieczeń**.
1. Z menu rozwijanego w górnej części skoroszytu wybierz subskrypcję platformy Azure, a następnie obszar roboczy Azure Monitor.

    Wybierz **zakres czasu**, taki jak *ostatnie 7 dni*, jak pokazano na poniższym przykładzie zrzutu ekranu:

    ![Wybierz opcję menu skoroszyty w Azure Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Opcje **widoku kafelków** i **widoku wykresu** można także zmienić, aby analizować i wizualizować dane zgodnie z potrzebami.

1. Aby przejść do szczegółów określonego typu zdarzenia, wybierz jedną z kart **wyników logowania** , takich jak *Konto zablokowane*, jak pokazano w następującym przykładzie:

    ![Przykładowe dane raportu Omówienie zabezpieczeń wizualizacja w Azure Monitor skoroszytach](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Dolna część raportu Omówienie zabezpieczeń poniżej wykresu powoduje przerwanie działania wybranego typu. Można filtrować według nazw użytkowników związanych z prawą stroną, jak pokazano w poniższym przykładzie raportu:

    [![Szczegóły blokad kont w skoroszytach Azure Monitor.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Użyj skoroszytu raportu aktywności konta

Aby ułatwić rozwiązywanie problemów dotyczących określonego konta użytkownika, raport aktywności konta powoduje przerwanie szczegółowych informacji dziennika zdarzeń inspekcji. Możesz sprawdzić, czy podczas logowania podano nieprawidłową nazwę użytkownika lub hasło, oraz Źródło próby logowania.

Aby uzyskać dostęp do szablonu skoroszytu dla raportu aktywność konta, wykonaj następujące czynności:

1. Wyszukaj i wybierz **Azure Active Directory Domain Services** w Azure Portal.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*
1. Z menu po lewej stronie wybierz pozycję **monitorowanie > skoroszyty**
1. Wybierz **raport aktywność konta**.
1. Z menu rozwijanego w górnej części skoroszytu wybierz subskrypcję platformy Azure, a następnie obszar roboczy Azure Monitor.

    Wybierz **zakres czasu**, taki jak *ostatnie 30 dni*, a następnie sposób, w jaki **Widok kafelka** ma reprezentować dane.

    Można filtrować według **nazwy użytkownika konta**, takiej jak *Felix*, jak pokazano w poniższym przykładowym raporcie:

    [![Raport aktywności konta w skoroszytach Azure Monitor.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Obszar poniżej wykresu pokazuje poszczególne zdarzenia logowania wraz z informacjami, takimi jak wynik działania i źródłowa stacja robocza. Te informacje mogą pomóc w ustaleniu powtarzających się źródeł zdarzeń logowania, które mogą powodować blokowanie kont lub wskazywać potencjalny atak.

Podobnie jak w przypadku raportu Omówienie zabezpieczeń, można przejść do szczegółów różnych kafelków w górnej części raportu, aby wizualizować i analizować dane zgodnie z wymaganiami.

## <a name="save-and-edit-workbooks"></a>Zapisz i edytuj skoroszyty

Dwa skoroszyty szablonów udostępniane przez usługę Azure AD DS są dobrym miejscem do rozpoczęcia pracy z własną analizą danych. Jeśli potrzebujesz bardziej szczegółowych informacji na temat zapytań i badań danych, możesz zapisać własne skoroszyty i edytować zapytania.

1. Aby zapisać kopię jednego z szablonów skoroszytów, wybierz opcję **edytuj > Zapisz jako > raporty udostępnione**, a następnie podaj nazwę i Zapisz ją.
1. Z własnej kopii szablonu wybierz pozycję **Edytuj** , aby przejść do trybu edycji. Możesz wybrać niebieski przycisk **Edytuj** obok dowolnej części raportu i zmienić go.

Wszystkie wykresy i tabele w Azure Monitor skoroszytach są generowane przy użyciu zapytań Kusto. Aby uzyskać więcej informacji na temat tworzenia własnych zapytań, zobacz [Azure monitor zapytań dzienników][azure-monitor-queries] i [zapytań Kusto][kusto-queries].

## <a name="next-steps"></a>Następne kroki

Jeśli musisz dostosować zasady dotyczące haseł i blokad, zobacz [zasady blokowania haseł i kont w domenach zarządzanych][password-policy].

Aby uzyskać informacje o problemach z użytkownikami, Dowiedz się, jak rozwiązywać problemy z [logowaniem do konta][troubleshoot-sign-in] lub [problemy z blokadą konta][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer
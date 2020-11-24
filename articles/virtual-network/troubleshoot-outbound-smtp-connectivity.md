---
title: Rozwiązywanie problemów z łącznością wychodzącą SMTP na platformie Azure | Microsoft Docs
description: Poznaj zalecaną metodę wysyłania wiadomości e-mail oraz rozwiązywania problemów z łącznością wychodzącą SMTP na platformie Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 063ebc40fd845fe6300b008e7ca048357a2fce49
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95806663"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Rozwiązywanie problemów z łącznością wychodzącą SMTP na platformie Azure

Od 15 listopada 2017 wychodzących wiadomości e-mail, które są wysyłane bezpośrednio do domen zewnętrznych (takich jak outlook.com i gmail.com) z maszyny wirtualnej, są dostępne tylko dla niektórych typów subskrypcji w Microsoft Azure. Wychodzące połączenia SMTP korzystające z portu 25 TCP zostały zablokowane. (Port 25 jest używany głównie do dostarczania nieuwierzytelnionych wiadomości e-mail).

Ta zmiana w zachowaniu dotyczy tylko nowych subskrypcji i nowych wdrożeń od 15 listopada 2017.

## <a name="recommended-method-of-sending-email"></a>Zalecana metoda wysyłania wiadomości e-mail

Zalecamy używanie uwierzytelnionych usług przekazywania SMTP (zwykle łączących się za pośrednictwem portu TCP 587 lub 443, ale również innych portów) do wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure lub z usługi Azure App Services. Te usługi służą do utrzymania reputacji adresów IP lub domen, aby zminimalizować prawdopodobieństwo odrzucenia komunikatu przez dostawców poczty e-mail innych firm. Takie usługi przekazywania SMTP obejmują, ale nie są ograniczone do [SendGrid](https://sendgrid.com/partners/azure/). Istnieje również możliwość, że masz bezpieczną usługę przekazywania SMTP działającą lokalnie, której można użyć.

Korzystanie z tych usług dostarczania poczty e-mail nie jest ograniczone na platformie Azure, niezależnie od typu subskrypcji.

## <a name="enterprise-agreement"></a>Enterprise Agreement

W przypadku Umowa Enterprise użytkowników platformy Azure nie ma zmian w zakresie możliwości wysyłania wiadomości e-mail bez użycia uwierzytelnionego przekaźnika. Nowi i istniejący Umowa Enterprise użytkownicy mogą wypróbować dostarczanie wychodzącej poczty e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń platformy Azure. Chociaż nie gwarantujemy, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od dowolnego użytkownika, próby dostarczenia nie będą blokowane przez platformę Azure dla maszyn wirtualnych w ramach subskrypcji Umowa Enterprise. Musisz współpracować bezpośrednio z dostawcami poczty e-mail, aby rozwiązać wszelkie problemy z dostarczaniem komunikatów lub filtrowaniem SPAMu, które obejmują określonych dostawców.

## <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem

Jeśli zarejestrowano się przed 15 listopada 2017 dla subskrypcji z płatność zgodnie z rzeczywistym użyciem, nastąpi zmiana zdolności technicznej do wypróbowania wychodzącej poczty e-mail. Będziesz nadal mieć możliwość wypróbowania wychodzącej poczty e-mail z maszyn wirtualnych platformy Azure w ramach tych subskrypcji bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń platformy Azure. Nie gwarantujemy, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od dowolnego użytkownika, a użytkownicy będą musieli bezpośrednio współpracować z dostawcami poczty e-mail w celu rozwiązania wszelkich problemów z dostarczaniem komunikatów lub filtrowaniem SPAMu, które obejmują określonych dostawców.

W przypadku subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, które zostały utworzone po 15 listopada 2017, będą obowiązywać ograniczenia techniczne, które blokują wiadomości e-mail wysyłane bezpośrednio z maszyn wirtualnych w ramach tych subskrypcji. Jeśli chcesz wysyłać wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz wykonać żądanie usunięcia ograniczenia w sekcji **połączenia** w bloku **diagnozowanie i rozwiązywanie** dla zasobu usługi Azure Virtual Network w Azure Portal. Jeśli jest kwalifikowana, Twoja subskrypcja zostanie włączona lub otrzymasz instrukcje dotyczące następnych kroków.

Po wyłączeniu subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, gdy maszyny wirtualne zostały zatrzymane i uruchomione w Azure Portal, wszystkie maszyny wirtualne w tej subskrypcji zostaną wykluczone w przód. Wyłączenie dotyczy tylko ruchu maszyn wirtualnych, który jest kierowany bezpośrednio do Internetu.

> [!NOTE]
> Firma Microsoft zastrzega sobie prawo do odwołania tego wykluczenia w przypadku określenia, że wystąpiło naruszenie warunków użytkowania usługi.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure — dostęp próbny, Azure w ramach programu licencjonowania Open, Education, Azure for Students, usługa Visual Studio i bezpłatna wersja próbna

Jeśli utworzono subskrypcję MSDN, Azure — dostęp próbny platformę Azure w ramach programu licencjonowania Open, Education, Azure student, bezpłatna wersja próbna lub dowolna subskrypcja programu Visual Studio po 15 listopada 2017, będziesz mieć ograniczenia techniczne, które blokują wiadomości e-mail wysyłane z maszyn wirtualnych w ramach tych subskrypcji bezpośrednio do dostawców poczty e-mail. Ograniczenia są wykonywane w celu zapobiegania nadużyciom. Nie zostaną udzielone żadne żądania usunięcia tego ograniczenia.

Jeśli używasz tych typów subskrypcji, zachęcasz do korzystania z usług przekazywania SMTP, jak opisano wcześniej w tym artykule, lub zmienić typ subskrypcji.

## <a name="cloud-service-provider-csp"></a>Dostawca usług w chmurze (CSP)

Jeśli używasz zasobów platformy Azure za pośrednictwem dostawcy usług kryptograficznych, możesz utworzyć żądanie usunięcia ograniczenia w sekcji "łączność" w bloku "diagnozowanie i rozwiązywanie" dla zasobu Virtual Network w witrynie Azure Portal. Jeśli jest kwalifikowana, Twoja subskrypcja zostanie włączona lub otrzymasz instrukcje dotyczące następnych kroków.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus lub Dostęp sponsorowany Azure

W przypadku Microsoft Partner Network (MPN), BizSpark Plus lub Dostęp sponsorowany Azure subskrypcje, które zostały utworzone po 15 listopada 2017, zostaną nałożone ograniczenia techniczne, które blokują wiadomości e-mail wysyłane bezpośrednio z maszyn wirtualnych w ramach tych subskrypcji. Aby móc wysyłać wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz wykonać żądanie, otwierając przypadek pomocy technicznej, używając następującego typu **problemu:**  >  **Virtual Network**  >  **łączności**  >  **nie można wysłać wiadomości e-mail (SMTP/port 25)**. Upewnij się, że dodano szczegółowe informacje o tym, dlaczego wdrożenie musi wysyłać pocztą bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekaźnika. Żądania będą analizowane i zatwierdzane według uznania firmy Microsoft. Żądania mogą być udzielane tylko po zakończeniu dodatkowych kontroli zabezpieczenia przed oszustwem. 

Po wyłączeniu subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, gdy maszyny wirtualne zostały zatrzymane i uruchomione w Azure Portal, wszystkie maszyny wirtualne w tej subskrypcji zostaną wykluczone w przód. Wyłączenie dotyczy tylko ruchu maszyn wirtualnych, który jest kierowany bezpośrednio do Internetu.

## <a name="restrictions-and-limitations"></a>Ograniczenia i ograniczenia

- Routing ruchu 25 portów za pośrednictwem usług Azure PaaS Services, takich jak [Zapora platformy Azure](https://azure.microsoft.com/services/azure-firewall/) , nie jest obsługiwany.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem, używając następującego typu problemu: typ problemu **zarządzania subskrypcją** : **żądanie włączenia przepływu poczty e-mail portu 25**.

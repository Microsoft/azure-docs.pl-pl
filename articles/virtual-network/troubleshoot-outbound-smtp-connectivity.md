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
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: c28790b2ef423a3d0f996d7c6030b04198756eb1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607615"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Rozwiązywanie problemów z łącznością wychodzącą SMTP na platformie Azure

Od 15 listopada 2017, wychodzące wiadomości e-mail wysyłane bezpośrednio do domen zewnętrznych (takich jak outlook.com i gmail.com) z maszyny wirtualnej są udostępniane tylko określonym typom subskrypcji na platformie Azure. Wychodzące połączenia SMTP korzystające z portu 25 TCP zostały zablokowane. (Port 25 jest używany głównie do dostarczania nieuwierzytelnionych wiadomości e-mail).

Ta zmiana w zachowaniu dotyczy tylko subskrypcji i wdrożeń, które zostały utworzone po 15 listopada 2017.

## <a name="recommended-method-of-sending-email"></a>Zalecana metoda wysyłania wiadomości e-mail

Zalecamy używanie uwierzytelnionych usług przekazywania SMTP do wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure lub z Azure App Service. (Te usługi przekaźnikowe zwykle łączą się za pośrednictwem portu TCP 587, ale obsługują inne porty). Te usługi są używane do obsługi reputacji adresów IP lub domen, co pozwala zminimalizować prawdopodobieństwo odrzucenia komunikatów przez dostawców poczty e-mail innych firm. [SendGrid](https://sendgrid.com/partners/azure/) to jedna taka usługa przekazywania SMTP, ale istnieją inne. Może być również używana bezpieczna Usługa przekazywania SMTP działająca lokalnie.

Korzystanie z tych usług dostarczania poczty e-mail nie jest ograniczone na platformie Azure, niezależnie od typu subskrypcji.

## <a name="enterprise-agreement"></a>Enterprise Agreement

W przypadku Enterprise Agreement użytkowników platformy Azure nie ma zmian w zakresie możliwości wysyłania wiadomości e-mail bez użycia uwierzytelnionego przekaźnika. Nowi i istniejący Enterprise Agreement użytkownicy mogą wypróbować dostarczanie wychodzącej poczty e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń platformy Azure. Nie ma gwarancji, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od danego użytkownika. Jednak platforma Azure nie będzie blokować prób dostarczenia dla maszyn wirtualnych w ramach subskrypcji Enterprise Agreement. Musisz współpracować bezpośrednio z dostawcami poczty e-mail, aby rozwiązać wszelkie problemy z dostarczaniem komunikatów lub filtrowaniem SPAMu, które obejmują określonych dostawców.

## <a name="pay-as-you-go"></a>Płatność zgodnie z rzeczywistym użyciem

Jeśli zarejestrowano się przed 15 listopada 2017 dla subskrypcji z płatność zgodnie z rzeczywistym użyciem, nie zmienisz możliwości technicznych w celu wypróbowania dostarczania wychodzącej poczty e-mail. Nadal będziesz mieć możliwość wypróbowania wychodzącego dostarczania poczty e-mail z maszyn wirtualnych platformy Azure w ramach tych subskrypcji bezpośrednio do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń platformy Azure. Nie ma żadnej gwarancji, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail od danego użytkownika. Użytkownicy będą musieli bezpośrednio współpracować z dostawcami poczty e-mail, aby rozwiązać wszelkie problemy z dostarczaniem komunikatów lub filtrowaniem SPAMu, które obejmują określonych dostawców.

W przypadku subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, które zostały utworzone po 15 listopada 2017, będą obowiązywać ograniczenia techniczne, które blokują wiadomości e-mail wysyłane bezpośrednio z maszyn wirtualnych w ramach subskrypcji. Jeśli chcesz mieć możliwość wysyłania wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP) i masz konto w dobrym stanie z historią płatniczą, możesz poprosić o usunięcie ograniczenia. Można to zrobić w sekcji **połączenia** w bloku **diagnozowanie i rozwiązywanie** dla zasobu usługi Azure Virtual Network w Azure Portal. Jeśli Twoje żądanie zostanie zaakceptowane, subskrypcja zostanie włączona lub otrzymasz instrukcje dotyczące następnych kroków. 

Po wyłączeniu subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, gdy maszyny wirtualne zostaną zatrzymane i uruchomione ponownie w Azure Portal, wszystkie maszyny wirtualne w tej subskrypcji zostaną wykluczone do przodu. Wyłączenie dotyczy tylko ruchu maszyn wirtualnych, który jest kierowany bezpośrednio do Internetu.

> [!NOTE]
> Firma Microsoft zastrzega sobie prawo do odwołania tych zwolnień w przypadku określenia, że wystąpiło naruszenie warunków użytkowania.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure — dostęp próbny, Azure w ramach programu licencjonowania Open, Education, Azure for Students, Visual Studio i bezpłatna wersja próbna

Jeśli utworzono jeden z następujących typów subskrypcji po 15 listopada 2017, będziesz mieć ograniczenia techniczne, które blokują wiadomości e-mail wysyłane z maszyn wirtualnych w ramach subskrypcji bezpośrednio do dostawców poczty e-mail:
- MSDN
- Azure — dostęp próbny
- Licencja Azure w ramach programu licencjonowania Open
- Education
- Azure for Students
- Bezpłatna wersja próbna
- Dowolna subskrypcja programu Visual Studio  

Ograniczenia są stosowane w celu zapobiegania nadużyciom. Nie zostanie przyznane żądanie usunięcia tych ograniczeń.

W przypadku korzystania z tych typów subskrypcji zachęcamy do korzystania z usług przekazywania SMTP, jak opisano wcześniej w tym artykule, lub zmienić typ subskrypcji.

## <a name="cloud-solution-provider"></a>Dostawca rozwiązań w chmurze

Jeśli korzystasz z zasobów platformy Azure za pośrednictwem dostawcy rozwiązań w chmurze, możesz utworzyć żądanie usunięcia ograniczenia w sekcji **połączenia** w okienku **diagnozowanie i rozwiązywanie** dla zasobu sieci wirtualnej w Azure Portal. Jeśli Twoje żądanie zostanie zaakceptowane, subskrypcja zostanie włączona lub otrzymasz instrukcje dotyczące następnych kroków.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus lub Dostęp sponsorowany Azure

W przypadku subskrypcji następujących typów, które zostały utworzone po 15 listopada 2017, będą dostępne ograniczenia techniczne, które blokują wiadomości e-mail wysyłane bezpośrednio z maszyn wirtualnych w ramach subskrypcji:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Dostęp sponsorowany Azure

Aby móc wysyłać wiadomości e-mail z maszyn wirtualnych platformy Azure bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz wykonać żądanie, otwierając zgłoszenie do pomocy technicznej, korzystając z następującego typu **problemu:**  >  **Virtual Network**  >  **łączności**  >  **nie można wysłać wiadomości e-mail (SMTP/port 25)**. Pamiętaj, aby dodać szczegółowe informacje o tym, dlaczego wdrożenie musi wysyłać pocztą bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekaźnika. Żądania będą analizowane i zatwierdzane według uznania firmy Microsoft. Żądania będą udzielane dopiero po zakończeniu dodatkowych sprawdzeń związanych z oszustwem. 

Gdy subskrypcja zostanie zwolniona, a maszyny wirtualne zostały zatrzymane i uruchomione ponownie w Azure Portal, wszystkie maszyny wirtualne w tej subskrypcji zostaną wykluczone do przodu. Wyłączenie dotyczy tylko ruchu maszyn wirtualnych, który jest kierowany bezpośrednio do Internetu.

## <a name="changing-subscription-type"></a>Zmienianie typu subskrypcji

Jeśli zmienisz typ subskrypcji lub dostawcę rozwiązań w chmurze lub subskrypcję z płatność zgodnie z rzeczywistym użyciem, musisz zatrzymać, cofnąć przydział, a następnie ponownie uruchomić maszynę wirtualną, aby nowe zasady zaczęły obowiązywać. Podobnie, jeśli masz typ subskrypcji, który jest dozwolony domyślnie, a następnie zmienisz na typ subskrypcji niedozwolony, port 25 może zostać zablokowany ze względu na zmiany wdrożenia.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem. Użyj tego typu problemu:   >    >  **łączność** Virtual Network techniczna  >  **nie może wysyłać wiadomości e-mail (SMTP/port 25)**.

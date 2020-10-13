---
title: Limity pojemności w Azure Lab Services
description: Więcej informacji na temat limitów pojemności (limitów maszyn wirtualnych) znajduje się w Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 92bdc714d70b3d73ca2cbc76b1f5dc5366582cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444101"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Limity pojemności w Azure Lab Services
Azure Lab Services ma domyślne limity pojemności dla subskrypcji platformy Azure, które są zgodne z ograniczeniami przydziału obliczeń platformy Azure i ograniczają oszustwo. Wszystkie subskrypcje platformy Azure będą miały początkowy limit pojemności, który może się różnić w zależności od typu subskrypcji, liczby standardowych rdzeni obliczeniowych i rdzeni procesora GPU dostępnych wewnątrz Azure Lab Services. Ogranicza liczbę maszyn wirtualnych, które można utworzyć w laboratoriach, zanim będzie konieczne zażądanie wzrostu limitu.  

Jeśli zbliżasz się do lub osiągniesz limit rdzeni maszyn wirtualnych subskrypcji, zobaczysz komunikaty z Azure Lab Services podczas próby wykonania akcji tworzących dodatkowe maszyny wirtualne. Na przykład: 

- Tworzenie laboratorium
- Publikowanie laboratorium
- Dostosuj pojemność laboratorium, aby dodać więcej maszyn wirtualnych do istniejącego laboratorium

Te akcje można także wyłączyć, jeśli osiągnięto już limit rdzeni. 

![Limity rdzeni — komunikat ostrzegawczy](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Subskrypcje z domyślnym limitem równym zero rdzeni
W przypadku niektórych rzadkich typów subskrypcji, które są bardziej często używane do celów oszustwa, może istnieć domyślny limit równy 0 rdzeni standardowych i 0 rdzeni procesora GPU. Jeśli używasz jednego z tych typów subskrypcji, administrator, który tworzy konto laboratorium, będzie musiał zażądać zwiększenia limitu przed użyciem Azure Lab Services. 

Administrator może wykonać następujące kroki, aby zażądać wzrostu limitu:  

1.  [Utwórz konto laboratorium](tutorial-setup-lab-account.md)w Twojej subskrypcji.
2.  Na stronie **Przegląd** konta laboratorium kliknij przycisk **Ogranicz limit żądań** w górnej części ekranu. 
3.  Postępuj zgodnie z instrukcjami w formularzu, aby przesłać żądanie pomocy technicznej w celu zwiększenia limitu.

## <a name="request-a-limit-increase"></a>Poproś o zwiększenie limitu
Jeśli osiągnięto limit rdzeni, możesz poprosić o zwiększenie limitu, aby nadal używać Azure Lab Services. Proces żądania jest punktem kontrolnym, aby zapewnić, że subskrypcja nie jest uwzględniana w żadnym przypadku oszustwa lub niezamierzonych wdrożeń na dużą skalę.

Komunikaty o limicie rdzeni maszyn wirtualnych w portalu Azure Lab Services zawierają link umożliwiający zwiększenie limitu. Link zostanie otwarta nowa karta przeglądarki, na której można utworzyć nowe żądanie obsługi. Informacje o typie problemu, subskrypcji i typie przydziału zostaną automatycznie wypełnione dla Ciebie, jak pokazano na poniższej ilustracji: 

![Nowy wniosek o pomoc techniczną](./media/capacity-limits/new-support-request.png)


Następnie zostanie wyświetlony monit o podanie dodatkowych informacji o zwiększeniu limitu. W polu **Opis** podaj następujące informacje:

- Co próbujesz zrobić (na przykład tworząc laboratorium do uczenia klasy nauki komputerowej, uruchom hackathon itd.)
- Rozmiar maszyny wirtualnej używany dla tego laboratorium
- Liczba potrzebnych maszyn wirtualnych

Po przesłaniu żądania pomocy technicznej sprawdzimy żądanie. W razie potrzeby skontaktujemy się z Tobą, aby uzyskać dodatkowe informacje. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem:
- [Często zadawane pytania](classroom-labs-faq.md).
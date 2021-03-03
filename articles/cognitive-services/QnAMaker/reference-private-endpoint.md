---
title: Jak skonfigurować prywatny punkt końcowy — QnA Maker
description: Informacje o tworzeniu prywatnych punktów końcowych dostępnych w QnA Maker zarządzane.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710553"
---
# <a name="private-endpoints"></a>Prywatne punkty końcowe

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Teraz QnA Maker zapewnia obsługę tworzenia prywatnych punktów końcowych usługi Azure Search. Ta funkcja jest dostępna w QnA Maker zarządzana. 

Prywatne punkty końcowe są udostępniane przez [łącze prywatne platformy Azure](../../private-link/private-link-overview.md)jako oddzielna usługa. Aby uzyskać więcej informacji o kosztach, zobacz [stronę z cennikiem.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).
> * [Zasób zarządzany](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QNA Maker utworzony w Azure Portal. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranym podczas tworzenia zasobu.

## <a name="steps-to-enable-private-endpoint"></a>Procedura włączania prywatnego punktu końcowego
1. Przypisz *rolę* współautora do QNA Maker usługi zarządzanej w wystąpieniu usługi Azure Search. Ta operacja wymaga dostępu *właściciela* do subskrypcji. Przejdź do karty tożsamość w zasobie usługi, aby uzyskać tożsamość.
![Tożsamość usługi zarządzanej](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Dodaj powyższą *tożsamość jako współautora,* przechodząc do karty Azure Search Service IAM. ![ Usługa Managed Service IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Kliknij pozycję *Dodaj przypisania ról*, Dodaj tożsamość i kliknij przycisk *Zapisz*.
![Przypisanie roli zarządzanej](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Teraz przejdź do karty *Sieć* w wystąpieniu usługi Azure Search i Przełącz dane łączności punktu końcowego z *publicznego* do *prywatnego*. Ta operacja jest długotrwałym procesem i może potrwać do 30 minut. 
![Zarządzana sieć usługi Azure Search](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Przejdź do karty *Sieć* w obszarze QNA Maker usługa zarządzana i w obszarze *Zezwalaj na dostęp z*, wybierz opcję *wybrane sieci i prywatne punkty końcowe* , a następnie kliknij przycisk *Zapisz*. 
![Zarządzana QnA Maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Spowoduje to nawiązanie połączenia z prywatnym punktem końcowym między usługą QnA Maker a wystąpieniem usługi wyszukiwania poznawczego platformy Azure. Połączenie z prywatnym punktem końcowym można sprawdzić na karcie *sieć* Azure Search wystąpienia usługi. Po zakończeniu całej operacji warto użyć usługi QnA Maker. 
![Zarządzana usługa sieciowa](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Szczegóły dotyczące obsługi
 * Nie obsługujemy usługi Change Azure Search po włączeniu prywatnego dostępu do usługi QnAMaker. Jeśli zmienisz usługę Azure Search za pomocą karty "Konfiguracja" po włączeniu dostępu prywatnego, usługa QnAMaker stanie się bezużyteczny.
 * Po ustanowieniu połączenia z prywatnym punktem końcowym w przypadku przełączenia usługi Azure Search sieci na "publiczne" nie będzie można używać usługi QnAMaker. Aby połączenie z prywatnym punktem końcowym działało, Sieć usługi Azure Search Service musi mieć wartość "Private"
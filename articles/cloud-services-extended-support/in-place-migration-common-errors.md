---
title: Typowe błędy i znane problemy podczas migracji do usługi Azure Cloud Services (obsługa rozszerzona)
description: Przegląd typowych błędów podczas migrowania z Cloud Services (klasycznego) do usługi w chmurze (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287041"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Typowe błędy i znane problemy podczas migracji do usługi Azure Cloud Services (obsługa rozszerzona)

W tym artykule opisano znane problemy i typowe błędy, które mogą wystąpić podczas migracji z Cloud Services (klasyczny) do Cloud Services (obsługa rozszerzona). 

## <a name="known-issues"></a>Znane problemy
Są znane i rozwiązywane następujące problemy.

| Znane problemy | Ograniczanie ryzyka | 
|---|---|
| Wystąpienia roli ponownie uruchamiają UD przez UD po pomyślnym zatwierdzeniu. | Operacja ponownego uruchomienia jest zgodna z tą samą metodą co comiesięczne wdrożenia systemu operacyjnego gościa. Nie zatwierdzaj migracji usług w chmurze z pojedynczym wystąpieniem roli lub na które miało wpływ ponowne uruchomienie.| 
| Azure Portal nie może odczytać stanu migracji po odświeżeniu przeglądarki. | Ponownie uruchom weryfikację i przygotowanie operacji, aby wrócić do oryginalnego stanu migracji. | 
| Certyfikat wyświetlany jako zasób tajny w magazynie kluczy. | Po migracji Przekaż certyfikat jako zasób certyfikatu, aby uprościć operację aktualizacji na Cloud Services (obsługa rozszerzona). | 
| Etykiety wdrożenia nie są zapisywane jako Tagi w ramach migracji. | Ręcznie Utwórz Tagi po migracji, aby zachować te informacje.
| Nazwa grupy zasobów jest wyłączana. | Brak wpływu. Rozwiązanie nie jest jeszcze dostępne. |
| Nazwa blokady na Cloud Services (obsługa rozszerzona) jest niepoprawna. | Brak wpływu. Rozwiązanie nie jest jeszcze dostępne. | 
| Nazwa adresu IP jest niepoprawna w bloku portalu Cloud Services (obsługa rozszerzona). | Brak wpływu. Rozwiązanie nie jest jeszcze dostępne. | 
| Nieprawidłowa nazwa DNS pokazana dla wirtualnego adresu IP po operacji Update dla zmigrowanej usługi w chmurze. | Brak wpływu. Rozwiązanie nie jest jeszcze dostępne. | 
| Po pomyślnym przygotowaniu nie jest dozwolone łączenie nowego wdrożenia Cloud Services (obsługa rozszerzona). | Nie łącz nowej usługi w chmurze jako wymienialnej do przygotowanej usługi w chmurze. | 
| Komunikaty o błędach muszą zostać zaktualizowane. | Brak wpływu. | 

## <a name="common-migration-errors"></a>Typowe błędy migracji
Typowe błędy migracji i kroki zaradcze. 

| Komunikat o błędzie | Szczegóły | 
|---|---|
| Nie można odnaleźć typu zasobu w przestrzeni nazw `Microsoft.Compute` dla interfejsu API w wersji "2020-10-01-Preview". | [Zarejestruj](in-place-migration-overview.md#setup-access-for-migration) flagę funkcji CloudServices w celu uzyskania dostępu do publicznej wersji zapoznawczej. | 
| Serwer napotkał błąd wewnętrzny. Ponów żądanie. | Spróbuj ponownie wykonać operację, użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną. | 
| Serwer napotkał nieoczekiwany błąd podczas próby przydzielenia zasobów sieciowych dla usługi w chmurze. Ponów żądanie. | Spróbuj ponownie wykonać operację, użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną. | 
| Wdrożenie wdrożenia — nazwa w chmurze usługi w chmurze — nazwa usługi musi znajdować się w sieci wirtualnej, która ma zostać zmigrowana. | Wdrożenie nie znajduje się w sieci wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z [tym](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentem. | 
| Migracja wdrożenia wdrożenia-nazwa w usłudze w chmurze Cloud-Service-Name nie jest obsługiwana, ponieważ znajduje się w regionie regionu-Name. Dozwolone regiony: [Lista dostępnych regionów]. | Region nie jest jeszcze obsługiwany w przypadku migracji. | 
| Nie można przeprowadzić migracji nazwy wdrożenia wdrożenia w usłudze w chmurze Cloud-Service-Name, ponieważ nie ma żadnych podsieci skojarzonych z rolą role-Name. Skojarz wszystkie role z podsiecią, a następnie ponów próbę migracji usługi w chmurze. | Zaktualizuj wdrożenie usługi w chmurze (klasycznej), umieszczając je w podsieci przed migracją. |  
| Nie można migrować nazwy wdrożenia wdrożenia w usłudze w chmurze Cloud-Service-Name, ponieważ wdrożenie wymaga co najmniej jednej funkcji, która nie jest zarejestrowana w ramach subskrypcji w Azure Resource Manager. Zarejestruj wszystkie wymagane funkcje, aby przeprowadzić migrację tego wdrożenia. Brakujące funkcje: [Lista brakujących funkcji]. | Skontaktuj się z pomocą techniczną, aby uzyskać zarejestrowane flagi funkcji. | 
| Nie można migrować wdrożenia, ponieważ usługa w chmurze wdrożenia ma dwa zajęte gniazda. Migracja usług Cloud Services jest obsługiwana tylko w przypadku wdrożeń, które są jedynym wdrożeniem w usłudze w chmurze. Usuń inne wdrożenie w usłudze w chmurze, aby kontynuować migrację tego wdrożenia. | Aby uzyskać więcej informacji, zapoznaj się z listą [nieobsługiwanych scenariuszy](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) . | 
| Wdrożenie wdrożenia — nazwa w chmurze usłudze hostowanej-Service-Name jest w stanie pośrednim: State. Migracja jest niedozwolona. | Trwa tworzenie, usuwanie lub aktualizowanie wdrożenia. Poczekaj na zakończenie operacji i spróbuj ponownie. | 
| Wdrożenie wdrożenia — nazwa w chmurze usługi hostowanej — nazwa usługi zawiera zastrzeżone adresy IP, ale nie zarezerwowaną nazwę IP. Aby rozwiązać ten problem, zaktualizuj zastrzeżoną nazwę IP lub skontaktuj się z pomocą techniczną Microsoft Azure. | Zaktualizuj wdrożenie usługi w chmurze. | 
| Wdrożenie wdrożenia — nazwa w chmurze usługi hostowanej nazwa usługi zawiera zastrzeżone adresy IP (s) zastrzeżone-IP-Name, ale nie ma punktu końcowego w zastrzeżonym adresie IP. Aby rozwiązać ten problem, Dodaj co najmniej jeden punkt końcowy do zastrzeżonego adresu IP. | Dodaj punkt końcowy do zastrzeżonego adresu IP. | 
| Migracja wdrożenia {0} w usłudze hostowanej {1} jest w trakcie zatwierdzania i nie można jej zmienić, dopóki ta operacja nie zostanie pomyślnie ukończona.  | Poczekaj lub ponów operację. | 
| Migracja wdrożenia {0} w usłudze hostowanej {1} jest w trakcie przerywania i nie można jej zmienić, dopóki nie zostanie ukończona pomyślnie. | Poczekaj lub ponów operację. |
| Co najmniej jedna maszyna wirtualna we wdrożeniu {0} w usłudze hostowanej {1} jest w trakcie operacji aktualizacji. Nie można przeprowadzić migracji do momentu pomyślnego zakończenia poprzedniej operacji. Spróbuj ponownie za jakiś czas. | Poczekaj na zakończenie operacji. | 
| Migracja nie jest obsługiwana w przypadku wdrażania {0} w programie usłudze hostowanej {1} , ponieważ używa on następujących funkcji, które nie są jeszcze obsługiwane w przypadku migracji: wdrożenie inne niż sieć wirtualna.| Wdrożenie nie znajduje się w sieci wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z [tym](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentem. | 
| Nazwa sieci wirtualnej nie może mieć wartości null ani być pusta. | Podaj nazwę sieci wirtualnej w treści żądania REST | 
| Nazwa podsieci nie może mieć wartości null ani być pusta. | Podaj nazwę podsieci w treści żądania REST. | 
| DestinationVirtualNetwork musi być ustawiona na jedną z następujących wartości: domyślne, nowe lub istniejące. | Podaj właściwość DestinationVirtualNetwork w treści żądania REST. | 
| Opcja domyślnej lokalizacji docelowej sieci wirtualnej nie została zaimplementowana. | Wartość "default" nie jest obsługiwana dla właściwości DestinationVirtualNetwork w treści żądania REST. | 
| {0}Nie można migrować wdrożenia, ponieważ CSPKG nie jest dostępny. | Uaktualnij wdrożenie i spróbuj ponownie. | 
| Podsieć o IDENTYFIKATORze " {0} " znajduje się w innej lokalizacji niż wdrożenie " {1} " w usłudze hostowanej " {2} ". Lokalizacja podsieci to " {3} ", a lokalizacja usługi hostowanej to " {4} ".  Określ podsieć w tej samej lokalizacji, w której znajduje się wdrożenie. | Zaktualizuj usługę w chmurze, tak aby była dostępna w tej samej lokalizacji przed migracją. | 
| Migracja wdrożenia {0} w usłudze hostowanej {1} jest w trakcie przerywania i nie można jej zmienić, dopóki nie zostanie ukończona pomyślnie. | Poczekaj na zakończenie przerwania lub Ponów próbę przerwania. Użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną w inny sposób. | 
| Wdrożenie {0} w usłudze hostowanej {1} nie zostało przygotowane do migracji. | Uruchom przygotowanie do usługi w chmurze przed uruchomieniem operacji zatwierdzania. | 
| UnknownExceptionInEndExecute: kontrakt. Assert nie powiódł się: rgName ma wartość null lub jest pusty: wyjątek otrzymany w EndExecute, który nie jest RdfeException. |   Użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną. | 
| UnknownExceptionInEndExecute: zadanie zostało anulowane: Wystąpił wyjątek w EndExecute, który nie jest RdfeException. | Użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną. | 
| XrpVirtualNetworkMigrationError: błąd migracji sieci wirtualnej. | Użyj funkcji [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) lub skontaktuj się z pomocą techniczną. | 
| Wdrożenie {0} w usłudze hostowanej {1}  należy do Virtual Network {2} . Przeprowadź migrację Virtual Network {2} , aby zmigrować ten usłudze hostowanej {1} . | Zapoznaj się z [Virtual Network migracji](in-place-migration-technical-details.md#virtual-network-migration). | 
| Bieżący limit przydziału dla nazwy zasobu w Azure Resource Manager jest za mały, aby ukończyć migrację. Bieżący limit przydziału jest {0} wymagany {1} . Zgłoś żądanie pomocy technicznej w celu podniesienia przydziału i ponów próbę migracji po podniesieniu limitu przydziału.    | Postępuj zgodnie z odpowiednimi kanałami, aby zażądać zwiększenia limitu przydziału: <br>[Zwiększenie limitu przydziału zasobów sieciowych](../azure-portal/supportability/networking-quota-requests.md) <br>[Zwiększenie limitu przydziału zasobów obliczeniowych](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat wymagań migracji, zobacz [szczegóły techniczne migracji do usługi Azure Cloud Services (obsługa rozszerzona)](in-place-migration-technical-details.md)

---
title: Azure Relay często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 83cbc4b02bb8b63878abd046ed2314728546e87a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250059"
---
# <a name="azure-relay-faqs"></a>Azure Relay często zadawane pytania

Ten artykuł zawiera odpowiedzi na kilka często zadawanych pytań dotyczących [Azure Relay](https://azure.microsoft.com/services/service-bus/). Aby uzyskać ogólne informacje o cenach i pomocy technicznej platformy Azure, zobacz [często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-relay"></a>Co to jest usługa Azure Relay?
[Usługa Azure Relay](relay-what-is-it.md) ułatwia aplikacjom hybrydowym, ułatwiając bezpieczniejsze Uwidacznianie usług, które znajdują się w firmowej sieci korporacyjnej w chmurze publicznej. Można uwidocznić te usługi bez konieczności otwierania połączenia zapory i nie wymagając niepożądanych zmian w infrastrukturze sieci firmowej.

### <a name="what-is-a-relay-namespace"></a>Co to jest przestrzeń nazw przekaźnika?
[Przestrzeń nazw](relay-create-namespace-portal.md) jest kontenerem zakresu, którego można użyć do adresowania zasobów przekaźnika w aplikacji. Należy utworzyć przestrzeń nazw, aby użyć przekaźnika. Jest to jeden z pierwszych kroków w temacie Wprowadzenie.

### <a name="what-happened-to-service-bus-relay-service"></a>Co się stało z Service Bus Relay usługą?
Wcześniej nazwana usługa Service Bus Relay jest teraz nazywana [Azure Relay](service-bus-relay-tutorial.md). Można nadal używać tej usługi w zwykły sposób. Funkcja Połączenia hybrydowe to zaktualizowana wersja usługi, która została przesadzona z usługi Azure BizTalk Services. WCF Relay i Połączenia hybrydowe są nadal obsługiwane.

## <a name="pricing"></a>Ceny
Ta sekcja zawiera odpowiedzi na kilka często zadawanych pytań dotyczących struktury cenowej przekaźnika. Zobaczysz również [często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/) dotyczące ogólnych informacji o cenach platformy Azure. Aby uzyskać pełne informacje na temat cennika usługi Relay, zobacz [szczegóły cennika Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak naliczane są opłaty za Połączenia hybrydowe i WCF Relay?
Aby uzyskać pełne informacje na temat cennika usługi Relay, zapoznaj się z tabelą [połączenia hybrydowe i przekaźników WCF][Pricing overview] na stronie Szczegóły cennika Service Bus. Oprócz cen zanotowanych na tej stronie opłaty są naliczane za powiązane transfery danych dla ruchu wychodzącego poza centrum danych, w którym Zainicjowano obsługę aplikacji.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak naliczane są opłaty za Połączenia hybrydowe?
Poniżej przedstawiono trzy przykładowe scenariusze rozliczania dla Połączenia hybrydowe:

*   Scenariusz 1.
    *   Istnieje pojedynczy odbiornik, taki jak wystąpienie Menedżera Połączenia hybrydowe zainstalowane i działające w sposób ciągły przez cały miesiąc.
    *   W ciągu miesiąca wysyłasz 3 GB danych za pośrednictwem połączenia. 
    *   Łączna opłata wynosi $5.
*   Scenariusz 2.
    *   Istnieje pojedynczy odbiornik, taki jak wystąpienie Menedżera Połączenia hybrydowe zainstalowane i działające w sposób ciągły przez cały miesiąc.
    *   W ciągu miesiąca wysyłasz 10 GB danych za pośrednictwem połączenia.
    *   Łączna opłata wynosi $7,50. To jest $5 dla połączenia i pierwsze 5 GB + $2,50, aby uzyskać dodatkowe 5 GB danych.
*   Scenariusz 3.
    *   Istnieją dwa wystąpienia, a i B programu Połączenia hybrydowe Manager zainstalowane i ciągle działające przez cały miesiąc.
    *   W ciągu miesiąca wysyłasz 3 GB danych między połączeniem A.
    *   W ciągu miesiąca wysyłasz 6 GB danych między połączeniem B.
    *   Łączna opłata wynosi $10,50. To jest $5 dla połączenia A + $5 dla połączenia B + $0,50 (dla szóstego gigabajta w połączeniu B).


### <a name="how-are-hours-calculated-for-relay"></a>Jak są obliczane godziny dla przekaźnika?

WCF Relay jest dostępny tylko w przestrzeniach nazw warstwy Standardowa. Limity cen i [połączeń](../service-bus-messaging/service-bus-quotas.md) dla przekaźników nie uległy zmianie. Oznacza to, że w ramach przekaźników nadal są naliczone opłaty na podstawie liczby komunikatów (nie operacji) i godzin przekazywania. Aby uzyskać więcej informacji, zobacz tabelę ["połączenia hybrydowe and WCF Relay"](https://azure.microsoft.com/pricing/details/service-bus/) na stronie szczegółów cennika.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co zrobić, jeśli mam więcej niż jeden odbiornik połączony z określonym przekaźnikiem?
W niektórych przypadkach pojedynczy przekaźnik może mieć wiele połączonych odbiorników. Przekaźnik jest uważany za otwarty w przypadku, gdy co najmniej jeden odbiornik przekazywania jest podłączony do niego. Dodawanie detektorów do otwartych wyników przekazywania w dodatkowych godzinach przekazywania. Liczba nadawców przekaźników (klientów, którzy wywołują lub wysyłają komunikaty do przekaźników), które są połączone z przekaźnikiem, nie wpływają na obliczanie godzin przekazywania.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak licznik komunikatów jest obliczany dla przekaźników WCF?
(**Dotyczy to tylko przekaźników WCF. Komunikaty nie są kosztami dla Połączenia hybrydowe.**)

Ogólnie rzecz biorąc, opłaty za przekaźniki są obliczane przy użyciu tej samej metody, która jest używana przez jednostki obsługiwane przez brokera (kolejki, tematy i subskrypcje), opisane wcześniej. Istnieją jednak pewne istotne różnice.

Wysyłanie komunikatu do Azure Relay jest traktowane jako "pełny poprzez" wysłanie do odbiornika przekazywania, który odbiera komunikat. Nie jest ona traktowana jako operacja wysyłania do Azure Relay, po którym następuje dostarczenie odbiornika przekaźnika. Wywołanie usługi stylu żądanie-odpowiedź (z maksymalnie 64 KB) odnoszące się do odbiornika przekaźnika powoduje wyświetlenie dwóch płatnych komunikatów: jeden płatny komunikat dla żądania i jeden płatny komunikat dla odpowiedzi (przy założeniu, że odpowiedź dotyczy również 64 KB lub mniejszych). Różni się to od używania kolejki do skorygowania między klientem a usługą. Jeśli użyjesz kolejki do skorygowania między klientem a usługą, ten sam wzorzec żądanie-odpowiedź wymaga wysłania żądania do kolejki, a następnie z kolejki/dostarczania do usługi. Następuje to odpowiedź wysyłana do innej kolejki, a w przypadku jej usuwania z kolejki/dostarczania. Przy użyciu tych samych założeń rozmiaru w całym (do 64 KB), wynikowy wzorzec kolejki powoduje 4 miliardy komunikatów. Opłaty są naliczane dwukrotnie za liczbę komunikatów w celu zaimplementowania tego samego wzorca, który został osiągnięty przy użyciu przekaźnika. Oczywiście istnieją korzyści z używania kolejek do osiągnięcia tego wzorca, takich jak trwałość i poziom obciążenia. Te korzyści mogą uzasadniać dodatkowe koszty.

Przekaźniki otwierane przy użyciu powiązania WCF **netTCPRelay** traktują komunikaty, które nie są pojedynczymi komunikatami, ale jako strumień danych przepływających przez system. W przypadku korzystania z tego powiązania tylko nadawca i odbiornik mają wgląd w ramki poszczególnych wysłanych i odebranych komunikatów. W przypadku przekaźników korzystających z powiązania **netTCPRelay** wszystkie dane są traktowane jako strumień służący do obliczania komunikatów rozliczanych. W takim przypadku Service Bus oblicza łączną ilość danych wysłanych lub odebranych za pośrednictwem poszczególnych przekaźników w oparciu o 5 minut. Następnie dzieli ten łączną ilość danych o 64 KB, aby określić liczbę płatnych komunikatów dla tego przekaźnika w tym okresie.

## <a name="quotas"></a>Przydziały
| Nazwa przydziału | Zakres |  Uwagi | Wartość |
| --- | --- | --- | --- |
| Współbieżne detektory w przekaźniku |Jednostka (połączenie hybrydowe lub przekaźnik WCF) |Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |25 |
| Współbieżne połączenia przekaźnikowe na wszystkie punkty końcowe przekaźnika w przestrzeni nazw usługi |Przestrzeń nazw |- |5000 |
| Punkty końcowe przekaźnika na przestrzeń nazw usługi |Przestrzeń nazw |- |10 000 |
| Rozmiar komunikatu dla przekaźników [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) i [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Przestrzeń nazw |Komunikaty przychodzące, które przekraczają te przydziały, są odrzucane i występuje wyjątek przez wywoływany kod. |64 KB |
| Rozmiar komunikatu dla przekaźników [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Przestrzeń nazw |Brak limitu rozmiaru komunikatu. |Nieograniczona liczba |

### <a name="does-relay-have-any-usage-quotas"></a>Czy przekaźnik ma przydziały użycia?
Domyślnie dla każdej usługi w chmurze firma Microsoft ustawia zagregowany miesięczny limit przydziału użycia obliczany we wszystkich subskrypcjach klienta. Zdajemy sobie sprawę, że w razie potrzeby może przekroczyć te limity. W dowolnym momencie możesz skontaktować się z obsługą klienta, aby poznać Twoje potrzeby i odpowiednio dostosować te limity. W przypadku Service Bus zagregowane przydziały użycia są następujące:

* 5 000 000 000 komunikatów
* 2 000 000 godzin przekazywania

Chociaż firma Microsoft zastrzega sobie prawo do wyłączenia konta przekraczającego miesięczne przydziały użycia, udostępniamy powiadomienie e-mail, a firma Microsoft podejmie próbę skontaktowania się z klientem przed podjęciem jakichkolwiek działań. Klienci, którzy przekroczyli te przydziały, są w dalszym ciągu odpowiedzialni za nadwyżkowe opłaty.

### <a name="naming-restrictions"></a>Ograniczenia nazewnictwa
Nazwa przestrzeni nazw przekaźnika musi mieć długość od 6 do 50 znaków.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcjami i przestrzenią nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak mogę zmigrować przestrzeni nazw do innej subskrypcji platformy Azure?

Aby przenieść przestrzeń nazw z jednej subskrypcji platformy Azure do innej subskrypcji, możesz użyć [Azure Portal](https://portal.azure.com) lub użyć poleceń programu PowerShell. Aby przenieść przestrzeń nazw do innej subskrypcji, przestrzeń nazw musi już być aktywna. Użytkownik uruchamiający polecenia musi być użytkownikiem administratora zarówno w ramach subskrypcji źródłowej, jak i docelowej.

#### <a name="azure-portal"></a>Azure Portal

Aby użyć Azure Portal do migracji przestrzeni nazw Azure Relay z jednej subskrypcji do innej subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Aby przenieść przestrzeń nazw z jednej subskrypcji platformy Azure do innej subskrypcji za pomocą programu PowerShell, użyj następującej sekwencji poleceń. Aby można było wykonać tę operację, przestrzeń nazw musi być już aktywna, a użytkownik uruchamiający polecenia programu PowerShell musi być użytkownikiem administratora zarówno w ramach subskrypcji źródłowej, jak i docelowej.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jakie są wyjątki generowane przez Azure Relay interfejsów API i sugerowane akcje, które można wykonać?
Opis typowych wyjątków i sugerowanych czynności, które można wykonać, znajduje się w temacie [wyjątki przekaźnika][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co to jest sygnatura dostępu współdzielonego i jakich języków można użyć do wygenerowania podpisu?
Sygnatury dostępu współdzielonego (SAS) są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Aby uzyskać informacje o sposobach generowania własnych podpisów w Node.js, PHP, Python, Java, C i C#, zobacz [Service Bus Authentication z sygnaturami dostępu współdzielonego][Shared Access Signatures].

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>Czy jest możliwe Zezwalanie tylko na niektóre punkty końcowe przekaźnika?
Tak. Klient usługi Relay nawiązuje połączenia z usługą Azure Relay przy użyciu w pełni kwalifikowanych nazw domen. Klienci mogą dodać wpis dla `*.servicebus.windows.net` zapory, które obsługują listę zatwierdzania DNS.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md

---
title: Azure Service Bus kontroli dostępu z sygnaturami dostępu współdzielonego
description: Omówienie kontroli dostępu Service Bus przy użyciu sygnatur dostępu współdzielonego — Omówienie, szczegółowe informacje na temat autoryzacji SAS i Azure Service Bus.
ms.topic: article
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f71320613682f7d4b9f3b706845e68f581b3dc10
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339414"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus kontroli dostępu z sygnaturami dostępu współdzielonego

*Sygnatury dostępu współdzielonego* (SAS) są podstawowym mechanizmem zabezpieczeń dla Service Bus komunikatów. W tym artykule omówiono SAS, sposób ich działania oraz sposób ich używania w niezależny od sposób na platformie.

Funkcja SAS chroni dostęp do Service Bus na podstawie reguł autoryzacji. Są one konfigurowane zarówno w przestrzeni nazw, jak i w jednostce obsługi komunikatów (przekaźnik, kolejki lub tematu). Reguła autoryzacji ma nazwę, jest skojarzona z określonymi prawami i przenosi parę kluczy kryptograficznych. Użyj nazwy i klucza reguły za pośrednictwem zestawu SDK Service Bus lub we własnym kodzie do wygenerowania tokenu sygnatury dostępu współdzielonego. Klient może następnie przekazać token do Service Bus, aby potwierdzić autoryzację dla żądanych operacji.

> [!NOTE]
> Azure Service Bus obsługuje autoryzowanie dostępu do przestrzeni nazw Service Bus i jej jednostek przy użyciu Azure Active Directory (Azure AD). Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w odniesieniu do sygnatur dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania tokenów w kodzie i ryzyka potencjalnych luk w zabezpieczeniach.
>
> Jeśli to możliwe, firma Microsoft zaleca korzystanie z usługi Azure AD z aplikacjami Azure Service Bus. Aby uzyskać więcej informacji, zobacz następujące artykuły:
> - [Uwierzytelnianie i Autoryzowanie aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do Azure Service Bus jednostek](authenticate-application.md).
> - [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Omówienie sygnatury dostępu współdzielonego

Sygnatury dostępu współdzielonego to mechanizm autoryzacji oparty na oświadczeniach korzystający z prostych tokenów. Przy użyciu sygnatury dostępu współdzielonego klucze nigdy nie są przesyłane do sieci. Klucze są używane do kryptograficznego podpisywania informacji, które mogą zostać później zweryfikowane przez usługę. Sygnatury dostępu współdzielonego można użyć podobnie jak w przypadku schematu nazwy użytkownika i hasła, w którym klient ma bezpośredni dostęp do nazwy reguły autoryzacji i pasującego klucza. Sygnatura dostępu współdzielonego może być również używana podobnie jak w przypadku federacyjnego modelu zabezpieczeń, w którym klient otrzymuje ograniczony czas i podpisany token dostępowy z usługi tokenu zabezpieczającego bez wcześniejszego posiadania klucza podpisywania.

Uwierzytelnianie za pomocą SYGNATURy dostępu współdzielonego w Service Bus jest skonfigurowane z nazwanymi [regułami autoryzacji dostęp współdzielony](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , które mają skojarzone prawa dostępu oraz parę podstawowych i pomocniczych kluczy kryptograficznych. Klucze są 256-bitowe wartości w reprezentacji Base64. Reguły można skonfigurować na poziomie przestrzeni nazw, na Service Bus [przekaźników](../azure-relay/relay-what-is-it.md), [kolejkach](service-bus-messaging-overview.md#queues)i [tematach](service-bus-messaging-overview.md#topics).

Token [sygnatury dostępu współdzielonego](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zawiera nazwę wybranej reguły autoryzacji, identyfikator URI zasobu, do którego można uzyskać dostęp, chwilę wygaśnięcia oraz sygnaturę szyfrowania HMAC-SHA256 obliczoną dla tych pól przy użyciu podstawowego lub pomocniczego klucza kryptograficznego wybranej reguły autoryzacji.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego

Każda przestrzeń nazw Service Bus i każda jednostka Service Bus ma zasady autoryzacji dostępu współdzielonego, które składają się z reguł. Zasady na poziomie przestrzeni nazw mają zastosowanie do wszystkich jednostek w przestrzeni nazw, niezależnie od ich konfiguracji poszczególnych zasad.

Dla każdej reguły zasad autoryzacji użytkownik wybiera trzy informacje: **imię i nazwisko** , **zakres** i **prawa**. **Nazwa** to po prostu; Unikatowa nazwa w tym zakresie. Zakres jest w łatwy sposób wystarczający: jest to identyfikator URI zasobu, którego dotyczy. W przypadku przestrzeni nazw Service Bus zakres jest w pełni kwalifikowaną nazwą domeny (FQDN), taką jak `https://<yournamespace>.servicebus.windows.net/` .

Prawa przyznane przez regułę zasad mogą być kombinacją:

* "Wyślij" — przyznaje prawo do wysyłania komunikatów do jednostki
* "Nasłuchiwanie" — przydaje prawo do nasłuchiwania (przekaźnika) lub odbierania (kolejek, subskrypcji) i całej powiązanej obsługi komunikatów
* "Zarządzaj" — przyznaje prawo do zarządzania topologią przestrzeni nazw, w tym tworzenia i usuwania jednostek

Uprawnienie "Zarządzaj" obejmuje prawa "Send" i "Receive".

W obszarze nazw lub zasadach jednostki można przechowywać maksymalnie 12 reguł autoryzacji dostępu współdzielonego, zapewniając miejsce na trzy zestawy reguł, z których każdy obejmuje prawa podstawowe i kombinację operacji wysyłania i nasłuchiwania. Ten limit podkreśla, że magazyn zasad SAS nie jest przeznaczony do magazynu kont użytkowników ani usług. Jeśli aplikacja musi udzielić dostępu do Service Bus w oparciu o tożsamości użytkowników lub usług, należy wdrożyć usługę tokenów zabezpieczających, która wystawia tokeny sygnatury dostępu współdzielonego po sprawdzeniu uwierzytelnienia i dostępności.

Do reguły autoryzacji przypisywany jest *klucz podstawowy* i *klucz pomocniczy*. Są to klucze kryptograficznie silnie. Nie tracą ani nie wycieków — będą zawsze dostępne w [Azure Portal][Azure portal]. Można użyć dowolnego z wygenerowanych kluczy i można je wygenerować w dowolnym momencie. W przypadku ponownego wygenerowania lub zmiany klucza w zasadach wszystkie wcześniej wystawione tokeny na podstawie tego klucza staną się natychmiast nieprawidłowe. Jednak stałe połączenia utworzone na podstawie takich tokenów będą nadal działały do momentu wygaśnięcia tokenu.

Podczas tworzenia przestrzeni nazw Service Bus reguła zasad o nazwie **RootManageSharedAccessKey** jest tworzona automatycznie dla przestrzeni nazw. Te zasady mają uprawnienia do zarządzania całą przestrzenią nazw. Zaleca się traktowanie tej reguły, takiej jak administracyjne konto **Główne** i nie używanie jej w aplikacji. Dodatkowe reguły zasad można utworzyć na karcie **Konfiguracja** dla przestrzeni nazw w portalu za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego
W przypadku używania sygnatur dostępu współdzielonego w aplikacjach należy znać dwa potencjalne zagrożenia:

- W przypadku przecieków sygnatury dostępu współdzielonego mogą one być używane przez każdego, kto je uzyska, co może spowodować naruszenie zasobów Event Hubs.
- Jeśli sygnatura dostępu współdzielonego dostarczona do aplikacji klienckiej wygaśnie i aplikacja nie może pobrać nowego skojarzenia zabezpieczeń z usługi, może to utrudnić działanie aplikacji.

Poniższe zalecenia dotyczące korzystania z sygnatur dostępu współdzielonego mogą pomóc w ograniczeniu ryzyka:

- **Klienci mają automatycznie odnawiać sygnaturę dostępu WSPÓŁdzielonego w razie potrzeby** : klienci powinni odnowić sygnaturę dostępu współdzielonego przed wygaśnięciem, aby umożliwić ponowną próbę, jeśli usługa dostarczająca sygnaturę dostępu współdzielonego Jeśli sygnatura dostępu współdzielonego ma być używana w przypadku niewielkiej liczby natychmiastowych, krótkoterminowych operacji, które powinny być wykonane w okresie wygaśnięcia, może to być niepotrzebne, ponieważ nie jest oczekiwane odnowienie sygnatury dostępu współdzielonego. Jednak jeśli masz klienta, który rutynowo przesyła żądania za pośrednictwem sygnatury dostępu współdzielonego, to możliwość wygaśnięcia jest dostępna. Kluczową kwestią jest zrównoważenie potrzeb, aby sygnatura dostępu współdzielonego była krótkoterminowa (jak wcześniej zostało to określone) z koniecznością zapewnienia, że klient żąda odnowienia na początku, aby uniknąć przerw w działaniu z powodu wygaśnięcia sygnatury dostępu współdzielonego przed pomyślnym odnowieniem.
- **Należy zachować ostrożność w czasie uruchamiania sygnatury dostępu współdzielonego** : Jeśli ustawisz **teraz** czas rozpoczęcia dla sygnatury dostępu współdzielonego, to z powodu pochylenia zegara (różnice w bieżącym czasie w zależności od różnych maszyn) błędy mogą być nieprzerwanie zaobserwowane przez pierwsze kilka minut. Ogólnie rzecz biorąc Ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub nie ustawiaj jej wcale, co spowoduje, że będzie ona natychmiast ważna we wszystkich przypadkach. To samo ogólnie dotyczy czasu wygaśnięcia. Należy pamiętać, że w dowolnym kierunku zegara może być pochylony do 15 minut na dowolnym z tych żądań. 
- **Być zależne od zasobu, do którego ma zostać uzyskany dostęp** : najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do pojedynczej jednostki, udziel im dostępu do odczytu dla tej pojedynczej jednostki, a dostęp do odczytu/zapisu/usuwania nie zostanie usunięty do wszystkich jednostek. Pomaga również zmniejszyć szkody w przypadku naruszenia bezpieczeństwa SAS, ponieważ SAS ma mniej mocy w ręce osoby atakującej.
- **Nie zawsze używaj sygnatury dostępu współdzielonego** : czasami ryzyko związane z konkretną operacją na Event Hubs ma większe korzyści wynikające z użycia sygnatury dostępu współdzielonego. W przypadku takich operacji Utwórz usługę warstwy środkowej, która zapisuje dane w Event Hubs po sprawdzeniu poprawności reguły biznesowej, uwierzytelnianiu i inspekcji.
- **Zawsze używaj protokołu HTTPS** : zawsze używaj protokołu HTTPS do tworzenia lub dystrybuowania sygnatury dostępu współdzielonego. Jeśli sygnatura dostępu współdzielonego jest przenoszona za pośrednictwem protokołu HTTP i przechwycona, osoba atakująca wykonująca dołączenie w środku jest w stanie odczytać sygnaturę dostępu współdzielonego, a następnie użyć jej w taki sam sposób, jak w przypadku zamierzonego użytkownika.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguracja uwierzytelniania sygnatury dostępu współdzielonego

Regułę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) można skonfigurować dla Service Bus przestrzenie nazw, kolejki lub tematy. Konfigurowanie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w subskrypcji Service Bus nie jest obecnie obsługiwane, ale możesz użyć reguł skonfigurowanych dla przestrzeni nazw lub tematu, aby zabezpieczyć dostęp do subskrypcji. Przykład roboczy, który ilustruje tę procedurę, można znaleźć w artykule [używanie uwierzytelniania sygnatury dostępu współdzielonego (SAS) z subskrypcją Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tym rysunku reguły autoryzacji *manageRuleNS* , *sendRuleNS* i *listenRuleNS* dotyczą zarówno kolejki Q1, jak i tematu T1, natomiast *listenRuleQ* i *sendRuleQ* mają zastosowanie tylko do kolejki Q1 i *sendRuleT* dotyczy tylko tematu T1.

## <a name="generate-a-shared-access-signature-token"></a>Generowanie tokenu sygnatury dostępu współdzielonego

Każdy klient, który ma dostęp do nazwy reguły autoryzacji i jednego z jego kluczy podpisywania, może generować token SAS. Token jest generowany przez przedpływanie ciągu w następującym formacie:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Natychmiastowe wygaśnięcie tokenu. Liczba całkowita odzwierciedlająca sekundy od `00:00:00 UTC` 1 stycznia 1970 (w systemie UNIX epoki) po wygaśnięciu tokenu.
* **`skn`** -Nazwa reguły autoryzacji.
* **`sr`** -Identyfikator URI zasobu, do którego uzyskuje się dostęp.
* **`sig`** Podpisane.

`signature-string`Jest to skrót SHA-256 obliczany na podstawie identyfikatora URI zasobu ( **zakres** zgodnie z opisem w poprzedniej sekcji) oraz ciąg reprezentacji tokenu wygasa natychmiast, oddzielony znakami LF.

Obliczenia skrótu wyglądają podobnie jak w poniższym pseudo kodzie i zwracają 256-bitową/32-bajtową wartość skrótu.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości niebędące skrótami, dzięki czemu odbiorca może ponownie obliczyć skrót z tymi samymi parametrami, sprawdzając, czy Wystawca ma prawidłowy klucz podpisywania.

Identyfikator URI zasobu to pełny identyfikator URI zasobu Service Bus, do którego odnosi się dostęp. Na przykład `http://<namespace>.servicebus.windows.net/<entityPath>` lub `sb://<namespace>.servicebus.windows.net/<entityPath>` `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` . 

**Identyfikator URI musi być [zakodowany przy użyciu wartości procentowej](/dotnet/api/system.web.httputility.urlencode).**

Reguła autoryzacji dostępu współdzielonego używana do podpisywania musi być skonfigurowana w jednostce określonej przez ten identyfikator URI lub według jednego z hierarchicznych obiektów nadrzędnych. Na przykład `http://contoso.servicebus.windows.net/contosoTopics/T1` lub `http://contoso.servicebus.windows.net` w poprzednim przykładzie.

Token sygnatury dostępu współdzielonego jest prawidłowy dla wszystkich zasobów poprzedzonych prefiksem `<resourceURI>` używanym w `signature-string` .

> [!NOTE]
> Przykłady generowania tokenu sygnatury dostępu współdzielonego przy użyciu różnych języków programowania można znaleźć w temacie [Generuj token SAS](/rest/api/eventhub/generate-sas-token). 

## <a name="regenerating-keys"></a>Ponowne generowanie kluczy

Zalecane jest okresowe ponowne wygenerowanie kluczy używanych w obiekcie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Istnieją podstawowe i pomocnicze miejsca na kluczach, dzięki czemu można stopniowo obrócić klucze. Jeśli aplikacja zwykle używa klucza podstawowego, można skopiować klucz podstawowy do gniazda klucza pomocniczego i ponownie wygenerować klucz podstawowy. Nową wartość klucza podstawowego można następnie skonfigurować w aplikacjach klienckich, które mają ciągły dostęp przy użyciu starego klucza podstawowego w gnieździe pomocniczym. Po zaktualizowaniu wszystkich klientów można ponownie wygenerować klucz pomocniczy, aby ostatecznie wycofać stary klucz podstawowy.

Jeśli wiesz lub podejrzewasz, że klucz został złamany, i musisz odwołać klucze, możesz ponownie wygenerować zarówno element [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , jak i [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), zastępując je nowymi kluczami. Ta procedura unieważnia wszystkie tokeny podpisane przy użyciu starych kluczy.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Uwierzytelnianie sygnatury dostępu współdzielonego za pomocą Service Bus

Scenariusze opisane w poniższej części obejmują konfigurację reguł autoryzacji, generowanie tokenów SAS i autoryzację klienta.

Aby uzyskać pełny przykład pracy aplikacji Service Bus, która ilustruje konfigurację i używa autoryzacji sygnatury [dostępu współdzielonego](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8), zapoznaj się z tematem Service Bus. Pokrewny przykład, który ilustruje użycie reguł autoryzacji SYGNATURy dostępu współdzielonego skonfigurowanych w przestrzeniach nazw lub tematach do zabezpieczania subskrypcji Service Bus, jest dostępny tutaj: [używanie uwierzytelniania za pomocą podpisu (SAS) z subskrypcjami Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Dostęp do reguł autoryzacji dostępu współdzielonego w jednostce

Za pomocą bibliotek .NET Framework Service Bus można uzyskać dostęp do obiektu [Microsoft. ServiceBus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) skonfigurowanych dla kolejki Service Bus lub tematu za pomocą kolekcji [reguł autoryzacji](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) w odpowiedniej [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Poniższy kod przedstawia sposób dodawania reguł autoryzacji dla kolejki.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Korzystanie z autoryzacji sygnatury dostępu współdzielonego

Aplikacje korzystające z zestawu Azure .NET SDK z bibliotekami Service Bus .NET mogą używać autoryzacji sygnatury dostępu współdzielonego za pośrednictwem klasy [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . Poniższy kod ilustruje użycie dostawcy tokenu do wysyłania komunikatów do kolejki Service Bus. Zamiast podanego tutaj użycia można także przekazać wcześniej wystawiony token do metody fabryki dostawcy tokenów.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Dostawcy tokenu można także używać bezpośrednio do wystawiania tokenów do przekazania innym klientom.

Parametry połączenia mogą zawierać nazwę reguły ( *SharedAccessKeyName* ) i klucz reguły ( *SharedAccessKey* ) lub wcześniej wystawiony token ( *SharedAccessSignature* ). Gdy są obecne w parametrach połączenia przesłanych do dowolnego konstruktora lub metody fabryki akceptujących parametry połączenia, dostawca tokenów SAS jest automatycznie tworzony i wypełniany.

Należy pamiętać, że aby korzystać z autoryzacji sygnatury dostępu współdzielonego z przekaźnikami Service Bus, można użyć kluczy SAS skonfigurowanych dla Service Bus przestrzeni nazw. Jeśli jawnie utworzysz przekaźnik w przestrzeni nazw ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) z obiektem [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), możesz ustawić reguły sygnatury dostępu współdzielonego tylko dla tego przekaźnika. Aby używać autoryzacji sygnatury dostępu współdzielonego z subskrypcjami Service Bus, można użyć kluczy SAS skonfigurowanych dla Service Bus przestrzeni nazw lub tematu.

## <a name="use-the-shared-access-signature-at-http-level"></a>Używanie sygnatury dostępu współdzielonego (na poziomie HTTP)

Teraz, gdy wiesz już, jak utworzyć sygnatury dostępu współdzielonego dla dowolnych jednostek w Service Bus, możesz wykonać wpis HTTP:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Pamiętaj, że wszystko to działa w przypadku wszystkich elementów. Można utworzyć sygnaturę dostępu współdzielonego dla kolejki, tematu lub subskrypcji.

W przypadku przyznania nadawcy lub klienta tokenu sygnatury dostępu współdzielonego klient nie ma klucza bezpośrednio i nie może wycofać skrótu w celu uzyskania go. W związku z tym masz kontrolę nad tym, do czego mogą uzyskać dostęp oraz jak długo. Należy pamiętać, że jeśli zmienisz klucz podstawowy w zasadach, wszystkie utworzone sygnatury dostępu współdzielonego zostaną unieważnione.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Użyj sygnatury dostępu współdzielonego (na poziomie AMQP)

W poprzedniej sekcji pokazano, jak używać tokenu sygnatury dostępu współdzielonego z żądaniem HTTP POST w celu wysyłania danych do Service Bus. Jak wiadomo, możesz uzyskać dostęp do Service Bus przy użyciu Advanced Message Queuing Protocol (AMQP), który jest preferowanym protokołem używanym ze względu na wydajność, w wielu scenariuszach. Użycie tokenu sygnatury dostępu współdzielonego z AMQP został opisany w dokumencie [AMQP Claim-Based Security w wersji 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , który działa w wersji roboczej od 2013, ale jest obecnie obsługiwany przez platformę Azure.

Przed rozpoczęciem wysyłania danych do Service Bus, Wydawca musi wysłać token sygnatury dostępu współdzielonego w komunikacie AMQP do dobrze zdefiniowanego węzła AMQP o nazwie **$CBS** (można go zobaczyć jako "Specjalna" Kolejka używana przez usługę, aby uzyskać i zweryfikować wszystkie tokeny sygnatury dostępu współdzielonego). Wydawca musi określić pole **ReplyTo** wewnątrz komunikatu AMQP; jest to węzeł, w którym usługa odpowiada na wydawcę, z wynikiem walidacji tokenu (prosty wzorzec żądania/odpowiedzi między wydawcą a usługą). Ten węzeł odpowiedzi jest tworzony "na bieżąco" mówiąc o "dynamicznym tworzeniu węzła zdalnego" zgodnie z opisem w specyfikacji AMQP 1,0. Po sprawdzeniu, czy token sygnatury dostępu współdzielonego jest prawidłowy, Wydawca może przejść do przodu i rozpocząć wysyłanie danych do usługi.

Poniższe kroki pokazują, jak wysłać token sygnatury dostępu współdzielonego z protokołem AMQP przy użyciu biblioteki [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) . Jest to przydatne, jeśli nie można użyć oficjalnego zestawu Service Bus SDK (na przykład w przypadku środowiska WinRT, .NET Compact Framework, .NET Micro Framework i mono), które rozwijają się w języku C \# . Oczywiście ta biblioteka ułatwia zrozumienie sposobu, w jaki zabezpieczenia oparte na oświadczeniach działają na poziomie AMQP, ponieważ zapoznaj się z tym, jak działa na poziomie protokołu HTTP (za pomocą żądania HTTP POST i tokenu sygnatury dostępu współdzielonego wysłanego w nagłówku "Autoryzacja"). Jeśli nie potrzebujesz takiej szczegółowej wiedzy na temat usługi AMQP, możesz użyć oficjalnego zestawu SDK Service Bus z aplikacjami .NET Framework, dzięki czemu będzie to możliwe.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()`Metoda odbiera *połączenie* (AMQP wystąpienie klasy połączenia zgodnie z [biblioteką AMQP .NET Lite](https://github.com/Azure/amqpnetlite)), które reprezentuje połączenie TCP z usługą i parametr *sasToken* , który jest wysyłany przez token sygnatury dostępu współdzielonego.

> [!NOTE]
> Należy pamiętać, że połączenie jest tworzone z **mechanizmem uwierzytelniania SASL ustawionym na wartość anonimowe** (a nie domyślnie zwykły z nazwą użytkownika i hasłem używanym, gdy nie trzeba wysyłać tokenu SAS).
>
>

Następnie wydawca tworzy dwa linki AMQP do wysyłania tokenu sygnatury dostępu współdzielonego i otrzymuje odpowiedź (wynik sprawdzania poprawności tokenu) z usługi.

Komunikat AMQP zawiera zestaw właściwości i więcej informacji niż zwykły komunikat. Token sygnatury dostępu współdzielonego jest treścią komunikatu (przy użyciu jego konstruktora). Właściwość **"ReplyTo"** jest ustawiana na nazwę węzła na potrzeby otrzymywania wyniku walidacji linku odbiorcy (można zmienić jej nazwę, jeśli chcesz, i zostanie ona utworzona dynamicznie przez usługę). Ostatnie trzy właściwości Application/Custom są używane przez usługę w celu wskazania rodzaju operacji, która ma zostać wykonana. Zgodnie z opisem w specyfikacji wersji roboczej CBS, muszą to być **nazwa operacji** ("Put-token"), **Typ tokenu** (w tym przypadku `servicebus.windows.net:sastoken` ) oraz **"nazwa" odbiorców** , do których stosuje się token (cała jednostka).

Po wysłaniu tokenu sygnatury dostępu współdzielonego na linku nadawcy Wydawca musi odczytać odpowiedź na linku odbiornika. Odpowiedź to prosty komunikat AMQP z właściwością aplikacji o nazwie **"status-Code"** , która może zawierać takie same wartości jak kod stanu HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Prawa wymagane dla Service Bus operacji

W poniższej tabeli przedstawiono prawa dostępu wymagane do różnych operacji na Service Bus zasobach.

| Operacja | Wymagane jest żądanie | Zakres roszczeń |
| --- | --- | --- |
| **Przestrzeń nazw** | | |
| Konfigurowanie reguły autoryzacji w przestrzeni nazw |Zarządzanie |Dowolny adres przestrzeni nazw |
| **Rejestr usługi** | | |
| Wyliczanie zasad prywatnych |Zarządzanie |Dowolny adres przestrzeni nazw |
| Rozpocznij nasłuchiwanie w przestrzeni nazw |Nasłuchiwanie |Dowolny adres przestrzeni nazw |
| Wysyłanie komunikatów do odbiornika w przestrzeni nazw |Wysyłanie |Dowolny adres przestrzeni nazw |
| **Kolejka** | | |
| Tworzenie kolejki |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie kolejki |Zarządzanie |Dowolny prawidłowy adres kolejki |
| Wyliczanie kolejek |Zarządzanie |/$Resources/Queues |
| Pobierz opis kolejki |Zarządzanie |Dowolny prawidłowy adres kolejki |
| Konfigurowanie reguły autoryzacji dla kolejki |Zarządzanie |Dowolny prawidłowy adres kolejki |
| Wyślij do kolejki |Wysyłanie |Dowolny prawidłowy adres kolejki |
| Odbieranie komunikatów z kolejki |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Porzucanie lub kończenie komunikatów po odebraniu komunikatu w trybie wglądu w tryb blokady |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Odłóż komunikat do późniejszego pobrania |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Wiadomość utracona |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Pobierz stan skojarzony z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Ustawianie stanu skojarzonego z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Zaplanuj wiadomość w celu późniejszego dostarczenia; na przykład [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Nasłuchiwanie | Dowolny prawidłowy adres kolejki
| **Temat** | | |
| Tworzenie tematu |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie tematu |Zarządzanie |Dowolny prawidłowy adres tematu |
| Wyliczenie tematów |Zarządzanie |/$Resources/topics |
| Pobierz opis tematu |Zarządzanie |Dowolny prawidłowy adres tematu |
| Konfigurowanie reguły autoryzacji tematu |Zarządzanie |Dowolny prawidłowy adres tematu |
| Wyślij do tematu |Wysyłanie |Dowolny prawidłowy adres tematu |
| **Subskrypcja** | | |
| Tworzenie subskrypcji |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie subskrypcji |Zarządzanie |.. /myTopic/Subscriptions |
| Pobierz opis subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Porzucanie lub kończenie komunikatów po odebraniu komunikatu w trybie wglądu w tryb blokady |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Odłóż komunikat do późniejszego pobrania |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Wiadomość utracona |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Pobierz stan skojarzony z sesją tematu |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Ustawianie stanu skojarzonego z sesją tematu |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| **Reguły** | | |
| Tworzenie reguły |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Usuwanie reguły |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie zasad |Zarządzanie lub nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
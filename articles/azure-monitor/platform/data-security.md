---
title: Zabezpieczenia danych Log Analytics | Microsoft Docs
description: Dowiedz się, jak Log Analytics chroni prywatność i zabezpiecza dane.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: ef34dbfd3af326dbf2d82e09a4c5c8c8e4a91a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319800"
---
# <a name="log-analytics-data-security"></a>Zabezpieczenia danych Log Analytics
Ten dokument ma na celu dostarczenie informacji specyficznych dla Log Analytics, które są funkcją Azure Monitor, aby uzupełnić informacje o [Centrum zaufania Azure](https://www.microsoft.com/en-us/trust-center?rtc=1).  

W tym artykule wyjaśniono sposób zbierania, przetwarzania i zabezpieczania danych przez usługę Log Analytics. Za pomocą agentów można nawiązać połączenie z usługą sieci Web, używać System Center Operations Manager do zbierania danych operacyjnych lub pobierania danych z usługi Azure Diagnostics do użycia przez Log Analytics. 

Usługa Log Analytics umożliwia bezpieczne zarządzanie danymi opartymi na chmurze przy użyciu następujących metod:

* Podział danych
* Przechowywanie danych
* Zabezpieczenia fizyczne
* Zarządzanie zdarzeniami
* Zgodność
* Certyfikaty standardów zabezpieczeń

Skontaktuj się z nami, podając wszelkie pytania, sugestie lub problemy dotyczące dowolnych z poniższych informacji, w tym naszych zasad zabezpieczeń w [opcjach pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Bezpieczne wysyłanie danych przy użyciu protokołu TLS 1,2 

Aby zapewnić bezpieczeństwo danych przesyłanych do Log Analytics, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsza wersja protokołu TLS/SSL (SSL) została uznana za narażoną, a mimo to nadal pracują w celu zapewnienia zgodności z poprzednimi wersjami, nie jest to **zalecane**, a branża szybko przenosi się do porzucenia, aby uzyskać pomoc techniczną dla tych starszych protokołów. 

[Rada normy zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustawił [termin 30 czerwca 2018,](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) aby wyłączyć starsze wersje protokołu TLS/SSL i uaktualnić je do bezpieczniejsze protokoły. Gdy platforma Azure pozostanie w starszej wersji, jeśli agenci nie będą mogli komunikować się za pomocą co najmniej protokołu TLS 1,2, nie można wysyłać danych do Log Analytics. 

Nie zalecamy jawnego ustawienia agenta do używania protokołu TLS 1,2, chyba że jest to absolutnie konieczne, ponieważ może to spowodować przerwanie funkcji zabezpieczeń na poziomie platformy, które umożliwiają automatyczne wykrywanie i korzystanie z nowszych bezpieczniejszych protokołów, gdy staną się dostępne, takich jak TLS 1,3. 

### <a name="platform-specific-guidance"></a>Wskazówki dotyczące platformy

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zależą od [OpenSSL](https://www.openssl.org) obsługi TLS 1,2.  | Sprawdź [Dziennik zmian OpenSSL](https://www.openssl.org/news/changelog.html) , aby potwierdzić, że wersja OpenSSL jest obsługiwana.|
| Windows 8,0 — 10 | Obsługiwane i domyślnie włączone. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 — 2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie włączone domyślnie. | Aby uzyskać szczegółowe informacje na temat włączania, zobacz stronę [Ustawienia rejestru Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-segregation"></a>Podział danych
Po pozyskaniu danych przez usługę Log Analytics dane są przechowywane logicznie osobno dla każdego składnika w ramach usługi. Wszystkie dane są otagowane dla każdego obszaru roboczego. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi. Dane są przechowywane w dedykowanej bazie danych w klastrze magazynu w wybranym regionie.

## <a name="data-retention"></a>Przechowywanie danych
Indeksowane dane przeszukiwane dzienników są przechowywane i przechowywane zgodnie z planem cenowym. Aby uzyskać więcej informacji, zobacz [Cennik usługi log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

W ramach [umowy subskrypcyjnej](https://azure.microsoft.com/support/legal/subscription-agreement/)firma Microsoft zachowa Twoje dane zgodnie z warunkami umowy.  Po usunięciu danych klienta żadne dyski fizyczne nie są niszczone.  

W poniższej tabeli wymieniono niektóre z dostępnych rozwiązań i przedstawiono przykłady typów zbieranych danych.

| **Rozwiązanie** | **Typy danych** |
| --- | --- |
| Capacity and Performance |Dane wydajności i metadane |
| Zarządzanie aktualizacjami |Metadane i dane stanu |
| Zarządzanie dziennikami |Dzienniki zdarzeń zdefiniowane przez użytkownika, dzienniki zdarzeń systemu Windows i/lub dzienniki usług IIS |
| Śledzenie zmian |Spis oprogramowania, metadane demona usługi systemu Windows i system Linux oraz metadane plików systemu Windows/Linux |
| SQL i Active Directory Assessment |Dane usługi WMI, dane rejestru, dane dotyczące wydajności i SQL Server dynamiczne wyniki widoku zarządzania |

W poniższej tabeli przedstawiono przykłady typów danych:

| **Typ danych** | **Pola** |
| --- | --- |
| Alerty |Nazwa alertu, opis alertu, identyfikatorze basemanagedentityid, identyfikator problemu, IsMonitorAlert, RuleId, stanu rozpoznania, priorytet, ważność, Kategoria, właściciel, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguracja |CustomerID, identyfikator agenta, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Wydarzenie |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Uwaga:** Gdy zapisujesz zdarzenia z polami niestandardowymi w dzienniku zdarzeń systemu Windows, Log Analytics je gromadzić. |
| Metadane |Identyfikatorze basemanagedentityid, upewnić, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, networkname, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adres IP, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Wydajność |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| State |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, identyfikatorze basemanagedentityid, elementu monitorid, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Usługa Log Analytics jest zarządzana przez personel firmy Microsoft, a wszystkie działania są rejestrowane i mogą być poddawane inspekcji. Log Analytics działa jako usługa platformy Azure i spełnia wszystkie wymagania dotyczące zgodności i zabezpieczeń platformy Azure. Szczegółowe informacje o zabezpieczeniach fizycznych zasobów platformy Azure można wyświetlić na stronie 18 [przeglądu zabezpieczeń Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Prawa dostępu fizycznego do bezpiecznych obszarów są zmieniane w ciągu jednego dnia roboczego dla każdej osoby, która nie ma już odpowiedzialności za usługę Log Analytics, w tym transfer i zakończenie. Możesz zapoznać się z globalną infrastrukturą fizyczną używaną w [centrach danych firmy Microsoft](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Log Analytics ma proces zarządzania zdarzeniami, do którego są przestrzegane wszystkie usługi firmy Microsoft. Podsumowując:

* Użyj współużytkowanego modelu odpowiedzialności, gdy część odpowiedzialności zabezpieczeń należy do firmy Microsoft, a część należy do klienta
* Zarządzanie zdarzeniami zabezpieczeń platformy Azure:
  * Rozpocznij badanie po wykryciu zdarzenia
  * Ocenianie wpływu i ważności incydentu przez członka zespołu odpowiedzi na zdarzenia dotyczącego wywołania. W oparciu o dowód, ocena może być niewynikiem dalszej eskalacji zespołu ds. odpowiedzi.
  * Diagnozuj zdarzenia przez ekspertów z odpowiedzią na zabezpieczenia, aby przeanalizować badanie techniczne lub śledczej, określić strategie zawierania, łagodzenia i rozwiązywania problemów. Jeśli zespół ds. zabezpieczeń uważa, że dane klienta mogły stać się narażone na nielegalną lub nieautoryzowaną osobę, równoległe wykonywanie procesu powiadamiania o zdarzeniach klienta rozpoczyna się równolegle.  
  * Stabilizacja i Odzyskaj ze zdarzenia. Zespół odpowiedzi na zdarzenia tworzy plan odzyskiwania w celu ograniczenia problemu. Kroki dotyczące zawierania kryzysowego, takie jak systemy, których dotyczy problem, mogą wystąpić natychmiast i równolegle z diagnostyką. Dłuższe czynniki zaradcze mogą być planowane, gdy zostanie przeprowadzone natychmiastowe ryzyko.  
  * Zamknij zdarzenie i przeprowadź pośmiertnie. Zespół ds. reagowania na zdarzenia tworzy, który zawiera szczegółowe informacje o zdarzeniu, z zamiarem zmiany zasad, procedur i procesów w celu uniknięcia wystąpienia zdarzenia.
* Powiadamiaj klientów o zdarzeniach dotyczących zabezpieczeń:
  * Określ zakres klientów, których dotyczy problem, i podaj każdy, którzy mają wpływ na szczegółowe powiadomienie, jak to możliwe
  * Utwórz powiadomienie, aby udostępnić klientom szczegółowe informacje, dzięki czemu mogą oni przeprowadzić badanie na ich końcu i spełnić wszelkie zobowiązania podjęte przez nich użytkownikom końcowym, bez konieczności nieuzasadnionego opóźnienia procesu powiadamiania.
  * Potwierdź i Zadeklaruj zdarzenie, w razie potrzeby.
  * Powiadamiaj klientów z powiadomieniem o zdarzeniu bez nieuzasadnionego opóźnienia i zgodnie z wszelkimi zobowiązaniami prawnymi lub umownymi. Powiadomienia o zdarzeniach związanych z zabezpieczeniami są dostarczane do co najmniej jednej z administratorów klienta, co oznacza, że firma Microsoft wybiera, w tym pocztą e-mail.
* Przeprowadzaj gotowość zespołu i szkolenia:
  * Personel firmy Microsoft jest zobowiązany do ukończenia szkolenia w zakresie zabezpieczeń i świadomości, co pomaga im identyfikować i zgłaszać podejrzane problemy z zabezpieczeniami.  
  * Operatory pracujące w ramach usługi Microsoft Azure mają dodatkowe obowiązki szkoleniowe, w tym ich dostęp do systemów poufnych zawierających dane klientów.
  * Pracownicy usługi Microsoft Security Response otrzymują wyspecjalizowane szkolenia dotyczące ich ról

W przypadku utraty danych klienta powiadamiamy każdego klienta w ciągu jednego dnia. Jednak utrata danych klienta nigdy nie wystąpił w przypadku usługi. 

Aby uzyskać więcej informacji o tym, jak firma Microsoft reaguje na zdarzenia związane z zabezpieczeniami, zobacz [Microsoft Azure Security Response w chmurze](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Zgodność
Program Log Analytics tworzenia oprogramowania i ochrony informacji zespołu usług wspiera jego wymagania biznesowe i stosuje przepisy zgodnie z opisem w artykule [Microsoft Azure Centrum zaufania](https://azure.microsoft.com/support/trust-center/) i [zgodność Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak Log Analytics ustalają wymagania dotyczące zabezpieczeń, w tym miejscu opisano również mechanizmy kontroli zabezpieczeń, zarządzania i monitorowania zagrożeń. Corocznie przeglądamy zasady, standardy, procedury i wskazówki.

Każdy członek zespołu deweloperów otrzymuje formalne szkolenia w zakresie zabezpieczeń aplikacji. Wewnętrznie system kontroli wersji jest używany do tworzenia oprogramowania. Każdy projekt oprogramowania jest chroniony przez system kontroli wersji.

Firma Microsoft ma zespół ds. zabezpieczeń i zgodności, który przewidział i ocenia wszystkie usługi w firmie Microsoft. Funkcjonariusze ds. bezpieczeństwa informacji tworzą zespół i nie są związani z zespołami inżynieryjnymi, którzy opracowują Log Analytics. Funkcjonariusze zabezpieczeń mają własny łańcuch zarządzania i przeprowadzają niezależne oceny produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Zarządu firmy Microsoft otrzymuje powiadomienie roczne sprawozdanie dotyczące wszystkich programów zabezpieczeń informacji w firmie Microsoft.

Log Analytics rozwój oprogramowania i zespół usług aktywnie współpracują z zespołami prawnymi i zgodnymi z firmą Microsoft oraz innymi partnerami branżowymi w celu uzyskania różnych certyfikatów.

## <a name="certifications-and-attestations"></a>Certyfikaty i zaświadczanie
Usługa Azure Log Analytics spełnia następujące wymagania:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standardowa karta płatnicza (zgodna z normą PCI) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) przez Radę standardu zabezpieczeń PCI.
* [Kontrolki organizacji usług (SOC) 1 typ 1 i SOC 2 typu 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA i HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) dla firm, które mają umowę HIPAA Business Partner Agreement
* Typowe kryteria inżynierów systemu Windows
* Wiarygodne technologie komputerowe firmy Microsoft
* Jako usługa platformy Azure składniki, których Log Analytics używa, są zgodne z wymaganiami dotyczącymi zgodności platformy Azure. Więcej informacji można znaleźć w artykule [zgodność Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> W niektórych certyfikatach/zaświadczeniu Log Analytics wymieniony poniżej jego imię *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Przepływ danych zabezpieczeń w chmurze obliczeniowej
Na poniższym diagramie przedstawiono architekturę zabezpieczeń chmury jako przepływ informacji od firmy i sposób jej zabezpieczania, która jest przenoszona do usługi Log Analytics, która jest ostatecznie widoczna w Azure Portal. Więcej informacji o każdym kroku znajduje się na diagramie.

![Obraz Log Analytics zbierania i zabezpieczania danych](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zarejestruj się, aby uzyskać Log Analytics i zebrać dane
Aby organizacja mogła wysyłać dane do Log Analytics, należy skonfigurować agenta systemu Windows lub Linux działającego w usłudze Azure Virtual Machines lub na komputerach wirtualnych lub fizycznych w środowisku lub innym dostawcy chmury.  W przypadku korzystania z Operations Manager z grupy zarządzania konfiguruje agenta Operations Manager. Użytkownicy (którzy mogą być użytkownikami, innymi indywidualni użytkownicy lub grupa osób) tworzą jeden lub więcej Log Analyticsych obszarów roboczych i rejestrują agentów przy użyciu jednego z następujących kont:

* [Identyfikator organizacji](../../active-directory/fundamentals/sign-up-organization.md)
* [Konto Microsoft — Outlook, Office Live, MSN](https://account.microsoft.com/account)

Obszar roboczy Log Analytics to miejsce, w którym zbierane są dane, agregowane, analizowane i przedstawiane. Obszar roboczy jest używany głównie jako środek do partycjonowania danych, a każdy obszar roboczy jest unikatowy. Na przykład możesz chcieć, aby dane produkcyjne były zarządzane z jednym obszarem roboczym i danymi testowymi zarządzanymi z innym obszarem roboczym. Obszary robocze pomagają również administratorowi kontrolować dostęp do danych. Z każdym obszarem roboczym może być skojarzonych wiele kont użytkowników, a każde konto użytkownika może uzyskiwać dostęp do wielu obszarów roboczych Log Analytics. Tworzysz obszary robocze na podstawie regionu centrum danych.

W przypadku Operations Manager Grupa zarządzania Operations Manager nawiązuje połączenie z usługą Log Analytics. Następnie należy skonfigurować, które systemy zarządzane przez agentów w grupie zarządzania mogą zbierać i wysyłać dane do usługi. W zależności od włączonego rozwiązania dane z tych rozwiązań są wysyłane bezpośrednio z Operations Manager serwera zarządzania do usługi Log Analytics lub z powodu ilości danych zbieranych przez system zarządzany przez agenta, są wysyłane bezpośrednio z agenta do usługi. W przypadku systemów, które nie są monitorowane przez Operations Manager, każda bezpośrednio nawiązuje połączenie z usługą Log Analytics.

Cała komunikacja między połączonymi systemami a usługą Log Analytics jest zaszyfrowana. Protokół TLS (HTTPS) jest używany do szyfrowania.  Proces Microsoft SDL jest używany w celu zapewnienia aktualności Log Analytics z najnowszymi postępami w protokołach kryptograficznych.

Każdy typ agenta zbiera dane dla Log Analytics. Typ zbieranych danych zależy od typów używanych rozwiązań. Możesz zobaczyć podsumowanie zbierania danych w [obszarze dodawanie log Analytics rozwiązań z Galeria rozwiązań](../insights/solutions.md). Ponadto w przypadku większości rozwiązań dostępne są bardziej szczegółowe informacje dotyczące zbierania danych. Rozwiązanie to pakiet wstępnie zdefiniowanych widoków, zapytań przeszukiwania dzienników, reguł zbierania danych i logiki przetwarzania. Tylko Administratorzy mogą zaimportować rozwiązanie przy użyciu Log Analytics. Po zaimportowaniu rozwiązania jest ono przenoszone do serwerów zarządzania Operations Manager (jeśli są używane), a następnie do wybranych agentów. Następnie Agenci zbierają dane.

## <a name="2-send-data-from-agents"></a>2. Wyślij dane z agentów
Należy zarejestrować wszystkie typy agentów z kluczem rejestracji i nawiązać bezpieczne połączenie między agentem a usługą Log Analytics przy użyciu uwierzytelniania opartego na certyfikatach i protokołu TLS z portem 443. Log Analytics używa magazynu wpisów tajnych do generowania kluczy i zarządzania nimi. Klucze prywatne są obracane co 90 dni i są przechowywane na platformie Azure i zarządzane przez operacje na platformie Azure, które przestrzegają rygorystycznych rozwiązań regulacyjnych i zgodności.

W przypadku Operations Manager Grupa zarządzania zarejestrowana w obszarze roboczym Log Analytics ustanawia bezpieczne połączenie HTTPS z serwerem zarządzania Operations Manager.

W przypadku agentów z systemem Windows lub Linux uruchomionym w usłudze Azure Virtual Machines klucz magazynu tylko do odczytu jest używany do odczytywania zdarzeń diagnostycznych w tabelach platformy Azure.  

W przypadku każdego z agentów raportowania do Operations Manager grupy zarządzania zintegrowanej z Log Analytics, jeśli serwer zarządzania nie może komunikować się z usługą z jakiegokolwiek powodu, zebrane dane są przechowywane lokalnie w tymczasowej pamięci podręcznej na serwerze zarządzania programu.   Próbują ponownie wysłać dane co osiem minut przez dwie godziny.  W przypadku danych, które pomijają serwer zarządzania i są wysyłane bezpośrednio do Log Analytics, zachowanie jest spójne z agentem systemu Windows.  

Dane przechowywane w pamięci podręcznej programu Windows lub agenta serwera zarządzania są chronione przez magazyn poświadczeń systemu operacyjnego. Jeśli usługa nie może przetworzyć danych po upływie dwóch godzin, agenci będą kolejkować dane. Jeśli kolejka zostanie zapełniona, Agent rozpocznie usuwanie typów danych, rozpoczynając od danych wydajności. Limit kolejki agentów jest kluczem rejestru, aby można było go zmodyfikować, w razie potrzeby. Zebrane dane są kompresowane i wysyłane do usługi, pomijając Operations Manager bazy danych grupy zarządzania, dlatego nie dodaje do nich żadnych obciążeń. Po wysłaniu zebrane dane zostaną usunięte z pamięci podręcznej.

Jak opisano powyżej, dane z serwera zarządzania lub agentów połączonych bezpośrednio są wysyłane za pośrednictwem protokołu TLS w celu Microsoft Azure centrów danych. Opcjonalnie możesz użyć ExpressRoute, aby zapewnić dodatkowe zabezpieczenia dla danych. ExpressRoute to sposób bezpośredniego łączenia się z platformą Azure z istniejącej sieci WAN, takiej jak wieloprotokołowe przełączanie etykiet (MPLS), udostępniane przez dostawcę usług sieciowych. Aby uzyskać więcej informacji, zobacz [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Usługa Log Analytics odbiera i przetwarza dane
Usługa Log Analytics zapewnia, że dane przychodzące pochodzą z zaufanego źródła, sprawdzając certyfikaty i integralność danych przy użyciu uwierzytelniania platformy Azure. Nieprzetworzone dane pierwotne są następnie przechowywane w centrum zdarzeń platformy Azure w regionie, w którym dane będą ostatecznie przechowywane w spoczynku. Typ przechowywanych danych zależy od typów rozwiązań, które zostały zaimportowane i używane do zbierania danych. Następnie usługa Log Analytics przetwarza pierwotne dane i pozyskuje ją w bazie danych.

Okres przechowywania zebranych danych przechowywanych w bazie danych zależy od wybranego planu cenowego. W przypadku warstwy *bezpłatna* zebrane dane są dostępne przez siedem dni. W przypadku warstwy *Płatne* zebrane dane są domyślnie dostępne przez 31 dni, ale można je przedłużyć do 730 dni. Dane są przechowywane w postaci zaszyfrowanej w usłudze Azure Storage, aby zapewnić poufność danych, a dane są replikowane w regionie lokalnym przy użyciu magazynu lokalnie nadmiarowego (LRS). Ostatnie dwa tygodnie danych również są przechowywane w pamięci podręcznej opartej na dyskach SSD, a ta pamięć podręczna jest zaszyfrowana.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Użyj Log Analytics, aby uzyskać dostęp do danych
Aby uzyskać dostęp do obszaru roboczego Log Analytics, zaloguj się do Azure Portal przy użyciu konta organizacyjnego lub konto Microsoft utworzonego wcześniej. Cały ruch między portalem a usługą Log Analytics jest przesyłany za pośrednictwem bezpiecznego kanału HTTPS. W przypadku korzystania z portalu identyfikator sesji jest generowany przez klienta użytkownika (przeglądarki sieci Web), a dane są przechowywane w lokalnej pamięci podręcznej, dopóki sesja nie zostanie zakończona. Po zakończeniu pamięć podręczna zostanie usunięta. Pliki cookie po stronie klienta, które nie zawierają informacji umożliwiających identyfikację użytkownika, nie są automatycznie usuwane. Pliki cookie sesji są oznaczone jako HTTPOnly i są zabezpieczone. Po upływie wstępnie ustalonego okresu bezczynności sesja Azure Portal zostanie zakończona.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak zbierać dane za pomocą Log Analytics dla maszyn wirtualnych platformy Azure po [rozszybkim samouczku maszyny wirtualnej platformy Azure](../learn/quick-collect-azurevm.md).  

*  Jeśli chcesz zbierać dane z komputerów fizycznych lub wirtualnych z systemami Windows lub Linux w środowisku, zobacz [Przewodnik Szybki Start dla](../learn/quick-collect-linux-computer.md) komputerów z systemem Linux lub [Szybki Start dla komputerów z systemem Windows](../learn/quick-collect-windows-computer.md)


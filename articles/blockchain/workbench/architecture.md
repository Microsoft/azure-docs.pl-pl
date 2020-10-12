---
title: Architektura usługi Azure Blockchain Workbench
description: Omówienie architektury Azure łańcucha bloków Workbench w wersji zapoznawczej i jej składników.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87049170"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura usługi Azure Blockchain Workbench

Usługa Azure łańcucha bloków Workbench Preview upraszcza tworzenie aplikacji łańcucha bloków, oferując rozwiązanie przy użyciu kilku składników platformy Azure. Łańcucha bloków Workbench można wdrożyć przy użyciu szablonu rozwiązania w portalu Azure Marketplace. Szablon umożliwia wybranie modułów i składników do wdrożenia, takich jak stos łańcucha bloków, typ aplikacji klienckiej i obsługa integracji IoT. Po wdrożeniu program łańcucha bloków Workbench zapewnia dostęp do aplikacji sieci Web, aplikacji dla systemu iOS i aplikacji systemu Android.

![Architektura łańcucha bloków Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Tożsamość i uwierzytelnianie

Korzystając z łańcucha bloków Workbench, konsorcjum może sfederować swoją tożsamość przedsiębiorstwa przy użyciu Azure Active Directory (Azure AD). Workbench generuje nowe konta użytkowników dla tożsamości w łańcuchu z tożsamościami przedsiębiorstwa przechowywanymi w usłudze Azure AD. Mapowanie tożsamości ułatwia uwierzytelnione logowanie do interfejsów API i aplikacji klienta oraz korzysta z zasad uwierzytelniania organizacji. Workbench umożliwia również kojarzenie tożsamości przedsiębiorstwa z określonymi rolami w ramach danego inteligentnego kontraktu. Ponadto Workbench udostępnia mechanizm umożliwiający zidentyfikowanie działań, które mogą być wykonywane przez role i w jakim czasie.

Po wdrożeniu usługi łańcucha bloków Workbench użytkownicy będą korzystać z łańcucha bloków Workbench przez aplikacje klienckie, interfejs API klienta oparty na protokole REST lub interfejs API obsługi komunikatów. We wszystkich przypadkach interakcje muszą być uwierzytelniane za pośrednictwem Azure Active Directory (Azure AD) lub poświadczeń specyficznych dla urządzenia.

Użytkownicy sfederować swoją tożsamość do usługi Azure AD konsorcjum, wysyłając wiadomość e-mail z zaproszeniem do uczestników na adres e-mail. Podczas logowania użytkownicy są uwierzytelniani przy użyciu nazwy, hasła i zasad. Na przykład uwierzytelnianie dwuskładnikowe swojej organizacji.

Usługa Azure AD służy do zarządzania wszystkimi użytkownikami, którzy mają dostęp do usługi łańcucha bloków Workbench. Każde urządzenie łączące się z kontraktem inteligentnym jest również skojarzone z usługą Azure AD.

Usługa Azure AD jest również używana do przypisywania użytkowników do specjalnej grupy administratorów. Użytkownicy skojarzeni z grupą administratorów uzyskują dostęp do praw i akcji w ramach łańcucha bloków Workbench, w tym wdrażania kontraktów i udzielania użytkownikowi uprawnień dostępu do kontraktu. Użytkownicy spoza tej grupy nie mają dostępu do akcji administratora.

## <a name="client-applications"></a>Aplikacje klienckie

Program Workbench udostępnia automatycznie generowane aplikacje klienckie dla sieci Web i urządzeń przenośnych (iOS, Android), które mogą służyć do sprawdzania poprawności, testowania i wyświetlania aplikacji łańcucha bloków. Interfejs aplikacji jest dynamicznie generowany na podstawie metadanych inteligentnej kontraktu i może pomieścić dowolny przypadek użycia. Aplikacje klienckie dostarczają fronton skierowany do kompletnych aplikacji łańcucha bloków generowanych przez łańcucha bloków Workbench. Aplikacje klienckie uwierzytelniają użytkowników za pośrednictwem Azure Active Directory (Azure AD), a następnie zaprezentowania środowiska użytkownika dopasowanego do kontekstu biznesowego dla kontraktu inteligentnego. Środowisko użytkownika umożliwia tworzenie nowych wystąpień kontraktów inteligentnych przez autoryzowanych użytkowników, a następnie przedstawia możliwość wykonywania określonych typów transakcji w odpowiednich punktach w procesie biznesowym, który reprezentuje kontrakt inteligentny.

W aplikacji sieci Web autoryzowani użytkownicy mogą uzyskiwać dostęp do konsola administratora. Konsola programu jest dostępna dla użytkowników w grupie Administratorzy w usłudze Azure AD i zapewnia dostęp do następujących funkcji:

* Wdrażaj firmy Microsoft, które zapewniają inteligentne kontrakty dla popularnych scenariuszy. Na przykład scenariusz transferu zasobów.
* Przekazuj i wdrażaj własne, inteligentne kontrakty.
* Przypisanie użytkownikowi dostępu do inteligentnego kontraktu w kontekście określonej roli.

Aby uzyskać więcej informacji, zobacz [przykładowe aplikacje klienckie usługi Azure łańcucha bloków Workbench w witrynie GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Interfejs API usługi bramy

Łańcucha bloków Workbench obejmuje interfejs API usługi bramy opartej na protokole REST. Podczas zapisywania do łańcucha bloków interfejs API generuje i dostarcza komunikaty do brokera zdarzeń. Gdy interfejs API żąda danych, zapytania są wysyłane do bazy danych poza łańcuchem. Baza danych zawiera replikę danych i metadanych w łańcuchu, która zawiera informacje o kontekście i konfiguracji dla obsługiwanych kontraktów inteligentnych. Zapytania zwracają wymagane dane z repliki spoza łańcucha w formacie, który został poinformowany o metadanych kontraktu.

Deweloperzy mogą uzyskać dostęp do interfejsu API usługi bramy w celu kompilowania lub integrowania rozwiązań łańcucha bloków bez polegania na aplikacjach klienckich łańcucha bloków Workbench.

> [!NOTE]
> Aby włączyć dostęp uwierzytelniony do interfejsu API, dwie aplikacje klienckie są rejestrowane w Azure Active Directory. Azure Active Directory wymaga oddzielnych rejestracji aplikacji poszczególnych typów aplikacji (natywnych i sieci Web).

## <a name="message-broker-for-incoming-messages"></a>Broker komunikatów dla wiadomości przychodzących

Deweloperzy, którzy chcą wysyłać wiadomości bezpośrednio do programu łańcucha bloków Workbench, mogą wysyłać wiadomości bezpośrednio do Service Bus. Na przykład interfejs API komunikatów może służyć do integracji systemu z systemem lub urządzeń IoT.

## <a name="message-broker-for-downstream-consumers"></a>Broker komunikatów dla odbiorców podrzędnych

W trakcie cyklu życia aplikacji wystąpią zdarzenia. Zdarzenia mogą być wyzwalane przez interfejs API bramy lub w księdze. Powiadomienia o zdarzeniach mogą inicjować kod podrzędny na podstawie zdarzenia.

Łańcucha bloków Workbench automatycznie wdraża dwa typy odbiorców zdarzeń. Jeden odbiorca jest wyzwalany przez zdarzenia łańcucha bloków w celu wypełnienia sklepu SQL w łańcuchu. Innym konsumentem jest przechwytywanie metadanych dla zdarzeń generowanych przez interfejs API związanych z przekazywaniem i przechowywaniem dokumentów.

## <a name="message-consumers"></a>Odbiorcy komunikatów

 Odbiorcy wiadomości pobierają wiadomości z Service Bus. Źródłowy model zdarzeń dla odbiorców wiadomości umożliwia korzystanie z rozszerzeń dodatkowych usług i systemów. Można na przykład dodać obsługę, aby wypełnić CosmosDB lub oszacować komunikaty przy użyciu usługi Azure Streaming Analytics. W poniższych sekcjach opisano klientów komunikatów uwzględnionych w łańcucha bloków Workbench.

### <a name="distributed-ledger-consumer"></a>Odbiorca w księdze rozproszonej

Komunikaty technologii Distributed Ledger (DLT) zawierają metadane dla transakcji, które mają być zapisywane w łańcucha bloków. Konsument pobiera komunikaty i wypycha dane do konstruktora transakcji, osoby podpisującej i routera.

### <a name="database-consumer"></a>Użytkownik bazy danych

Odbiorca bazy danych pobiera komunikaty z Service Bus i wypycha dane do dołączonej bazy danych, takiej jak baza danych w Azure SQL Database.

### <a name="storage-consumer"></a>Odbiorca magazynu

Odbiorca magazynu pobiera komunikaty z Service Bus i wypycha dane do dołączonego magazynu. Na przykład przechowywanie dokumentów z wynikami mieszania w usłudze Azure Storage.

## <a name="transaction-builder-and-signer"></a>Konstruktor transakcji i osoba podpisująca

Jeśli komunikat na brokerze komunikatów przychodzących musi być zapisany w łańcucha bloków, zostanie on przetworzony przez klienta DLT. Odbiorca DLT to usługa, która pobiera komunikat zawierający metadane dla żądanej transakcji do wykonania, a następnie wysyła informacje do *konstruktora transakcji i osoby podpisującej*. *Konstruktor transakcji i osoba podpisująca* gromadzą transakcję łańcucha bloków na podstawie danych i żądanego miejsca docelowego łańcucha bloków. Po złożeniu transakcja jest podpisana. Klucze prywatne są przechowywane w Azure Key Vault.

 Łańcucha bloków Workbench pobiera odpowiedni klucz prywatny z Key Vault i podpisuje transakcję poza Key Vault. Po podpisaniu transakcja jest wysyłana do routerów i ksiąg transakcji.

## <a name="transaction-routers-and-ledgers"></a>Routery transakcji i księgi

Routery transakcji i księgi uwzględniają podpisane transakcje i kierują je do odpowiednich łańcucha bloków. Obecnie łańcucha bloków Workbench obsługuje Ethereum jako element docelowy łańcucha bloków.

## <a name="dlt-watcher"></a>Obserwator DLT

Obserwator rozproszonej technologii księgi (DLT) monitoruje zdarzenia występujące na łańcuchach blokowych dołączonych do łańcucha bloków Workbench.
Zdarzenia odzwierciedlają informacje istotne dla użytkowników indywidualnych i systemów. Na przykład tworzenie nowych wystąpień kontraktu, wykonywanie transakcji i zmiana stanu. Zdarzenia są przechwytywane i wysyłane do brokera komunikatów wychodzących, dzięki czemu mogą być używane przez użytkowników podrzędnych.

Na przykład klient SQL monitoruje zdarzenia, wykorzystuje je i wypełnia bazę danych z uwzględnieniem zawartych wartości. Kopia umożliwia ponowne tworzenie repliki danych w łańcuchu w magazynie spoza łańcucha.

## <a name="azure-sql-database"></a>Azure SQL Database

Baza danych dołączona do łańcucha bloków Workbench przechowuje definicje kontraktów, metadane konfiguracji i replikę danych z ułatwieniami dostępu do języka SQL przechowywanych w łańcucha bloków. Te dane mogą być łatwo badane, wizualizowane lub analizowane przez bezpośrednie uzyskiwanie dostępu do bazy danych. Deweloperzy i inni użytkownicy mogą korzystać z bazy danych do raportowania, analizy lub innych integracji skoncentrowanych na danych. Na przykład użytkownicy mogą wizualizować dane transakcji przy użyciu Power BI.

Ten magazyn poza łańcuchem zapewnia organizacjom korporacyjnym możliwość wykonywania zapytań dotyczących danych w języku SQL, a nie w księdze łańcucha bloków. Ponadto dzięki standaryzacji standardowego schematu niezależny od z stosów technologicznych łańcucha bloków, magazyn poza łańcuchem umożliwia ponowne użycie raportów i innych artefaktów między projektami, scenariuszami i organizacjami.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage służy do przechowywania kontraktów i metadanych skojarzonych z kontraktami.

W przypadku zamówień zakupu i weksli do obrazów używanych w wiadomościach i obrazach medycznych do wideo pochodzącego z składnika Continuum, w tym kamer związanych z treścią policyjną i głównych obrazów ruchu, dokumenty odgrywają rolę w wielu scenariuszach skoncentrowanych na łańcucha bloków. Dokumenty nie są odpowiednie do bezpośredniego umieszczania w łańcucha bloków.

Łańcucha bloków Workbench obsługuje możliwość dodawania dokumentów lub innej zawartości multimedialnej przy użyciu logiki biznesowej łańcucha bloków. Skrót zawartości dokumentu lub nośnika jest przechowywany w łańcucha bloków, a rzeczywista zawartość dokumentu lub nośnika jest przechowywana w usłudze Azure Storage. Informacje o skojarzonych transakcjach są dostarczane do brokera komunikatów przychodzących, spakowane, podpisane i kierowane do łańcucha bloków. Ten proces wyzwala zdarzenia, które są udostępniane za pośrednictwem brokera komunikatów wychodzących. Baza danych SQL wykorzystuje te informacje i wysyła je do bazy danych w celu późniejszego wykonywania zapytań. Systemy podrzędne mogą również wykorzystać te zdarzenia do działania w razie potrzeby.

## <a name="monitoring"></a>Monitorowanie

Workbench zapewnia rejestrowanie aplikacji przy użyciu Application Insights i Azure Monitor. Application Insights służy do przechowywania wszystkich zarejestrowanych informacji z łańcucha bloków Workbench i zawiera błędy, ostrzeżenia i operacje zakończone powodzeniem. Application Insights mogą być używane przez deweloperów do debugowania problemów z łańcucha bloków Workbench. 

Azure Monitor zawiera informacje dotyczące kondycji sieci łańcucha bloków. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Blockchain Workbench](./deploy.md)

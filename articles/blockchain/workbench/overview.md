---
title: Omówienie usługi Azure łańcucha bloków Workbench w wersji zapoznawczej
description: Omówienie usługi Azure łańcucha bloków Workbench w wersji zapoznawczej i jej możliwości.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87003155"
---
# <a name="what-is-azure-blockchain-workbench"></a>Czym jest usługa Azure Blockchain Workbench?

Usługa Azure łańcucha bloków Workbench w wersji zapoznawczej to zbiór usług i funkcji platformy Azure, które ułatwiają tworzenie i wdrażanie aplikacji łańcucha bloków w celu udostępniania procesów i danych firmowych innym organizacjom. Usługa Azure Blockchain Workbench udostępnia szkielet infrastruktury do tworzenia aplikacji łańcucha bloków, umożliwiając deweloperom skoncentrowanie się na tworzeniu logiki biznesowej i kontraktów inteligentnych. Ułatwia również tworzenie aplikacji łańcucha bloków dzięki integracji kilku usług i funkcji platformy Azure w celu zautomatyzowania typowych zadań projektowych.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Tworzenie aplikacji łańcucha bloków

Usługa Blockchain Workbench umożliwia definiowanie aplikacji łańcucha bloków przy użyciu konfiguracji oraz pisanie kodu kontraktu inteligentnego. Zamiast tworzenia szkieletu i konfigurowania usług pomocniczych, można od razu rozpocząć tworzenie aplikacji łańcucha bloków i skoncentrować się na definiowaniu umowy i pisaniu logiki biznesowej.

## <a name="manage-applications-and-users"></a>Zarządzanie aplikacjami i użytkownikami

Usługa Azure Blockchain Workbench udostępnia aplikację internetową i interfejsy API REST do zarządzania aplikacjami i użytkownikami łańcucha bloków. Administratorzy usługi Blockchain Workbench mogą zarządzać dostępem do aplikacji i przypisywać użytkowników do ról aplikacji. Użytkownicy usługi Azure AD są automatycznie mapowani na członków w aplikacji.

## <a name="integrate-blockchain-with-applications"></a>Integracja łańcucha bloków z aplikacjami

Interfejsy API REST usługi Blockchain Workbench i interfejsy API oparte na komunikatach umożliwiają integrację z istniejącymi systemami. Interfejsy API zapewniają interfejs umożliwiający zastępowanie lub korzystanie z wielu technologii rejestru rozproszonego, magazynu i ofert baz danych.

Łańcucha bloków Workbench może przekształcać komunikaty wysyłane do interfejsu API opartego na komunikatach w celu kompilowania transakcji w formacie oczekiwanym przez natywny interfejs API łańcucha bloków.  Usługa Workbench umożliwia rejestrowanie transakcji i kierowanie ich do odpowiedniego łańcucha bloków. 

Usługa Workbench automatycznie dostarcza zdarzenia do usług Service Bus i Event Grid w celu wysyłania wiadomości do użytkowników podrzędnych. Deweloperzy mogą zintegrować usługę z jednym z tych systemów obsługi wiadomości, aby realizować transakcje i przyglądać się wynikom.

## <a name="deploy-a-blockchain-network"></a>Wdrażanie sieci łańcucha bloków

Usługa Azure Blockchain Workbench, jako wstępnie skonfigurowane rozwiązanie z szablonem rozwiązania Azure Resource Manager, upraszcza konfigurację sieci łańcucha bloków konsorcjum. Ten szablon obejmuje uproszczone wdrażanie, które powoduje wdrożenie wszystkich składników potrzebnych do uruchomienia konsorcjum. Obecnie usługa Blockchain Workbench obsługuje platformę Ethereum.

## <a name="use-active-directory"></a>Użyj usługi Active Directory

W przypadku istniejących protokołów łańcucha bloków tożsamości łańcucha bloków są reprezentowane jako adres w sieci. W usłudze Azure Blockchain Workbench nie trzeba troszczyć się o tożsamość łańcucha bloków, ponieważ jest ona skojarzona z tożsamością usługi Active Directory, co ułatwia tworzenie aplikacji przedsiębiorstwa za pomocą tożsamości usługi Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Synchronizowanie danych łańcucha z magazynem poza łańcuchem

Usługa Azure Blockchain Workbench ułatwia analizowanie zdarzeń i danych łańcucha bloków dzięki automatycznemu synchronizowaniu danych łańcucha bloków z magazynem poza łańcuchem. Zamiast wyodrębniać dane bezpośrednio z łańcucha bloków, można wysyłać zapytania do systemów baz danych poza łańcuchem, takich jak SQL Server. Znajomość łańcucha bloków nie jest wymagana dla użytkowników końcowych, którzy wykonują zadania analizy danych.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Współpracuj z inżynierami firmy Microsoft i ekspertów społeczności Azure łańcucha bloków.

* [Microsoft Q&stronie pytania dotyczącej usługi Azure łańcucha bloków Workbench](/answers/topics/azure-blockchain-workbench.html)
* [Witryna Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Architektura usługi Azure Blockchain Workbench](architecture.md)

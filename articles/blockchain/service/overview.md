---
title: Omówienie usługi Azure łańcucha bloków
description: Omówienie usługi Azure łańcucha bloków Service
ms.date: 01/04/2021
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: 15d1126a98a4d3194a256ed07de3918cb066a4e2
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963882"
---
# <a name="what-is-azure-blockchain-service"></a>Co to jest usługa Azure Blockchain Service?

Usługa Azure łańcucha bloków to w pełni zarządzana usługa księgi, która umożliwia użytkownikom rozwój i obsługę sieci łańcucha bloków w skali na platformie Azure. Zapewniając ujednoliconą kontrolę zarówno w zakresie zarządzania infrastrukturą, jak i łańcucha bloków sieci, usługa Azure łańcucha bloków Service oferuje następujące funkcje:

* Proste wdrażanie i operacje w sieci
* Wbudowane zarządzanie konsorcjum
* Opracowywanie inteligentnych kontraktów przy użyciu znanych narzędzi programistycznych

Usługa Azure łańcucha bloków Service została zaprojektowana do obsługi wielu protokołów finansowych. Obecnie zapewnia ona obsługę Ethereumego dla księgi [kworum](https://www.goquorum.com/) przy użyciu mechanizmu consensusu [Byzantine Fault tolerancja (IBFT)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) o konsensusie.

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Możesz skupić się na tworzeniu aplikacji i logice biznesowej zamiast przydzielać czas i zasoby do zarządzania maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu narzędzi typu "open source" i wybranej platformy do dostarczania rozwiązań bez konieczności uczenia się nowych umiejętności.

## <a name="network-deployment-and-operations"></a>Wdrażanie sieci i operacje

Wdrażanie usługi Azure łańcucha bloków jest realizowane za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure lub za pośrednictwem programu Visual Studio Code przy użyciu rozszerzenia łańcucha bloków platformy Azure. Wdrożenie jest uproszczone, łącznie z obsługą zarówno węzłów transakcji, jak i modułów sprawdzania poprawności, sieci wirtualnych platformy Azure pod kątem izolacji zabezpieczeń oraz magazynu zarządzanego przez usługę.  Ponadto podczas wdrażania nowego elementu członkowskiego łańcucha bloków użytkownicy tworzą także konsorcjum lub przyłączają się do niego.  Konsorcja umożliwiają wielu stronom w różnych subskrypcjach platformy Azure bezpieczne komunikowanie się ze sobą przy użyciu współużytkowanego łańcucha bloków.  To uproszczone wdrożenie zmniejsza liczbę dni wdrożenia sieci łańcucha bloków w ciągu kilku minut.

### <a name="performance-and-service-tiers"></a>Warstwy wydajności i usługi

Usługa Azure łańcucha bloków Service oferuje dwie warstwy usług: *podstawowa* i *standardowa*. Każda warstwa oferuje różne wydajności i możliwości, które umożliwiają obsługę lekkich obciążeń programistycznych i testowych do wysoce skalowalnych wdrożeń łańcucha bloków produkcyjnych. Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy *standardowa* dla wdrożeń klasy produkcyjnej. Obie warstwy obejmują co najmniej jeden węzeł transakcji i jeden węzeł modułu sprawdzania poprawności (podstawowy) lub dwa węzły modułu sprawdzania poprawności (standard). 

![Warstwy cenowe](./media/overview/pricing-tiers.png)

Oprócz oferowania dwóch węzłów modułu sprawdzania poprawności warstwa *standardowa* oferuje dwie *rdzeni wirtualnych* dla każdej transakcji i węzła modułu sprawdzania poprawności, natomiast warstwa *podstawowa* oferuje konfigurację 1 rdzeń wirtualny.  Dzięki ofercie 2 rdzeni wirtualnych dla węzłów transakcji i modułu sprawdzania poprawności 1 rdzeń wirtualny może być dedykowany dla księgi kworum, a pozostałe 1 rdzeń wirtualny mogą być używane dla innych usług związanych z infrastrukturą, zapewniając optymalną wydajność dla obciążeń łańcucha bloków produkcyjnych. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure łańcucha bloków](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Bezpieczeństwo i konserwacja

Po zainicjowaniu obsługi pierwszego elementu członkowskiego łańcucha bloków można dodać do niego dodatkowe węzły transakcji.  Domyślnie węzły transakcji są chronione za pomocą reguł zapory i wymagają konfiguracji w celu uzyskania dostępu.  Ponadto wszystkie węzły transakcji szyfrują dane w ruchu za pośrednictwem protokołu TLS.  Istnieje wiele opcji zabezpieczania dostępu do węzła transakcji, w tym reguł zapory, uwierzytelniania podstawowego, kluczy dostępu i integracji Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Konfigurowanie węzłów transakcji](configure-transaction-nodes.md) i [Konfigurowanie dostępu Azure Active Directory](configure-aad.md).

Jako usługa zarządzana usługa Azure łańcucha bloków gwarantuje, że węzły elementu członkowskiego łańcucha bloków są poprawione przy użyciu najnowszych aktualizacji stosu systemu operacyjnego hosta i księgi oprogramowania, skonfigurowanych pod kątem wysokiej dostępności (tylko w warstwie Standardowa), eliminując większość DevOps wymaganych dla tradycyjnych węzłów IaaS łańcucha bloków.  Aby uzyskać więcej informacji na temat poprawek i aktualizacji, zobacz [obsługiwane wersje finansów usługi Azure łańcucha bloków](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Ponadto usługa Azure łańcucha bloków zapewnia zaawansowane metryki za pomocą usługi Azure Monitor, która zapewnia wgląd w dane użycia procesora CPU, pamięci i magazynu.  Azure Monitor zapewnia również przydatny wgląd w działanie sieci łańcucha bloków, takie jak Transactions i Blocks analizowane pod, Głębokość kolejki transakcji i aktywne połączenia.  Metryki można dostosować w celu udostępnienia widoków szczegółowych informacji, które są ważne dla aplikacji łańcucha bloków.  Ponadto progi można definiować za pomocą alertów umożliwiających użytkownikom wyzwalanie akcji, takich jak wysyłanie wiadomości e-mail lub SMS, uruchamianie aplikacji logiki, funkcja platformy Azure lub wysyłanie do niestandardowego elementu webhook.

![Przechwytywanie ekranu pokazuje monitorowanie, z wartościami bloków, transakcji, oczekujących transakcji i obsłużonych żądań.](./media/overview/metrics.png)

Za pomocą usługi Azure Log Analytics użytkownicy mogą wyświetlać dzienniki związane z księgą kworum lub innymi ważnymi informacjami, takimi jak próby połączeń z węzłami transakcji.

## <a name="built-in-consortium-management"></a>Wbudowane zarządzanie konsorcjum

Podczas wdrażania pierwszego elementu członkowskiego łańcucha bloków można dołączyć lub utworzyć nowego konsorcjum.  Konsorcjum jest grupą logiczną służącą do zarządzania zarządzaniem i łącznością między członkami łańcucha bloków, którzy Transact w procesie wieloskładnikowym.  Usługa Azure łańcucha bloków zapewnia wbudowane kontrolki ładu za pomocą wstępnie zdefiniowanych, inteligentnych kontraktów, które określają, jakie działania mogą wykonać Członkowie konsorcjum.  Te kontrolki ładu można dostosować zgodnie z potrzebami administratora konsorcjum. Podczas tworzenia nowego konsorcjum członek łańcucha bloków jest domyślnym administratorem konsorcjum, umożliwiając Zapraszanie innych stron do dołączenia do konsorcjum.  Możesz dołączyć do konsorcjum tylko wtedy, gdy wcześniej zaproszono użytkownika.  Podczas dołączania do konsorcjum członek łańcucha bloków podlega kontrolom nadzoru obowiązującym przez administratora konsorcjum.

![Zarządzanie konsorcjum](./media/overview/consortium.png)

Do akcji związanych z zarządzaniem konsorcjum, takich jak dodawanie i usuwanie członków z konsorcjum, można uzyskać dostęp za pośrednictwem programu PowerShell i interfejsu API REST. Można programowo zarządzać konsorcjum przy użyciu wspólnych interfejsów zamiast modyfikować i przesyłać kontrakty inteligentne oparte na wypełnieniu. Aby uzyskać więcej informacji, zobacz [Zarządzanie konsorcjum](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Opracowywanie przy użyciu znanych narzędzi programistycznych

Na podstawie księgi Ethereum kworum Open Source można opracowywać aplikacje dla usługi Azure łańcucha bloków w taki sam sposób, jak w przypadku istniejących aplikacji Ethereum. Korzystając z wiodących partnerów branżowych, rozszerzenie Azure łańcucha bloków Development Visual Studio Code Kit pozwala deweloperom korzystać z dobrze znanych narzędzi, takich jak Truffle Suite, aby kompilować inteligentne kontrakty. Za pomocą rozszerzenia usługi Azure łańcucha bloków Visual Studio Code można utworzyć istniejący konsorcjum lub połączyć się z nim, aby można było kompilować i wdrażać inteligentne kontrakty z jednego środowiska IDE. Aby uzyskać więcej informacji, zobacz [Azure łańcucha bloków Development Kit w witrynie vs Code Marketplace](https://aka.ms/vscodebcextension) i [podręczniku użytkownika zestawu Azure łańcucha bloków Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Publikowanie danych łańcucha bloków

Łańcucha bloków Data Manager w usłudze Azure łańcucha bloków Service przechwytuje, przekształca i dostarcza dane transakcji usługi Azure łańcucha bloków Service do Azure Event Grid tematów zapewniających niezawodną i skalowalną integrację z księgą łańcucha bloków z usługami platformy Azure. Za pomocą Data Manager łańcucha bloków można zintegrować aplikacje w łańcuchu i magazyny danych. Aby uzyskać więcej informacji, zobacz [łańcucha bloków Data Manager for Azure łańcucha bloków Service](data-manager.md).

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Współpracuj z inżynierami firmy Microsoft i ekspertów społeczności Azure łańcucha bloków.

* [Microsoft Q&stronie pytania dotyczącej usługi Azure łańcucha bloków Service](/answers/topics/azure-blockchain-service.html)
* [Witryna Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, wypróbuj Przewodnik Szybki start lub Dowiedz się więcej na temat tych zasobów.
* [Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [Tworzenie elementu członkowskiego łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Postępuj zgodnie z Microsoft Learn ścieżką [Rozpocznij pracę z łańcucha bloków programowaniem](/learn/paths/ethereum-blockchain-development)
* Obejrzyj [serię początkujących do łańcucha bloków](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain)
* Porównania kosztów i kalkulatorów można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/blockchain-service) .
* Tworzenie pierwszej aplikacji przy użyciu [zestawu Azure łańcucha bloków Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Podręcznik użytkownika](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) rozszerzenia usługi Azure łańcucha bloków programu vscode

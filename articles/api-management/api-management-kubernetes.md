---
title: Korzystanie z API Management platformy Azure z mikrousługami wdrożonymi w usłudze Azure Kubernetes Service | Microsoft Docs
description: W tym artykule opisano opcje wdrażania API Management za pomocą AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 293a47bc3e8499d7eda4e64bb68bc95eb4c85ab0
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108402"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Korzystanie z usługi Azure API Management z mikrousługami wdrożonymi w usłudze Azure Kubernetes Service

Mikrousługi doskonale nadaje się do kompilowania interfejsów API. Za pomocą [usługi Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) można szybko wdrożyć i obsługiwać [architekturę opartą na mikrousługach](/azure/architecture/guide/architecture-styles/microservices) w chmurze. Następnie można wykorzystać [platformę Azure API Management](https://aka.ms/apimrocks) (API Management) do publikowania mikrousług jako interfejsów API do użytku wewnętrznego i zewnętrznego. W tym artykule opisano opcje wdrażania API Management z AKS. Założono podstawową wiedzę na temat Kubernetes, API Management i sieci platformy Azure. 

## <a name="background"></a>Tło

Podczas publikowania mikrousług jako interfejsów API do użycia może być trudne do zarządzania komunikacją między mikrousługami i klientami, którzy ich używają. Występuje wiele problemów z wycinaniem, takich jak uwierzytelnianie, autoryzacja, ograniczanie, buforowanie, przekształcenie i monitorowanie. Te zagadnienia są ważne niezależnie od tego, czy mikrousługi są udostępniane klientom wewnętrznym, czy zewnętrznym. 

Wzorzec [bramy interfejsu API](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) dotyczy tych zagadnień. Brama interfejsu API służy jako przód do mikrousług, oddziela klientów od mikrousług, dodaje dodatkową warstwę zabezpieczeń i zmniejsza złożoność mikrousług, eliminując obciążenie związane z obcinaniem. 

[API Management platformy Azure](https://aka.ms/apimrocks) to rozwiązanie gotowe, które pozwala rozwiązać potrzeby bramy interfejsu API. Można szybko utworzyć spójną i nowoczesne bramy dla mikrousług i opublikować je jako interfejsy API. W ramach rozwiązania do zarządzania interfejsami API pełnego cyklu życia dostępne są również dodatkowe funkcje, w tym samoobsługowy portal dla deweloperów umożliwiający odnajdywanie interfejsów API, zarządzanie cyklem życia interfejsu API oraz analizowanie interfejsów API.

Gdy są używane razem, AKS i API Management udostępniają platformę do wdrażania, publikowania, zabezpieczania i monitorowania interfejsów API opartych na mikrousługach oraz zarządzania nimi. W tym artykule przedstawiono kilka opcji wdrażania AKS w połączeniu z API Management. 

## <a name="kubernetes-services-and-apis"></a>Usługi Kubernetes i interfejsy API

W klastrze Kubernetes kontenery są wdrażane w [zasobnikach](https://kubernetes.io/docs/concepts/workloads/pods/pod/), które są tymczasowe i mają cykl życia. Po zakończeniu pracy nad węzłem procesu roboczego są one tracone. W związku z tym adres IP pod może ulec zmianie w dowolnym czasie. Nie można polegać na tym, aby komunikować się z pod. 

Aby rozwiązać ten problem, Kubernetes wprowadza koncepcję [usług](https://kubernetes.io/docs/concepts/services-networking/service/). Usługa Kubernetes jest warstwą abstrakcji, która definiuje grupę logiki na podst. pozwala na zewnętrzne wykrycie ruchu, równoważenie obciążenia i odnajdowanie usług dla tych zasobników. 

Gdy wszystko jest gotowe do publikowania naszych mikrousług jako interfejsów API za API Management, musimy myśleć, jak mapować nasze usługi w Kubernetes na interfejsy API w API Management. Brak ustawionych reguł. Jest to zależne od tego, jak zostały zaprojektowane i podzielone na partycje możliwości lub domeny w mikrousługach. Na przykład, jeśli moduł w ramach usługi jest odpowiedzialny za wszystkie operacje dotyczące danego zasobu (np. klienta), usługa może być zamapowana na jeden interfejs API. Jeśli operacje na zasobie są podzielone na wiele mikrousług (np. GetOrder, PlaceOrder), wiele usług może być logicznie zagregowane w jednym interfejsie API w usłudze API Management (zobacz rys. 1). 

Mapowania mogą również być rozwijane. Ponieważ API Management tworzy fasadę przed mikrousługami, pozwala nam na refaktoryzację i zmianę rozmiaru na mikrousługi w czasie. 

![Mapowanie usług na interfejsy API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Wdróż API Management przed AKS

Istnieje kilka opcji wdrażania API Management przed klastrem AKS. 

Mimo że klaster AKS jest zawsze wdrażany w sieci wirtualnej (VNet), nie trzeba wdrażać wystąpienia API Management. Gdy API Management nie znajduje się w sieci wirtualnej klastra, klaster AKS musi opublikować publiczne punkty końcowe dla API Management do nawiązania połączenia. W takim przypadku istnieje potrzeba zabezpieczenia połączenia między API Management i AKS. Innymi słowy musimy upewnić się, że dostęp do klastra można uzyskać wyłącznie za pomocą API Management. Przejdźmy do opcji. 

### <a name="option-1-expose-services-publicly"></a>Opcja 1: ujawnianie usług publicznie

Usługi w klastrze AKS mogą być udostępniane publicznie przy użyciu [typów usług](../aks/concepts-network.md) NodePort, modułu równoważenia obciążenia lub zewnętrznegoname. W takim przypadku usługi są dostępne bezpośrednio z publicznej sieci Internet. Po wdrożeniu API Management przed klastrem musimy upewnić się, że cały ruch przychodzący przechodzi przez API Management, stosując uwierzytelnianie w mikrousługach. Na przykład API Management może zawierać token dostępu w każdym żądaniu wykonywanym w klastrze. Każda mikrousługa jest odpowiedzialna za sprawdzanie poprawności tokenu przed przetworzeniem żądania. 


Może to być najłatwiejsza opcja wdrażania API Management przed AKS, zwłaszcza jeśli masz już implementację logiki uwierzytelniania w mikrousługach. 

![Bezpośrednie Publikowanie usług](./media/api-management-aks/direct.png)

Formaty
* Łatwa konfiguracja po stronie API Management, ponieważ nie trzeba jej wprowadzać do sieci wirtualnej klastra
* Nie zmieniaj po stronie AKS, jeśli usługi zostały już ujawnione publicznie, a logika uwierzytelniania już istnieje w mikrousługach

Wada
* Potencjalne ryzyko związane z bezpieczeństwem ze względu na publiczną widoczność punktów końcowych usługi
* Brak punktu pojedynczego wejścia dla ruchu przychodzącego klastra
* Komplikuje mikrousługi przy użyciu zduplikowanej logiki uwierzytelniania

### <a name="option-2-install-an-ingress-controller"></a>Opcja 2: Instalacja kontrolera transferu danych przychodzących

Chociaż opcja 1 może być łatwiejsza, ma znaczące wady, jak wspomniano powyżej. Jeśli wystąpienie API Management nie znajduje się w sieci wirtualnej klastra, uwierzytelnianie wzajemne TLS (mTLS) jest niezawodnym sposobem zapewnienia bezpieczeństwa i zaufanego ruchu w obu kierunkach między wystąpieniem API Management a klastrem AKS. 

Uwierzytelnianie wzajemne przy użyciu protokołu TLS jest [natywnie obsługiwane](./api-management-howto-mutual-certificates.md) przez API Management i można je włączyć w Kubernetes, [instalując kontroler](../aks/ingress-own-tls.md) transferu danych przychodzących (rys. 3). W związku z tym uwierzytelnianie zostanie przeprowadzone w kontrolerze transferu danych przychodzących, co upraszcza mikrousługi. Ponadto adresy IP API Management można dodać do listy dozwolonych przez ruch przychodzący, aby upewnić się, że tylko API Management ma dostęp do klastra.  

 
![Publikowanie za pośrednictwem kontrolera transferu danych przychodzących](./media/api-management-aks/ingress-controller.png)


Formaty
* Łatwa konfiguracja po stronie API Management, ponieważ nie trzeba jej dodawać do sieci wirtualnej klastra, a mTLS jest natywnie obsługiwana
* Scentralizowana ochrona ruchu klastra przychodzącego w warstwie kontrolera transferu danych wejściowych
* Zmniejsza zagrożenie bezpieczeństwa przez zminimalizowanie widocznych publicznie punktów końcowych klastra

Wada
* Zwiększa złożoność konfiguracji klastra z powodu dodatkowej pracy, aby zainstalować, skonfigurować i obsłużyć kontroler transferu danych przychodzących i zarządzać certyfikatami używanymi do mTLS
* Zagrożenie bezpieczeństwa z powodu publicznego wglądu w punkty końcowe kontrolera transferu danych przychodzących


Przy publikowaniu interfejsów API za pomocą API Management, jest to łatwe i powszechne do zabezpieczenia dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, muszą uwzględnić prawidłowy klucz subskrypcji w żądaniach HTTP, gdy wykonują wywołania do tych interfejsów API. W przeciwnym razie wywołania są odrzucane natychmiast przez bramę API Management. Nie są one przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja jest zasadniczo nazwą kontenera dla pary kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, mogą uzyskać subskrypcje. Nie potrzebują oni jednak zatwierdzania od wydawców interfejsu API. Wydawcy interfejsu API mogą również tworzyć subskrypcje bezpośrednio dla użytkowników interfejsu API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opcja 3: wdrażanie APIM wewnątrz sieci wirtualnej klastra

W niektórych przypadkach klienci z ograniczeniami obowiązującymi lub rygorystycznymi wymaganiami dotyczącymi zabezpieczeń mogą znaleźć opcję 1 i 2 nieżywotne rozwiązania ze względu na publicznie uwidocznione punkty końcowe. W innych przypadkach klaster AKS i aplikacje korzystające z mikrousług mogą znajdować się w tej samej sieci wirtualnej, dlatego nie istnieje powód, aby udostępnić klaster publicznie, ponieważ cały ruch interfejsu API pozostanie w sieci wirtualnej. W tych scenariuszach można wdrożyć API Management w sieci wirtualnej klastra. [API Management warstwa Premium](https://aka.ms/apimpricing) obsługuje wdrożenie sieci wirtualnej. 

Istnieją dwa tryby [wdrażania API Management w sieci wirtualnej](./api-management-using-with-vnet.md) — zewnętrzna i wewnętrzna. 

Jeśli odbiorcy interfejsu API nie znajdują się w sieci wirtualnej klastra, należy użyć trybu zewnętrznego (rys. 4). W tym trybie Brama API Management jest wstrzykiwana do sieci wirtualnej klastra, ale jest dostępna z publicznej sieci Internet za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Dzięki temu klienci zewnętrzni mogą korzystać z mikrousług. Ponadto można użyć funkcji sieciowych platformy Azure, takich jak sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby ograniczyć ruch sieciowy.

![Tryb zewnętrznej sieci wirtualnej](./media/api-management-aks/vnet-external.png)

Jeśli wszyscy klienci interfejsu API znajdują się w sieci wirtualnej klastra, można użyć trybu wewnętrznego (rys. 5). W tym trybie Brama API Management jest wstrzykiwana do sieci wirtualnej klastra i dostępna tylko w tej sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Nie ma możliwości nawiązania połączenia z bramą API Management lub klastrem AKS z publicznej sieci Internet. 

![Tryb wewnętrznej sieci wirtualnej](./media/api-management-aks/vnet-internal.png)

 W obu przypadkach klaster AKS nie jest widoczny publicznie. W porównaniu z opcją 2, kontroler transferu danych przychodzących może być niepotrzebny. W zależności od scenariusza i konfiguracji uwierzytelnianie może być nadal wymagane między API Management i mikrousług. Na przykład jeśli zostanie przyjmowana siatka usług, zawsze wymaga ona wzajemnego uwierzytelniania TLS. 

Formaty
* Opcja najbezpieczniejsza, ponieważ klaster AKS nie ma publicznego punktu końcowego
* Upraszcza konfigurację klastra, ponieważ nie ma publicznego punktu końcowego
* Możliwość ukrycia zarówno API Management, jak i AKS wewnątrz sieci wirtualnej przy użyciu trybu wewnętrznego
* Możliwość kontrolowania ruchu sieciowego przy użyciu funkcji sieciowych platformy Azure, takich jak sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)

Wada
* Zwiększa złożoność wdrażania i konfigurowania API Management do pracy wewnątrz sieci wirtualnej

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojęciach sieci dla aplikacji w AKS](../aks/concepts-network.md)
* Dowiedz się więcej [na temat używania API Management z sieciami wirtualnymi](./api-management-using-with-vnet.md)

---
title: Konfiguracja połączeń z maszyn wirtualnych do SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Konfiguracja połączenia z maszyn wirtualnych na potrzeby używania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9caf0a69d2f6caf23edbc35864e1aa560aaf63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673011"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Łączenie maszyn wirtualnych platformy Azure z funkcją HANA — duże wystąpienia

[Co to jest SAP HANA na platformie Azure (duże wystąpienia)?](./hana-overview-architecture.md) Należy zauważyć, że minimalne wdrożenie dużych wystąpień usługi HANA przy użyciu warstwy aplikacji SAP na platformie Azure wygląda następująco:

![Sieć wirtualna Azure podłączona do SAP HANA na platformie Azure (duże wystąpienia) i lokalnie](./media/hana-overview-architecture/image1-architecture.png)

Bliżej sieci wirtualnej platformy Azure, istnieje potrzeba:

- Definicja sieci wirtualnej platformy Azure, w której zamierzasz wdrożyć maszyny wirtualne w warstwie aplikacji SAP.
- Definicja domyślnej podsieci w sieci wirtualnej platformy Azure, która jest rzeczywiście wdrożona na maszynach wirtualnych.
- Utworzona sieć wirtualna platformy Azure musi mieć co najmniej jedną podsieć maszyny wirtualnej i jedną podsieć bramy sieci wirtualnej platformy Azure ExpressRoute. Do tych podsieci należy przypisać zakresy adresów IP określone i omówione w poniższych sekcjach.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Tworzenie sieci wirtualnej platformy Azure dla dużych wystąpień usługi HANA

>[!Note]
>Duże wystąpienia usługi Azure Virtual Network for HANA muszą zostać utworzone za pomocą modelu wdrażania Azure Resource Manager. Starszy model wdrażania platformy Azure, powszechnie znany jako klasyczny model wdrażania, nie jest obsługiwany przez rozwiązanie dużej instancji HANA.

Do utworzenia sieci wirtualnej można użyć Azure Portal, programu PowerShell, szablonu platformy Azure lub interfejsu wiersza polecenia platformy Azure. (Aby uzyskać więcej informacji, zobacz [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)). W poniższym przykładzie pokazano sieć wirtualną, która została utworzona przy użyciu Azure Portal.

W przypadku odwoływania się do **przestrzeni adresowej** w tej dokumentacji do przestrzeni adresowej, która może być używana przez sieć wirtualną platformy Azure. Ta przestrzeń adresowa jest również zakresem adresów wykorzystywanym przez sieć wirtualną do propagacji tras BGP. Tę **przestrzeń adresową** można zobaczyć tutaj:

![Przestrzeń adresowa sieci wirtualnej platformy Azure wyświetlanej w Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

W poprzednim przykładzie z 10.16.0.0/16 usługa Azure Virtual Network uzyskała raczej duży i szeroki zakres adresów IP, który ma być używany. W związku z tym wszystkie zakresy adresów IP kolejnych podsieci w tej sieci wirtualnej mogą mieć swoje zakresy w tej przestrzeni adresowej. Zazwyczaj nie zaleca się używania tego dużego zakresu adresów dla pojedynczej sieci wirtualnej na platformie Azure. Przyjrzyjmy się podsieciom zdefiniowanym w sieci wirtualnej platformy Azure:

![Podsieci sieci wirtualnej platformy Azure i ich zakresy adresów IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Przyjrzyjmy się sieci wirtualnej z pierwszą podsiecią maszyny wirtualnej (nazywaną "domyślną") i podsiecią o nazwie "GatewaySubnet".

W obszarze dwie Poprzednia grafika **przestrzeń adresowa sieci wirtualnej** obejmuje zarówno **zakres adresów IP podsieci maszyny wirtualnej platformy Azure** , jak i bramę sieci wirtualnej.

**Przestrzeń adresów sieci wirtualnej** można ograniczyć do określonych zakresów używanych przez każdą podsieć. Możesz również zdefiniować **przestrzeń adresową sieci** wirtualnej sieci wirtualnej jako wiele konkretnych zakresów, jak pokazano poniżej:

![Przestrzeń adresowa sieci wirtualnej platformy Azure z dwoma spacjami](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

W takim przypadku **przestrzeń adresów sieci wirtualnej** ma zdefiniowane dwie spacje. Są one takie same jak zakresy adresów IP, które są zdefiniowane dla zakresu adresów IP podsieci maszyny wirtualnej platformy Azure i bramy sieci wirtualnej. 

Można użyć dowolnego standardu nazewnictwa dla tych podsieci dzierżawców (podsieci maszyn wirtualnych). Jednak **zawsze musi istnieć jeden i tylko jedna podsieć bramy dla każdej sieci wirtualnej** , która łączy się z SAP HANA na platformie Azure (duże wystąpienia) obwód ExpressRoute. **Ta podsieć bramy musi mieć nazwę "GatewaySubnet"** , aby upewnić się, że brama ExpressRoute jest prawidłowo umieszczona.

> [!WARNING] 
> Należy pamiętać, że podsieć bramy zawsze nosi nazwę "GatewaySubnet".

Można użyć wielu podsieci maszyn wirtualnych i nieciągłych zakresów adresów. Te zakresy adresów muszą być objęte **przestrzenią adresową sieci wirtualnej** sieci wirtualnej. Mogą one być w postaci zagregowanej. Mogą również znajdować się na liście dokładnych zakresów podsieci maszyn wirtualnych i podsieci bramy.

Poniżej znajduje się podsumowanie ważnych faktów dotyczących sieci wirtualnej platformy Azure, które łączą się z dużymi wystąpieniami HANA:

- **Przestrzeń adresową sieci wirtualnej** należy przesłać do firmy Microsoft, gdy wykonywane jest początkowe wdrożenie dużych wystąpień platformy Hana. 
- **Przestrzeń adresowa sieci wirtualnej** może być jednym z większych zakresów obejmujących zakresy dla zakresu adresów IP podsieci maszyny wirtualnej platformy Azure i bramy sieci wirtualnej.
- Można też przesłać wiele zakresów, które obejmują różne zakresy adresów IP w zakresach adresów IP podsieci maszyn wirtualnych i zakres adresów IP bramy sieci wirtualnej.
- Zdefiniowana **przestrzeń adresowa sieci wirtualnej** jest używana na potrzeby propagacji routingu BGP.
- Nazwa podsieci bramy musi być: **"GatewaySubnet"**.
- Przestrzeń adresowa jest używana jako filtr na stronie duże wystąpienie HANA, aby zezwalać na ruch do jednostek dużych wystąpień usługi HANA lub go zabronić z poziomu platformy Azure. Informacje o routingu BGP sieci wirtualnej platformy Azure i zakresów adresów IP, które są skonfigurowane do filtrowania na stronie dużego wystąpienia HANA, powinny być zgodne. W przeciwnym razie mogą wystąpić problemy z łącznością.
- Poniżej znajdują się pewne szczegółowe informacje o podsieci bramy, które zostały omówione później, w sekcji **łączenie sieci wirtualnej z usługą Hana duże wystąpienie ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Różne zakresy adresów IP, które mają być zdefiniowane 

Niektóre z zakresów adresów IP, które są niezbędne do wdrożenia dużych wystąpień usługi HANA, zostały już wprowadzone. Ale istnieją również więcej zakresów adresów IP, które są ważne. Nie wszystkie z następujących zakresów adresów IP należy przesłać do firmy Microsoft. Należy jednak zdefiniować je przed wysłaniem żądania wstępnego wdrożenia:

- **Przestrzeń adresów sieci wirtualnej**: **przestrzeń adresowa sieci wirtualnej** to zakresy adresów IP, które można przypisać do parametru przestrzeni adresów w sieciach wirtualnych platformy Azure. Te sieci nawiązują połączenie z środowiskiem SAP HANA dużym wystąpieniem. Zaleca się, aby ten parametr przestrzeni adresowej miał wartość wielowierszową. Powinien on zawierać zakres podsieci maszyny wirtualnej platformy Azure i zakres podsieci bramy platformy Azure. Ten zakres podsieci został pokazany w poprzedniej grafice. NIE może nakładać się na pulę adresów IP w sieci lokalnej lub na serwerze ani na zakresy adresu ER-P2P. Jak można uzyskać te zakresy adresów IP? Zespół sieci firmowej lub dostawca usług powinien udostępnić jeden lub wiele zakresów adresów IP, które nie są używane w sieci. Na przykład podsieć maszyny wirtualnej platformy Azure to 10.0.1.0/24, a podsieć bramy platformy Azure to 10.0.2.0/28.  Zalecamy, aby przestrzeń adresowa sieci wirtualnej platformy Azure została zdefiniowana jako: 10.0.1.0/24 i 10.0.2.0/28. Chociaż wartości przestrzeni adresowej można agregować, zalecamy ich dopasowanie do zakresów podsieci. W ten sposób można przypadkowo uniknąć używania nieużywanych zakresów adresów IP w większych przestrzeniach adresowych w sieci. **Przestrzeń adresów sieci wirtualnej jest zakresem adresów IP. Należy przesłać do firmy Microsoft, gdy zostanie poproszony o wstępne wdrożenie**.
- **Zakres adresów IP podsieci maszyny wirtualnej platformy Azure:** Ten zakres adresów IP jest przypisany do parametru podsieci sieci wirtualnej platformy Azure. Ten parametr znajduje się w sieci wirtualnej platformy Azure i łączy się z środowiskiem SAP HANA dużym wystąpieniem. Ten zakres adresów IP służy do przypisywania adresów IP do maszyn wirtualnych platformy Azure. Adresy IP z tego zakresu mogą nawiązywać połączenia z serwerami SAP HANA dużymi wystąpieniami. W razie konieczności można użyć wielu podsieci maszyn wirtualnych platformy Azure. Zalecamy użycie bloku CIDR/24 dla każdej podsieci maszyny wirtualnej platformy Azure. Ten zakres adresów musi być częścią wartości, które są używane w przestrzeni adresowej sieci wirtualnej platformy Azure. Jak uzyskać ten zakres adresów IP? Zespół sieci firmowej lub dostawca usług powinien podać zakres adresów IP, który nie jest używany w sieci.
- **Zakres adresów IP podsieci bramy sieci wirtualnej:** W zależności od funkcji, których planujesz użyć, zalecany rozmiar to:
   - Brama usługi ExpressRoute o najwyższej wydajności:/26 blok adresów — wymagany dla klasy SKU typu II.
   - Współistnienie z sieciami VPN i ExpressRoute przy użyciu bramy sieci wirtualnej ExpressRoute o wysokiej wydajności (lub mniejszej):/27 bloku adresów.
   - Wszystkie inne sytuacje:/28 blok adresów. Ten zakres adresów musi być częścią wartości używanych w wartości "przestrzeń adresów sieci wirtualnej". Ten zakres adresów musi być częścią wartości, które są używane w wartościach przestrzeni adresowej sieci wirtualnej platformy Azure przesyłanych do firmy Microsoft. Jak uzyskać ten zakres adresów IP? Zespół sieci firmowej lub dostawca usług powinien podać zakres adresów IP, który nie jest obecnie używany w sieci. 
- **Zakres adresów dla łączności er-P2P:** Ten zakres jest zakresem adresów IP dla połączenia P2P ExpressRoute SAP HANA z dużym wystąpieniem (ER). Ten zakres adresów IP musi być zakresem adresów IP/29 CIDR. Ten zakres nie może nakładać się na lokalne lub inne zakresy adresów IP platformy Azure. Ten zakres adresów IP służy do konfigurowania łączności ER z bramy wirtualnej ExpressRoute na serwerach z dużymi wystąpieniami SAP HANA. Jak uzyskać ten zakres adresów IP? Zespół sieci firmowej lub dostawca usług powinien podać zakres adresów IP, który nie jest obecnie używany w sieci. **Ten zakres jest zakresem adresów IP. Należy przesłać do firmy Microsoft, gdy zostanie poproszony o wstępne wdrożenie**.  
- **Zakres adresów puli IP serwera:** Ten zakres adresów IP służy do przypisywania indywidualnego adresu IP do serwerów z dużymi wystąpieniami platformy HANA. Zalecany rozmiar podsieci to blok CIDR/24. Jeśli jest to konieczne, może być mniejsze, z maksymalnie 64 adresami IP. Z tego zakresu pierwsze 30 adresów IP są zastrzeżone do użytku przez firmę Microsoft. Upewnij się, że w przypadku wybrania rozmiaru zakresu należy uwzględnić ten fakt. Ten zakres nie może nakładać się na lokalne lub inne adresy IP platformy Azure. Jak uzyskać ten zakres adresów IP? Zespół sieci firmowej lub dostawca usług powinien podać zakres adresów IP, który nie jest obecnie używany w sieci.  **Ten zakres jest zakresem adresów IP, który musi zostać przesłany do firmy Microsoft w przypadku pytania wstępnego wdrożenia**.

Opcjonalne zakresy adresów IP, które ostatecznie muszą zostać przesłane do firmy Microsoft:

- Jeśli zdecydujesz się używać [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md) do włączenia bezpośredniego routingu z jednostek lokalnych do platformy Hana, musisz zarezerwować inny zakres adresów IP. Ten zakres nie może pokrywać się z żadnym innym zakresem adresów IP zdefiniowanym wcześniej.
- Jeśli zdecydujesz się używać [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md) , aby włączyć kierowanie bezpośrednie z dzierżawy dużego wystąpienia Hana w jednym regionie platformy Azure do innej dzierżawy dużego wystąpienia Hana w innym regionie świadczenia usługi Azure, musisz zarezerwować inny zakres adresów IP. Ten zakres nie może pokrywać się z żadnym innym zakresem adresów IP zdefiniowanym wcześniej.

Aby uzyskać więcej informacji na temat ExpressRoute Global Reach i użycia wokół dużych wystąpień platformy HANA, zapoznaj się z dokumentami:

- [Architektura sieci SAP HANA (duże wystąpienia)](./hana-network-architecture.md)
- [Łączenie sieci wirtualnej z dużymi wystąpieniami platformy HANA](./hana-connect-vnet-express-route.md)
 
Należy zdefiniować i zaplanować zakresy adresów IP, które zostały opisane wcześniej. Nie trzeba jednak przesyłać wszystkich z nich do firmy Microsoft. Zakresy adresów IP wymagane jako nazwa firmy Microsoft to:

- Przestrzenie adresów sieci wirtualnej platformy Azure
- Zakres adresów dla łączności ER-P2P
- Zakres adresów pul adresów IP serwera

W przypadku dodawania dodatkowych sieci wirtualnych, które muszą łączyć się z dużymi wystąpieniami HANA, należy przesłać nową przestrzeń adresową sieci wirtualnej platformy Azure, która jest dodawana do firmy Microsoft. 

Poniżej znajduje się przykład różnych zakresów i niektórych przykładowych zakresów, które należy skonfigurować i ostatecznie udostępnić firmie Microsoft. Wartość przestrzeni adresowej sieci wirtualnej platformy Azure nie jest agregowana w pierwszym przykładzie. Jest to jednak zdefiniowane z zakresów pierwszego zakresu adresów IP podsieci maszyn wirtualnych platformy Azure i zakresu adresów IP podsieci bramy sieci wirtualnej. 

Podczas konfigurowania i przesyłania dodatkowych zakresów adresów IP dodatkowych podsieci maszyn wirtualnych w ramach przestrzeni adresów sieci wirtualnej platformy Azure można użyć wielu podsieci maszyn wirtualnych w ramach sieci wirtualnej platformy Azure.

![Zakresy adresów IP wymagane w SAP HANA na platformie Azure (duże wystąpienia) minimalnego wdrożenia](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Ilustracja nie pokazuje dodatkowych zakresów adresów IP, które są wymagane do zastosowania opcjonalnego ExpressRoute Global Reach.

Możesz również agregować dane przesyłane do firmy Microsoft. W takim przypadku przestrzeń adresowa sieci wirtualnej platformy Azure zawiera tylko jedno miejsce. Korzystając z zakresów adresów IP z wcześniejszego przykładu, zagregowana przestrzeń adresowa sieci wirtualnej może wyglądać jak na poniższej ilustracji:

![Druga możliwość korzystania z zakresów adresów IP wymaganych w SAP HANA na platformie Azure (duże wystąpienia) minimalnego wdrożenia](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

W przykładzie, zamiast dwóch mniejszych zakresów, które definiuje przestrzeń adresową sieci wirtualnej platformy Azure, mamy jeden większy zakres obejmujący 4096 adresów IP. Taka duża definicja przestrzeni adresowej pozostawia niektóre raczej nieużywane zakresy. Ponieważ wartości przestrzeni adresów sieci wirtualnej są używane do propagacji tras BGP, użycie nieużywanych zakresów lokalnie lub w innym miejscu w sieci może powodować problemy z routingiem. Ilustracja nie pokazuje dodatkowych zakresów adresów IP, które są wymagane do zastosowania opcjonalnego ExpressRoute Global Reach.

Zalecamy ścisłe wyrównanie przestrzeni adresowej przy użyciu rzeczywistej przestrzeni adresowej podsieci. W razie potrzeby bez ponoszenia przestojów w sieci wirtualnej zawsze możesz dodać nowe wartości przestrzeni adresowej później.
 
> [!IMPORTANT] 
> Każdy zakres adresów IP w aplikacji ER-P2P, Pula adresów IP serwera i przestrzeń adresów sieci wirtualnej platformy Azure **nie** mogą nakładać się na siebie ani z żadnym innym zakresem używanym w sieci. Każdy z nich musi być dyskretny. Jak pokazuje dwie poprzednie grafiki, również nie mogą być podsiecią innego zakresu. Jeśli nastąpi nakładanie się między zakresami, Sieć wirtualna platformy Azure może nie połączyć się z obwodem usługi ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Następne kroki po zdefiniowaniu zakresów adresów
Po zdefiniowaniu zakresów adresów IP należy wykonać następujące czynności:

1. Prześlij zakresy adresów IP dla przestrzeni adresowej sieci wirtualnej platformy Azure, połączenia ER-P2P i zakresu adresów IP puli, a także inne dane, które zostały wymienione na początku dokumentu. W tym momencie można również rozpocząć tworzenie sieci wirtualnej i podsieci maszyny wirtualnej. 
2. Obwód usługi ExpressRoute jest tworzony przez firmę Microsoft między subskrypcją platformy Azure i sygnaturą dużego wystąpienia HANA.
3. Sieć dzierżawców jest tworzona w sygnaturze dużej instancji przez firmę Microsoft.
4. Firma Microsoft konfiguruje sieć w SAP HANA na platformie Azure (duże wystąpienia), aby akceptować adresy IP z przestrzeni adresowej sieci wirtualnej platformy Azure, która komunikuje się z dużymi wystąpieniami HANA.
5. W SAP HANA zależności od zakupionej jednostki SKU na platformie Azure (duże wystąpienia) firma Microsoft przypisuje jednostkę obliczeniową w sieci dzierżawców. Przypisuje także i instaluje magazyn oraz zainstaluje system operacyjny (SUSE lub Red Hat Linux). Adresy IP dla tych jednostek są pobierane z zakresu adresów puli IP serwera, który został przesłany do firmy Microsoft.

Po zakończeniu procesu wdrażania firma Microsoft udostępnia następujące dane:
- Informacje, które są konieczne do łączenia sieci wirtualnych platformy Azure z obwodem usługi ExpressRoute, który łączy sieci wirtualne platformy Azure z dużymi wystąpieniami HANA:
     - Klucze autoryzacji
     - ExpressRoute PeerID
- Dane umożliwiające dostęp do dużych wystąpień platformy HANA po ustanowieniu obwodu usługi ExpressRoute i sieci wirtualnej platformy Azure.

Możesz również znaleźć sekwencję połączeń dużych wystąpień HANA w dokumencie [SAP HANA na platformie Azure (duże wystąpienia)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Wiele z poniższych kroków przedstawiono w przykładowym wdrożeniu w tym dokumencie. 

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [łączenie sieci wirtualnej z dużym wystąpieniem platformy Hana ExpressRoute](hana-connect-vnet-express-route.md).

---
title: Omówienie autonomicznych klastrów Service Fabric
description: Klastry Service Fabric są uruchamiane w systemach Windows Server i Linux, co oznacza, że będzie można wdrażać i hostować aplikacje Service Fabric wszędzie tam, gdzie będzie można uruchamiać system Windows Server lub Linux.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684002"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Omówienie Service Fabric klastrów autonomicznych

Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem klastra. Klastry mogą być skalowane do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, Service Fabric ponownie zrównoważą repliki partycji usługi i wystąpienia w większej liczbie węzłów. Ogólna wydajność aplikacji zwiększa się i rywalizacja o zmniejszenie ilości pamięci. Jeśli węzły w klastrze nie są efektywnie używane, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie zrównoważy repliki partycji i wystąpienia na zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu węzłów w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu.

Proces tworzenia klastra Service Fabric lokalnie jest podobny do procesu tworzenia klastra w dowolnej wybranej chmurze z zestawem maszyn wirtualnych. Początkowe kroki w celu aprowizacji maszyn wirtualnych podlegają dostawcy usług w chmurze lub środowisku lokalnym, z którego korzystasz. Po włączeniu zestawu maszyn wirtualnych z włączoną łącznością sieciową między nimi należy wykonać kroki konfigurowania pakietu Service Fabric, edytowania ustawień klastra i uruchamiania skryptów tworzenia klastra i zarządzania nimi. Zapewnia to, że wiedza i doświadczenie związane z działaniem i zarządzaniem klastrami Service Fabric jest możliwe do przetransferowania w przypadku wybrania opcji ukierunkowanych na nowe środowiska hostingu.

## <a name="cluster-security"></a>Zabezpieczenia klastra

Klaster Service Fabric jest posiadanym zasobem.  Użytkownik jest odpowiedzialny za zabezpieczanie klastrów w celu zapobiegania łączeniu się z nimi nieautoryzowanych użytkowników. Bezpieczny klaster jest szczególnie ważny w przypadku uruchamiania obciążeń produkcyjnych w klastrze.

> [!NOTE]
> Uwierzytelnianie systemu Windows jest oparte na protokole Kerberos. Uwierzytelnianie NTLM nie jest obsługiwane jako typ uwierzytelniania.
>
> Za każdym razem, gdy to możliwe, Użyj uwierzytelniania certyfikatu X. 509 dla klastrów Service Fabric.

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami

Zabezpieczenia między węzłami zabezpieczają komunikację między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń zapewnia, że tylko komputery autoryzowane do dołączenia do klastra mogą uczestniczyć w aplikacjach i usługach obsługujących klaster. Service Fabric używa certyfikatów X. 509 w celu zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji.  Certyfikat klastra jest wymagany do zabezpieczenia ruchu klastra i zapewnienia uwierzytelniania klastra i serwera.  Do klastrów testowych można używać certyfikatów z podpisem własnym, ale do zabezpieczania klastrów produkcyjnych należy używać certyfikatu z zaufanego urzędu certyfikacji.

Zabezpieczenia systemu Windows można również włączyć dla autonomicznego klastra systemu Windows. Jeśli masz systemy Windows Server 2012 R2 i Windows Active Directory, zalecamy użycie zabezpieczeń systemu Windows z kontami usług zarządzanymi przez grupę. W przeciwnym razie Użyj zabezpieczeń systemu Windows z kontami systemu Windows.

Aby uzyskać więcej informacji, zapoznaj się z tematem [zabezpieczenia między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpieczenia między klientem a węzłem

Zabezpieczenia klienta w węźle uwierzytelniają klientów i pomagają w zabezpieczaniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mają dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani przy użyciu swoich poświadczeń zabezpieczeń certyfikatu X. 509. Dowolna liczba opcjonalnych certyfikatów klienta może służyć do uwierzytelniania klientów administratorów lub użytkowników z klastrem.

Oprócz certyfikatów klienta Azure Active Directory można również skonfigurować tak, aby uwierzytelniać klientów w klastrze.

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zabezpieczenia klienta-węzła](service-fabric-cluster-security.md#client-to-node-security)

### <a name="service-fabric-role-based-access-control"></a>Service Fabric kontroli dostępu opartej na rolach
Service Fabric obsługuje również kontrolę dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Dzięki temu klaster jest bezpieczniejszy. Obsługiwane są dwa typy kontroli dostępu dla klientów łączących się z klastrem: rola administratora i rola użytkownika.  

Aby uzyskać więcej informacji, Przeczytaj [Service Fabric kontroli dostępu opartej na rolach](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

## <a name="scaling"></a>Skalowanie

Wymagania aplikacji zmieniają się w miarę upływu czasu. Może być konieczne zwiększenie zasobów klastra w celu spełnienia zwiększonych obciążeń aplikacji lub ruchu sieciowego albo zmniejszenie zasobów klastra po spadku popytu. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie (zmienić liczbę węzłów) lub w pionie (zmienić zasoby węzłów). Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze. W miarę skalowania klastra aplikacje są automatycznie skalowane.

Aby uzyskać więcej informacji, zobacz [skalowanie klastrów autonomicznych](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uaktualnianie

Autonomiczny klaster jest zasobem, który jesteś całkowicie własnym. Użytkownik jest odpowiedzialny za stosowanie poprawek do podstawowego systemu operacyjnego i Inicjowanie uaktualnień sieci szkieletowej. Można ustawić, aby klaster otrzymywał aktualizacje automatycznego środowiska uruchomieniowego, gdy firma Microsoft wyprowadzi nową wersję, lub wybrać wybraną obsługiwaną wersję środowiska uruchomieniowego. Oprócz uaktualnień sieci szkieletowej można również zastosować poprawki do systemu operacyjnego i zaktualizować konfigurację klastra, na przykład certyfikaty lub porty aplikacji. 

Aby uzyskać więcej informacji, przeczytaj temat [uaktualnianie klastrów autonomicznych](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Możesz tworzyć klastry na maszynach wirtualnych lub komputerach z tymi systemami operacyjnymi (system Linux nie jest jeszcze obsługiwany):

* Windows Server 2012 z dodatkiem R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat [zabezpieczania](service-fabric-cluster-security.md), [skalowania](service-fabric-cluster-scaling-standalone.md)i [uaktualniania](service-fabric-cluster-upgrade-standalone.md) klastrów autonomicznych.

Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).

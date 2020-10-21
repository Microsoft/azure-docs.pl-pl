---
title: Dowiedz się więcej o opcjach pomocy technicznej dla usługi Azure Service Fabric
description: Obsługiwane wersje klastra platformy Azure Service Fabric i linki do biletów pomocy technicznej dotyczącej plików
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316498"
---
# <a name="azure-service-fabric-support-options"></a>Opcje pomocy technicznej platformy Azure Service Fabric

Utworzyliśmy wiele opcji żądania pomocy technicznej, które zaspokoją potrzeby zarządzania klastrami Service Fabric i obciążeniami aplikacji. W zależności od pilności potrzebnej pomocy technicznej i ważności problemu możesz wybrać opcję, która jest odpowiednia dla Ciebie.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Zgłoś problemy produkcyjne lub zażądaj płatnej pomocy technicznej dla platformy Azure

Aby zgłosić problemy związane z klastrem Service Fabric uruchomionym na platformie Azure, Otwórz bilet pomocy technicznej [w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) lub [portalu pomocy technicznej firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej na następujące tematy:
 
- [Pomoc techniczna firmy Microsoft dla platformy Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Klastry działające w ramach warstwy niezawodności Bronów lub klastra z jednym węzłem będą umożliwiały uruchamianie obciążeń testowych. Jeśli występują problemy z klastrem uruchomionym w usłudze Bronze lub klastrze z jednym węzłem, zespół pomocy technicznej firmy Microsoft pomoże Ci uniknąć problemu, ale nie będzie wykonywał analizy głównej przyczyny. Aby uzyskać więcej informacji, zapoznaj się z [charakterystyką niezawodności klastra](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Aby uzyskać więcej informacji o tym, co jest wymagane w przypadku klastra gotowego do produkcji, zapoznaj się z [listą kontrolną gotowości produkcyjnej](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Zgłoś problemy produkcyjne lub zażądaj płatnej pomocy technicznej dla autonomicznych klastrów Service Fabric

Aby zgłosić problemy związane z klastrami Service Fabric działającymi lokalnie lub w innych chmurach, możesz otworzyć bilet dla profesjonalnej pomocy technicznej w [portalu pomocy technicznej firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Dowiedz się więcej na następujące tematy:

- [Profesjonalne wsparcie firmy Microsoft dla firm lokalnych](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Zgłoś problemy dotyczące Service Fabric platformy Azure

Skonfigurowano repozytorium GitHub na potrzeby raportowania Service Fabric problemów.  Aktywnie monitorujemy również następujące fora.

### <a name="github-repo"></a>Repozytorium GitHub 

Zgłoś problemy dotyczące Service Fabric platformy Azure w [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues). To repozytorium jest przeznaczone do raportowania i śledzenia problemów oraz do tworzenia małych żądań funkcji związanych z usługą Azure Service Fabric. **Nie używaj tego nośnika do zgłaszania problemów z witryną na żywo**.

### <a name="stackoverflow-and-msdn-forums"></a>Fora StackOverflow i MSDN

[Tag Service Fabric na StackOverflow][stackoverflow] i [forum Service Fabric w witrynie MSDN][msdn-forum] są najlepiej używane do zadawania pytań ogólnych dotyczących działania platformy i sposobu korzystania z nich w celu wykonywania określonych zadań.

### <a name="azure-feedback-forum"></a>Forum opinii na platformie Azure

[Forum opinii na platformie Azure dla Service Fabric][uservoice-forum] jest najlepszym miejscem do przesyłania znaczących pomysłów dotyczących funkcji produktu. Przeglądamy najpopularniejsze żądania i ich czynniki są przeznaczone do długoterminowego planowania. Zachęcamy do Rally pomocy technicznej dla swoich sugestii w społeczności.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Wersje zapoznawcze Service Fabric — nieobsługiwane do użycia w środowisku produkcyjnym

Czasami oferujemy specjalne wersje zapoznawcze zawierające znaczące zmiany funkcji, dla których chcemy przebadać wczesną opinię. Wersji zapoznawczej należy używać tylko w środowiskach izolowanych testów, które nie obsługują obciążeń produkcyjnych. Klaster produkcyjny powinien mieć zawsze uruchomioną obsługiwaną, stabilną wersję Service Fabric. Nie oferujemy opcji płatnej pomocy technicznej dla tej wersji zapoznawczej.

Wersja zapoznawcza zawsze rozpoczyna się od wersji głównej i pomocniczej o numerze 255. Jeśli na przykład zostanie wyświetlona Service Fabric wersja 255.255.5703.949, ta wersja jest w wersji zapoznawczej i jest przeznaczona tylko do użycia w klastrach testowych. Te wersje zapoznawcze są również ogłoszone na [blogu zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) i zawiera szczegółowe informacje dotyczące funkcji uwzględnionych w tym obszarze. Skorzystaj z jednej z opcji wymienionych w sekcji [Zgłoś problemy Service Fabric platformy Azure](#report-azure-service-fabric-issues) , aby zadać pytania lub przekazać opinię.

## <a name="next-steps"></a>Następne kroki

[Obsługiwane Service Fabric wersje](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
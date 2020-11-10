---
title: Lista kontrolna zabezpieczeń operacyjnych platformy Azure | Microsoft Docs
description: Zapoznaj się z tą listą kontrolną, aby pomóc przedsiębiorstwom w rozważaniu zagadnień związanych z zabezpieczeniami operacyjnymi
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 0a5a82cecdb5571d10bbb9e0f38c3645483e7402
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413279"
---
# <a name="azure-operational-security-checklist"></a>Lista kontrolna zabezpieczeń operacyjnych platformy Azure
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed wdrożeniem aplikacji w chmurze w środowisku produkcyjnym warto wyświetlić listę kontrolną, aby pomóc w ocenie aplikacji w oparciu o listę najważniejszych i zalecanych działań w zakresie zabezpieczeń, które należy wziąć pod uwagę.

## <a name="introduction"></a>Wprowadzenie

Platforma Azure udostępnia pakiet usług infrastruktury, za pomocą którego można wdrażać aplikacje. Zabezpieczenia operacyjne platformy Azure odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów w Microsoft Azure.

-   Aby uzyskać maksymalną korzyść z platformy w chmurze, zalecamy skorzystanie z usług platformy Azure i wykonanie listy kontrolnej.
-   Organizacje, które zainwestowają czas i zasoby w celu oceny gotowości do działania aplikacji przed ich uruchomieniem, mają znacznie wyższą stopę zadowolenia niż te, które nie są. Podczas wykonywania tej czynności listy kontrolne mogą być niecennym mechanizmem, aby zapewnić spójność i całościową ocenę aplikacji.
-   Poziom oceny operacyjnej różni się w zależności od poziomu okresu zapadalności w chmurze i fazy rozwoju aplikacji, potrzeb dotyczących dostępności i wymagań dotyczących poufności danych.

## <a name="checklist"></a>Lista kontrolna

Ta lista kontrolna ma pomóc przedsiębiorstwom w różnych kwestiach związanych z bezpieczeństwem, które wdrażają zaawansowane aplikacje dla przedsiębiorstw na platformie Azure. Może również służyć do tworzenia bezpiecznej migracji w chmurze i strategii operacji dla organizacji.

|Kategoria listy kontrolnej| Opis|
| ------------ | -------- |
| [<br>Role zabezpieczeń & kontroli dostępu](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) można zapewnić specyficzny dla użytkownika sposób przypisywania uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie.</li></ul> |
| [<br>Magazyn & zbierania danych](../../storage/blobs/security-recommendations.md)|<ul><li>Aby zabezpieczyć konto magazynu przy użyciu [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../../role-based-access-control/role-assignments-portal.md), Użyj zabezpieczeń płaszczyzny zarządzania.</li><li>Zabezpieczenia płaszczyzny danych w celu zabezpieczenia dostępu do danych za pomocą [sygnatur dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md) i przechowywanych zasad dostępu.</li><li>Użyj szyfrowania Transport-Level — przy użyciu protokołu HTTPS i szyfrowania używanego przez protokół [SMB (protokołu bloku komunikatów serwera) 3,0](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) dla [udziałów plików platformy Azure](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>[Szyfrowanie po stronie klienta](../../storage/common/storage-client-side-encryption.md) służy do zabezpieczania danych wysyłanych do kont magazynu, gdy wymagana jest wyłącznie kontrola nad kluczami szyfrowania. </li><li>Użyj [szyfrowanie usługi Storage (SSE)](../../storage/common/storage-service-encryption.md)  , aby automatycznie szyfrować dane w usłudze Azure Storage, a [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) do szyfrowania plików dysków maszyny wirtualnej dla systemu operacyjnego i dysków danych.</li><li>Użyj usługi Azure [analityka magazynu](/rest/api/storageservices/storage-analytics) do monitorowania typu autoryzacji; Podobnie jak w przypadku Blob Storage, można zobaczyć, czy użytkownicy użyły sygnatury dostępu współdzielonego lub kluczy konta magazynu.</li><li>Użyj funkcji [udostępniania zasobów między źródłami (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) , aby uzyskać dostęp do zasobów magazynu z różnych domen.</li></ul> |
|[<br>Zasady zabezpieczeń & zalecenia](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Użyj [Azure Security Center](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-) , aby wdrożyć rozwiązania punktów końcowych.</li><li>Dodaj [zaporę aplikacji sieci Web (WAF)](../../web-application-firewall/ag/ag-overview.md) , aby zabezpieczyć aplikacje sieci Web.</li><li>  Aby zwiększyć ochronę zabezpieczeń, należy użyć [zapory](../../sentinel/connect-data-sources.md) od partnera firmy Microsoft. </li><li>Zastosuj szczegóły dotyczące kontaktu z zabezpieczeniami dla subskrypcji platformy Azure; to [centrum Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) kontaktuje się z nim w przypadku wykrycia, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.</li></ul> |
| [<br>Zarządzanie dostępem & tożsamości](identity-management-best-practices.md)|<ul><li>[Zsynchronizuj katalog lokalny z katalogiem w chmurze za pomocą usługi Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Użyj [logowania jednokrotnego](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) , aby umożliwić użytkownikom dostęp do swoich aplikacji SaaS w oparciu o konta organizacyjne w usłudze Azure AD.</li><li>Raport dotyczący [działania rejestracji resetowania haseł](../../active-directory/authentication/howto-sspr-reporting.md) służy do monitorowania użytkowników, którzy rejestrują rejestrację.</li><li>Włącz [uwierzytelnianie wieloskładnikowe (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) dla użytkowników.</li><li>Deweloperzy mogą korzystać z funkcji bezpiecznej tożsamości dla aplikacji, takich jak [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Aktywnie Monitoruj dla podejrzanych działań, korzystając z raportów anomalii Azure AD — wersja Premium i [możliwości usługi Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).</li></ul> |
|[<br>Bieżące monitorowanie zabezpieczeń](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Użyj [dzienników Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) malware Assessment, aby zgłosić stan ochrony przed złośliwym kodem w infrastrukturze.</li><li>Użyj [oceny aktualizacji](../../automation/update-management/overview.md) , aby określić ogólny wpływ na potencjalne problemy z zabezpieczeniami, oraz określić, czy krytyczne aktualizacje mają być używane w danym środowisku.</li><li>[Tożsamość i dostęp](../../security-center/security-center-monitoring.md) udostępniają użytkownikowi przegląd </li><ul><li>stan tożsamości użytkownika,</li><li>liczba nieudanych prób zalogowania,</li><li>    konto użytkownika, które było używane podczas tych prób, konta, które zostały zablokowane</li> <li>konta ze zmienionym lub resetowanym hasłem </li><li>Bieżąca liczba kont, które zostały zalogowane.</li></ul></ul> |
| [<br>Funkcje wykrywania usługi Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Korzystaj z [możliwości wykrywania](../../security-center/security-center-alerts-overview.md#detect-threats), aby identyfikować aktywne zagrożenia dla zasobów Microsoft Azure.</li><li>Korzystaj ze [zintegrowanej analizy zagrożeń](/archive/blogs/azuresecurity/get-threat-intelligence-reports-with-azure-security-center) , która szuka znanych nieprawidłowych aktorów, wykorzystując globalne analizy zagrożeń z produktów i usług firmy Microsoft, [jednostkę Microsoft Digital zbrodnis (DCU)](https://www.microsoft.com/trustcenter/security/cybercrime), [Microsoft Security Response Center (MSRC)](https://www.microsoft.com/msrc?rtc=1)i zewnętrzne źródła danych.</li><li>Użyj [analizy behawioralnej](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) , która stosuje znane wzorce w celu odnalezienia złośliwego zachowania. </li><li>Użyj [wykrywania anomalii](/azure/machine-learning/studio-module-reference/anomaly-detection) , które używa profilowania statystycznego do tworzenia historycznej linii bazowej.</li></ul> |
| [<br>Operacje dla deweloperów (DevOps)](/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktura jako kod (IaC)](../../azure-resource-manager/templates/template-syntax.md) to rozwiązanie, które umożliwia automatyzację i sprawdzanie poprawności tworzenia i usuwania sieci oraz maszyn wirtualnych w celu ułatwienia dostarczania bezpiecznych, stabilnych platform hostingu aplikacji.</li><li>[Ciągła integracja i wdrażanie](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) polegające na trwającym scalaniu i testowaniu kodu, który prowadzi do wczesnego znajdowania wad. </li><li>[Release Management](/azure/devops/pipelines/overview?viewFallbackFrom=azure-devops) Zarządzaj zautomatyzowanymi wdrożeniami przy użyciu poszczególnych etapów potoku.</li><li>[Monitorowanie wydajności aplikacji](../../azure-monitor/app/asp-net.md) dla uruchomionych aplikacji, w tym środowisk produkcyjnych w celu zapewnienia kondycji aplikacji i korzystania z klientów, tworzy hipotezę i szybko weryfikuje lub odrzucanie strategie.</li><li>Przy użyciu [testowania obciążenia & automatyczne skalowanie](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) możemy znaleźć problemy z wydajnością w naszej aplikacji, aby zwiększyć jakość wdrożenia i upewnić się, że aplikacja jest zawsze dostępna do potrzeb związanych z działalnością.</li></ul> |


## <a name="conclusion"></a>Podsumowanie
Wiele organizacji pomyślnie wdrożyła i uruchomiła swoje aplikacje w chmurze na platformie Azure. Listy kontrolne mają wyróżnione kilka list kontrolnych, które są niezbędne, i pomagają zwiększyć prawdopodobieństwo pomyślnego wdrożenia i frustrację operacji. Zdecydowanie zalecamy te zagadnienia operacyjne i strategiczne dla istniejących i nowych wdrożeń aplikacji na platformie Azure.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zabezpieczeniach, zobacz następujące artykuły:

- [Bezpieczeństwo projektowania i działania](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Azure Security Center planowanie i operacje](../../security-center/security-center-planning-and-operations-guide.md).
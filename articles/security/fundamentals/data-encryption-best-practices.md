---
title: Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń i szyfrowania danych przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: 1b6fcf38f9f69976e6ed8d64040cfbcf44f090e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85124055"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure
W tym artykule opisano najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania danych.

Najlepsze rozwiązania są oparte na konsensusie opinii i współpracują z bieżącymi funkcjami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się wraz z upływem czasu, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="protect-data"></a>Ochrona danych
Aby chronić dane w chmurze, należy uwzględnić możliwe stany, w których mogą wystąpić dane, oraz informacje o kontrolkach dostępnych dla tego stanu. Najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania danych platformy Azure odnoszą się do następujących stanów danych:

- W spoczynku: obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które istnieją statycznie na nośniku fizycznym, niezależnie od tego, czy dysk magnetyczny czy optyczny.
- Podczas przesyłania: gdy dane są przesyłane między składnikami, lokalizacjami lub programami, jest w trakcie przesyłania. Przykłady są przesyłane przez sieć, przez magistralę usług (od lokalnego do chmury i na odwrót, łącznie z połączeniami hybrydowymi, takimi jak ExpressRoute), lub w procesie wejścia/wyjścia.

## <a name="choose-a-key-management-solution"></a>Wybierz rozwiązanie do zarządzania kluczami

Ochrona kluczy jest istotna dla ochrony danych w chmurze.

Usługa [Azure Key Vault](/azure/key-vault/key-vault-overview) ułatwia ochronę kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi w chmurze. Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą w klika minut utworzyć klucze do programowania i testowania, a następnie przeprowadzić ich migrację do kluczy produkcyjnych. W razie potrzeby administratorzy zabezpieczeń mogą przydzielić (i cofnąć) uprawnienia do używania kluczy.

Za pomocą Key Vault można utworzyć wiele bezpiecznych kontenerów o nazwie magazyny. Te magazyny są obsługiwane przez sprzętowych modułów zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny kluczy umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Azure Key Vault może obsługiwać żądania i odnawianie certyfikatów Transport Layer Security (TLS). Udostępnia ona funkcje niezawodnego rozwiązania do zarządzania cyklem życia certyfikatów.

Azure Key Vault jest zaprojektowana do obsługi kluczy i wpisów tajnych aplikacji. Key Vault nie jest przeznaczona do przechowywania haseł użytkowników.

Poniżej przedstawiono najlepsze rozwiązania w zakresie zabezpieczeń dotyczące korzystania z Key Vault.

**Najlepsze rozwiązanie**: udzielanie dostępu użytkownikom, grupom i aplikacjom w określonym zakresie.   
**Szczegóły**: Użyj wstępnie zdefiniowanych ról RBAC. Na przykład aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną rolę [Key Vault współautor](/azure/role-based-access-control/built-in-roles) do tego użytkownika w określonym zakresie. Zakres w tym przypadku będzie subskrypcją, grupą zasobów lub tylko określonym magazynem kluczy. Jeśli wstępnie zdefiniowane role nie pasują do Twoich potrzeb, możesz [zdefiniować własne role](/azure/role-based-access-control/custom-roles).

**Najlepsze rozwiązanie**: Określanie, do czego użytkownicy mają dostęp.   
**Szczegóły**: dostęp do magazynu kluczy jest kontrolowany przez dwa oddzielne interfejsy: płaszczyzna zarządzania i płaszczyzna danych. Mechanizmy kontroli dostępu do płaszczyzny zarządzania i płaszczyzny danych działają niezależnie.

Użyj kontroli dostępu na podstawie ról, aby kontrolować, do czego użytkownicy mają dostęp. Jeśli na przykład chcesz udzielić aplikacji dostępu do kluczy w magazynie kluczy, musisz udzielić uprawnień dostępu do płaszczyzny danych za pomocą zasad dostępu magazynu kluczy, a dla tej aplikacji nie jest wymagany dostęp do płaszczyzny zarządzania. Z kolei aby umożliwić użytkownikowi odczyt właściwości i tagów magazynu, ale bez dostępu do kluczy, wpisów tajnych i certyfikatów, możesz udzielić temu użytkownikowi uprawnienia do odczytu przy użyciu kontroli dostępu opartej na rolach, dzięki czemu nie będzie potrzebny dostęp do płaszczyzny danych.

**Najlepsze rozwiązanie**: przechowywanie certyfikatów w magazynie kluczy. Certyfikaty są najwyższej wartości. W niewłaściwej ręce zabezpieczenia aplikacji lub zabezpieczenia danych mogą zostać naruszone.   
**Szczegóły**: Azure Resource Manager może bezpiecznie wdrażać certyfikaty przechowywane w Azure Key Vault na maszynach wirtualnych platformy Azure po wdrożeniu maszyn wirtualnych. Ustawiając odpowiednie zasady dostępu dla magazynu kluczy, możesz również kontrolować, kto uzyskuje dostęp do certyfikatu. Kolejną zaletą jest zarządzanie wszystkimi certyfikatami w jednym miejscu za pomocą usługi Azure Key Vault. Aby uzyskać więcej informacji [, zobacz Wdrażanie certyfikatów na maszynach wirtualnych z Key Vault zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) .

**Najlepsze rozwiązanie**: Upewnij się, że możesz odzyskać usuwanie magazynów kluczy lub obiektów magazynu kluczy.   
**Szczegóły**: usuwanie magazynów kluczy lub obiektów magazynu kluczy może być przypadkowe lub złośliwe. Włącz usuwanie nietrwałe i funkcje ochrony przed ochrony przed przeczyszczaniem w usłudze Key Vault, szczególnie w przypadku kluczy używanych do szyfrowania danych w spoczynku. Usunięcie tych kluczy jest równoznaczne z utratą danych, więc w razie potrzeby możesz odzyskać usunięte magazyny i obiekty magazynu. Należy regularnie Key Vault operacje odzyskiwania.

> [!NOTE]
> Jeśli użytkownik ma uprawnienia współautora (w ramach kontroli dostępu na podstawie ról) do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Zalecamy ścisłą kontrolę nad tym, kto ma dostęp współautora do magazynów kluczy, aby mieć pewność, że tylko autoryzowane osoby będą mogły uzyskiwać dostęp do Twoich magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
>
>

## <a name="manage-with-secure-workstations"></a>Zarządzanie przy użyciu bezpiecznych stacji roboczych

> [!NOTE]
> Administrator subskrypcji lub właściciel powinien korzystać z bezpiecznej stacji roboczej dostępu lub stacji roboczej dostępu uprzywilejowanego.
>
>

Ze względu na to, że zdecydowane ataki są ukierunkowane na użytkownika końcowego, punkt końcowy jest jednym z głównych punktów ataku. Osoba atakująca, która narusza punkt końcowy, może użyć poświadczeń użytkownika w celu uzyskania dostępu do danych organizacji. Większość ataków na punkt końcowy korzysta z faktu, że użytkownicy są administratorami na swoich lokalnych stacjach roboczych.

**Najlepsze rozwiązanie**: Użyj bezpiecznej stacji roboczej zarządzania do ochrony poufnych kont, zadań i danych.   
**Szczegóły**: aby zmniejszyć obszar narażony na ataki, użyj [stacji roboczej z dostępem uprzywilejowanym](https://technet.microsoft.com/library/mt634654.aspx) . Te bezpieczne zarządzanie stacjami roboczymi może pomóc w ograniczeniu niektórych ataków i upewnieniu się, że dane są bezpieczniejsze.

**Najlepsze rozwiązanie**: Upewnij się, że program Endpoint Protection.   
**Szczegóły**: wymuszanie zasad zabezpieczeń na wszystkich urządzeniach, które są używane do korzystania z danych, niezależnie od lokalizacji danych (w chmurze lub lokalnie).

## <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[Szyfrowanie danych w spoczynku](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) to obowiązkowy krok w kierunku prywatności, zgodności i suwerenności danych.

**Najlepsze rozwiązanie**: zastosowanie szyfrowania dysków w celu zapewnienia lepszej ochrony danych.   
**Szczegóły**: Użyj [Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview). Umożliwia ona administratorom IT szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Szyfrowanie dysków łączy funkcję funkcji Windows BitLocker Standard Industry i system Linux dm-crypt w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych.

Usługa Azure Storage i Azure SQL Database szyfrują dane domyślnie, a wiele usług oferuje szyfrowanie jako opcję. Za pomocą usługi Azure Key Vault można zachować kontrolę nad kluczami, za pomocą których jest uzyskiwany dostęp do danych i następuje ich szyfrowanie. [Aby dowiedzieć się więcej, zobacz Obsługa modelu szyfrowania dostawców zasobów platformy Azure](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Najlepsze rozwiązania**: Użyj szyfrowania, aby pomóc w ograniczeniu ryzyka związanego z nieautoryzowanym dostępem do danych.   
**Szczegóły**: Zaszyfruj dyski przed zapisaniem w nich poufnych danych.

Organizacje, które nie wymuszają szyfrowania danych, są bardziej narażone na problemy związane z poufnością danych. Na przykład nieautoryzowani lub Złośliwi użytkownicy mogą wykraść dane na naruszonych kontach lub uzyskać nieautoryzowany dostęp do danych zakodowanych w postaci czystego formatu. Firmy muszą także udowodnić, że są sumienni i używają odpowiednich kontroli zabezpieczeń w celu zwiększenia bezpieczeństwa danych w celu zapewnienia zgodności z przepisami branżowymi.

## <a name="protect-data-in-transit"></a>Ochrona danych podczas przesyłania

Ochrona danych podczas ich przesyłania powinna być istotną częścią strategii ochrony danych. Ponieważ dane są przenoszone pomiędzy wieloma lokalizacjami, zazwyczaj zalecamy, aby do wymiany danych między różnymi lokalizacjami zawsze używać protokołów SSL/TLS. W niektórych sytuacjach może być konieczne odizolowanie całego kanału komunikacyjnego łączącego infrastruktury lokalne i chmury przy użyciu sieci VPN.

W przypadku danych przenoszonych między infrastrukturą lokalną i platformą Azure należy wziąć pod uwagę odpowiednie zabezpieczenia, takie jak protokół HTTPS lub sieć VPN. W przypadku wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznej sieci Internet Użyj [usługi Azure VPN Gateway](../../vpn-gateway/index.yml).

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z usługi Azure VPN Gateway, protokołu SSL/TLS i protokołu HTTPS.

**Najlepsze rozwiązanie**: bezpieczny dostęp z wielu stacji roboczych znajdujących się lokalnie do sieci wirtualnej platformy Azure.   
**Szczegóły**: Użyj [sieci VPN typu lokacja-lokacja](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Najlepsze rozwiązanie**: bezpieczny dostęp z pojedynczej stacji roboczej znajdującej się w środowisku lokalnym do sieci wirtualnej platformy Azure.   
**Szczegóły**: Użyj [sieci VPN typu punkt-lokacja](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Najlepsze rozwiązanie**: przenoszenie większych zestawów danych za pośrednictwem dedykowanego łącza sieci WAN o dużej szybkości.   
**Szczegóły**: Użyj [ExpressRoute](/azure/expressroute/expressroute-introduction). Jeśli wybierzesz korzystanie z usługi ExpressRoute, możesz także szyfrować dane na poziomie aplikacji przy użyciu protokołu SSL/TLS lub innych protokołów w celu zapewnienia dodatkowej ochrony.

**Najlepsze rozwiązanie**: Współpracuj z usługą Azure Storage za pomocą Azure Portal.   
**Szczegóły**: wszystkie transakcje odbywają się za pośrednictwem protokołu HTTPS. Do współpracy z [usługą Azure Storage](https://azure.microsoft.com/services/storage/)można także używać [interfejsu API REST usługi Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) za pośrednictwem protokołu HTTPS.

Organizacje, które nie chronią przesyłanych danych, są bardziej podatne na [ataki typu man-in-the-Middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwanie](https://technet.microsoft.com/library/gg195641.aspx)i przejmowanie sesji. Takie ataki mogą być pierwszym krokiem do uzyskania dostępu do poufnych danych.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpieczanie poczty e-mail, dokumentów i danych poufnych

Chcesz kontrolować i zabezpieczać wiadomości e-mail, dokumenty i dane poufne, które są udostępniane poza firmą. [Azure Information Protection](/azure/information-protection/) to rozwiązanie chmurowe ułatwiające klasyfikowanie, etykietowanie i ochronę dokumentów oraz wiadomości e-mail w firmie. Można to zrobić automatycznie przez administratorów, którzy definiują zasady i warunki, ręcznie przez użytkowników lub kombinację, w której użytkownicy otrzymują zalecenia.

Klasyfikacja jest identyfikowana przez cały czas, bez względu na to, gdzie są przechowywane dane lub do kogo są udostępniane. Etykiety zawierają oznaczenia wizualne, takie jak nagłówek, stopka lub znak wodny. Metadane dodawane do plików i nagłówków wiadomości e-mail mają postać zwykłego tekstu. Czysty tekst gwarantuje, że inne usługi, takie jak rozwiązania, które uniemożliwiają utratę danych, mogą identyfikować klasyfikację i podejmować odpowiednie działania.

Technologia ochrony używa platformy Azure Rights Management (Azure RMS). Ta technologia jest zintegrowana z innymi usługami i aplikacjami w chmurze firmy Microsoft, takimi jak Microsoft 365 i Azure Active Directory. Ta technologia ochrony używa zasad szyfrowania, tożsamości i autoryzacji. Ochrona zastosowana w Azure RMS pozostaje w dokumentach i wiadomościach e-mail niezależnie od lokalizacji — wewnątrz lub na zewnątrz organizacji, sieci, serwerów plików i aplikacji.

To rozwiązanie do ochrony informacji pozwala zachować kontrolę nad danymi nawet wtedy, gdy są one udostępniane innym osobom. Możesz również użyć Azure RMS z własnymi aplikacjami biznesowymi i rozwiązaniami do ochrony informacji od dostawców oprogramowania, niezależnie od tego, czy te aplikacje i rozwiązania są lokalne, czy w chmurze.

Zalecamy wykonanie następujących czynności:

- [Wdróż Azure Information Protection](/azure/information-protection/deployment-roadmap) organizacji.
- Zastosuj etykiety odzwierciedlające wymagania biznesowe. Na przykład: Zastosuj etykietę o nazwie "wysoce poufne" do wszystkich dokumentów i wiadomości e-mail, które zawierają dane typu Top-Secret, aby sklasyfikować i chronić te dane. Następnie tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do tych danych z wszelkimi ograniczeniami, które określisz.
- Skonfiguruj [Rejestrowanie użycia Azure RMS](/azure/information-protection/log-analyze-usage) , aby można było monitorować sposób korzystania z usługi ochrony przez organizację.

Organizacje, które mają słabą [klasyfikację danych](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochronę plików, mogą być bardziej podatne na wycieki danych lub niewłaściwe użycie danych. Dzięki prawidłowej ochronie plików można analizować przepływy danych, aby uzyskać wgląd w swoją firmę, wykrywać ryzykowne zachowania i podejmować działania naprawcze, śledzić dostęp do dokumentów i tak dalej.

## <a name="next-steps"></a>Następne kroki

Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby umożliwiające dostarczenie bardziej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — na bieżąco z najnowszymi informacjami na temat zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku których luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub wysyłane pocztą e-mail secure@microsoft.com

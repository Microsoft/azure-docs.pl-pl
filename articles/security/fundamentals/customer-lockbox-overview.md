---
title: Skrytka klienta Microsoft Azure
description: Omówienie techniczne Skrytka klienta dla Microsoft Azure, które zapewnia kontrolę nad dostępem dostawcy w chmurze, gdy firma Microsoft może potrzebować dostępu do danych klienta.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: 5330c751aaa3fcbd5c7fc268e4a4de08d336d474
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735440"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Skrytka klienta Microsoft Azure

> [!NOTE]
> Aby skorzystać z tej funkcji, organizacja musi mieć [Plan pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/) o minimalnym poziomie **dewelopera**.

Skrytka klienta dla Microsoft Azure udostępnia interfejs umożliwiający klientom przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Jest on używany w przypadkach, gdy inżynier firmy Microsoft musi uzyskać dostęp do danych klienta w trakcie żądania pomocy technicznej.

W tym artykule opisano, jak żądania Skrytka klienta są inicjowane, śledzone i przechowywane na potrzeby późniejszych przeglądów i inspekcji.

Skrytka klienta jest teraz ogólnie dostępna i obecnie włączona na potrzeby dostępu pulpitu zdalnego do maszyn wirtualnych.

## <a name="workflow"></a>Przepływ pracy

Poniższe kroki przedstawiają typowy przepływ pracy dla żądania Skrytka klienta.

1. Ktoś w organizacji ma problem z obciążeniem platformy Azure.

2. Gdy ta osoba rozwiąże problem, ale nie może go rozwiązać, otwarcie biletu pomocy technicznej z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Bilet jest przypisany do inżyniera pomocy technicznej platformy Azure.

3. Inżynier pomocy technicznej systemu Azure przegląda żądanie obsługi i określa następne kroki w celu rozwiązania problemu.

4. Jeśli inżynier pomocy technicznej nie może rozwiązać problemu przy użyciu standardowych narzędzi i telemetrii, następnym krokiem jest zażądanie podwyższonego poziomu uprawnień przy użyciu usługi dostępu just-in-Time (JIT). To żądanie może pochodzić od oryginalnego inżyniera pomocy technicznej. Może być też z innego inżyniera, ponieważ problem jest eskalacją zespołu usługi Azure DevOps.

5. Gdy żądanie dostępu zostanie przesłane przez inżyniera platformy Azure, usługa just in Time obliczy żądanie biorąc pod uwagę czynniki, takie jak:
    - Zakres zasobu
    - Czy obiekt żądający jest tożsamością izolowaną lub korzystasz z uwierzytelniania wieloskładnikowego
    - Poziomy uprawnień

    Na podstawie reguły JIT to żądanie może również obejmować zatwierdzenie od wewnętrznych osób zatwierdzających firmy Microsoft. Osoba zatwierdzająca może na przykład być liderem działu obsługi klienta lub DevOps.

6. Gdy żądanie wymaga bezpośredniego dostępu do danych klienta, inicjowane jest żądanie Skrytka klienta. Na przykład dostęp pulpitu zdalnego do maszyny wirtualnej klienta.

    Żądanie jest teraz **zgłaszane przez klienta** w stanie "Oczekiwanie na zatwierdzenie klienta" przed udzieleniem dostępu.

7. W organizacji klienta użytkownik, który ma [rolę właściciela](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) dla subskrypcji platformy Azure, otrzymuje wiadomość E-mail od firmy Microsoft w celu powiadomienia o oczekującym żądaniu dostępu. W przypadku żądań Skrytka klienta ta osoba jest Wyznaczeni osoby zatwierdzającej.

    Przykład wiadomości e-mail:

    ![Azure Skrytka klienta — powiadomienie e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Powiadomienie e-mail zawiera link do bloku **skrytka klienta** w Azure Portal. Za pomocą tego linku Wyznaczeni osoba zatwierdzająca loguje się do Azure Portal, aby wyświetlić wszystkie oczekujące żądania, które organizacja ma dla Skrytka klienta:

    ![Skrytka klienta platformy Azure — Strona docelowa](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Żądanie pozostaje w kolejce klienta przez cztery dni. Po upływie tego czasu żądanie dostępu zostanie automatycznie wygaśnie i nie zostanie udzielony dostęp do inżynierów firmy Microsoft.

9. Aby uzyskać szczegółowe informacje o oczekujących żądania, Wyznaczeni osoby zatwierdzającej może wybrać żądanie skrytki z **oczekujących żądań**:

    ![Skrytka klienta platformy Azure — Wyświetl oczekujące żądanie](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Wyznaczeni osoba zatwierdzająca może również wybrać **Identyfikator żądania** obsługi, aby wyświetlić żądanie biletu pomocy technicznej utworzone przez oryginalnego użytkownika. Te informacje zapewniają kontekst, dla którego pomoc techniczna firmy Microsoft jest zaangażowany, i historię zgłoszonego problemu. Na przykład:

    ![Skrytka klienta platformy Azure — wyświetlanie żądania biletu pomocy technicznej](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Po przejrzeniu żądania wyznaczono opcję **Zatwierdź** lub **Odmów**:

    ![Skrytka klienta platformy Azure — wybierz pozycję Zatwierdź lub Odmów](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    W wyniku zaznaczenia:
    - **Zatwierdź**: dostęp jest udzielany do inżynierów firmy Microsoft. Dostęp jest udzielany przez domyślny okres ośmiu godzin.
    - **Odmów**: żądanie dostępu z podwyższonym poziomem uprawnień przez inżyniera firmy Microsoft zostało odrzucone i nie są podejmowane żadne dalsze działania.

W celach inspekcji akcje wykonywane w tym przepływie pracy są rejestrowane w [dziennikach żądań skrytka klienta](#auditing-logs).

## <a name="auditing-logs"></a>Dzienniki inspekcji

Dzienniki Skrytka klienta są przechowywane w dziennikach aktywności. W Azure Portal wybierz pozycję **dzienniki aktywności** , aby wyświetlić informacje dotyczące inspekcji powiązane z żądaniami skrytka klienta. Można odfiltrować określone akcje, takie jak:
- **Odmów żądania skrytki**
- **Utwórz żądanie skrytki**
- **Zatwierdź żądanie skrytki**
- **Wygaśnięcie żądania skrytki**

Przykład:

![Azure Skrytka klienta — dzienniki aktywności](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Obsługiwane usługi i scenariusze ogólnie dostępne

Poniższe usługi i scenariusze są obecnie ogólnie dostępne dla Skrytka klienta.

### <a name="remote-desktop-access-to-virtual-machines"></a>Dostęp pulpitu zdalnego do maszyn wirtualnych

Skrytka klienta jest obecnie włączona dla żądań dostępu pulpitu zdalnego do maszyn wirtualnych. Obsługiwane są następujące obciążenia:
- Platforma jako usługa (PaaS) — Cloud Services platformy Azure (rola sieci Web i proces roboczy)
- Infrastruktura jako usługa (IaaS) — systemy Windows i Linux (tylko Azure Resource Manager)
- Zestaw skalowania maszyn wirtualnych — systemy Windows i Linux

> [!NOTE]
> Wystąpienia klasyczne IaaS nie są obsługiwane przez Skrytka klienta. Jeśli masz obciążenia działające na IaaS klasyczne wystąpienia, zalecamy przeprowadzenie migracji z klasycznego do Menedżer zasobów modeli wdrażania. Aby uzyskać instrukcje, zobacz [Migrowanie zasobów IaaS z platformy klasycznej do Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Szczegółowe dzienniki inspekcji

W przypadku scenariuszy obejmujących dostęp do pulpitu zdalnego można użyć dzienników zdarzeń systemu Windows, aby przejrzeć działania podejmowane przez inżyniera firmy Microsoft. Rozważ użycie Azure Security Center do zbierania dzienników zdarzeń i kopiowania danych do obszaru roboczego w celu analizy. Aby uzyskać więcej informacji, zobacz [zbieranie danych w Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Obsługiwane usługi i scenariusze w wersji zapoznawczej

Następujące usługi są obecnie dostępne w wersji zapoznawczej dla Skrytka klienta:

- Azure Storage

- Azure SQL DB

- Azure Data Explorer

- Maszyny wirtualne (teraz obejmują również dostęp do zrzutów pamięci i dysków zarządzanych)

- Transfery subskrypcji platformy Azure

Aby włączyć Skrytka klienta dla tych ofert w wersji zapoznawczej dla Twojej organizacji, zarejestruj się, aby uzyskać [skrytka klienta dla publicznej wersji zapoznawczej platformy Azure](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Wykluczenia

Żądania Skrytka klienta nie są wyzwalane w następujących scenariuszach obsługi inżynierów:

- Inżynier firmy Microsoft musi wykonać działania, które wykraczają poza standardowe procedury operacyjne. Na przykład w celu odzyskania lub przywrócenia usług w nieoczekiwanych lub nieprzewidywalnych scenariuszach.

- Inżynier firmy Microsoft uzyskuje dostęp do platformy Azure w ramach rozwiązywania problemów i przypadkowo ma dostęp do danych klientów. Na przykład zespół sieci platformy Azure wykonuje Rozwiązywanie problemów z wynikiem przechwycenia pakietu na urządzeniu sieciowym. Jeśli jednak klient zaszyfrował dane podczas przesyłania, inżynier nie może odczytać danych.

## <a name="next-steps"></a>Następne kroki

Skrytka klienta jest automatycznie dostępna dla wszystkich klientów z [planem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) o minimalnym poziomie **dewelopera**.

Jeśli masz uprawniający plan pomocy technicznej, nie musisz mieć żadnej akcji, aby włączyć Skrytka klienta. Żądania Skrytka klienta są inicjowane przez inżyniera firmy Microsoft, jeśli jest to konieczne do wykonania biletu pomocy technicznej, który został zgłoszony przez kogoś w organizacji.

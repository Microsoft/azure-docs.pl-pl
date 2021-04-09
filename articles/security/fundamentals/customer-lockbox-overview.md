---
title: Funkcja Skrytka klienta dla platformy Microsoft Azure
description: Omówienie techniczne Skrytka klienta dla Microsoft Azure, które zapewnia kontrolę nad dostępem dostawcy w chmurze, gdy firma Microsoft może potrzebować dostępu do danych klienta.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: 0146e4fcaf70d37975dc587a266c47bf4b3f4601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461678"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Funkcja Skrytka klienta dla platformy Microsoft Azure

> [!NOTE]
> Aby skorzystać z tej funkcji, organizacja musi mieć [Plan pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/) o minimalnym poziomie **dewelopera**.

Funkcja Skrytka klienta dla platformy Microsoft Azure udostępnia klientom interfejs umożliwiający przeglądanie oraz zatwierdzanie lub odrzucanie żądań dotyczących dostępu do danych klientów. Jest ona używana, gdy inżynier firmy Microsoft musi uzyskać dostęp do danych klientów podczas rozpatrywania wniosku o pomoc techniczną.

W tym artykule opisano sposób włączania Skrytka klienta i sposobu, w jaki żądania skrytki są inicjowane, śledzone i przechowywane na potrzeby późniejszych przeglądów i inspekcji.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios-general-availability"></a>Obsługiwane usługi i scenariusze (ogólna dostępność)

Następujące usługi są teraz ogólnie dostępne dla Skrytka klienta:

- Usługa Azure API Management
- Azure App Service
- Azure Cognitive Services
- Azure Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Transfery subskrypcji platformy Azure
- Azure Synapse Analytics
- Maszyny wirtualne na platformie Azure (obejmujące dostęp do pulpitu zdalnego, dostęp do zrzutów pamięci i dysków zarządzanych)

## <a name="enable-customer-lockbox"></a>Włącz Skrytka klienta

Teraz można włączyć Skrytka klienta z poziomu [modułu administracyjnego](https://aka.ms/customerlockbox/administration) w bloku skrytka klienta.  

> [!NOTE]
> Aby włączyć Skrytka klienta, konto użytkownika musi mieć [przypisaną rolę administratora globalnego](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Przepływ pracy

Poniższe kroki przedstawiają typowy przepływ pracy dla żądania Skrytka klienta.

1. Ktoś w organizacji ma problem z obciążeniem platformy Azure.

2. Gdy ta osoba rozwiąże problem, ale nie może go rozwiązać, otwarcie biletu pomocy technicznej z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Bilet jest przypisany do inżyniera pomocy technicznej platformy Azure.

3. Inżynier pomocy technicznej systemu Azure przegląda żądanie obsługi i określa następne kroki w celu rozwiązania problemu.

4. Jeśli inżynier pomocy technicznej nie może rozwiązać problemu przy użyciu standardowych narzędzi i telemetrii, następnym krokiem jest zażądanie podwyższonego poziomu uprawnień przy użyciu usługi dostępu just-in-Time (JIT). To żądanie może pochodzić z oryginalnego inżyniera pomocy technicznej lub innego inżyniera, ponieważ problem został przekazany do zespołu usługi Azure DevOps.

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

8. Powiadomienie e-mail zawiera link do bloku **skrytka klienta** w module Administracja. Za pomocą tego linku Wyznaczeni osoba zatwierdzająca loguje się do Azure Portal, aby wyświetlić wszystkie oczekujące żądania, które organizacja ma dla Skrytka klienta:

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

Dzienniki funkcji Skrytka klienta są przechowywane w dziennikach aktywności. W Azure Portal wybierz pozycję **dzienniki aktywności** , aby wyświetlić informacje dotyczące inspekcji powiązane z żądaniami skrytka klienta. Można odfiltrować określone akcje, takie jak:
- **Odmowa żądania skrytki**
- **Utworzenie żądania skrytki**
- **Zatwierdzenie żądania skrytki**
- **Wygaśnięcie żądania skrytki**

Przykład:

![Azure Skrytka klienta — dzienniki aktywności](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Integracja funkcji Skrytka klienta z testem porównawczym zabezpieczeń platformy Azure

Wprowadziliśmy nową kontrolkę bazową ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) na platformie Azure Security test, która obejmuje skrytka klienta możliwości zastosowania. Klienci mogą teraz korzystać z testu porównawczego w celu przejrzenia Skrytka klienta zastosowania do usługi.

## <a name="exclusions"></a>Wykluczenia

Żądania Skrytka klienta nie są wyzwalane w następujących scenariuszach obsługi inżynierów:

- Inżynier firmy Microsoft musi wykonać działanie, które wykracza poza standardowe procedury operacyjne. Musi na przykład odzyskać lub odtworzyć usługi w nieoczekiwanych lub nieprzewidywalnych scenariuszach.
- Inżynier firmy Microsoft uzyskuje dostęp do platformy Azure w ramach procedury rozwiązywania problemów i przypadkowo uzyskuje dostęp do danych klienta. Na przykład zespół ds. sieci platformy Azure wykonuje procedurę rozwiązywania problemów, której rezultatem jest przechwycenie pakietów na urządzeniu sieciowym. W tym scenariuszu, jeśli klient szyfruje dane podczas przesyłania, inżynier nie może odczytać danych.

## <a name="next-steps"></a>Następne kroki

Skrytka klienta jest dostępna dla wszystkich klientów, którzy mają [Plan pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) o minimalnym poziomie **dewelopera**. Skrytka klienta można włączyć z poziomu [modułu administracyjnego](https://aka.ms/customerlockbox/administration) w bloku skrytka klienta.

Żądania Skrytka klienta są inicjowane przez inżyniera firmy Microsoft, jeśli ta akcja jest wymagana do postępu w przypadku pomocy technicznej.

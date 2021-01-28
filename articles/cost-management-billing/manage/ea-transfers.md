---
title: Transfery umowy Azure Enterprise
description: Tu opisano przenoszenie umów EA platformy Azure
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943681"
---
# <a name="azure-enterprise-transfers"></a>Transfery umowy Azure Enterprise

W tym artykule omówiono przenoszenie dla przedsiębiorstw.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Przenoszenie konta przedsiębiorstwa do nowej rejestracji

Przeniesienie konta powoduje przeniesienie właściciela konta z jednej rejestracji do innej. Wszystkie powiązane subskrypcje należące do właściciela konta zostaną przeniesione do rejestracji docelowej. Użyj procedury przenoszenia konta, gdy masz wiele aktywnych rejestracji i chcesz przenieść tylko wybranych właścicieli kont.

Ta sekcja służy tylko do celów informacyjnych, ponieważ akcji nie może wykonać administrator przedsiębiorstwa. W celu przeniesienia konta przedsiębiorstwa do nowej rejestracji jest wymagany wniosek o pomoc techniczną.

Podczas przenoszenia konta przedsiębiorstwa do nowej rejestracji pamiętaj o następujących kwestiach:

- Przenoszone są tylko konta określone w żądaniu. Jeśli wybierzesz wszystkie konta, wszystkie zostaną przeniesione.
- Rejestracja źródłowa zachowuje stan jako aktywny lub rozszerzony. Z rejestracji można korzystać do momentu jej wygaśnięcia.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia konta podaj następujące informacje:

- Numer rejestracji docelowej, nazwa konta i adres e-mail właściciela konta do przeniesienia
- W przypadku rejestracji źródłowej numer rejestracji i konto do przeniesienia

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem konta:

- Zatwierdzenie od administratora umowy EA jest wymagane w przypadku rejestracji docelowej i źródłowej
- Jeśli przeniesienie konta nie spełnia Twoich wymagań, rozważ przeniesienie rejestracji.
- Przenoszenie kont powoduje przeniesienie wszystkich usług i subskrypcji powiązanych z określonymi kontami.
- Po zakończeniu przenoszenia przeniesione konto będzie widoczne jako nieaktywne w ramach rejestracji źródłowej i jako aktywne w ramach rejestracji docelowej.
- Data zakończenia widoczna na koncie będzie odpowiadać efektywnej dacie przeniesienia w rejestracji źródłowej oraz dacie rozpoczęcia w rejestracji docelowej.
- Wszelkie użycie konta przed efektywną datą przeniesienia pozostanie w ramach rejestracji źródłowej.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Przenoszenie rejestracji przedsiębiorstwa do nowej rejestracji

Przeniesienie rejestracji rozważa się, gdy:

- Został osiągnięty termin przedpłaty bieżącej rejestracji.
- Rejestracja ma stan wygasła/rozszerzona i jest negocjowana nowa umowa.
- Jeśli masz wiele rejestracji i chcesz połączyć wszystkie konta oraz rozliczenia w ramach jednej rejestracji.

Ta sekcja służy tylko do celów informacyjnych, ponieważ akcji nie może wykonać administrator przedsiębiorstwa. Żądanie pomocy technicznej jest konieczne do przetransferowania rejestracji w przedsiębiorstwie do nowej, chyba że rejestracja jest zgodna z [transferem automatycznej rejestracji](#auto-enrollment-transfer).

Po utworzeniu żądania dotyczącego przeniesienia całej rejestracji w przedsiębiorstwie do rejestracji zostaną wykonane następujące akcje:

- Wszystkie usługi, subskrypcje, konta, działy i cała struktura rejestracji, w tym wszyscy administratorzy działu umów EA, zostaną przeniesione do nowej rejestracji docelowej.
- Stan rejestracji zostanie ustawiono na _Przeniesione_. Przeniesiona rejestracja jest dostępna wyłącznie dla celów raportowania użycia historycznego.
- Do przeniesionej rejestracji nie można dodawać ról ani subskrypcji. Stan transferu uniemożliwia korzystanie z rejestracji.
- Pozostałe saldo przedpłaty za platformę Azure w umowie zostanie utracone, łącznie z przyszłymi okresami.
-    Jeśli rejestracja, z której korzystasz, ma zakupy na poziomie RI, opłaty za zakup z RI pozostają w rejestracji źródłowej, jednak wszystkie korzyści wynikające z wystąpienia zarezerwowanego zostaną przeniesione do użycia w ramach nowej rejestracji.
-    Opłata za jednorazowe zakupy w witrynie Marketplace i wszystkie miesięczne opłaty stałe już naliczane w ramach starej rejestracji nie są przenoszone do nowej rejestracji. Opłaty witryny Marketplace naliczone na podstawie użycia zostaną przeniesione.

### <a name="effective-transfer-date"></a>Data obowiązywania przeniesienia

Data obowiązywania przeniesienia może być datą początkową rejestracji docelowej lub późniejszą.

Użycie rejestracji źródłowej jest obciążane opłatą w odniesieniu do przedpłaty za platformę Azure lub jako nadwyżka. Użycie nastąpi po przeniesieniu obowiązującej daty przeniesienia do nowej rejestracji i naliczania opłat.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia rejestracji podaj następujące informacje:

- W przypadku rejestracji źródłowej numer rejestracji.
- W przypadku rejestracji docelowej numer rejestracji, do której nastąpi przeniesienie.
- W przypadku daty obowiązywania przeniesienia rejestracji może to być data początkowa rejestracji docelowej lub późniejsza. Wybrana Data nie może wpływać na użycie dla żadnej już wydanej faktury.

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem rejestracji:

- Wymagane jest zatwierdzenie od administratorów umowy EA rejestracji docelowej i źródłowej.
- Jeśli przeniesienie rezerwacji nie spełnia Twoich wymagań, rozważ przeniesienie konta.
- Stan rejestracji źródłowej zostanie zaktualizowany na „przeniesiona” i będzie dostępny tylko dla celów raportowania użycia historycznego.

### <a name="auto-enrollment-transfer"></a>Automatyczne Transferowanie rejestracji

Może się okazać, że rejestracja ma stan **transferowany** , nawet jeśli bilet pomocy technicznej nie został przesłany do żądania przetransferowania rejestracji. **Przesłany** stan wynika z procesu automatycznego transferu rejestracji. Aby proces automatycznego przesyłania rejestracji miał miejsce w ramach zwrotu odnowienia, istnieje kilka elementów, które muszą zostać uwzględnione w nowej umowie:

- Wcześniejszy numer rejestracji (musi istnieć w portalu EA)
- Data wygaśnięcia wcześniejszego numeru rejestracji to jeden dzień przed datą rozpoczęcia obowiązywania nowej umowy
- Nowa Umowa zawiera zamówienie przedpłaty na platformie Azure, które ma bieżącą datę lub jest przestarzałe
- Nowa rejestracja zostanie utworzona w portalu EA

Jeśli nie ma żadnych danych użycia w portalu EA między wcześniejszą rejestracją a nową rejestracją, nie trzeba tworzyć biletu pomocy technicznej dotyczącej transferu.

### <a name="azure-prepayment"></a>Przedpłata za platformę Azure

Przedpłaty za platformę Azure nie można przenosić między rejestracjami. Salda przedpłaty za platformę Azure są powiązane z umową w ramach rejestracji, w której zostały zamówione. Przedpłata za platformę Azure nie jest przenoszona jako część procesu przenoszenia konta lub rejestracji.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Brak usług, których dotyczą przeniesienia kont i rejestracji

Podczas przenoszenia konta lub rejestracji nie występują przestoje. Można je wykonać w tym samym dniu żądania, jeśli zostaną podane wszystkie wymagane informacje.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Przenoszenie subskrypcji Enterprise do subskrypcji rozliczanej zgodnie z rzeczywistym użyciem

Aby przenieść subskrypcję Enterprise do pojedynczej subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem, należy utworzyć nowy wniosek o pomoc techniczną w witrynie Azure Enterprise Portal. Aby utworzyć wniosek o pomoc techniczną, wybierz pozycję **Nowy wniosek o pomoc techniczną** w obszarze **Pomoc i obsługa techniczna**.

## <a name="change-azure-subscription-or-account-ownership"></a>Zmienianie własności subskrypcji platformy Azure lub konta

W witrynie Azure EA Portal można przenosić subskrypcje od jednego właściciela konta do innego. Aby uzyskać więcej informacji, zobacz [Zmienianie własności subskrypcji platformy Azure lub konta](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Efekty przeniesienia subskrypcji

Jeśli subskrypcja platformy Azure zostanie przeniesiona na konto w tej dzierżawie usługi Azure Active Directory, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [kontrolą dostępu na podstawie ról platformy Azure (Azure RBAC)](../../role-based-access-control/overview.md) używaną do zarządzania zasobami, zachowają dostęp.

Aby wyświetlić użytkowników z dostępem RBAC do subskrypcji:

1. W witrynie Azure Portal otwórz pozycję **Subskrypcje**.
2. Wybierz subskrypcję, którą chcesz wyświetlić, a następnie wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** .
3. Wybierz pozycję **Przypisania ról**. Na stronie przypisań ról są wyświetlani wszyscy użytkownicy z dostępem RBAC do subskrypcji.

Jeśli subskrypcja zostanie przeniesiona na konto w innej dzierżawie usługi Azure AD, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [dostępem RBAC](../../role-based-access-control/overview.md) do zarządzania zasobami _utracą_ dostęp. Chociaż dostęp RBAC nie istnieje, dostęp do subskrypcji może być dostępny w ramach mechanizmów zabezpieczeń, takich jak przykład:

- Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
- Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../../storage/common/storage-account-overview.md).
- Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**.
3. Wybierz zasób.
4. Na stronie zasobu wybierz pozycję **Ustawienia**, aby wyświetlić i zaktualizować istniejące wpisy tajne.

## <a name="next-steps"></a>Następne kroki

- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
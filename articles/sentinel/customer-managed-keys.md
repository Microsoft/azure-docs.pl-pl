---
title: Konfigurowanie kluczy zarządzanych przez klienta w usłudze Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta (CMK) w usłudze Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461637"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Konfigurowanie klucza zarządzanego przez klienta usługi Azure Sentinel


Ten artykuł zawiera podstawowe informacje i kroki konfigurowania klucza zarządzanego przez klienta (CMK) dla usługi Azure Sentinel. CMK umożliwia szyfrowanie wszystkich danych zapisanych lub wysłanych do usługi Azure Sentinel we wszystkich odpowiednich zasobach magazynu za pomocą klucza usługi Azure Key Vault utworzonego lub należącego do Ciebie.

> [!NOTE]
> -   Funkcja cmk usługi Azure Sentinel jest dostępna tylko dla klientów, którzy są **nowi,** a dostęp do tej funkcji jest kontrolowany przez rejestrację funkcji platformy Azure.Możesz poprosić o azuresentinelCMK@microsoft.comdostęp, kontaktując się , a ponieważ pojemność jest dostępna, oczekujące żądania zostaną zatwierdzone.
> -   Funkcja cmk usługi Azure Sentinel jest dostępna tylko w wschodnich stanach USA, zachodnich stanach USA 2 i południowo-środkowych regionach USA.
> -   Funkcja CMK jest dostępna tylko dla klientów wysyłających 1 TB dziennie lub więcej. Otrzymasz informacje o dodatkowych cenach, gdy wystąpisz do firmy Microsoft o udostępnienie cmk w ramach subskrypcji platformy Azure. Dowiedz się więcej o [ładowaniu usługi Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Jak działa CMK 

Rozwiązanie Usługi Azure Sentinel używa kilku zasobów magazynu do zbierania dzienników i funkcji, w tym usługi Log Analytics i innych zasobów magazynu. W ramach konfiguracji zestawu CMK usługi Azure Sentinel należy skonfigurować ustawienia CMK na powiązanych zasobów magazynu, jak również. Dane zapisane w zasobach magazynu innych niż Usługa Log Analytics również będą szyfrowane.

> [!NOTE]
> Jeśli włączysz cmk na platformie Azure Sentinel, żadna funkcja publicznej wersji zapoznawczej, która nie obsługuje cmk nie będzie włączona.

## <a name="enable-cmk"></a>Włączanie zestawu CMK 

Aby aprowizować cmk, wykonaj następujące kroki: 

1.  Utwórz magazyn kluczy platformy Azure i przechowuj klucz.

2.  Włącz cmk w obszarze roboczym usługi Log Analytics.

3.  Zarejestruj się w usłudze Cosmos DB.

4.  Dodaj zasady dostępu do wystąpienia usługi Azure Key Vault.

5.  Włącz cmk w usłudze Azure Sentinel.

6.  Włącz usługę Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>KROK 1: Tworzenie magazynu kluczy platformy Azure i przechowywanie klucza

1.  [Utwórz zasób usługi Azure Key Vault,](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)a następnie wygeneruj lub zaimportuj klucz do szyfrowania danych.
    > [!NOTE]
    >  Usługa Azure Key Vault musi być skonfigurowana jako możliwe do odzyskania, aby chronić klucz i dostęp.

1.  [Włącz opcje odzyskiwania:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Upewnij się, że [opcja Usuwanie nietrwałe](../key-vault/general/overview-soft-delete.md) jest włączona.

    -   Włącz [ochronę przed przeczyszczaniem,](../key-vault/general/overview-soft-delete.md#purge-protection) aby chronić przed wymuszonym usunięciem klucza tajnego/przechowalni nawet po usunięciu nietrwałym.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>KROK 2: Włącz cmk w obszarze roboczym usługi Log Analytics

Postępuj zgodnie z instrukcjami w [konfiguracji klucza zarządzanego przez klienta usługi Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) w celu utworzenia obszaru roboczego CMK, który będzie używany jako obszar roboczy usługi Azure Sentinel w poniższych krokach.

### <a name="step-3-register-for-cosmos-db"></a>KROK 3: Zarejestruj się w Cosmos DB

Usługa Azure Sentinel współpracuje z usługą Cosmos DB jako dodatkowym zasobem magazynu. Upewnij się, że zarejestrujesz się w usłudze Cosmos DB.

Postępuj zgodnie z instrukcją usługi Cosmos DB, aby [zarejestrować dostawcę zasobów usługi Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) dla subskrypcji platformy Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>KROK 4: Dodawanie zasad dostępu do wystąpienia usługi Azure Key Vault

Upewnij się, aby dodać dostęp z usługi Cosmos DB do wystąpienia usługi Azure Key Vault. Postępuj zgodnie z instrukcją usługi Cosmos DB, aby [dodać zasady dostępu do wystąpienia usługi Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) za pomocą podmiotu usługi Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>KROK 5: Włącz cmk w usłudze Azure Sentinel

Funkcja cmk usługi Azure Sentinel jest dostępna dla nowych klientów tylko po otrzymaniu dostępu bezpośrednio z grupy produktów platformy Azure. Użyj kontaktów w firmie Microsoft, aby otrzymać zatwierdzenie od zespołu Usługi Azure Sentinel, aby włączyć cmk w rozwiązaniu.

Po dokonaniu zatwierdzenia zostaniesz poproszony o podanie następujących informacji, aby włączyć funkcję CMK.

-  Identyfikator obszaru roboczego, na którym chcesz włączyć cmk

-  Adres URL magazynu kluczy: skopiuj "Identyfikator klucza" do ostatniego ukośnika do przodu:  
    

    ![identyfikator klucza](./media/customer-managed-keys/key-identifier.png)

    Zespół usługi Azure Sentinel włączy funkcję usługi Azure Sentinel CMK dla dostarczonego obszaru roboczego.

-  Weryfikacja z zespołu produktów usługi Azure Sentinel, który został zatwierdzony do korzystania z tej funkcji. Musisz mieć to przed kontynuowaniem.

### <a name="step-6-enable-azure-sentinel"></a>KROK 6: Włącz usługę Azure Sentinel


Przejdź do witryny Azure portal i włącz usługę Azure Sentinel w obszarze roboczym, w którym skonfigurowana jest usługa CMK. Aby uzyskać więcej informacji, zobacz [Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Odwoływanie lub usuwanie klucza szyfrowania klucza


W przypadku, gdy użytkownik odwoła klucz szyfrowania klucza, albo przez usunięcie go lub usunięcie dostępu dla usługi Azure Sentinel, w ciągu jednej godziny usługi Azure Sentinel będzie honorować zmiany i zachowywać się tak, jakby dane nie są już dostępne. W tym momencie zostanie uniemożliwiona każda operacja wykonana, która używa trwałych zasobów magazynu, takich jak pozyskiwania danych, trwałe zmiany konfiguracji i tworzenie zdarzeń. Wcześniej przechowywane dane nie zostaną usunięte, ale pozostaną niedostępne. Niedostępne dane podlegają zasadom przechowywania danych i zostaną usunięte zgodnie z tymi zasadami.

Jedyną możliwą operacją po odwołaniu lub usunięciu klucza szyfrowania jest usunięcie konta.

Jeśli dostęp zostanie przywrócony po odwołaniu, usługa Azure Sentinel przywróci dostęp do danych w ciągu godziny.

Aby dowiedzieć się więcej o tym, jak to działa w usłudze Azure Monitor, zobacz [odwołanie cmk monitora platformy Azure Monitor.](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)

## <a name="key-encryption-key-rotation"></a>Obrót klucza szyfrowania klucza


Usługa Azure Sentinel i usługa Log Analytics obsługują rotację kluczy. Gdy użytkownik wykonuje rotację kluczy w usłudze Key Vault, usługa Azure Sentinel obsługuje nowy klucz w ciągu godziny.

W przechowalni kluczy można wykonywać obrót klawiszy, tworząc nową wersję klucza:

![obrót klawiszy](./media/customer-managed-keys/key-rotation.png)

Poprzednią wersję klucza można wyłączyć po 24 godzinach lub po tym, jak dzienniki inspekcji usługi Azure Key Vault nie będą już wykazywać żadnych działań korzystających z poprzedniej wersji.

Jeśli używasz tego samego klucza w usłudze Azure Sentinel i w usłudze Log Analytics, konieczne jest wykonanie rotacji kluczy, które należy jawnie zaktualizować zasób klastra w usłudze Log Analytics przy użyciu nowej wersji klucza usługi Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Azure Monitor rotacji cmk](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak skonfigurować klucz zarządzany przez klienta w usłudze Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


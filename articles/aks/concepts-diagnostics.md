---
title: Omówienie diagnostyki usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat klastrów samodiagnostyki w usłudze Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011562"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Omówienie usługi Azure Kubernetes Service Diagnostics (wersja zapoznawcza)

Rozwiązywanie problemów z klastrem usługi Azure Kubernetes Service (AKS) odgrywa ważną rolę w obciążeniu klastra, szczególnie w przypadku, gdy w klastrze działają obciążenia o kluczowym znaczeniu. Diagnostyka AKS to inteligentne, samoobsługowe środowisko diagnostyczne, które:
* Pomaga identyfikować i rozwiązywać problemy w klastrze. 
* Jest chmurą natywną.
* Nie wymaga dodatkowej konfiguracji ani kosztu rozliczeń.

Ta funkcja jest teraz dostępna w publicznej wersji zapoznawczej. 

## <a name="open-aks-diagnostics"></a>Otwórz diagnostykę AKS

Aby uzyskać dostęp do diagnostyki AKS:

1. Przejdź do klastra Kubernetes w [Azure Portal](https://portal.azure.com).
1. Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** w lewym okienku nawigacji, co spowoduje otwarcie diagnostyki AKS.
1. Wybierz kategorię, która najlepiej opisuje problem klastra, na przykład problemy z _węzłem klastra_, wykonując następujące działania:
    * Używanie słów kluczowych na kafelku strony głównej.
    * Wpisanie słowa kluczowego, które najlepiej opisuje Twój problem na pasku wyszukiwania.

![Strona główna](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Wyświetl raport diagnostyczny

Po kliknięciu kategorii można wyświetlić raport diagnostyczny specyficzny dla danego klastra. Raporty diagnostyczne w sposób inteligentny wywołują wszelkie problemy w klastrze przy użyciu ikon stanu. Aby przejść do szczegółów poszczególnych tematów, kliknij pozycję **więcej informacji** , aby wyświetlić szczegółowy opis:
* Problemy
* Zalecane akcje
* Linki do przydatnych dokumentów
* Powiązane — metryki
* Rejestrowanie danych 

Raporty diagnostyczne generują na podstawie bieżącego stanu klastra po uruchomieniu różnych sprawdzeń. Mogą one być przydatne do lokalizowania problemu z klastrem i poznania dalszych kroków w celu rozwiązania problemu.

![Raport diagnostyczny](./media/concepts-diagnostics/diagnostic-report.png)

![Rozwinięty raport diagnostyczny](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Szczegółowe informacje o klastrze

W usłudze **cluster Insights** są dostępne następujące testy diagnostyczne.

### <a name="cluster-node-issues"></a>Problemy z węzłem klastra

Problemy z węzłem klastra sprawdzają, czy występują problemy związane z węzłem, które powodują nieoczekiwane zachowanie klastra.

- Problemy z gotowością węzłów
- Awarie węzłów
- Niewystarczające zasoby
- Brak konfiguracji adresu IP węzła
- Awarie CNI węzła
- Nie znaleziono węzła
- Wyłącz zasilanie węzła
- Niepowodzenie uwierzytelniania węzła
- Node polecenia — nieodświeżony serwer proxy

### <a name="create-read-update--delete-crud-operations"></a>Tworzenie, odczytywanie, aktualizowanie & operacji usuwania (CRUD)

CRUD operacji sprawdza wszystkie operacje CRUD, które powodują problemy w klastrze.

- Błąd operacji usuwania podsieci w użyciu
- Błąd operacji usuwania sieciowej grupy zabezpieczeń
- Błąd operacji usuwania tabeli tras w użyciu
- Przywoływany błąd aprowizacji zasobów
- Błąd operacji usuwania publicznego adresu IP
- Niepowodzenie wdrożenia z powodu przydziału wdrożenia
- Błąd operacji ze względu na zasady organizacji
- Brak rejestracji subskrypcji
- Błąd inicjowania obsługi rozszerzenia maszyny wirtualnej
- Pojemność podsieci
- Błąd przekroczenia limitu przydziału

### <a name="identity-and-security-management"></a>Zarządzanie tożsamościami i zabezpieczeniami

Zarządzanie tożsamościami i zabezpieczeniami wykrywa błędy uwierzytelniania i autoryzacji, które uniemożliwiają komunikację z klastrem.

- Błędy autoryzacji węzła
- Błędy 401
- Błędy 403

## <a name="next-steps"></a>Następne kroki

* Zbierz dzienniki, aby ułatwić dalsze Rozwiązywanie problemów z klastrem za pomocą [AKS Periscope](https://aka.ms/aksperiscope).

* Zapoznaj się z [sekcją Klasyfikacja Practices](/azure/architecture/operator-guides/aks/aks-triage-practices) w Przewodniku obsługi AKS Day-2.

* Opublikuj swoje pytania lub opinie w witrynie [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) , dodając w tytule "[diag]".
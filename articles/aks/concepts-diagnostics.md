---
title: Omówienie diagnostyki usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat klastrów samodiagnostyki w usłudze Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 9d06ad2f740394a4959f3665c5d6311e71841241
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960839"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Omówienie usługi Azure Kubernetes Service Diagnostics (wersja zapoznawcza)

Rozwiązywanie problemów z klastrem usługi Azure Kubernetes Service (AKS) jest ważną częścią obsługi klastra, zwłaszcza jeśli klaster działa w przypadku obciążeń o kluczowym znaczeniu. Diagnostyka AKS to inteligentne środowisko do samodzielnej diagnostyki, które ułatwia identyfikowanie i rozwiązywanie problemów z klastrem. Diagnostyka AKS jest natywna w chmurze i można jej używać bez dodatkowych opłat.

Ta funkcja jest teraz dostępna w publicznej wersji zapoznawczej.

## <a name="open-aks-diagnostics"></a>Otwórz diagnostykę AKS

Aby uzyskać dostęp do diagnostyki AKS:

- Przejdź do klastra Kubernetes w [Azure Portal](https://portal.azure.com).
- Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** w lewym okienku nawigacji, co spowoduje otwarcie diagnostyki AKS.
- Wybierz kategorię, która najlepiej opisuje problem z klastrem przy użyciu słów kluczowych na kafelku strony głównej, lub wpisz słowo kluczowe, które najlepiej opisuje Twój problem na pasku wyszukiwania, na przykład _problemy z węzłem klastra_.

![Strona główna](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Wyświetl raport diagnostyczny

Po kliknięciu kategorii można wyświetlić raport diagnostyczny specyficzny dla danego klastra. Raport diagnostyczny jest inteligentnie wywoływany, jeśli wystąpił problem w klastrze z ikonami stanu. Aby przejść do szczegółów poszczególnych tematów, kliknij pozycję **więcej informacji** , aby wyświetlić szczegółowy opis problemu, zalecane akcje, linki do przydatnych dokumentów, metryk powiązanych i danych rejestrowania. Raporty diagnostyczne są generowane w sposób inteligentny na podstawie bieżącego stanu klastra po uruchomieniu różnych kontroli. Raporty diagnostyczne mogą być użytecznym narzędziem do lokalizowania problemu z klastrem i znajdowania następnych kroków w celu rozwiązania problemu.

![Raport diagnostyczny](./media/concepts-diagnostics/diagnostic-report.png)

![Rozwinięty raport diagnostyczny](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Szczegółowe informacje o klastrze

W usłudze **cluster Insights** są dostępne następujące testy diagnostyczne.

### <a name="cluster-node-issues"></a>Problemy z węzłem klastra

Problemy z węzłem klastra sprawdzają, czy występują problemy związane z węzłem, które mogą spowodować nieoczekiwane zachowanie klastra.

- Problemy z gotowością węzłów
- Awarie węzłów
- Niewystarczające zasoby
- Brak konfiguracji adresu IP węzła
- Awarie CNI węzła
- Nie znaleziono węzła
- Wyłącz zasilanie węzła
- Niepowodzenie uwierzytelniania węzła
- Node polecenia — nieodświeżony serwer proxy

### <a name="create-read-update--delete-operations"></a>Tworzenie, odczytywanie, aktualizowanie & operacji usuwania

CRUD operacji sprawdza wszystkie operacje CRUD, które mogą powodować problemy w klastrze.

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

Zarządzanie tożsamościami i zabezpieczeniami wykrywa błędy uwierzytelniania i autoryzacji, które mogą uniemożliwić komunikację z klastrem.

- Błędy autoryzacji węzła
- Błędy 401
- Błędy 403

## <a name="next-steps"></a>Następne kroki

Zbierz dzienniki, aby ułatwić dalsze Rozwiązywanie problemów z klastrem za pomocą [AKS Periscope](https://aka.ms/aksperiscope).

Zapoznaj się z [sekcją Klasyfikacja Practices](https://docs.microsoft.com/azure/architecture/operator-guides/aks/aks-triage-practices) w Przewodniku obsługi AKS Day-2.

Opublikuj swoje pytania lub opinie w witrynie [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) , dodając w tytule "[diag]".

---
title: Omówienie zabezpieczeń
description: Informacje o zabezpieczeniach na serwerach z obsługą usługi Azure Arc.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117031"
---
# <a name="azure-arc-for-servers-security-overview"></a>Usługa Azure ARC dla serwerów — Omówienie zabezpieczeń

W tym artykule opisano konfigurację zabezpieczeń i zagadnienia, które należy oszacować przed wdrożeniem serwerów z obsługą usługi Azure Arc w przedsiębiorstwie.

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

Każdy serwer z obsługą usługi Azure Arc ma zarządzaną tożsamość jako część grupy zasobów w ramach subskrypcji platformy Azure. Ta tożsamość reprezentuje serwer działający lokalnie lub w innym środowisku chmury. Dostęp do tego zasobu jest kontrolowany przy użyciu standardowej [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Na stronie [**Access Control (IAM)**](../../role-based-access-control/role-assignments-portal.md) w Azure Portal można sprawdzić, kto ma dostęp do serwera z włączonym usługą Azure Arc.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Kontrola dostępu do serwera z obsługą usługi Azure Arc" border="false" lightbox="./media/security-overview/access-control-page.png":::

Użytkownicy i aplikacje z uprawnieniami [współautora](../../role-based-access-control/built-in-roles.md#contributor) lub administratora dostępu do zasobu mogą wprowadzać zmiany w zasobie, w tym wdrażać lub usuwać [rozszerzenia](manage-vm-extensions.md) na komputerze. Rozszerzenia mogą zawierać dowolne skrypty, które są uruchamiane w kontekście uprzywilejowanym, więc Rozważ wszelkie współautorów zasobów platformy Azure, aby być pośrednim administratorem serwera.

Rola **dołączania maszyny połączonej z platformą Azure** jest dostępna na potrzeby dołączenia do skalowania i może odczytywać i tworzyć nowe serwery z obsługą łuku na platformie Azure. Nie można jej użyć do usunięcia serwerów już zarejestrowanych lub do zarządzania rozszerzeniami. Najlepszym rozwiązaniem jest przypisanie tej roli tylko do jednostki usługi Azure Active Directory (Azure AD) używanej do dołączania maszyn w odpowiedniej skali.

Użytkownicy będący członkiem roli **administratora zasobów maszyny połączonej z platformą Azure** mogą odczytywać, modyfikować, reonboard i usuwać maszyny. Ta rola została zaprojektowana do obsługi zarządzania serwerami z obsługą łuku, ale nie innymi zasobami w grupie zasobów lub subskrypcji.

## <a name="agent-security-and-permissions"></a>Zabezpieczenia i uprawnienia agenta

Aby zarządzać agentem usługi Azure Connected Machine (azcmagent) w systemie Windows, konto użytkownika musi być członkiem lokalnej grupy administratorów. W systemie Linux wymagane są uprawnienia dostępu do katalogu głównego.

Agent maszyny połączonej z platformą Azure składa się z trzech usług, które są uruchamiane na komputerze.

* Usługa hybrydowej Instance Metadata Service (himds) jest odpowiedzialna za wszystkie podstawowe funkcje łuku. Obejmuje to wysyłanie pulsu do platformy Azure, ujawnienie lokalnej usługi metadanych wystąpienia dla innych aplikacji, aby dowiedzieć się więcej o IDENTYFIKATORze zasobu platformy Azure i pobrać tokeny usługi Azure AD w celu uwierzytelnienia w innych usługach platformy Azure. Ta usługa jest uruchamiana jako konto usługi wirtualnej nieuprzywilejowanej w systemie Windows i jako użytkownik **himds** w systemie Linux.

* Usługa konfiguracji gościa (GCService) jest odpowiedzialna za ocenę Azure Policy na komputerze.

* Usługa rozszerzenia konfiguracji gościa (ExtensionService) jest odpowiedzialna za Instalowanie, aktualizowanie i usuwanie rozszerzeń (agentów, skryptów lub innego oprogramowania) na komputerze.

Usługi konfiguracji i rozszerzenia gościa działają jako system lokalny w systemie Windows i jako główne w systemie Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Korzystanie z tożsamości zarządzanej z serwerami z obsługą łuku

Domyślnie tożsamość przypisana przez system Azure Active Directory używana przez funkcję ARC może być używana tylko do aktualizowania stanu serwera z włączonym łukiem na platformie Azure. Na przykład *ostatni widoczny* stan pulsu. Opcjonalnie można przypisać dodatkowe role do tożsamości, jeśli aplikacja na serwerze używa tożsamości przypisanej do systemu do uzyskiwania dostępu do innych usług platformy Azure.

Gdy Instance Metadata Service hybrydowe są dostępne dla dowolnej aplikacji uruchomionej na maszynie, tylko autoryzowane aplikacje mogą zażądać tokenu usługi Azure AD dla tożsamości przypisanej do systemu. Przy pierwszej próbie uzyskania dostępu do identyfikatora URI tokenu Usługa generuje losowo wygenerowany kryptograficzny obiekt BLOB w lokalizacji w systemie plików, który może odczytać tylko zaufane obiekty wywołujące. Obiekt wywołujący musi odczytać plik (potwierdzający, że ma odpowiednie uprawnienia) i ponowić próbę wysłania żądania przez zawartość pliku w nagłówku autoryzacji w celu pomyślnego pobrania tokenu usługi Azure AD.

* W systemie Windows obiekt wywołujący musi być członkiem lokalnej grupy **administratorów** lub grupy **aplikacji rozszerzenia agenta hybrydowego** , aby odczytać obiekt BLOB.

* W systemie Linux obiekt wywołujący musi być członkiem grupy **himds** , aby odczytać obiekt BLOB.

## <a name="using-disk-encryption"></a>Korzystanie z szyfrowania dysków

Agent połączonej platformy Azure używa uwierzytelniania klucza publicznego do komunikowania się z usługą platformy Azure. Po dołączeniu serwera do usługi Azure Arc klucz prywatny jest zapisywany na dysku i używany za każdym razem, gdy Agent komunikuje się z platformą Azure. W przypadku kradzieży klucza prywatnego można użyć na innym serwerze w celu komunikowania się z usługą i działania tak, jakby był to oryginalny serwer. Dotyczy to również uzyskiwania dostępu do tożsamości przypisanej do systemu i wszystkich zasobów, do których tożsamość ma dostęp. Plik klucza prywatnego jest chroniony, aby zezwalać na dostęp do konta **himds** tylko do odczytu. Aby zapobiec atakom w trybie offline, zdecydowanie zalecamy użycie pełnego szyfrowania dysków (na przykład funkcji BitLocker, dm-crypt itp.) na woluminie systemu operacyjnego serwera.

## <a name="next-steps"></a>Następne kroki

Przed przeszacowaniem lub włączeniem serwerów z obsługą łuku na wielu maszynach hybrydowych Przejrzyj [Omówienie agenta połączonej maszyny](agent-overview.md) , aby poznać wymagania, szczegóły techniczne dotyczące agenta i metod wdrażania.

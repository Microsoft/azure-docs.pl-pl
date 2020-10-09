---
title: Zarządzanie kontem — rozwiązanie Azure VMware według portalu CloudSimple
description: Opisuje sposób zarządzania kontami w ramach rozwiązania Azure VMware według portalu CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025371"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Zarządzanie kontami w ramach rozwiązania Azure VMware według portalu CloudSimple

Utworzenie usługi CloudSimple powoduje utworzenie konta w witrynie CloudSimple. Konto jest skojarzone z subskrypcją platformy Azure, w której znajduje się usługa. Wszyscy użytkownicy z rolami właściciela i współautora w subskrypcji mają dostęp do portalu CloudSimple. Identyfikator subskrypcji platformy Azure i identyfikator dzierżawy skojarzone z usługą CloudSimple można znaleźć na stronie konta.

Aby zarządzać kontami w portalu CloudSimple, [uzyskaj dostęp do portalu](access-cloudsimple-portal.md) i wybierz pozycję **konto** w menu po stronie.

Wybierz pozycję **Podsumowanie** , aby wyświetlić informacje o konfiguracji CloudSimple firmy. Zostanie wyświetlona aktualna pojemność konfiguracji chmury, w tym liczba chmur prywatnych, łączny magazyn, Konfiguracja klastra vSphere, liczba węzłów i liczba rdzeni obliczeniowych. Łącze jest dołączone do zakupu dodatkowych węzłów, jeśli Bieżąca konfiguracja nie spełnia wszystkich Twoich potrzeb.

## <a name="email-alerts"></a>Alerty e-mail

Możesz dodać adresy e-mail wszystkich osób, które mają powiadamiać o zmianach w konfiguracji chmury prywatnej.

1. W obszarze **dodatkowe alerty e-mail** kliknij pozycję **Dodaj nowy**.
2. Wprowadź adres e-mail.
3. Naciśnij klawisz Return.  

Aby usunąć wpis, kliknij przycisk **X**.

## <a name="cloudsimple-operator-access"></a>Dostęp do operatora CloudSimple

Ustawienie dostęp operatora umożliwia CloudSimpleom Rozwiązywanie problemów, umożliwiając inżynierowi pomocy technicznej logowanie się do portalu CloudSimple.  Ustawienie jest domyślnie włączone. Wszystkie akcje wykonywane przez inżyniera pomocy technicznej po zalogowaniu się na koncie klienta są rejestrowane i dostępne do przeglądu na stronie **Activity**  >  **Inspekcja** aktywności.

Kliknij przełącznik **włączony dostęp operatora CloudSimple** , aby włączyć lub wyłączyć dostęp.

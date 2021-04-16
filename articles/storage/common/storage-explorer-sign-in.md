---
title: Zaloguj się do Eksplorator usługi Azure Storage | Microsoft Docs
description: Dokumentacja dotycząca logowania do Eksplorator usługi Azure Storage
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568730"
---
# <a name="sign-in-to-storage-explorer"></a>Zaloguj się do Eksplorator usługi Storage

Logowanie to zalecany sposób uzyskiwania dostępu do zasobów usługi Azure Storage za pomocą Eksplorator usługi Storage. Logując się, możesz korzystać z uprawnień kopii zapasowej usługi Azure AD, takich jak RBAC i listy ACL POSIX gen2. 

## <a name="how-to-sign-in"></a>Jak się zalogować

Aby zalogować się do Eksplorator usługi Storage, otwórz **okno dialogowe Łączenie.** Okno dialogowe Łączenie można otworzyć **na** pionowym pasku narzędzi po lewej stronie lub klikając pozycję Dodaj **konto...** na **panelu konta.**

Po otwarciu okna dialogowego wybierz **pozycję Subskrypcja** jako typ zasobu, z którym chcesz nawiązać połączenie, a następnie kliknij przycisk **Dalej.**

Teraz musisz wybrać środowisko platformy Azure, do którego chcesz się zalogować. Możesz wybrać dowolne ze znanych środowisk, na przykład Azure lub Azure (Chiny), lub dodać własne środowisko. Po wybraniu środowiska kliknij przycisk **Dalej.**

W tym momencie zostanie otwarta domyślna **przeglądarka** internetowa systemu operacyjnego i zostanie otwarta strona logowania. Aby uzyskać najlepsze wyniki, pozostaw to okno przeglądarki otwarte, dopóki używasz usługi Eksplorator usługi Storage lub co najmniej do momentu wykonania wszystkich oczekiwanych uwierzytelniania wieloskładnikowego. Po zakończeniu logowania możesz wrócić do Eksplorator usługi Storage.

## <a name="managing-accounts"></a>Zarządzanie kontami

Kontami platformy Azure, do których się zalogowano, można zarządzać i usuwać z **panelu konta**. Panel konta można **otworzyć,** klikając przycisk **Zarządzaj** kontami na pionowym pasku narzędzi po lewej stronie.

W **Panelu** konta zobaczysz wszystkie konta, do których się zalogowano. W ramach każdego konta będą:
- Dzierżawy, do których należy konto
- Dla każdej dzierżawy subskrypcje, do których masz dostęp

Domyślnie aplikacja Eksplorator usługi Storage tylko do Twojej dzierżawy głównej. Jeśli chcesz wyświetlić subskrypcje i zasoby z innej dzierżawy, musisz aktywować ją. Aby aktywować dzierżawę, zaznacz pole wyboru obok tej dzierżawy. Po zakończeniu pracy z dzierżawą możesz usunąć jej zaznaczenie, aby ją zdezaktywować. Nie można dezaktywować dzierżawy głównej.

Po aktywowaniu dzierżawy może być konieczne ponowne wjechanie poświadczeń, zanim Eksplorator usługi Storage będzie można załadować subskrypcje lub uzyskać dostęp do zasobów z dzierżawy. Ponowne wejrzecie poświadczeń zwykle jest spowodowane przez zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe (MFA). Mimo że uwierzytelniania wieloskładnikowego dla innej dzierżawy zostało już wykonane, być może trzeba będzie to zrobić ponownie. Aby ponownie wedytować poświadczenia, po prostu kliknij pozycję Ponownie wejdź **poświadczenia...**. Możesz również kliknąć pozycję Szczegóły **błędu...,** aby zobaczyć, dlaczego nie można załadować subskrypcji.

Po załadowaniu subskrypcji możesz wybrać te, które chcesz filtrować i wyfiltrować, zaznaczając lub zaznaczając ich pola wyboru.

Jeśli chcesz usunąć całe konto platformy Azure, kliknij przycisk **Usuń** obok konta.

## <a name="changing-where-sign-in-happens"></a>Zmienianie miejsca logowania

Domyślnie logowanie odbywa się w domyślnej przeglądarce internetowej **systemu operacyjnego.** Logowanie przy użyciu domyślnej przeglądarki internetowej usprawnia uzyskiwanie dostępu do zasobów zabezpieczonych za pośrednictwem zasad urzędu certyfikacji, takich jak uwierzytelniania wieloskładnikowego. Jeśli z jakiegoś powodu logowanie  przy użyciu domyślnej przeglądarki internetowej systemu operacyjnego nie działa, możesz zmienić miejsce lub sposób Eksplorator usługi Storage logowania.

W **obszarze**  >  **Ustawienia**  >  **Logowanie do aplikacji** odszukaj ustawienie Zaloguj się **przy** użyciu. Dostępne są trzy opcje:
- **Domyślna przeglądarka sieci Web:** logowanie nastąpi w domyślnej **przeglądarce internetowej systemu operacyjnego.** Ta opcja jest zalecana.
- **Zintegrowane logowanie:** logowanie będzie odbywać się w Eksplorator usługi Storage oknie. Ta opcja może być przydatna, jeśli próbujesz zalogować się jednocześnie przy użyciu wielu kont Microsoft (MSA). Jeśli wybierzesz tę opcję, mogą wystąpić problemy z niektórymi zasadami urzędu certyfikacji.
- **Przepływ kodu urządzenia:** Eksplorator usługi Storage kod do wprowadzenia w oknie przeglądarki. Ta opcja nie jest zalecana. Przepływ kodu urządzenia nie jest zgodny z wieloma zasadami urzędu certyfikacji.

## <a name="troubleshooting-sign-in-issues"></a>Rozwiązywanie problemów związanych z logowaniem

Jeśli masz problemy z logowaniem lub masz problemy z kontem platformy Azure po zalogowaniu się, zapoznaj się z sekcją logowania w Eksplorator usługi Storage [rozwiązywania problemów.](./storage-explorer-troubleshooting.md#sign-in-issues)
